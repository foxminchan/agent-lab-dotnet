# Soc Ops — Copilot Workspace Instructions

## Mandatory Pre-Commit Checklist

Before committing **any** changes, all three must pass:

- [ ] **Lint** — no unused `@using` directives, variables, or unreachable code
- [ ] **Build** — `dotnet build SocOps/SocOps.csproj` exits with 0 errors/warnings
- [ ] **Test** — `dotnet test` passes (when tests exist)

## Project

**Soc Ops** — Social Bingo for in-person mixers. Blazor WebAssembly (.NET 10). Players mark a 5×5 board by finding people who match icebreaker prompts; first to complete a row/column/diagonal wins.

```bash
dotnet run --project SocOps    # dev server → http://localhost:5166
dotnet build SocOps/SocOps.csproj
dotnet test
```

## Architecture

```
SocOps/
├── Components/   StartScreen · GameScreen · BingoBoard · BingoSquare · BingoModal
├── Models/       GameState (enum) · BingoSquareData · BingoLine
├── Services/     BingoGameService (state) · BingoLogicService (pure static)
├── Data/         Questions.cs — 24 prompts + FREE_SPACE constant
├── Pages/        Home.razor — sole routable page
└── wwwroot/css/app.css — custom Tailwind-like utilities
```

## Key Patterns

- **State** — `BingoGameService` is the single source of truth; fires `event Action? OnStateChanged`. localStorage persistence via `IJSRuntime`; fire-and-forget saves: `_ = SaveGameStateAsync()`.
- **Logic** — `BingoLogicService` is pure static only. Board: Fisher-Yates shuffle → 24 questions → FREE_SPACE at index 12.
- **Components** — data down via `[Parameter]`; events up via `EventCallback<T>`. CSS computed in `GetCssClasses()` — no inline styles.

## Styling

Utility classes live in `app.css` (never add `<style>` blocks to components):

| | Classes |
|--|---------|
| Layout | `.flex` `.flex-col` `.grid` `.grid-cols-5` `.items-center` `.justify-center` |
| Spacing | `.p-1`–`.p-6` `.px-*` `.py-*` `.mb-2`–`.mb-8` `.gap-1` `.mx-auto` |
| Colors | `.bg-accent` `.bg-marked` `.bg-gray-50` `.bg-white` |
| Type | `.text-xs`–`.text-5xl` `.font-semibold` `.font-bold` `.leading-tight` |

Use CSS variables (`:root`) for new theme values.

## Agents & Prompts

| File | Purpose |
|------|---------|
| `agents/quiz-master.agent.md` | Themed icebreaker questions → `Questions.cs` |
| `agents/ui-review.agent.md` | Playwright UI review |
| `agents/pixel-jam.agent.md` | Creative frontend redesign |
| `agents/tdd-*.agent.md` | Red/Green/Refactor TDD cycle |
| `instructions/css-utilities.instructions.md` | CSS reference (auto-applied) |
| `instructions/frontend-design.instructions.md` | Design principles (auto-applied) |

Workshop guides: [`workshop/`](workshop/) — start at `00-overview.md`.
