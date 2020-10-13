# Dependency Injection

Op dit moment hebben we gewoon in de `Controller` een lijst met objecten gemaakt en deze gebruikt om te tonen aan de gebruiker. We willen er voor zorgen dat onze applicatie klaar is voor het gebruik van een echte database, maar dit gaan we pas in latere hoofdstukken zien. We gaan dus tijdelijk de objecten in het geheugen bijhouden.

## Repository

Om dit te verwezenlijken is er een bekend software patroon. Dit is het repository pattern. We maken een interface waarmee de applicatie kan `Product` objecten op te vragen zonder zich aan te trekken of deze opgeslagen wordt in een database of in memory.

We maken in de `Model` map een nieuwe `Interface` `IProductRepostory` met de inhoud

```csharp
public interface IProductRepository
{
    IQueryable<Product> GetAll();
    Product Get(int id);
}
```

We gebruiken hier `IQueryable` als return type om hier zo algemeen mogelijk te zijn voor later. Dit zou even goed een `List` kunnen geweest zijn. 

Nu maken we in de `Model` map een nieuwe klasse `ProductsInMemoryRepository` die de `IProductRepository` implementeert.

```csharp
public class ProductsInMemoryRepository
{
    private List<Product> products;

    public ProductsInMemoryRepository()
    {
        products = new List<Product>
        {
            new Product { Id = 1, Name = "Fluffy Llama", Description = "A fluffy llama that is very nice for small children", Price = 13.99M },
            new Product { Id = 2, Name = "Colorful Llama", Description = "A colorful llama that will make you warm with joy", Price = 20.99M },
        };
    }

    public IQueryable<Product> GetAll()
    {
        return products.AsQueryable();
    }
    
    public Product Get(int id)
    {
        foreach (Product product in products)
        {
            if (product.Id == id)
            {
                return product;
            }
        }
        return null;
    }
}
```

We hebben hier een lijst met producten in de constructor vullen we de lijst op met een aantal default waarden. We implementeren de interface door de `GetAll` methode in te vullen. We geven hier gewoon de lijst van producten terug. We maken ook een `Get` functie die een product gaat teruggeven op basis van zijn id.

We passen de `ProductController` een beetje aan zodat we nu deze klasse gebruiken in plaats van elke keer de `List` aan te maken.

```csharp
public class ProductController : Controller
{

    private IProductRepository productRepository;

    public ProductController()
    {
        this.productRepository = new ProductsInMemoryRepository();
    }
    public IActionResult Index()
    {
        return View(productRepository.GetAll());
    }
    public IActionResult Details(int id)
    {
        Product product = this.productRepository.Get(id);
        if (product != null)
        {
            return View(product);
        }
        return NotFound();
    }
}
```

We passen ook nog in `Views/Product` het `Index.cshtml` bestand aan dat dit ook gebruik maakt van de `IQueryable` interface.

```text
@model IQueryable<Product>
```

## Klasse afhankelijkheid

Men spreekt van een afhankelijkheid als een klasse een externe klasse nodig heeft om zijn werk te kunnen doen. Net zoals ik, wanneer ik dit lesmateriaal intyp, afhankelijk ben van bijvoorbeeld mijn computer. Verwijder de computer en ik zou het lesmateriaal niet kunnen voltooien.

In het voorbeeld van de `ProductController` is er een sterke afhankelijkheid ontstaan met de `ProductInMemoryRepository` klasse. De controller kan niet meer werken zonder.

Dependency injection in de klasse, die ze nodig heeft, is een goede manier om onze klassen te ontkoppelen. Alhoewel elke klasse zelf een nieuw object kan instanciëren en toegang heeft tot de methoden en eigenschappen ervan, is daarom niet zo dat die klasse daar thuis hoort. Het injecteren van een klassenafhankelijkheid is een goede manier om de single responsability patroon toe te passen. Dit patroon bevordert een goed objectgericht ontwerp. Dit principe houdt in dat elke klasse verantwoordelijkheid is voor een specifiek deel van de functionaliteit die door de applicatie wordt geleverd, en dat die verantwoordelijkheid volledig door de klasse moet worden ingekapseld.

