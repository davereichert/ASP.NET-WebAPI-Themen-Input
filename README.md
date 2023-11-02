## Parameterbinding in .NET Web-APIs

In .NET Web-APIs werden Daten oft aus verschiedenen Teilen einer HTTP-Anfrage abgerufen, um Aktionen in Controllern auszuführen. Das System, das diese Zuordnung verwaltet, wird als "Parameterbinding" bezeichnet. Es erlaubt, dass Daten aus verschiedenen Teilen einer Anfrage (z.B. URL, Abfragezeichenfolge, Header, Body) an die Parameter von Controller-Methoden gebunden werden.

### [FromBody]

Die `[FromBody]`-Anmerkung gibt an, dass ein Parameterwert aus dem Body der HTTP-Anfrage abgerufen werden soll. Dies ist besonders nützlich bei POST-, PUT- und PATCH-Anfragen, bei denen Daten oft im Body der Anfrage gesendet werden, typischerweise als JSON oder XML.

#### Zusammenhang mit dem HTTP-Body:
Wenn ein Client (z.B. ein Webbrowser) eine HTTP-Anfrage an den Server sendet, kann er Daten im Body dieser Anfrage senden. Dies ist besonders häufig bei Formulareinsendungen oder AJAX-Anfragen der Fall. Mit `[FromBody]` kann die Web-API diese Daten direkt aus dem Body extrahieren und sie einem Parameter zuweisen.

#### Codebeispiel:
```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```
```csharp

[HttpPost]
public IHttpActionResult CreateProduct([FromBody] Product newProduct)
{
    // Hier kann newProduct verarbeitet werden, z.B. in einer Datenbank speichern
    return Ok(newProduct);
}
```
### [FromUri]

Die `[FromUri]`-Anmerkung gibt an, dass ein Parameterwert aus der URI der HTTP-Anfrage abgerufen werden soll. Dies ist besonders nützlich bei GET-Anfragen, bei denen Daten oft in der URL oder der Abfragezeichenfolge enthalten sind.

#### Zusammenhang mit der URI:
In einer HTTP-Anfrage können Daten als Teil der URL oder der Abfragezeichenfolge übergeben werden. Zum Beispiel könnte die URL `/products/5` die ID `5` für ein Produkt angeben. Mit `[FromUri]` kann die Web-API diese Daten direkt aus der URI extrahieren und sie einem Parameter zuweisen.

#### Codebeispiel:
```csharp
[HttpGet]
public IHttpActionResult GetProduct([FromUri] int id)
{
    Product product = FindProductById(id); // Zum Beispiel eine Methode, die ein Produkt aus einer Datenbank holt
    if (product == null)
    {
        return NotFound();
    }
    return Ok(product);
}
```
