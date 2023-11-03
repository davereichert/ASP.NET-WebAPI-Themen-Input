## Inhaltsverzeichnis

1. [Routing in Webanwendungen](#routing)
    - [Beispiel](#routing-beispiel)
    - [Codebeispiel: BooksController](#routing-codebeispiel)
2. [ASP.NET Core Controller-Methoden-Attribute](#controller-attribute)
    - [[HttpGet]](#httpget)
    - [[HttpPost]](#httppost)
    - [[HttpPut]](#httpput)
    - [[HttpDelete]](#httpdelete)
3. [Parameterbinding in .NET Web-APIs](#parameterbinding)
    - [[FromBody]](#frombody)
        - [Zusammenhang mit dem HTTP-Body](#frombody-zusammenhang)
        - [Codebeispiel](#frombody-codebeispiel)
    - [[FromUri]](#fromuri)
        - [Zusammenhang mit der URI](#fromuri-zusammenhang)
        - [Codebeispiel](#fromuri-codebeispiel)
4. [Dependency Injection (DI) in ASP.NET Core](#di)
    - [AddTransient](#addtransient)
    - [AddScoped](#addscoped)
    - [AddSingleton](#addsingleton)


## Routing in Webanwendungen

Routing ist ein wichtiger Teil jeder Webanwendung. Es bestimmt, welche Seite angezeigt wird, wenn ein bestimmter URL aufgerufen wird. Dabei wird der URL in verschiedene Teile zerlegt: den Controller und die dazugehörige Action. Der Controller entscheidet, welche Logik ausgeführt wird, und die Action bestimmt, welche spezifische Funktion innerhalb des Controllers aufgerufen wird.

### Beispiel

Angenommen, du hast eine Webseite für ein Online-Shop. Wenn ein Kunde die Seite "/produkte" aufruft, würde der Router den "ProduktController" auswählen und die "index" Action aufrufen. In dieser Action könnte die Logik implementiert sein, um alle verfügbaren Produkte aus der Datenbank abzurufen und anzuzeigen.

```csharp
public class BooksController : ApiController
{
// GET /api/books
public IEnumerable<Book> Get()
{
// Hier würde die Logik stehen, um alle Bücher abzurufen und zurückzugeben
}

// GET /api/books/{id}
public Book Get(int id)
{
    // Hier würde die Logik stehen, um das Buch mit der angegebenen ID abzurufen und zurückzugeben
}

// POST /api/books
public void Post([FromBody] Book newBook)
{
    // Hier würde die Logik stehen, um ein neues Buch hinzuzufügen
}

// PUT /api/books/{id}
public void Put(int id, [FromBody] Book updatedBook)
{
    // Hier würde die Logik stehen, um ein vorhandenes Buch zu aktualisieren
}

// DELETE /api/books/{id}
public void Delete(int id)
{
    // Hier würde die Logik stehen, um ein Buch zu löschen
}
```

## ASP.NET Core Controller-Methoden-Attribute

1. **[HttpGet]**  
   Dieses Attribut wird verwendet, um eine Methode in einem Controller für HTTP GET-Anfragen zuzuordnen. Hier ist ein Beispiel:

   ```csharp
   [HttpGet]
   public IActionResult GetItem(int id)
   {
       // Code, um ein Element abzurufen
       return Ok(result);
   }
    ```
   **[HttpPost]**  
Dieses Attribut wird verwendet, um eine Methode in einem Controller für HTTP POST-Anfragen zuzuordnen. Hier ist ein Beispiel:

```csharp
[HttpPost]
public IActionResult CreateItem([FromBody] Item item)
{
    // Code, um ein neues Element zu erstellen
    return CreatedAtRoute("GetItem", new { id = item.Id }, item);
}
```
### [HttpPut]
Dieses Attribut wird verwendet, um eine Methode in einem Controller für HTTP PUT-Anfragen zuzuordnen. Hier ist ein Beispiel:

```csharp
[HttpPut("{id}")]
public IActionResult UpdateItem(int id, [FromBody] Item updatedItem)
{
    // Code, um ein vorhandenes Element zu aktualisieren
    return Ok(updatedItem);
}
```
### [HttpDelete]
Dieses Attribut wird verwendet, um eine Methode in einem Controller für HTTP DELETE-Anfragen zuzuordnen. Hier ist ein Beispiel:

```csharp
[HttpDelete("{id}")]
public IActionResult DeleteItem(int id)
{
    // Code, um ein Element zu löschen
    return NoContent();
}
```

## Parameterbinding in .NET Web-APIs

In .NET Web-APIs werden Daten oft aus verschiedenen Teilen einer HTTP-Anfrage abgerufen, um Aktionen in Controllern auszuführen. Das System, das diese Zuordnung verwaltet, wird als "Parameterbinding" bezeichnet. Es erlaubt, dass Daten aus verschiedenen Teilen einer Anfrage (z.B. URL, Abfragezeichenfolge, Header, Body) an die Parameter von Controller-Methoden gebunden werden.

### [FromBody]

Die `[FromBody]`-Anmerkung gibt an, dass ein Parameterwert aus dem Body der HTTP-Anfrage abgerufen werden soll. Dies ist besonders nützlich bei POST-, PUT- und PATCH-Anfragen, bei denen Daten oft im Body der Anfrage gesendet werden, typischerweise als JSON oder XML.


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

Die `[FromUri]`-Anmerkung gibt an, dass ein Parameterwert aus der URI der HTTP-Anfrage abgerufen werden soll. Dies ist besonders nützlich bei GET-Anfragen, bei denen Daten oft in der URL oder der Abfragezeichenfolge enthalten sind. Das Uri steht für Unifotm Resource identifier


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
# Dependency Injection (DI) in ASP.NET Core

Dependency Injection (DI) ist ein Software-Design-Muster, das die Kopplung zwischen Komponenten in einem System verringert. Es ermöglicht das Einfügen von Abhängigkeiten von außen, anstatt sie innerhalb einer Komponente zu erstellen. ASP.NET Core verfügt über ein integriertes DI-System, das die Registrierung und Auflösung von Diensten erleichtert.

In ASP.NET Core gibt es drei Hauptarten, wie Dienste registriert werden können:

## 1. AddTransient

Mit AddTransient wird ein neuer Service-Instanzen jedes Mal erstellt, wenn er angefordert wird. Das bedeutet, dass verschiedene Controller und Aktionen unterschiedliche Instanzen eines transienten Dienstes erhalten.

```csharp
public interface ITransientService
{
    string GetOperationId();
}

public class TransientService : ITransientService
{
    private readonly Guid _operationId;

    public TransientService()
    {
        _operationId = Guid.NewGuid();
    }

    public string GetOperationId() => _operationId.ToString();
}

// In der Startup-Klasse oder im ConfigureServices-Methode:
services.AddTransient<ITransientService, TransientService>();
```
Das Guid steht für Globally Unique Identifier

## 2. AddScoped
Ein AddScoped-Service erstellt eine Instanz für jeden einzelnen HTTP-Anforderungszyklus. Das bedeutet, dass alle Controller und Aktionen, die während einer einzelnen HTTP-Anforderung erstellt werden, dieselbe Instanz eines scoped Dienstes verwenden.

```csharp
public interface IScopedService
{
    string GetOperationId();
}

public class ScopedService : IScopedService
{
    private readonly Guid _operationId;

    public ScopedService()
    {
        _operationId = Guid.NewGuid();
    }

    public string GetOperationId() => _operationId.ToString();
}

// In der Startup-Klasse oder im ConfigureServices-Methode:
services.AddScoped<IScopedService, ScopedService>();
````
## 3. AddSingleton
Ein AddSingleton-Service erstellt eine Instanz, die während der gesamten Anwendungslebensdauer beibehalten wird. Dies bedeutet, dass alle Controller und Aktionen, unabhängig von der Anzahl der HTTP-Anforderungen, dieselbe Singleton-Instanz verwenden.

```csharp
public interface ISingletonService
{
    string GetOperationId();
}

public class SingletonService : ISingletonService
{
    private readonly Guid _operationId;

    public SingletonService()
    {
        _operationId = Guid.NewGuid();
    }

    public string GetOperationId() => _operationId.ToString();
}

// In der Startup-Klasse oder im ConfigureServices-Methode:
services.AddSingleton<ISingletonService, SingletonService>();
```