## Inversion of control

Inversion of Control is een principe in software engineering waar de controle van objecten worden uitbesteed aan een framework \(in ons geval het framework van ASP.NET Core MVC\). De `ProductController` is niet meer verantwoordelijk voor het aanmaken van de `ProductsInMemoryRepository` klasse, maar we vertrouwen er op dat het framework dit wel zal doen voor ons.

We passen dus de `ProductController` aan zodat de `IProductRepository` via de constructor binnenkomt. We trekken ons dus niets aan van waar die komt, of wat de implementatie is.

```csharp
public class ProductController : Controller
{
    private IProductRepository productRepository;

    public ProductController(IProductRepository productRepository)
    {
        this.productRepository = productRepository;
    }
    
    public IActionResult Index()
    {
        return View(productRepository.GetAll());
    }   
     
    public IActionResult Details(int id)
    {
        Product product = this.productRepository.Get(id);
        if (product != null)
        {
            return View(product);
        }
        return NotFound();
    }
}
```

Als we de applicatie opstarten en naar de Products gaan zien dan krijgen we deze 'cryptische' error message:

![](../.gitbook/assets/image%20%2846%29.png)

Maar eigenlijk is deze niet zo cryptisch. Hij zegt dat we geen service kunnen vinden voor het type `IProductRepository`. Dat klopt, we hebben ook nergens aangegeven wat voor repository het framework voor ons moet aanmaken, dus hij kan dit natuurlijk niet doen op dit moment.

Dit doen we in de `ConfigureServices` methode in de `Startup.cs` klasse.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();

    services.AddSingleton<IProductRepository, ProductsInMemoryRepository>();
}
```

Hier geven we aan dat we een service registreren als een Singleton \(een klasse die 1 keer geinstantieerd wordt voor de gehele levensduur van de applicatie\). `IProductRepository` is het soort interface dat je aanbiedt en `ProductsInMemoryRepository` is de implementatie die het framework zal aanbieden. Dus elke keer als een `Controller` bijvoorbeeld een product repository wil, dan zal hij deze krijgen. Nu wordt het ook gemakkelijker om een ander soort `Repository` aan te bieden als we deze op termijn gaan vervangen met een echte database.

Nu zal het voorbeeld terug werken. Maar nu is de koppeling tussen de `ProductController` en de `ProductsInMemoryRepository` volledig weg gewerkt.

## Nog een extra methode

We willen nu een `Create` methode toevoegen aan de `IProductRepository` dat we ook nieuwe `Product` objecten kunnen opslaan. We voegen dus een extra methode toe aan de `IProductRepository`

```aspnet
void Create(Product product);
```

en implementeren die in de `ProductsInMemoryRepository` 

```csharp
public void Create(Product product)
{
     int maxId = products.Select(x => x.Id).DefaultIfEmpty(0).Max() + 1;
     product.Id = maxId;
     products.Add(product);
}
```

Omdat we onze Id's automatisch willen ophogen naar de maximale ID + 1 hebben we hier gebruik gemaakt van de Max functie uit LINQ. We tellen er altijd 1 bij zodat we altijd een unieke Id hebben die 1 hoger is dan de vorige. Daarna voegen we het product toe aan de lijst.

Omdat de eeuwen oude term in de programmeerwereld zegt: "Eat your own dogfood" gaan we onze eigen methode gebruiken in onze constructor zodat we nu ook zelf geen Id's meer moeten aanmaken.

```csharp
public ProductsInMemoryRepository()
{
    products = new List<Product>();
    Create(new Product { Name = "Fluffy Llama", Description = "A fluffy llama that is very nice for small children", Price = 13.99M });
    Create(new Product { Name = "Colorful Llama", Description = "A colorful llama that will make you warm with joy", Price = 20.99M });
}
```

