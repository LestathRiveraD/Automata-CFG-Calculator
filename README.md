# Calculadora-CS

A C# desktop calculator prototype built with Windows Forms and .NET 10. The project combines a calculator interface with an expression tokenizer: it breaks typed input into numbers, operators, and parentheses for inspection.

**Current stage:** the interface and tokenization are implemented. Pressing `=` opens a token preview and arithmetic evaluation.

## Summary

This repository is a compact example of desktop UI programming and the first stage of processing mathematical expressions. Its main implementation is small enough to review directly in [Form1.cs](Form1.cs).

| Skill demonstrated | Evidence in the project |
| --- | --- |
| C# and .NET fundamentals | A Windows Forms application with a startup entry point, a partial form class, collections, and iteration. |
| Event-driven programming | Button click handlers append input, clear the display, or tokenize an expression. |
| Programmatic UI construction | A two-dimensional button definition generates a 5 × 4 grid using `TableLayoutPanel`. |
| String processing and regular expressions | `SepararTokens` extracts integers, decimal numbers, operators, and parentheses into a `List<string>`. |
| Visual hierarchy | A dark interface distinguishes operators in orange and the clear action in gray, with a right-aligned display. |

The current code demonstrates these fundamentals. Parsing, automated tests, and separation of UI from expression-processing logic are potential next steps, described below.

## What it does

- Accepts input through on-screen buttons or direct editing of the text box.
- Provides buttons for digits, decimal points, `+`, `-`, `*`, `/`, `^`, and parentheses.
- Clears the display with `AC`.
- Shows recognized tokens when `=` is clicked, followed by an end marker named `FIN`.

For example, entering `12.5+(3*2)` and clicking `=` produces this message:

```text
Tokens encontrados: [12.5], [+], [(], [3], [*], [2], [)], [FIN],
```

The message label means “Tokens found.” The expression remains in the display; the app does not compute `18.5`.

## Technology

| Component | Implementation |
| --- | --- |
| Language | C# |
| Target framework | .NET 10, `net10.0-windows` |
| Desktop UI | Windows Forms |
| Token recognition | `System.Text.RegularExpressions.Regex` |
| Dependencies | No third-party package references in the project file |
| Runtime platform | Windows |

## Run locally

On Windows, install the .NET 10 SDK and Git, then run:

```powershell
git clone https://github.com/LestathRiveraD/Calculadora-CS.git
cd Calculadora-CS
dotnet run --project MyWindowApp.csproj
```

The window is titled **Calculator**. Enter `12.5+(3*2)` and click `=` to inspect its tokens. See [How to run and check the application](docs/getting-started.md) for build instructions, a walkthrough, and troubleshooting.

## Code tour

| File | Responsibility |
| --- | --- |
| [Program.cs](Program.cs) | Initializes application configuration and starts the main form. |
| [Form1.cs](Form1.cs) | Builds the interface, connects click handlers, and tokenizes expressions. |
| [Form1.Designer.cs](Form1.Designer.cs) | Contains the base form initialization and component disposal code. |
| [MyWindowApp.csproj](MyWindowApp.csproj) | Defines the Windows executable, target framework, and compiler settings. |
| [MyWindowApp.sln](MyWindowApp.sln) | Solution entry point for development tools. |

For a quick technical review, start with the button matrix and click handlers in `Form1.cs`, then read `SepararTokens`. The [technical guide](docs/technical-overview.md) explains the execution flow, token rules, and implementation trade-offs.

## Documentation

- [How to run and check the application](docs/getting-started.md)
- [Architecture and tokenizer reference](docs/technical-overview.md)
