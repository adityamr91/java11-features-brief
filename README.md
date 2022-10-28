# java11-features-brief


1. Running Java File with single command, no need for javac command explicitly, can directly run the file with java command and it implicitly compiles. 

2. new methods to the String class: 
```
isBlank()
lines()
strip()
stripLeading()
stripTrailing()
repeat()
```
3. Local-Variable Syntax for Lambda Parameters, This JEP adds support for the var keyword in lambda parameters.
```
 List<String> list = Arrays.asList("a", "b", "c");
  String result = list.stream()
          .map((var x) -> x.toUpperCase())
          .collect(Collectors.joining(","));
  System.out.println(result2);
  ```
However, the lambda can make type inference; the above example is equivalent to this:

```
  List<String> list = Arrays.asList("a", "b", "c");
  String result = list.stream()
          .map(x -> x.toUpperCase())
          .collect(Collectors.joining(","));
```
then, why this JEP adds var in lambda parameters? The benefit is now we can add annotations to the lambda parameters, see this example:


import org.jetbrains.annotations.NotNull;
```
  List<String> list = Arrays.asList("a", "b", "c", null);
  String result = list.stream()
          .map((@NotNull var x) -> x.toUpperCase())
          .collect(Collectors.joining(","));
  System.out.println(result3);
```  
4. Nested Based Access Control, It supports private access within nest members directly, no more via an auto-generated bridge method

Before Java 11 this was possible:
```
public class Main {
 
    public void myPublic() {
    }
 
    private void myPrivate() {
    }
 
    class Nested {
 
        public void nestedPublic() {
            myPrivate();
        }
    }
}
```
private method of the main class is accessible from the above-nested class in the above manner. But if we use Java Reflection, it will give an IllegalStateException.
```
Method method = ob.getClass().getDeclaredMethod("myPrivate");
method.invoke(ob);
```
Java 11 nested access control addresses this concern in reflection. java.lang.Class introduces three methods in the reflection API: getNestHost(), getNestMembers(), and isNestmateOf().

after java 11
```
public class Demo {
    private void privateMethod() {
        System.out.print("Private Method");
    }
    class NestedClass {
        public void callPrivateMethod() {
            privateMethod();
        }
    }
    public static void main(String[] args) {
        System.out.println(Demo.class.isNestmateOf(Demo.NestedClass.class)); //Demo class is nestmate of NestedClass
        System.out.println(Demo.NestedClass.class.isNestmateOf(Demo.class)); //NestedClass is nestmate of Demo class		
        System.out.println(Demo.NestedClass.class.getNestHost()); //Nest host of NestedClass
        System.out.println(Demo.class.getNestMembers()); //Nest host of Demo class		
    }
}
```
```
true
true
class Demo
[Ljava.lang.Class;@36baf30c
```
5. JEP 321: HTTP Client, This HTTP Client API, in the java.net.http package was introduced in Java 9, updated in Java 10, now a standard feature in Java 11.
```
 HttpClient httpClient = HttpClient.newBuilder()
            .version(HttpClient.Version.HTTP_1_1)
            .connectTimeout(Duration.ofSeconds(10))
            .build();
```
```
    HttpRequest request = HttpRequest.newBuilder()
            .GET()
            .uri(URI.create("https://httpbin.org/get"))
            .setHeader("User-Agent", "Java 11 HttpClient Bot")
            .build();

    HttpResponse<String> response =
      httpClient.send(request, HttpResponse.BodyHandlers.ofString());

    HttpHeaders headers = response.headers();
    headers.map().forEach((k, v) -> System.out.println(k + ":" + v));

    System.out.println(response.statusCode());
    System.out.println(response.body());
```	
6. Reading/Writing Strings to and from the Files, The readString() and writeString() static methods are added to the Files class for this purpose.
```
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class Demo
{
	public static void main(String[] args) throws IOException
	{
		Path path = Files.createTempFile("temporaryFile", ".txt");
		//Writing to the file
		Files.writeString(path, "Hello World");
		//Reading from the file
		String s = Files.readString(path);
		System.out.print(s);
	}
}
```
