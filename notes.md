# Module Notes

## 1. Module evidence and additional record

Evidence from the end of **Create a web API with ASP.NET Core controllers**:

- Module status: **Completed, 200 XP**.
- The sample `WeatherForecastController` inherits from `ControllerBase`, which provides standard HTTP request handling for web APIs.
- `[ApiController]` enables API behaviors such as parameter source inference, required attribute routing, and improved model-validation errors.
- `[Route("[controller]")]` maps the controller to `/weatherforecast` after removing the `Controller` suffix from the class name.
- `[HttpGet(Name = "GetWeatherForecast")]` exposes the `Get()` action as an HTTP `GET` endpoint that returns five generated forecasts.
- `Controllers/PizzaController.cs` contains the retained `[ApiController]` with the route `[controller]`, which maps to `/Pizza`.
- The controller exposes `GET` all, `GET` by id, `POST`, `PUT`, and `DELETE` endpoints.
- The controller uses `NotFound()`, `CreatedAtAction(...)`, and `NoContent()` responses.
- `Program.cs` registers controllers with `AddControllers()` and exposes them with `MapControllers()`.
- `Services/PizzaService.cs` provides the shared in-memory data store with Classic Italian and Veggie as its initial pizzas.

Additional record: the repository currently has no existing sales, order, revenue, or sales-summary implementation. The duplicate `PizzasController` was removed; all pizza API operations now use `PizzaController` and `PizzaService`.

Historical runtime evidence captured from Swagger at `http://localhost:5283/swagger/v1/swagger.json` before the controller consolidation:

- `GET /api/Pizzas` returned `200 OK`.
- The response content type was `application/json; charset=utf-8`.
- The response body contained:

```json
[
    {
        "id": 1,
        "name": "Margherita",
        "isGlutenFree": false
    },
    {
        "id": 2,
        "name": "Hawaiian",
        "isGlutenFree": false
    }
]
```

- Swagger documented the remaining controller operations: `POST /api/Pizzas`, `GET /api/Pizzas/{id}`, `PUT /api/Pizzas/{id}`, and `DELETE /api/Pizzas/{id}`.

Current API routes are `GET|POST /Pizza` and `GET|PUT|DELETE /Pizza/{id}`.

## 2. Part 2 sales summary function

The following is the text copy of the added working sales-summary function. It totals quantity and revenue from sales lines:

```csharp
public record SaleLine(string ProductName, int Quantity, decimal UnitPrice);

public static (int UnitsSold, decimal Revenue) GetSalesSummary(
    IEnumerable<SaleLine> sales)
{
    var salesList = sales.ToList();

    return (
        salesList.Sum(sale => sale.Quantity),
        salesList.Sum(sale => sale.Quantity * sale.UnitPrice));
}
```