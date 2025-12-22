# Contributing to **ViveCore**

Welcome, and thank you for taking the time to help improve ViveCore!  
This document describes the rules, tooling, and workflow we follow so that every change—big or small—remains easy to understand, test, and maintain.

---

## 1. Getting Started

1. Install the latest **.NET 9 SDK**.  
   We rely on .NET 9-specific features; earlier SDKs will fail to build.
2. Clone your fork:
   ```bash
   git clone git@github.com:<your-user>/ViveCore.git
   cd ViveCore
   ```
3. Run a sanity build:
   ```bash
   dotnet build
   dotnet test
   ```
   If everything passes, you are ready to code 🎉

---

## 2. Git Workflow

We use a linear history with **squash & merge** only.  
Pushing directly to `master` is disabled.

### 2.1  Branch-naming convention

| Purpose                               | Prefix          | Example                                |
| ------------------------------------- | -------------- | -------------------------------------- |
| New user-visible functionality        | `feature/`     | `feature/discord-message-scheduler`    |
| Fix for incorrect behaviour           | `fix/`         | `fix/rounding-error-in-elo-service`    |
| High-priority, risk-accepted quick fix| `hotfix/`      | `hotfix/null-ref-startup`              |
| Refactor / CI / docs / chores         | `chore/`       | `chore/github-actions-cache-dotnet`    |

### 2.2  Commit convention

We follow **Conventional Commits** with **git-emoji** prefixes:

```
<emoji> <scope>: <imperative summary>
```

• **Emoji** — any from https://gitmoji.dev (use the Unicode character, not the shortcode)  
• **Scope** — `feat`, `fix`, `hotfix` or `chore`  
• **Message** — why the change exists, not what files changed

Examples  
```
✨ feat: implement match history endpoint
🐛 fix: escape markdown in usernames
⬆️ chore: upgrade OpenApi to version 9.0.7
🚑 hotfix: revert faulty migrations
```

Golden rules  
1. Write in the imperative: “add logging” not “added logging”.  
2. One logical change per commit; unrelated changes → separate commits.  
3. Avoid “wip”, “update”, “stuff” — future-you will thank you.

### 2.3  Pull-request checklist

Before opening / marking “Ready for review”:

- [ ] Rebase onto `master` (`git pull --rebase upstream master`)  
- [ ] Run `dotnet format` (or `dotnet format verify-no-changes`)  
- [ ] `dotnet test` is green  
- [ ] Added/updated unit or Aspire integration tests where relevant  
- [ ] Added/updated XML or markdown documentation if behaviour changed  
- [ ] CI is green 🟢  

---

## 3. Code Style & Tooling

• Coding conventions are enforced through `.editorconfig` and `dotnet format`.  
• Nullable reference types are **enabled**; treat warnings as errors.  
• Public APIs must be XML-documented.  
• Prefer `readonly record` types for DTOs, `sealed class` for behaviours.  

IDE-agnostic commands:
```bash
# Format
dotnet format

# Static analysis (Roslyn + analyzers)
dotnet build -warnaserror
```

---

## 4. Testing

We use xUnit + Aspire’s test host.

Adding a new microservice? Provide:

1. A _unit_ test for isolated business logic.  
2. An _Aspire integration_ test that spins up the dependent containers/redis/etc.

Quick template:

```csharp
public class MatchApiTests : IClassFixture<AspireFixture>
{
    private readonly HttpClient _client;

    public MatchApiTests(AspireFixture fixture)
        => _client = fixture.CreateClient();

    [Fact]
    public async Task CreateMatch_Returns201()
    {
        var res = await _client.PostAsJsonAsync("/matches", new { /* … */ });
        res.StatusCode.Should().Be(HttpStatusCode.Created);
    }
}
```

Coverage is not gated, yet new code **should** ship with tests; external-service integrations **must** be covered by Aspire tests.

---

## 5. Issue & Discussion Etiquette

• Search existing issues/PRs before opening a new one.  
• Provide reproduction steps, stack traces, Aspire logs where possible.  
• Keep the discussion constructive and on topic.

---

## 6. License & CLA

By contributing, you agree that your code will be licensed under the [AGPLv3 license](LICENSE).  

---

Happy coding!  
— The ViveCore Maintainers