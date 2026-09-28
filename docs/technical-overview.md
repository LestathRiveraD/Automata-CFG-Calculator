# Architecture and tokenizer reference

For the project summary and recruiter overview, see the [README](../README.md). For a hands-on walkthrough, see [How to run and check the application](getting-started.md).

## Execution flow

The application has one form and keeps its UI and tokenization logic in the same class. It has no database, network integration, or persistence layer.

```text
Program.Main
  → ApplicationConfiguration.Initialize()
  → Application.Run(new Form1())
      → InitializeComponent()
      → Build button grid and display
      → Wait for user interaction
          Input button → append its label to display.Text
          AC           → clear display.Text
          =            → SepararTokens(display.Text)
                       → format token list
                       → show MessageBox
```

[Program.cs](../Program.cs) marks `Main` with `[STAThread]` and starts the Windows Forms event loop. [Form1.cs](../Form1.cs) and [Form1.Designer.cs](../Form1.Designer.cs) declare parts of the same `Form1` class.

## UI reference

The constructor configures a black form titled `Calculator`, sets `Width` to `350` and `Height` to `700`, uses a fixed single border, and disables the maximize button. These are configured dimensions; display scaling can affect the rendered size.

A `TableLayoutPanel` fills the available area with four equally sized columns and five equally sized rows. A separate text box docks to the top of the form. It is editable, right-aligned, and uses `0` as placeholder text when empty.

The button matrix is:

| Column 1 | Column 2 | Column 3 | Column 4 |
| --- | --- | --- | --- |
| `AC` | `(` | `)` | `/` |
| `7` | `8` | `9` | `*` |
| `4` | `5` | `6` | `-` |
| `1` | `2` | `3` | `+` |
| `0` | `.` | `^` | `=` |

| Action | Behavior |
| --- | --- |
| Click a digit, punctuation, or operator button | Append that button's label to `display.Text`. |
| Edit the text box directly | Change the expression using ordinary text-box input. |
| Click `AC` | Replace the display contents with `String.Empty`. |
| Click `=` | Tokenize the display and show a message; leave the display contents unchanged. |

There are no custom keyboard shortcuts for calculation. The click handler for `=` formats each token as `[token], `, including a trailing comma and space, and prefixes the result with `Tokens encontrados: `.

## Tokenizer reference

The private method in [Form1.cs](../Form1.cs) has this signature:

```csharp
private List<String> SepararTokens(string expr)
```

It calls `Regex.Matches` with the following pattern:

```regex
(?:\d+\.\d+|\d+|\.\d+)|[+\-/*^()]
```

Each matched substring is appended to a new list in input order. The method then appends the literal string `FIN` and returns the list. Tokens are plain strings; they do not carry a token type or source position.

| Pattern | Recognizes | Example |
| --- | --- | --- |
| `\d+\.\d+` | Digits on both sides of a decimal point | `12.5` |
| `\d+` | One or more digits | `42` |
| `\.\d+` | A decimal point followed by digits | `.75` |
| `[+\-/*^()]` | One operator or parenthesis | `+`, `^`, `(` |

The decimal separator in the pattern is a literal period. Under the default .NET regex behavior, `\d` matches Unicode decimal digits, not only ASCII `0` through `9`.

These examples describe the implementation, including its permissive behavior:

| Input | Returned tokens | Interpretation |
| --- | --- | --- |
| `12.5+(3*2)` | `12.5`, `+`, `(`, `3`, `*`, `2`, `)`, `FIN` | Numbers, operators, and parentheses are preserved. |
| `.5^2` | `.5`, `^`, `2`, `FIN` | Leading-dot decimals and the caret are recognized. |
| `-3+2` | `-`, `3`, `+`, `2`, `FIN` | A minus sign is a separate token. |
| `2 + abc3` | `2`, `+`, `3`, `FIN` | Spaces and letters are silently skipped. |
| `1..2` | `1`, `.2`, `FIN` | A malformed number can produce multiple matches. |
| `5.` | `5`, `FIN` | An unmatched trailing decimal point is skipped. |
| Empty string | `FIN` | The end marker is always present. |

Tokenization only identifies matching fragments. It does not enforce expression grammar, operator precedence, balanced parentheses, or numeric ranges. The `^` token has no evaluation behavior yet. The `FIN` marker currently appears in the preview; no parser consumes it.

## Implementation trade-offs

**A matrix defines the keypad.** The nested loops apply shared layout, styling, and event registration to all buttons. This keeps the keypad definition compact, while special cases such as `AC` and `=` remain inside the construction loop.

**UI and processing share one class.** The current flow is easy to follow in a small prototype. Extracting tokenization would make it possible to test expression handling without creating a Windows form and would give a future parser a clearer interface.

**Regex matching keeps recognition concise.** It handles the supported token shapes in one pattern, but `Regex.Matches` searches for matches rather than requiring every character to be consumed. Invalid input can therefore appear acceptable in the preview. A future validating tokenizer would need to account for gaps between matches.

**Windows Forms provides a native desktop UI.** The project enables `UseWindowsForms` and targets `net10.0-windows`. Running the interface requires Windows. The SDK project enables nullable reference analysis and implicit using directives, and declares no third-party package references.

These are observations about the current implementation, not claims about the author's original design rationale.

## Verification status

The repository contains no automated tests or CI configuration. The [manual checks](getting-started.md#manual-checks) cover the visible interaction flow and selected tokenizer edge cases. Passing those checks would verify the prototype's current behavior, not arithmetic correctness, because evaluation is not implemented.
