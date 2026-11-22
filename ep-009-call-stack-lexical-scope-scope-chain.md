## Sample 1: Global Execution Context (Basic Declarations)

Variables declared outside any function or block belong to the global execution context and can be read across the script.

```javascript
// Sample 1: Global Execution Context
var globalVar = "I am global (var)";
let globalLet = "I am global (let)";
const globalConst = "I am global (const)";

console.log("[Sample 1] globalVar:", globalVar);   // "I am global (var)"
console.log("[Sample 1] globalLet:", globalLet);   // "I am global (let)"
console.log("[Sample 1] globalConst:", globalConst); // "I am global (const)"

```

---

## Sample 2: Function Execution Context (Local Variables + Outer Access)

Calling a function creates a new function execution context. Its local variables stay inside the function, while outer variables remain visible inside it.

```javascript
// Sample 2: Function Execution Context
const appName = "Scope Trainer";

function showFunctionScope() {
  var localVar = "Inside function (var)";
  let localLet = "Inside function (let)";
  const localConst = "Inside function (const)";

  console.log("[Sample 2] appName (from global):", appName); // "Scope Trainer"
  console.log("[Sample 2] localVar:", localVar);            // "Inside function (var)"
  console.log("[Sample 2] localLet:", localLet);            // "Inside function (let)"
  console.log("[Sample 2] localConst:", localConst);        // "Inside function (const)"
}

showFunctionScope();
// Outside showFunctionScope(), localVar, localLet, and localConst are not accessible.

```

---

## Sample 3: Block Execution Context (`{ ... }`)

`let` and `const` stay confined inside `{ ... }` blocks, whereas `var` leaks out of blocks to the enclosing function or global scope.

```javascript
// Sample 3: Block Execution Context
if (true) {
  var leakedVar = "var escapes the block!";
  let blockLet = "let stays inside the block";
  const blockConst = "const stays inside the block";

  console.log("[Sample 3] inside block -> blockLet:", blockLet);     // "let stays inside the block"
  console.log("[Sample 3] inside block -> blockConst:", blockConst); // "const stays inside the block"
}

console.log("[Sample 3] outside block -> leakedVar:", leakedVar); // "var escapes the block!"
// Outside the block, blockLet and blockConst throw a ReferenceError if accessed.

```

---

## Sample 4: All Levels Together (Scope Chain Lookup)

JavaScript resolves identifiers from the innermost scope outward through the scope chain until it reaches the global execution context.

```javascript
// Sample 4: Scope Chain Across Global, Function, and Block
const chainLevel = "Global";

function outerFunction() {
  const outerMessage = "From outer function";

  if (true) {
    const blockMessage = "From if block";

    console.log("[Sample 4] 1. Global:", chainLevel);       // "Global"
    console.log("[Sample 4] 2. Function:", outerMessage); // "From outer function"
    console.log("[Sample 4] 3. Block:", blockMessage);    // "From if block"
  }

  console.log("[Sample 4] back in function -> outerMessage:", outerMessage); // "From outer function"
}

outerFunction();

```

---

## Sample 5: Redeclaring Same Variable Name (Variable Shadowing)

Declaring `let` or `const` with the same name in a function or block creates a separate inner variable that shadows the outer one without changing the outer value.

```javascript
// Sample 5: Variable Shadowing Across Scopes
let userRole = "Guest";
const maxRetries = 3;

function loginUser() {
  let userRole = "Admin";
  const maxRetries = 10;

  console.log("[Sample 5] inside function -> userRole:", userRole);     // "Admin"
  console.log("[Sample 5] inside function -> maxRetries:", maxRetries); // 10

  if (true) {
    let userRole = "SuperAdmin";
    console.log("[Sample 5] inside block -> userRole:", userRole);      // "SuperAdmin"
  }

  console.log("[Sample 5] back in function -> userRole:", userRole);    // "Admin"
}

loginUser();
console.log("[Sample 5] global -> userRole:", userRole);                // "Guest"
console.log("[Sample 5] global -> maxRetries:", maxRetries);            // 3

```

