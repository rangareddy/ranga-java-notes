## Java 11

### Launching JShell
```
jshell
```

#### JShell Shortcuts

Shift-Tab

**Variable Declaration** - After a complete expression, hold down <shift> while pressing <tab>, then release and press "v"
```sh
jshell> class Employee {}
jshell> new Employee()
jshell> Employee e = new Employee()
```
**Auto Import** - hold down <shift> while pressing <tab>, then release and press "i"
```
jshell> Duration
0: Do nothing
1: import: java.time.Duration
2: import: javax.xml.datatype.Duration
```
**Method Creation** - After a complete expression or statement, hold down <shift> while pressing <tab>, then release and press "m"
```
10 + 20 --> Place the curser at 20 then press Shift + Tab relase then press m
int getSum() { return 10 + 20; }
```

**List the all variables** 
```
jshell> /vars
|    int $1 = 2
|    int $2 = 3
|    int[] $3 = int[10] { 0, 1, 4, 9, 16, 25, 36, 49, 64, 81 }
```

**List the all methods** 
```sh
jshell> /methods
|    int getSum()
```
**List the all**
```sh
jshell> /list

   1 : 1 + 1
   2 : $1 + 1
   3 : IntStream.range(0,10).map(i -> i * i).toArray()
   4 : int getSum() { return 10 + 20; }
```

## Collection Literals

```sh
jshell> List<String> fullNames = Arrays.asList("Ranga Reddy", "Raja Sekahar Reddy", "Nishanth Reddy");
fullNames ==> [Ranga Reddy, Raja Sekahar Reddy, Nishanth Reddy]

jshell> List<String> names = List.of("Ranga", "Reddy", "Nishanth");
names ==> [Ranga, Reddy, Nishanth]

jshell> Set<String> fruitNames = Set.of("Orange", "Grape", "Apple");
fruitNames ==> [Orange, Grape, Apple]

jshell> Map<Integer,String> employeeMap = Map.of(1, "Ranga", 2, "Nishanth")
employeeMap ==> {2=Nishanth, 1=Ranga}

Note: Map.of() supported 10 elements only

jshell> Map<Integer,String> personMap = Map.ofEntries(Map.entry(1, "Ranga"), Map.entry(2, "Nishanth"))
personMap ==> {2=Nishanth, 1=Ranga}

```

## Streams

## Process

## HttpClient

**Initial Setup**
module-info.java

```
module com.baeldung.java9.httpclient {   
  requires jdk.incubator.httpclient;
}
```

The API consists of 3 core classes:
* HttpRequest – represents the request to be sent via the HttpClient
* HttpClient – behaves as a container for configuration information common to multiple requests
* HttpResponse – represents the result of an HttpRequest call

### HttpRequest

**Setting URI**
```
HttpRequest.newBuilder(new URI("https://postman-echo.com/get"))
 
HttpRequest.newBuilder().uri(new URI("https://postman-echo.com/get"))
```
**Specifying the HTTP Method**
```
GET()
POST(BodyProcessor body)
PUT(BodyProcessor body)
DELETE(BodyProcessor body)
```

**Setting HTTP Protocol Version**
```
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/get"))
  .version(HttpClient.Version.HTTP_2)
  .GET()
  .build();
```

**Setting Headers**
```
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/get"))
  .headers("key1", "value1", "key2", "value2")
  .GET()
  .build();

HttpRequest request2 = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/get"))
  .header("key1", "value1")
  .header("key2", "value2")
  .GET()
  .build();
```
**Setting a Timeout**
```
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/get"))
  .timeout(Duration.of(10, SECONDS))
  .GET()
  .build();
```
### Setting a Request Body

```
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/post"))
  .POST(HttpRequest.noBody())
  .build();
```

**StringBodyProcessor**
```
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/post"))
  .headers("Content-Type", "text/plain;charset=UTF-8")
  .POST(HttpRequest.BodyProcessor.fromString("Sample request body"))
  .build();
```

