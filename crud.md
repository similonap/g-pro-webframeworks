# Delete en Update

Tot nu toe hebben we vooral nog maar nieuwe producten toegevoegd en opgelijst. Om een volledige CRUD \(Create, Read, Update en Delete\) applicatie te hebben moeten we dus nog Update en Delete implementeren.

### Delete

Het eerste wat we moeten doen is onze `IProductRepository` interface aanpassen zodat er ook een delete mogelijk is. We voegen dus 

```csharp
void Delete(Product product);
```

toe aan onze interface en implementeren die als volgt

```csharp
public void Delete(Product product)
{
    this.products.Remove(product);
}
```

Nu moeten we nog de Action schrijven om de delete uit te voeren. Het eerste wat we hier doen is het opvragen van het product uit onze repository aan de hand van de id. Als deze gevonden is dan kunnen we deze verwijderen. Net zoals met de `Create` actie plaatsen we nu in TempData een bericht dat het product verwijderd is. 

```csharp
[HttpGet]
public IActionResult Delete(int id)
{
    Product product = this.productRepository.Get(id);

    if (product != null)
    {
        this.productRepository.Delete(product);
        TempData["Message"] = $"{product.Name} was deleted succesfully";
        return RedirectToAction("Index", "Product");
    }

    return NotFound();
}
```

Nu we deze actie hebben aangemaakt moeten we enkel nog maar onze views aanpassen zodat we nu ook producten kunnen verwijderen. 

We willen gebruik kunnen maken van icons in **fontawesome** want we willen een button maken met een vuilbakje op. Daarom moeten we nog deze css nog toevoegen aan onze gesharede `_Layout.cshtml` 

```markup
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.7.0/css/all.css" integrity="sha384-lZN37f5QGtY3VHgisS14W3ExzMWZxybE1SJSEsQp9S+oqd12jhcu+A56Ebc1zFSJ" crossorigin="anonymous">
```

Nu voegen we aan de tabel nog een extra kolom toe voor de delete icon toe te plaatsen

```markup
<td><a asp-controller="Product" asp-action="Delete" asp-route-id="@product.Id" class="btn btn-labeled btn-danger"><i class="fas fa-trash"></a></td>
```

en bovenaan nog een lege header voor de kolom

```markup
<th scope="col"></th>
```

Als we dit nu proberen dan werkt dit perfect. Maar we willen voorkomen dat de gebruiker perongeluk producten verwijderd uit onze winkel door op de knop te drukken. We willen nog een popup zien die de gebruiker eerst vraagt of hij zeker is dat hij het product wil verwijderen.

Onderaan Index view gaan we nu een aantal Modals genereren die we kunnen gebruiken om een bevestigingsscherm te laten zien. 

```markup
@foreach (Product product in Model.Products)
{
<div class="modal fade" id="delete-modal-@product.Id" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLabel">Delete @product.Name</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <div class="modal-body">
                Are you sure you want to delete @product.Name
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                <a asp-controller="Product" asp-action="Delete" asp-route-id="@product.Id" role="button" class="btn btn-danger">Delete</a>
            </div>
        </div>
    </div>
</div>
}

```

We doen weer een loop over alle producten in het model en maken voor elk product een aparte modal met zijn eigen tekst. Als Id nemen we delete-modal- gevolgd door het product id, zodat we de juiste modal kunnen aanroepen bij het klikken van de overeenkomstige button. We zorgen ook dat de link in deze modal wel naar de delete action verwijst.

We passen nu ook nog de link naar de delete page aan zodat dit een button wordt die eerst het bevestigingsscherm laat zien vooraleer hij het product gaat verwijderen

```markup
<td><button type="button" data-toggle="modal" data-target="#delete-modal-@product.Id" class="btn btn-labeled btn-danger"><i class="fas fa-trash"></button></td>
```

![](.gitbook/assets/image%20%2876%29.png)

![](.gitbook/assets/image%20%2875%29.png)



