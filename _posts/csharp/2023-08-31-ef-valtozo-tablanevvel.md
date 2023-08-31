---
title: Entity Framework összekapcsolása változó táblanévvel
date: 2023-08-31 13:47:00 +0100
categories: [Programozás, C#]
tags: [entity framework, adatbázis]
img_path: /assets/img/2023/08/
image:
  path: ef-tabla.png
  alt: Entity Framework összekapcsolása változó táblanévvel
---

Az Entity Framework *(későbbiekben EF)* egy nagyszerű [ORM](https://hu.wikipedia.org/wiki/Objektum-rel%C3%A1ci%C3%B3s_lek%C3%A9pz%C3%A9s) eszköz, azonban vannak olyan helyzetek, amikor nem a hagyományos, internetes útmutatót követve kell megvalósítanunk az elképzelésünket.  Az egyik ilyen eset, pl. a változó táblanév kapcsolása ugyanolyan szerkezetű entitás osztályokhoz. A következő pár sorban bemutatom, hogy hogyan tudjuk ezt megoldani.

# Alaphelyzet

Készítettünk vagy örököltünk egy adatbázist, ahol a táblák létrehozását egy másik folyamat vezérli. Legyen ez az adatbázis az időjárás előrejelzéshez köthető, ahol minden hónapra egy új tábla generálódik a napi adatokkal, a szerkezete, mindig ugyanaz:

![Időjárás adatbázis](elorejelzes_db.png)

# Entitás - Tábla leképezés

Az *EF*-ben általában az osztály neve megegyezik az adatbázis tábla nevével, de ezt a szabály felülírhatjuk kétféleképpen is:

Attribútum *(Data Annotation)* segítségével:
```csharp
[Table("idojaras_2023-08")]
public class Idojaras
{
    [Key]
    public int nap { get; set; }
    public int maximum_ho { get; set; }
    public int minimum_ho { get; set; }
    public string jellemzes { get; set; } = null!;
}
```

Vagy Fluent API segítéségével:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Idojaras>().ToTable("idojaras_2023-08");
}
```

Azonban, mint láthatjuk eléggé kötve a kezünk, mert így csak egyetlen egy táblát a 2023. augusztusi időjárás adatait tudjuk leképezni a programunkba. Ha több táblát is szeretnénk elérni, akkor a fenti megoldások nem működnek.

# DbContext konstruktor

Erre megoldásként készíthetünk egy olyan konstruktort a Context osztályunkhoz, amelyik paraméterként vár egy dátumot, amit konfigurálásnál hozzá tud fűzni a tábla nevéhez és azzal fogja már megoldani az egyeztetést.

```csharp
public class ElorejelzesContext : DbContext
{
    public ElorejelzesContext(DateTime datum)
    {
        _datum = datum;
    }

    private readonly DateTime _datum;
    public string TablaNev => "idojaras_" + _datum.ToString("yyyy-MM");

    public DbSet<Idojaras> Idojarasok { get; set; } = null!;

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Idojaras>().ToTable(TablaNev);
    }

    // Adatbázis kapcsolat beállítása pl. az OnConfiguring metódus felülírásával
    // ...
}
```

# Tesztelés

Egy teszt projektben gyorsan le is ellenőrizhetjük, hogy működik-e a megoldásunk:

```csharp
[TestMethod()]
public void ContextKapcsolodas()
{
    var context = new ElorejelzesContext(new DateTime(2023, 08, 31));
    bool kacsolatSiker = context.Database.CanConnect();
    Assert.IsTrue(kacsolatSiker);

    int maximumHo = 29; // Augusztus 1.-ei adat az adatbázisunkból
    var elsoNap = context.Idojarasok.FirstOrDefault(x => x.nap == 1);
    Assert.AreEqual(maximumHo, elsoNap?.maximum_ho);
}
```

A teszt sikeresen lefutott, létrehozott egy Context példányt, amiben a *TablaNeve* érték megfelel az augsztusi tábla nevének, ezért a **ToTable**  megfelelően társította az adatbázis tábláját az entitásra.

# Változó életciklusú DbContextek

Idáig remekül haladunk, csak mi van abban az esetben, ha a DbContextet nem tudom kézzel példányosítani, - vagy inkább nem szeretném - hanem mondjuk a függőségi injektálásra *([DI](https://hu.wikipedia.org/wiki/A_f%C3%BCgg%C5%91s%C3%A9g_befecskendez%C3%A9se))* szeretném bízni.

A program indulásakor létrehozza a DbContext példányt, de ez az egy példány él azzal a dátummal, amit a legelején megadtunk neki. Ha a program futása közben megváltozik a dátum, akkor a DbContextünk nem fogja ezt követni, mert az már a konstruktorban megkapta a dátumot és az élettartama alatt nem változik.

Vannak olyan alkalmazások is, ahol nincs megoldva az objektumok eldobása desktruktorral, szemétgyűjtővel (pl. Blazor) ezért használjuk a **DbContextFactory**-t a kapcsolat létrehozásához.

Ki kell először egészíteni az elkészített Context osztályunkat egy másik konstruktorral:

```csharp
public ElorejelzesContext(DbContextOptions options, DateTime datum) : base(options)
{
    _datum = datum;
}
```

Majd készítünk egy DbContextFactory osztályt, ami felelős lesz a Context létrehozásáért. Mivel ő a létrehozó, ezért neki is átadjuk a dátumot, vagy azt a változót, amiből a táblanevet generáljuk, majd ezt átadjuk a Context példánynak is.

```csharp
public class ElorejelzesContextFactory : IDbContextFactory<ElorejelzesContext>
{
    private readonly DateTime _datum;