**InputStreamBodyProcessor**
```
byte[] sampleData = "Sample request body".getBytes();
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/post"))
  .headers("Content-Type", "text/plain;charset=UTF-8")
  .POST(HttpRequest.BodyProcessor
   .fromInputStream(() -> new ByteArrayInputStream(sampleData)))
  .build();
```

**ByteArrayProcessor**
```
byte[] sampleData = "Sample request body".getBytes();
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/post"))
  .headers("Content-Type", "text/plain;charset=UTF-8")
  .POST(HttpRequest.BodyProcessor.fromByteArray(sampleData))
  .build();
```

**FileProcessor**
```
byte[] sampleData = "Sample request body".getBytes();
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/post"))
  .headers("Content-Type", "text/plain;charset=UTF-8")
  .POST(HttpRequest.BodyProcessor.fromByteArray(sampleData))
  .build();
```

### HttpClient

**Setting a Proxy**
```
HttpResponse<String> response = HttpClient
  .newBuilder()
  .proxy(ProxySelector.getDefault())
  .build()
  .send(request, HttpResponse.BodyHandler.asString());
```

**Setting Authenticator for a Connection**
```
HttpResponse<String> response = HttpClient.newBuilder()
  .authenticator(new Authenticator() {
    @Override
    protected PasswordAuthentication getPasswordAuthentication() {
      return new PasswordAuthentication(
        "username", 
        "password".toCharArray());
    }
}).build()
  .send(request, HttpResponse.BodyHandler.asString());
```

**Send Requests – Sync vs. Async**
```
HttpResponse<String> response = HttpClient.newBuilder()
  .build()
  .send(request, HttpResponse.BodyHandler.asString());

CompletableFuture<HttpResponse<String>> response = HttpClient.newBuilder()
  .build()
  .sendAsync(request, HttpResponse.BodyHandler.asString());
```
**multiple responses**
```
List<URI> targets = Arrays.asList(
  new URI("https://postman-echo.com/get?foo1=bar1"),
  new URI("https://postman-echo.com/get?foo2=bar2"));
HttpClient client = HttpClient.newHttpClient();
List<CompletableFuture<String>> futures = targets.stream()
  .map(target -> client
    .sendAsync(
      HttpRequest.newBuilder(target).GET().build(),
      HttpResponse.BodyHandler.asString())
    .thenApply(response -> response.body()))
  .collect(Collectors.toList());
```

**Setting Executor for Asynchronous Calls**
```
ExecutorService executorService = Executors.newFixedThreadPool(2);

CompletableFuture<HttpResponse<String>> response1 = HttpClient.newBuilder()
  .executor(executorService)
  .build()
  .sendAsync(request, HttpResponse.BodyHandler.asString());

CompletableFuture<HttpResponse<String>> response2 = HttpClient.newBuilder()
  .executor(executorService)
  .build()
  .sendAsync(request, HttpResponse.BodyHandler.asString());
```

### HttpResponse Object

**URI of Response Object**
```
ssertThat(request.uri().toString(), equalTo("http://stackoverflow.com"));
assertThat(response.uri().toString(), equalTo("https://stackoverflow.com/"));
```

**Headers from Response**
```
HttpResponse<String> response = HttpClient.newHttpClient()
  .send(request, HttpResponse.BodyHandler.asString());
HttpHeaders responseHeaders = response.headers();
```

**Java 11 HttpClient**
```
// Pre Java 11
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandler.asString());

// Java 11
HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
```

### Example
```
jshell> import java.net.*
jshell> import java.net.http.*
jshell> HttpClient client = HttpClient.newBuilder().build()
client ==> jdk.internal.net.http.HttpClientImpl@1165b38(1)
jshell> HttpRequest request = HttpRequest.newBuilder().uri(URI.create("https://www.gutenberg.org/files/11/11-0.txt")).GET().build()
request ==> https://www.gutenberg.org/files/11/11-0.txt GET
jshell> client.sendAsync(request, HttpResponse.BodyHandlers.ofString()).thenAccept(response -> System.out.println(response.body())
```