+++
title = 'Dependency Inversion'
date = '2026-03-10'
tags = ['dev']
#author = 'adriano'
header_image = "/images/open_closed.png"
+++

## Dependencies, Dependencies, Dependencies

### Object Oriented Programming

Robert Martin introduced a set of principles to address common problems in software design: code that is hard to reuse and change, when changes are done in one place something breaks in another, and as the project grows it becomes easier to make mistakes. 

Those principles were packaged in the acronym: **SOLID**

### Dependency Inversion Principle

Here I will explore the **D**, which stands for **Dependency Inversion Principle**.

It states that high-level modules should not depend on low-level modules. Both should depend on abstractions.  
Abstractions should not depend on details. Details should depend on abstractions. This decouples the code and makes it more maintainable and testable.

```
High-level module  ──depends on──▶  «interface»
                                         ▲
Low-level module   ──implements──────────┘
```

### Dependency Injection

Dependency Injection is a design pattern that helps achieve DIP. Instead of a class creating its own dependencies, they are provided from the outside — *injected* into it.

DI is typically implemented using an **IoC (Inversion of Control) container** in modern frameworks. The container takes ownership of wiring dependencies together so you don't have to do it manually.

When using an IoC container, you need to define the **scope** of each dependency — how long an instance lives and how it is shared.

Common scopes across frameworks:

| Scope | Lifetime |
|---|---|
| **Singleton** | One instance for the entire application lifetime |
| **Scoped / Request** | One instance per request or unit of work |
| **Transient / Prototype** | A new instance every time it is requested |

---

### .NET Core

In .NET Core the registration of dependencies is done **explicitly** in `Program.cs`:

```csharp
builder.Services.AddScoped<IUserRepository, UserRepository>();
builder.Services.AddSingleton<ILogger, ConsoleLogger>();
builder.Services.AddTransient<IEmailService, EmailService>();
```

Then the container injects them through the constructor automatically:

```csharp
public class UserService
{
    private readonly IUserRepository _repository;

    public UserService(IUserRepository repository)
    {
        _repository = repository;
    }
}
```

---

### Spring Boot

In Spring the registration is done **implicitly** via annotations. You annotate your classes and Spring scans them at startup to build its application context.

```java
@Service
public class UserService {
    private final UserRepository userRepository;

    // Spring injects the dependency through the constructor
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

@Repository
public class UserRepository { ... }
```

Stereotype annotations tell Spring what role each bean plays:

| Annotation | Purpose |
|---|---|
| `@Component` | Generic bean |
| `@Service` | Business logic layer |
| `@Repository` | Data access layer |
| `@Controller` | Web layer |

Scopes are configured with `@Scope`. The default is **singleton**:

```java
@Service
@Scope("prototype") // new instance every time
public class ReportGenerator { ... }

@Service
@RequestScope // one instance per HTTP request
public class RequestContext { ... }
```

---

### Angular

Angular has a **built-in DI system** that is central to the entire framework. Services are marked with `@Injectable` and registered with a **provider**.

```typescript
@Injectable({
  providedIn: 'root' // singleton across the whole app
})
export class UserService {
  getUser(id: string) { ... }
}
```

Then injected through the constructor:

```typescript
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html'
})
export class UserComponent {
  constructor(private userService: UserService) {}
}
```

The `providedIn` property controls scope. Using `'root'` creates a singleton. You can also provide a service at the module or component level, which creates a separate instance scoped to that part of the component tree — useful for isolating state.

---

### NestJS

NestJS (a Node.js framework) takes heavy inspiration from Angular and brings the same DI system to the backend.

```typescript
@Injectable()
export class UserService {
  constructor(private readonly userRepository: UserRepository) {}
}
```

Dependencies are registered inside a **Module**:

```typescript
@Module({
  providers: [UserService, UserRepository],
  controllers: [UserController],
})
export class UserModule {}
```

Scopes work the same way:

```typescript
@Injectable({ scope: Scope.REQUEST }) // new instance per request
export class RequestScopedService { ... }
```

---

### React

React is a UI library, not a framework, so it has no built-in DI container. But the same ideas appear in different forms.

**Context API** is the closest equivalent — it lets you inject values into any component deep in the tree without passing props manually:

```tsx
// Define and provide a dependency at the top of the tree
const ThemeContext = React.createContext<Theme>(defaultTheme);

function App() {
  const theme = useTheme(); // comes from config, API, etc.
  return (
    <ThemeContext.Provider value={theme}>
      <MyPage />
    </ThemeContext.Provider>
  );
}

// Consume it anywhere below — no prop drilling needed
function Button() {
  const theme = useContext(ThemeContext);
  return <button style={{ color: theme.primary }}>Click</button>;
}
```

**Custom hooks** are another form of DI — they encapsulate and inject behaviour rather than concrete values:

```tsx
// The component doesn't care how data is fetched
function UserProfile({ userId }: { userId: string }) {
  const { user, loading } = useUser(userId);
  if (loading) return <Spinner />;
  return <div>{user.name}</div>;
}
```

By swapping the hook implementation (or mocking it in tests) you get the same decoupling benefit that a DI container provides.

For full IoC container support in TypeScript projects, libraries like [InversifyJS](https://inversify.io/) or [tsyringe](https://github.com/microsoft/tsyringe) can be added on top.

---

### Final Thoughts

Every major framework eventually converges on the same idea: **your classes should declare what they need, not how to get it**. The container, the context, or the hook figures out the rest.

This makes code easier to test (swap real dependencies for fakes), easier to extend (change implementations without touching consumers), and easier to reason about (no hidden global state).

The mechanics differ — annotations, explicit registration, context providers — but the principle is the same D that Robert Martin wrote about decades ago.
