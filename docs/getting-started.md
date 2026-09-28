# How to run and check the application

Build the Windows desktop prototype and inspect the tokens extracted from a mathematical expression. For the project summary, see the [README](../README.md); for token rules, see the [technical reference](technical-overview.md).

## Prerequisites

- Windows to run the Windows Forms interface.
- The .NET 10 SDK, matching the project's `net10.0-windows` target.
- Git if cloning the repository from the command line.

Use PowerShell or another terminal on Windows. Check the installed SDKs:

```powershell
dotnet --list-sdks
```

The output should include a `10.0.x` SDK. Use a fresh build from source rather than relying on the `bin/` and `obj/` files currently tracked in the repository.

## Run your first example

1. Clone the repository and enter its directory:

   ```powershell
   git clone https://github.com/LestathRiveraD/Calculadora-CS.git
   cd Calculadora-CS
   ```

2. Start the application:

   ```powershell
   dotnet run --project MyWindowApp.csproj
   ```

   This command restores dependencies as needed, builds the project, and opens the **Calculator** window.

3. Enter `12.5+(3*2)` using the keypad or text box, then click `=`.

   The dialog displays:

   ```text
   Tokens encontrados: [12.5], [+], [(], [3], [*], [2], [)], [FIN],
   ```

   Close the dialog and click `AC` to clear the expression. The empty display shows its `0` placeholder.

### What you built

You have built and launched a desktop interface that turns expression text into a sequence of tokens. The token preview is the current result; the application does not yet calculate an answer.

## Build without launching

From the repository root:

```powershell
dotnet build MyWindowApp.csproj --configuration Release
```

Build output goes under `bin/Release/net10.0-windows/`. To launch that configuration:

```powershell
dotnet run --project MyWindowApp.csproj --configuration Release
```

## Manual checks

There is no automated test project. On Windows, use these checks to review the current behavior. Click `AC` between cases unless the case explicitly tests clearing.

| Check | Steps | Expected behavior |
| --- | --- | --- |
| Input | Click `1`, `2`, `.`, `5` | The display reads `12.5`. |
| Clear | Click `AC` after entering text | The text clears and the `0` placeholder appears. |
| Token preview | Enter `12.5+(3*2)` and click `=` | The dialog lists all seven expression tokens followed by `FIN`. |
| Leading-dot decimal | Enter `.5^2` and click `=` | Tokens are `.5`, `^`, `2`, `FIN`. |
| Empty input | Click `AC`, then `=` | The dialog contains only `[FIN], ` after its label. |
| Unsupported characters | Type `2+abc3` and click `=` | Tokens are `2`, `+`, `3`, `FIN`; letters are skipped. |
| Invalid syntax | Enter `1..2` and click `=` | Tokens are `1`, `.2`, `FIN`; no validation error appears. |
| Display retention | Close a token dialog | The original expression remains in the text box. |

The last two input cases document limitations, not desired validation behavior. No arithmetic result is expected from these checks.

## Troubleshooting

| Symptom | Action or explanation |
| --- | --- |
| `dotnet` is not recognized | Install the .NET 10 SDK and reopen the terminal so its PATH is refreshed. |
| The SDK cannot target .NET 10 | Check `dotnet --list-sdks` and install the matching SDK. |
| The application cannot run on Linux or macOS | Run the UI on Windows; the project targets Windows Forms. |
| `=` opens a token dialog | This is the current implementation. Calculation is not implemented. |
| Letters or punctuation disappear from the token list | The regex skips unsupported characters. See the [tokenizer reference](technical-overview.md#tokenizer-reference). |
| Pressing Enter does not calculate | Click the on-screen `=` button; the code defines no custom Enter shortcut. |

The Windows UI walkthrough requires manual execution on Windows. Documentation checks alone do not establish that these runtime checks have passed.