---

## Sample 6: Overriding (Modifying) Global Variables

Omitting `let`, `const`, and `var` inside a function or block looks up the scope chain and reassigns the existing outer variable (provided it is not `const`).

```javascript
// Sample 6: Overriding Global Variables
let currentTheme = "Light";
let loginCount = 0;

function switchToDarkMode() {
  currentTheme = "Dark";
  loginCount = loginCount + 1;

  console.log("[Sample 6] inside function -> currentTheme:", currentTheme); // "Dark"
  console.log("[Sample 6] inside function -> loginCount:", loginCount);     // 1

  if (true) {
    currentTheme = "High-Contrast Dark";
    console.log("[Sample 6] inside block -> currentTheme:", currentTheme);  // "High-Contrast Dark"
  }
}

console.log("[Sample 6] before function -> currentTheme:", currentTheme); // "Light"
switchToDarkMode();
console.log("[Sample 6] after function -> currentTheme:", currentTheme);  // "High-Contrast Dark"
console.log("[Sample 6] after function -> loginCount:", loginCount);      // 1

```

---

## Sample 7: Combined Shadowing vs. Overriding in One Flow

This sample pairs shadowing (`status`) and overriding (`score`) across global, function, and block levels in a single execution run.

```javascript
// Sample 7: Combined Shadowing and Overriding
let status = "Global: Ready";
let score = 100;

function playRound() {
  // Shadowing 'status' in function scope; overriding 'score' in global scope
  let status = "Function: Playing";
  score = 200;

  console.log("[Sample 7] [Function] status (shadowed):", status); // "Function: Playing"
  console.log("[Sample 7] [Function] score (overridden):", score); // 200

  if (true) {
    // Shadowing 'status' again in block scope; overriding 'score' again
    let status = "Block: Bonus Round";
    score = 300;

    console.log("[Sample 7] [Block] status (shadowed):", status);  // "Block: Bonus Round"
    console.log("[Sample 7] [Block] score (overridden):", score);  // 300
  }

  console.log("[Sample 7] [Function after block] status:", status); // "Function: Playing"
}

console.log("[Sample 7] [Before] status:", status); // "Global: Ready"
console.log("[Sample 7] [Before] score:", score);   // 100

playRound();

console.log("[Sample 7] [After] status (unchanged):", status); // "Global: Ready"
console.log("[Sample 7] [After] score (overridden):", score);  // 300

```

---

## Sample 8: Block `var` Trap (Accidental Overwrite)

Because `var` is not block-scoped, redeclaring `var` inside an `if` block overwrites the outer `var` variable instead of isolating a new block-only variable.

```javascript
// Sample 8: Block var Trap
var mode = "Production";

if (true) {
  var mode = "Debug"; // Overwrites outer 'mode' because var ignores block boundaries
  console.log("[Sample 8] inside block -> mode:", mode); // "Debug"
}

console.log("[Sample 8] outside block -> mode:", mode); // "Debug"

```

---

## Complete Samples Overview

| Sample # | Topic | Key Behavior Logged |
| --- | --- | --- |
| **Sample 1** | Global Execution Context | `var`, `let`, and `const` declared at top level |
| **Sample 2** | Function Execution Context | Local variables stay inside function; outer globals remain readable |
| **Sample 3** | Block Execution Context | `let`/`const` stay in `{ ... }`; `var` escapes the block |
| **Sample 4** | Scope Chain Lookup | Inner block reads block, function, and global values |
| **Sample 5** | Variable Shadowing | Inner `let`/`const` hides outer name without changing outer value |
| **Sample 6** | Overriding Global Variables | Reassigning without declaration keyword updates outer `let` variable |
| **Sample 7** | Combined Flow | Side-by-side shadowing (`status`) and overriding (`score`) across 3 levels |
| **Sample 8** | Block `var` Trap | Redeclaring `var` in a block overwrites the outer `var` value |
