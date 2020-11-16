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



