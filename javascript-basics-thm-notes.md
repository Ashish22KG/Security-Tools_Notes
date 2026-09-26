# JavaScript Basics — TryHackMe Room Notes

## Syntax Reference Table

| Syntax / Keyword | Example | Description |
|---|---|---|
| `var` | `var x = 5;` | Declares a function-scoped variable (older style, avoid in modern code). |
| `let` | `let age = 25;` | Declares a block-scoped variable that can be reassigned. |
| `const` | `const rollNumbers = [101, 102, 103];` | Declares a block-scoped variable whose reference cannot be reassigned. |
| `console.log()` | `console.log("Hello, World!");` | Prints output to the browser console. |
| `if / else` | `if (age >= 18) {...} else {...}` | Control flow statement — executes code based on a condition. |
| `function` | `function greet(name) {...}` | Defines a reusable block of code that performs a specific task. |
| Function call | `greet("Bob");` | Executes (invokes) a defined function. |
| `for` loop | `for (let i = 0; i < 100; i++) {...}` | Repeats a block of code a set number of times. |
| `while` / `do...while` | — | Other loop types used to repeat code while a condition is true. |
| `alert()` | `alert("Hello THM");` | Displays a dialogue box with a message and an OK button. |
| `prompt()` | `name = prompt("What is your name?");` | Displays a dialogue box asking for user input; returns the entered value or `null` if cancelled. |
| `confirm()` | `confirm("Are you sure?");` | Displays a dialogue box with OK/Cancel; returns `true` or `false`. |
| `<script>...</script>` | Placed in `<head>` or `<body>` | Embeds JS directly inside an HTML file (Internal JS). |
| `<script src="file.js">` | `<script src="script.js"></script>` | Loads JS from a separate external file using the `src` attribute. |
| `document.getElementById()` | `document.getElementById("result").innerHTML = "...";` | Selects an HTML element by its `id` and allows JS to read/update its content. |
| Data types | `string`, `number`, `boolean`, `null`, `undefined`, `object` | Defines the kind of value a variable can hold. |

## Short Notes

- **var vs let vs const**: `var` is function-scoped; `let` and `const` are block-scoped, giving tighter control over where a variable is visible/valid.
- **Interpreted language**: JS is not compiled — the browser executes the code directly, line by line.
- **Internal vs External JS**:
  - Internal = code written inline inside `<script>` tags within the HTML file itself.
  - External = code stored in a separate `.js` file and linked via `<script src="filename.js"></script>`.
  - External is preferred when reusing the same JS across multiple pages.
- **Verifying JS type during pentesting**: Use "View Page Source" — inline `<script>` blocks (no `src`) = internal JS; a `<script src="...">` tag = external JS being loaded.
- **Dialogue functions as an attack surface**: `alert`, `prompt`, and `confirm` are legitimate UI functions, but attackers can abuse them (e.g., spamming `alert()` in a loop) to annoy or disrupt a victim, and they are foundational to understanding XSS later on.
- **Client-side validation is not security**: Since users can disable or manipulate JS in the browser, any validation done in JS must be duplicated on the server side.
- **Untrusted libraries risk**: Including third-party JS via `src` from an unverified source can introduce malicious code — always verify the source.
- **Never hardcode secrets**: API keys, tokens, or credentials in JS source are visible to anyone who views the page source.
- **Minification vs Obfuscation**:
  - *Minification* removes whitespace/comments and shortens names to reduce file size and improve load time.
  - *Obfuscation* deliberately scrambles code (renamed variables, dummy code) to make it hard for humans to read — though it still runs identically and can be reverse-engineered with effort or deobfuscation tools.
