# complex-controller-demo

> Spring MVC controller methods with @RequestMapping, @PathVariable, @RequestParam, and @RequestBody

[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://openjdk.org/)
[![Spring MVC](https://img.shields.io/badge/Spring%20MVC-6.2.5-blue.svg)](https://spring.io/projects/spring-framework)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A comprehensive demonstration of **Spring MVC controller methods** featuring `@RequestMapping` attributes, HTTP method shortcuts (`@GetMapping`, `@PostMapping`), parameter handling (`@PathVariable`, `@RequestParam`, `@RequestBody`), and response configuration.

## Features

- `@Controller` vs `@RestController`
- `@RequestMapping` with multiple attributes (path, method, params, headers, consumes, produces)
- HTTP method shortcuts (`@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`)
- `@PathVariable` for URL path variables
- `@RequestParam` for query parameters
- `@RequestBody` for JSON request body
- `@ResponseBody` for JSON response
- `@ResponseStatus` for HTTP status codes
- `params` condition matching (params vs no params)
- `consumes` and `produces` for media type control
- RESTful API design demonstration
- H2 database integration

## Tech Stack

- Spring Boot 3.4.5
- Spring MVC 6.2.5
- Spring Data JPA
- Java 21
- H2 Database 2.3.232
- Joda Money 2.0.2
- Lombok
- Maven 3.8+

## Getting Started

### Prerequisites

- JDK 21 or higher
- Maven 3.8+ (or use included Maven Wrapper)

### Quick Start

**Run the application:**

```bash
./mvnw spring-boot:run
```

**Test the API:**

```bash
# Get all coffees
curl http://localhost:8080/coffee/

# Get coffee by ID
curl http://localhost:8080/coffee/1

# Get coffee by name
curl "http://localhost:8080/coffee/?name=mocha"
```

## Configuration

### Application Properties

```properties
# JPA/Hibernate configuration
spring.jpa.hibernate.ddl-auto=none
spring.jpa.properties.hibernate.show_sql=true
spring.jpa.properties.hibernate.format_sql=true

# Error response configuration (for development only)
server.error.include-message=always
server.error.include-binding-errors=always
```

**Important:**
- `show_sql=true`: Show SQL statements (development only)
- `include-message=always`: Include error messages in response (development only)

## API Documentation

### Coffee API

#### 1. Get All Coffees

```bash
curl -X GET http://localhost:8080/coffee/
```

**Endpoint:** `GET /coffee/`  
**Condition:** No `name` parameter (`params = "!name"`)  
**Response:** List of all coffees

**Sample Response:**

```json
[
  {
    "id": 1,
    "name": "espresso",
    "price": "TWD 100.00",
    "createTime": "2025-10-16T16:52:02",
    "updateTime": "2025-10-16T16:52:02"
  },
  {
    "id": 2,
    "name": "latte",
    "price": "TWD 125.00",
    "createTime": "2025-10-16T16:52:02",
    "updateTime": "2025-10-16T16:52:02"
  }
]
```

#### 2. Get Coffee by ID

```bash
curl -X GET http://localhost:8080/coffee/1
```

**Endpoint:** `GET /coffee/{id}`  
**Path Variable:** `id` (Long)  
**Produces:** `application/json`

**Sample Response:**

```json
{
  "id": 1,
  "name": "espresso",
  "price": "TWD 100.00",
  "createTime": "2025-10-16T16:52:02",
  "updateTime": "2025-10-16T16:52:02"
}
```

#### 3. Get Coffee by Name

```bash
curl -X GET "http://localhost:8080/coffee/?name=mocha"
```

**Endpoint:** `GET /coffee/`  
**Condition:** Has `name` parameter (`params = "name"`)  
**Query Parameter:** `name` (String)

**Sample Response:**

```json
{
  "id": 4,
  "name": "mocha",
  "price": "TWD 150.00",
  "createTime": "2025-10-16T16:52:02",
  "updateTime": "2025-10-16T16:52:02"
}
```

### Order API

#### 1. Get Order by ID

```bash
curl -X GET http://localhost:8080/order/1
```

**Endpoint:** `GET /order/{id}`  
**Path Variable:** `id` (Long)

**Sample Response:**

```json
{
  "id": 1,
  "customer": "Ray Chu",
  "items": [
    {
      "id": 4,
      "name": "mocha",
      "price": "TWD 150.00",
      "createTime": "2025-10-16T16:52:02",
      "updateTime": "2025-10-16T16:52:02"
    }
  ],
  "state": "INIT",
  "createTime": "2025-10-16T16:52:02",
  "updateTime": "2025-10-16T16:52:02"
}
```

#### 2. Create New Order

```bash
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "customer": "Ray Chu",
    "items": ["mocha", "latte"]
  }'
```

**Endpoint:** `POST /order/`  
**Consumes:** `application/json` (required)  
**Produces:** `application/json` (required)  
**Request Body:** `NewOrderRequest` object  
**Response Status:** `201 CREATED`

**Sample Response:**

```json
{
  "id": 2,
  "customer": "Ray Chu",
  "items": [
    {
      "id": 4,
      "name": "mocha",
      "price": "TWD 150.00",
      "createTime": "2025-10-16T16:52:02",
      "updateTime": "2025-10-16T16:52:02"
    },
    {
      "id": 2,
      "name": "latte",
      "price": "TWD 125.00",
      "createTime": "2025-10-16T16:52:02",
      "updateTime": "2025-10-16T16:52:02"
    }
  ],
  "state": "INIT",
  "createTime": "2025-10-16T16:52:05",
  "updateTime": "2025-10-16T16:52:05"
}
```

## Key Components

### CoffeeController

```java
@Controller
@RequestMapping("/coffee")
public class CoffeeController {
    
    @Autowired
    private CoffeeService coffeeService;
    
    /**
     * Get all coffees
     * Condition: When NO name parameter exists
     * params = "!name" means "name parameter must not exist"
     */
    @GetMapping(path = "/", params = "!name")
    @ResponseBody
    public List<Coffee> getAll() {
        return coffeeService.getAllCoffee();
    }
    
    /**
     * Get coffee by ID
     * @PathVariable: Extract id from URL path
     * produces: Only produce application/json response
     */
    @RequestMapping(path = "/{id}", method = RequestMethod.GET,
            produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public Coffee getById(@PathVariable Long id) {
        Coffee coffee = coffeeService.getCoffee(id);
        return coffee;
    }
    
    /**
     * Get coffee by name
     * Condition: When name parameter exists
     * params = "name" means "name parameter must exist"
     * @RequestParam: Extract name from query parameter
     */
    @GetMapping(path = "/", params = "name")
    @ResponseBody
    public Coffee getByName(@RequestParam String name) {
        return coffeeService.getCoffee(name);
    }
}
```

**Annotation Explanation:**

- **@Controller**: Mark class as MVC controller
- **@RequestMapping("/coffee")**: Base path for all methods
- **@ResponseBody**: Convert return value to HTTP response body
- **params = "!name"**: Match when `name` parameter does NOT exist
- **params = "name"**: Match when `name` parameter EXISTS
- **@PathVariable**: Extract variable from URL path
- **@RequestParam**: Extract value from query parameter

**URL Routing:**
- `GET /coffee/` → `getAll()` (no name param)
- `GET /coffee/?name=mocha` → `getByName()` (has name param)
- `GET /coffee/1` → `getById()` (path variable)

### CoffeeOrderController

```java
@RestController
@RequestMapping("/order")
@Slf4j
public class CoffeeOrderController {
    
    @Autowired
    private CoffeeOrderService orderService;
    
    @Autowired
    private CoffeeService coffeeService;
    
    /**
     * Get order by ID
     * @RestController automatically includes @ResponseBody
     */
    @GetMapping("/{id}")
    public CoffeeOrder getOrder(@PathVariable("id") Long id) {
        return orderService.get(id);
    }
    
    /**
     * Create new order
     * consumes: Only accept application/json
     * produces: Only produce application/json
     * @RequestBody: Extract JSON from request body
     * @ResponseStatus: Set response status to 201 CREATED
     */
    @PostMapping(path = "/", 
                 consumes = MediaType.APPLICATION_JSON_VALUE,
                 produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseStatus(HttpStatus.CREATED)
    public CoffeeOrder create(@RequestBody NewOrderRequest newOrder) {
        log.info("Receive new Order {}", newOrder);
        Coffee[] coffeeList = coffeeService.getCoffeeByName(newOrder.getItems())
                .toArray(new Coffee[] {});
        return orderService.createOrder(newOrder.getCustomer(), coffeeList);
    }
}
```

**Annotation Explanation:**

- **@RestController**: `@Controller` + `@ResponseBody` combined
- **consumes = APPLICATION_JSON_VALUE**: Only accept JSON requests
- **produces = APPLICATION_JSON_VALUE**: Only produce JSON responses
- **@RequestBody**: Auto-convert JSON to Java object
- **@ResponseStatus(CREATED)**: Set HTTP status to 201

### NewOrderRequest

```java
@Getter
@Setter
@ToString
public class NewOrderRequest {
    private String customer;        // Customer name
    private List<String> items;     // List of coffee names
}
```

**Sample JSON:**

```json
{
  "customer": "Ray Chu",
  "items": ["mocha", "latte"]
}
```

## Spring MVC Annotations

### @RequestMapping Attributes

```java
@RequestMapping(
    path = "/coffee",              // URL path
    method = RequestMethod.GET,    // HTTP method
    params = "name",               // Parameter condition
    headers = "X-Custom-Header",   // Header condition
    consumes = "application/json", // Accept Content-Type
    produces = "application/json"  // Response Content-Type
)
```

### HTTP Method Shortcuts

```java
// Equivalent shortcuts
@GetMapping("/coffee")           // GET
@PostMapping("/order")           // POST
@PutMapping("/order/{id}")       // PUT
@DeleteMapping("/order/{id}")    // DELETE
@PatchMapping("/order/{id}")     // PATCH

// Instead of verbose @RequestMapping
@RequestMapping(path = "/coffee", method = RequestMethod.GET)
```

### Parameter Annotations

```java
// @PathVariable: From URL path
@GetMapping("/coffee/{id}")
public Coffee get(@PathVariable Long id) { }

// @RequestParam: From query string
@GetMapping("/coffee")
public Coffee get(@RequestParam String name) { }

// @RequestBody: From request body
@PostMapping("/order")
public Order create(@RequestBody NewOrderRequest request) { }

// @RequestHeader: From HTTP headers
@GetMapping("/coffee")
public Coffee get(@RequestHeader("User-Agent") String userAgent) { }
```

### Response Annotations

```java
// @ResponseBody: Convert to HTTP response
@ResponseBody
public Coffee get() { }

// @ResponseStatus: Set HTTP status code
@ResponseStatus(HttpStatus.CREATED)
public Order create() { }

// @RestController: Auto @ResponseBody for all methods
@RestController
public class OrderController { }
```

## Params Condition Matching

### How It Works

```java
// Method 1: Match when NO name parameter
@GetMapping(path = "/", params = "!name")
public List<Coffee> getAll() { }

// Method 2: Match when name parameter EXISTS
@GetMapping(path = "/", params = "name")
public Coffee getByName(@RequestParam String name) { }
```

**URL Routing:**

| URL | Matched Method | Explanation |
|-----|----------------|-------------|
| `GET /coffee/` | `getAll()` | No `name` param → matches `params = "!name"` |
| `GET /coffee/?name=mocha` | `getByName()` | Has `name` param → matches `params = "name"` |
| `GET /coffee/1` | `getById()` | Path variable → different mapping |

**Advanced Params:**

```java
// Multiple conditions
@GetMapping(params = {"name", "size"})  // Both must exist

// Value matching
@GetMapping(params = "name=mocha")  // name must equal "mocha"

// Negation
@GetMapping(params = "!debug")  // debug param must not exist
```

## Media Type Control

### consumes (Request Content-Type)

```java
@PostMapping(path = "/order/", 
             consumes = MediaType.APPLICATION_JSON_VALUE)
public Order create(@RequestBody NewOrderRequest request) { }
```

**Test:**

```bash
# ✅ Correct: Content-Type is application/json
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -d '{"customer": "Ray", "items": ["mocha"]}'

# ❌ Wrong: Content-Type is application/pdf
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/pdf" \
  -d '{"customer": "Ray", "items": ["mocha"]}'

# Error: 415 Unsupported Media Type
```

### produces (Response Content-Type)

```java
@GetMapping(path = "/{id}", 
            produces = MediaType.APPLICATION_JSON_VALUE)
public Coffee getById(@PathVariable Long id) { }
```

**Test:**

```bash
# ✅ Correct: Accept is application/json (or not specified)
curl -H "Accept: application/json" http://localhost:8080/coffee/1

# ❌ Wrong: Accept is application/xml (not supported)
curl -H "Accept: application/xml" http://localhost:8080/coffee/1

# Error: 406 Not Acceptable
```

## HTTP Status Codes

### Common Status Codes

| Code | Status | Usage | This Project |
|------|--------|-------|-------------|
| 200 | OK | Successful GET | `getAll()`, `getById()`, `getByName()`, `getOrder()` |
| 201 | Created | Successful POST | `create()` with @ResponseStatus(CREATED) |
| 204 | No Content | Successful DELETE | - |
| 400 | Bad Request | Invalid request body | Missing required fields |
| 404 | Not Found | Resource not found | Coffee or Order not exists |
| 406 | Not Acceptable | Accept header mismatch | Accept: application/xml |
| 415 | Unsupported Media Type | Content-Type mismatch | Content-Type: application/pdf |

### Set Response Status

```java
// Automatic 200 OK
@GetMapping("/order/{id}")
public Order getOrder(@PathVariable Long id) { }

// Explicit 201 CREATED
@PostMapping("/order/")
@ResponseStatus(HttpStatus.CREATED)
public Order create(@RequestBody NewOrderRequest request) { }

// 204 NO_CONTENT (for delete)
@DeleteMapping("/order/{id}")
@ResponseStatus(HttpStatus.NO_CONTENT)
public void delete(@PathVariable Long id) { }
```

## Testing

### cURL Examples

**1. Get All Coffees:**

```bash
curl -v http://localhost:8080/coffee/
```

**2. Get Coffee by ID:**

```bash
curl -v http://localhost:8080/coffee/1
```

**3. Get Coffee by Name:**

```bash
curl -v "http://localhost:8080/coffee/?name=mocha"
```

**4. Get Order:**

```bash
curl -v http://localhost:8080/order/1
```

**5. Create Order (Success):**

```bash
curl -v -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "customer": "Ray Chu",
    "items": ["mocha", "latte"]
  }'

# Response: 201 CREATED
```

**6. Create Order (Error - Wrong Content-Type):**

```bash
curl -v -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/pdf" \
  -H "Accept: application/json" \
  -d '{
    "customer": "Ray Chu",
    "items": ["mocha"]
  }'

# Response: 415 Unsupported Media Type
```

**7. Create Order (Error - Wrong Accept):**

```bash
curl -v -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -H "Accept: application/xml" \
  -d '{
    "customer": "Ray Chu",
    "items": ["mocha"]
  }'

# Response: 406 Not Acceptable
```

### Postman Examples

**Create Order:**

- **Method**: POST
- **URL**: `http://localhost:8080/order/`
- **Headers**:
  - `Content-Type`: `application/json`
  - `Accept`: `application/json`
- **Body** (raw JSON):
  ```json
  {
    "customer": "Ray Chu",
    "items": ["mocha", "latte"]
  }
  ```

## @Controller vs @RestController

### @Controller

```java
@Controller
@RequestMapping("/coffee")
public class CoffeeController {
    
    @GetMapping("/")
    @ResponseBody  // Required for JSON response
    public List<Coffee> getAll() {
        return coffeeService.getAllCoffee();
    }
}
```

**Characteristics:**
- Traditional MVC controller
- Needs `@ResponseBody` for each JSON method
- Can return view names (for template engines)

### @RestController

```java
@RestController
@RequestMapping("/order")
public class OrderController {
    
    @GetMapping("/{id}")
    // No @ResponseBody needed!
    public Order getOrder(@PathVariable Long id) {
        return orderService.get(id);
    }
}
```

**Characteristics:**
- `@Controller` + `@ResponseBody` combined
- Automatic JSON response for all methods
- Best for RESTful APIs

**Selection Guide:**
- **@Controller**: For traditional MVC (returning views)
- **@RestController**: For RESTful APIs (returning JSON/XML)

## Best Practices

### 1. Use HTTP Method Shortcuts

```java
// ✅ Recommended: Use shortcuts
@GetMapping("/orders")
@PostMapping("/orders")
@PutMapping("/orders/{id}")
@DeleteMapping("/orders/{id}")

// ❌ Not recommended: Verbose @RequestMapping
@RequestMapping(path = "/orders", method = RequestMethod.GET)
@RequestMapping(path = "/orders", method = RequestMethod.POST)
```

### 2. Explicit Path Variable Names

```java
// ✅ Recommended: Explicit name
@GetMapping("/orders/{orderId}")
public Order get(@PathVariable("orderId") Long orderId) { }

// ⚠️ Acceptable: Same name
@GetMapping("/orders/{id}")
public Order get(@PathVariable Long id) { }  // Variable name = path name
```

### 3. RequestParam with Defaults

```java
// ✅ Recommended: Set defaults and required
@GetMapping("/orders")
public List<Order> getOrders(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @RequestParam(required = false) String status) {
    // Implementation
}
```

### 4. Specify Media Types

```java
// ✅ Recommended: Explicit media types
@PostMapping(path = "/orders",
             consumes = MediaType.APPLICATION_JSON_VALUE,
             produces = MediaType.APPLICATION_JSON_VALUE)
public Order create(@RequestBody OrderRequest request) { }

// ⚠️ Acceptable: Default (any media type)
@PostMapping("/orders")
public Order create(@RequestBody OrderRequest request) { }
```

### 5. Set Proper HTTP Status

```java
// ✅ Recommended: Explicit status codes
@PostMapping("/orders")
@ResponseStatus(HttpStatus.CREATED)  // 201
public Order create(@RequestBody OrderRequest request) { }

@DeleteMapping("/orders/{id}")
@ResponseStatus(HttpStatus.NO_CONTENT)  // 204
public void delete(@PathVariable Long id) { }

@PutMapping("/orders/{id}")
@ResponseStatus(HttpStatus.OK)  // 200 (default)
public Order update(@PathVariable Long id, @RequestBody OrderRequest request) { }
```

### 6. Request Validation

```java
// Add Jakarta Bean Validation
import jakarta.validation.Valid;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotEmpty;

@PostMapping("/orders")
@ResponseStatus(HttpStatus.CREATED)
public Order create(@Valid @RequestBody NewOrderRequest request) { }

// NewOrderRequest with validation
public class NewOrderRequest {
    @NotBlank(message = "Customer name is required")
    private String customer;
    
    @NotEmpty(message = "Items list cannot be empty")
    private List<String> items;
}
```

## Common Issues

### Issue 1: 415 Unsupported Media Type

**Error:**

```json
{
  "timestamp": "2025-10-16T16:52:02.000+00:00",
  "status": 415,
  "error": "Unsupported Media Type",
  "message": "Content-Type 'application/pdf' is not supported"
}
```

**Cause:** Request Content-Type doesn't match `consumes` attribute

**Solution:**

```bash
# Ensure Content-Type is application/json
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -d '{"customer": "Ray", "items": ["mocha"]}'
```

### Issue 2: 406 Not Acceptable

**Error:**

```json
{
  "timestamp": "2025-10-16T16:52:02.000+00:00",
  "status": 406,
  "error": "Not Acceptable",
  "message": "Could not find acceptable representation"
}
```

**Cause:** Request Accept header doesn't match `produces` attribute

**Solution:**

```bash
# Ensure Accept is application/json or omit it
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"customer": "Ray", "items": ["mocha"]}'
```

### Issue 3: 400 Bad Request

**Error:**

```json
{
  "timestamp": "2025-10-16T16:52:02.000+00:00",
  "status": 400,
  "error": "Bad Request",
  "message": "Required request body is missing"
}
```

**Cause:** Missing request body for `@RequestBody`

**Solution:**

```bash
# Include request body
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -d '{
    "customer": "Ray Chu",
    "items": ["mocha"]
  }'
```

### Issue 4: Params Condition Not Matching

**Problem:** Wrong method is called

**Example:**

```bash
# Want: getAll() - all coffees
curl http://localhost:8080/coffee/

# Get: Error if name param exists
curl "http://localhost:8080/coffee/?name=mocha"
```

**Solution:** Check `params` attribute matches request

```java
// No name param → getAll()
@GetMapping(path = "/", params = "!name")

// Has name param → getByName()
@GetMapping(path = "/", params = "name")
```

## Database Schema

**schema.sql:**

```sql
drop table t_coffee if exists;
drop table t_order if exists;
drop table t_order_coffee if exists;

create table t_coffee (
    id bigint auto_increment,
    create_time timestamp,
    update_time timestamp,
    name varchar(255),
    price bigint,
    primary key (id)
);

create table t_order (
    id bigint auto_increment,
    create_time timestamp,
    update_time timestamp,
    customer varchar(255),
    state integer not null,
    primary key (id)
);

create table t_order_coffee (
    coffee_order_id bigint not null,
    items_id bigint not null
);

insert into t_coffee (name, price, create_time, update_time) 
    values ('espresso', 10000, now(), now());
insert into t_coffee (name, price, create_time, update_time) 
    values ('latte', 12500, now(), now());
insert into t_coffee (name, price, create_time, update_time) 
    values ('capuccino', 12500, now(), now());
insert into t_coffee (name, price, create_time, update_time) 
    values ('mocha', 15000, now(), now());
insert into t_coffee (name, price, create_time, update_time) 
    values ('macchiato', 15000, now(), now());
```

## RESTful API Design

### Best Practices

**1. Use Proper HTTP Methods:**

```java
// ✅ Recommended
GET    /orders        // List all orders
GET    /orders/{id}   // Get specific order
POST   /orders        // Create new order
PUT    /orders/{id}   // Update entire order
PATCH  /orders/{id}   // Update partial order
DELETE /orders/{id}   // Delete order
```

**2. Use Plural Nouns:**

```java
// ✅ Recommended
/orders
/coffees

// ❌ Not recommended
/order
/coffee
```

**3. Use Nested Resources:**

```java
// ✅ Recommended
GET /orders/{orderId}/items        // Get order items
POST /orders/{orderId}/items       // Add item to order

// ❌ Not recommended
GET /order-items?orderId=1
```

**4. Use Query Parameters for Filtering:**

```java
// ✅ Recommended
GET /orders?status=PAID&customer=Ray

// Implementation
@GetMapping("/orders")
public List<Order> getOrders(
    @RequestParam(required = false) String status,
    @RequestParam(required = false) String customer) {
    // Filter logic
}
```

**5. Return Appropriate Status Codes:**

```java
// ✅ Recommended
@PostMapping("/orders")
@ResponseStatus(HttpStatus.CREATED)  // 201
public Order create() { }

@DeleteMapping("/orders/{id}")
@ResponseStatus(HttpStatus.NO_CONTENT)  // 204
public void delete() { }
```

## Best Practices Demonstrated

1. **@RequestMapping Attributes**: path, method, params, consumes, produces
2. **HTTP Method Shortcuts**: @GetMapping, @PostMapping
3. **Parameter Handling**: @PathVariable, @RequestParam, @RequestBody
4. **Response Configuration**: @ResponseBody, @ResponseStatus
5. **Params Condition**: Dynamic routing based on query parameters
6. **Media Type Control**: Restrict request/response formats
7. **RESTful API Design**: Standard HTTP methods and status codes

## References

- [Spring MVC Documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc)
- [Spring Boot Web Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/web.html)
- [RESTful API Design Best Practices](https://restfulapi.net/)
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

## License

MIT License - see [LICENSE](LICENSE) file for details.

## About Us

我們主要專注在敏捷專案管理、物聯網（IoT）應用開發和領域驅動設計（DDD）。喜歡把先進技術和實務經驗結合，打造好用又靈活的軟體解決方案。近來也積極結合 AI 技術，推動自動化工作流，讓開發與運維更有效率、更智慧。持續學習與分享，希望能一起推動軟體開發的創新和進步。

## Contact

**風清雲談** - 專注於敏捷專案管理、物聯網（IoT）應用開發和領域驅動設計（DDD）。

- 🌐 官方網站：[風清雲談部落格](https://blog.fengqing.tw/)
- 📘 Facebook：[風清雲談粉絲頁](https://www.facebook.com/profile.php?id=61576838896062)
- 💼 LinkedIn：[Chu Kuo-Lung](https://www.linkedin.com/in/chu-kuo-lung)
- 📺 YouTube：[雲談風清頻道](https://www.youtube.com/channel/UCXDqLTdCMiCJ1j8xGRfwEig)
- 📧 Email：[fengqing.tw@gmail.com](mailto:fengqing.tw@gmail.com)

---

**⭐ 如果這個專案對您有幫助，歡迎給個 Star！**