    public ElorejelzesContextFactory(DateTime datum)
    {
        _datum = datum;
    }

    public ElorejelzesContext CreateDbContext()
    {
        var optionsBuilder = new DbContextOptionsBuilder<ElorejelzesContext>();
        optionsBuilder.ReplaceService<IModelCacheKeyFactory, ElorejelzesModelCacheFactory>();
        // Adatbázis kapcsolat beállítása
        // ...
        return new ElorejelzesContext(optionsBuilder.Options, _datum);
    }
}
```

Feltűnhet egy eddig szóba nem került fogalom, az **IModelCachheKeyFactory**.
E nélkül, ha több DbContextFactoryt készítünk, akkor mindegyik ugyanazokra az entitásokra fog mutatni (pl. mindig 2023. augusztusát). Erre azért van szükség, hogy a gyorsítótár (cache) egy kulcs alapján el tudja választani a DbContext leképezéseket, így akár több hónap adatait is le tudjuk kérdezni és nem kell az objektumok eldobását külön megírnunk.

```csharp
public class ElorejelzesModelCacheFactory : IModelCacheKeyFactory
{
    public object Create(DbContext context, bool designTime)
    {
        if (context is ElorejelzesContext _context)
        {
            return (context.GetType(), _context.TablaNev);
        }
        return context.GetType();
    }
}
```
Ha a Context példányunk megfelel az **EloreJelzesContext**-nek, akkor a dátum táblanevet is hozzáfűzi a context nevéhez, így nem fog összeütközni a gyorsítótárban meghívásnál, nem a legutolsó példányt fogja visszaadni.

# Teszt kiegészítés

A jelenlegi tesztünket kiegészítjük a DbContextFactory használatával és az új két paraméteres konstruktorral:

```csharp
[TestMethod()]
public void ContextFactoryKapcsolodas()
{
    var contextFactory = new ElorejelzesContextFactory(new DateTime(2023, 08, 31));
    var context = contextFactory.CreateDbContext();
    bool kacsolatSiker = context.Database.CanConnect();
    Assert.IsTrue(kacsolatSiker);

    int maximumHo = 29; // Augusztus 1.
    var elsoNap = context.Idojarasok.FirstOrDefault(x => x.nap == 1);
    Assert.AreEqual(maximumHo, elsoNap?.maximum_ho);
}
```

![Teszt kiegészítés](egyseg_teszt.png)

# Összefoglalás

Ezzel a megoldással sikerülhet EF segítségével több ugyanolyan szerkezetű táblát lekérdeznünk eltérő tábla névvel. A DbContextFactory segítségével pedig a DbContext élettartama is megfelelően kezelhető.
