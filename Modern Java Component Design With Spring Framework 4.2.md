# Modern Java Component Design with Spring Framework 4.2

## Component Classes
* @Service
* @Lazy
* @Autowired
* @Transactional
* ...

The annotations have a descriptive and runtime role.

Annotations can be composed. Example:

```
@Service
@Scope("session")
@Primary
@Transactional(rollbackFor=Exception.class)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyService {}

@MyService
public class MyBookAdminService { ... }
```

## Composable Annotations with Overridable Attributes

```
@Scope("session")
@Retention(RetentionPolicy.RUNTIME)
public @interface MySessionScoped {
    ScopedProxyMode proxyMode() default ScopedProxyMode.NO; // provide a default value but let the annotation users to override the default
}

@Transactional(rollbackFor=Exception.class)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyTransactional {
    boolean readOnly; // redeclares the read-only attribute. It does not define a default here: the user must explicitly provide a value (forcing the user to set a concrete value)
}
```

## Composable Annotations with Explicitly Declared Attributes

```
@Scope(scopeName="session")
@Retention(RetentionPolicy.RUNTIME)
public @interface MySessionScoped {
    @AliasFor(annotation=Scope.class, attribute="proxyMode")
    ScopeProxyMode mode() default ScopedProxyMode.NO;
}

@Transactional(rollbackFor=Exception.class)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyTransactional {
    @AliasFor(annotation=Transaction.class,attribute="readOnly")
    ...
}
```

## Configuration classes
Allows to define @Bean methods. Container for configuration..

```
@Configuration
@Profile("standalone")
@EnableTransactionManagement
public class MyBookAdminConfig {
    @Bean
    @Scope("session")
    public BookAdminService myBookAdminService(){
        ...
    }
}
```

## Default methods in configuration classes
```
@Configurationpublic class MyApplicationConfig implements MyBookAdminConfig { ... }

public interface MyBookAdminConfig{
    @Bean
    default BookAdminService myBookAdminService(){
        MyBookAdminService service = new MyBookAdminService();
        ...
    }
}
```

## Ordered Configuration Classes
Put `@Order(int)` annotations on Configuration classes. Lower order value configuration gets loaded first.

## Generics-based Injection Matching

```
@Bean
public MyRepository<Account> myAccountRepository() { ... }

@Bean
public MyRepository<Product> myProductRepository() { ... }

@Service
public class MyBookAdminService implements BookAdminService {
    @Autowired
    public MyBookAdminService(MyRepository<Account> repo) {
        // specific match, even with other MyRepository beans around
    }
}
```

The generic type will be taken into account to find the best match.

## Ordered Collection Injection
```
@Bean @Order(2)
public MyRepository<Account> myAccountRepositoryX() { ... }

@Bean @Order(1)
public MyRepository<Account> myAccountRepositoryY() { ... }

@Service
public class ... {
    @Autowired
    public MyBookAdminService(List<MyRepository<Account>> repos) {
        ...
    }
}
```

Another example with @Lazy:

```
@Bean
@Lazy
public MyRepository<Account> myAccountRepositoryX() { ... }

@Service
public class ... {
    @Autowired
    public MyBookAdminService(@Lazy MyRepository<Account> repo) {
        // repo will be a lazy-initializing proxy
    }
}
```

Better semantics for injection points. The first method invocation on the proxy will trigger the actual instanciation of the target bean.

Can be used to break bean cycles if we used @Lazy at least on one side.

## Component Declarations with JSR-250 and JSR-330

```
import javax.annotation.*;
import javax.inject.*;

@ManagedBean
public class MyBookAdminService implements BookAdminService {
    @Injection public MyBookAdminService(Provider<MyRepository<Account>> repo) {
        // repo will be a lazy handle, allowing for .get() access
    }

    @PreDestroy
    public void shutdown() {
        ...
    }
}
```

## Optional Injection points on Java 8


```
import javax.annotation.*;
import javax.inject.*;

@ManagedBean
public class MyBookAdminService implements BookAdminService {
    @Injection public MyBookAdminService(Optional<MyRepository<Account>> repo) {
        if(repo.isPresent()){
            ...
        }
    }

    @PreDestroy
    public void shutdown() {
        ...
    }
}
```

## Declarative Formatting with Java 8 Data-Time

```
import java.time.*;
import javax.validation.constraints.*;
import org.springframework.format.annotation.*;

public class Customer {
    // @DateTimeFormat(iso=ISO.DATE)
    private LocalDate birthDate; // Spring can infer the date time format based on the type

    @DateTimeFormat(pattern="M/d/yy h:mm")
    @NotNull
    @Past
    private LocalDateTime lastContact;
}
```

Also works with JodaTime.

## Declarative Formatting with JSR-354 Money and Currency

```
import javax.money.*;
import org.springframework.format.annotation.*;

public class Product {
    private MonetaryAmount basePrice;

    @NumberFormat(pattern="... #000.000#")
    private MonetaryAmount...
    ...
}
```

Also works with the Money & Currency API.

## Declarative Scheduling
```
@Async
public Future<Integer> sendEmail() {
    return new AsyncResult<Integer> (... );
}

@Async
public CompletableFuture<Integer> sendEmail() {
    return CompletableFuture.completedFuture(...);
}

@Scheduled(cron="0 0 12 * * ?")
@Scheduled(cron="0 0 18 * * ?") // Java 8 supports using twice the same annotation type on the method! (the annotation MUST opt-in to be repeatable)
public void performTempFileCleanup() {
    ...
}
```

Spring takes care of the boilerplate.

## Annotated MVC Controllers

```
@RestController
@CrossOrigin
public class MyRestController {
    @RequestMapping(valuer = "/books/{id}", method=GET)
    public Book findBook(@PathVariable long id) { // extract the variable
        ...
    }

    @RequestMapping("/books/new", method=POST)
    public void newBook(@Valid Book book){
        ...
    }
}
```

## STOMP on WebSocket

```
@Controller
public class MyStompController {
    ...
}
```
