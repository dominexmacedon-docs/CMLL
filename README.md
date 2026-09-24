# CMLL

CMLL (**Command Language Made Legible**) is a small command-line programming language implemented in C.

It is designed for command-line programming: source files can contain multiple statements, variables, expressions, functions, control flow, arrays, objects, file operations, environment-variable access, and shell commands.

This README describes the **CMLL implementation contained in this release**, rather than describing features that are not currently implemented.

---

## Installation

CMLL provides a Linux x86_64 release that can be installed directly into the system executable directory.

### Requirements

- Linux x86_64
- `curl`
- `unzip`
- `sudo`

### Install

Create or use the installation `Makefile` in the project directory:
### 1. Makefile

Makefile

```
CMLL_VERSION := cmll-v1.0.0
CMLL_URL := https://github.com/dominexmacedon-docs/CMLL/releases/download/$(CMLL_VERSION)/cmll-linux-x86_64.zip

CMLL_EXTENSION_VERSION := cmll-vscode-extension-v1.0.1
CMLL_EXTENSION_URL := https://github.com/dominexmacedon-docs/CMLL/releases/download/cmll-vscode-extension-v1.0.1/cmll-vscode-bd7dae97981430e9558d364e9d25bf0de60b523c.zip

INSTALL_DIR := /usr/local/bin
BINARY := cmll

TEMP_DIR := .cmll-install
CLI_ZIP := $(TEMP_DIR)/cmll-linux-x86_64.zip
EXTENSION_ZIP := $(TEMP_DIR)/cmll-vscode-extension.zip

.PHONY: install install-cli install-extension uninstall clean

install: install-cli install-extension
	@echo "CMLL CLI and VS Code extension installed successfully."

install-cli:
	@mkdir -p "$(TEMP_DIR)"
	@curl -fL "$(CMLL_URL)" -o "$(CLI_ZIP)"
	@rm -rf "$(TEMP_DIR)/cli"
	@mkdir -p "$(TEMP_DIR)/cli"
	@unzip -o "$(CLI_ZIP)" -d "$(TEMP_DIR)/cli"
	@BINARY_PATH=$$(find "$(TEMP_DIR)/cli" -type f -name "$(BINARY)" -print -quit); \
	if [ -z "$$BINARY_PATH" ]; then \
		echo "Error: CMLL binary was not found."; \
		exit 1; \
	fi; \
	sudo install -Dm755 "$$BINARY_PATH" "$(INSTALL_DIR)/$(BINARY)"

install-extension:
	@command -v code >/dev/null 2>&1 || { \
		echo "Error: VS Code 'code' command was not found."; \
		exit 1; \
	}
	@mkdir -p "$(TEMP_DIR)"
	@curl -fL "$(CMLL_EXTENSION_URL)" -o "$(EXTENSION_ZIP)"
	@rm -rf "$(TEMP_DIR)/extension"
	@mkdir -p "$(TEMP_DIR)/extension"
	@unzip -o "$(EXTENSION_ZIP)" -d "$(TEMP_DIR)/extension"
	@VSIX_PATH=$$(find "$(TEMP_DIR)/extension" -type f -name "*.vsix" -print -quit); \
	if [ -z "$$VSIX_PATH" ]; then \
		echo "Error: VSIX file was not found."; \
		exit 1; \
	fi; \
	code --install-extension "$$VSIX_PATH" --force

uninstall:
	@sudo rm -f "$(INSTALL_DIR)/$(BINARY)"
	@code --uninstall-extension dominexmacedon.cmll-language || true

clean:
	@rm -rf "$(TEMP_DIR)"
```

### 2. Plain command

Run this command in your terminal to install both the CMLL CLI and the VS Code extension:

Bash

```
curl -fL "https://github.com/dominexmacedon-docs/CMLL/releases/download/cmll-v1.0.0/cmll-linux-x86_64.zip" -o /tmp/cmll.zip && \
rm -rf /tmp/cmll-install && \
mkdir -p /tmp/cmll-install && \
unzip -o /tmp/cmll.zip -d /tmp/cmll-install && \
BINARY_PATH=$(find /tmp/cmll-install -type f -name cmll -print -quit) && \
sudo install -Dm755 "$BINARY_PATH" /usr/local/bin/cmll && \
curl -fL "https://github.com/dominexmacedon-docs/CMLL/releases/download/cmll-vscode-extension-v1.0.1/cmll-vscode-bd7dae97981430e9558d364e9d25bf0de60b523c.zip" -o /tmp/cmll-extension.zip && \
rm -rf /tmp/cmll-extension && \
mkdir -p /tmp/cmll-extension && \
unzip -o /tmp/cmll-extension.zip -d /tmp/cmll-extension && \
VSIX_PATH=$(find /tmp/cmll-extension -type f -name "*.vsix" -print -quit) && \
code --install-extension "$VSIX_PATH" --force
```

Run:

```bash
make install

```

The executable is installed as:

```text
/usr/local/bin/cmll

```

After installation:

```bash
cmll --version
cmll --help

```

---

## Running a CMLL program

CMLL source files use the `.cmll` extension.

Example:

```bash
cmll hello.cmll

```

A CMLL source file can contain multiple lines:

```cmll
project "Hello World";
say "Hello from CMLL!";
say "This is a multi-line program.";

```

CMLL currently executes `.cmll` files. The release executable does **not** contain an interactive REPL prompt; "command-line language" refers to the language's command-oriented programming model and its ability to execute shell commands from a program.

---

# Language Structure

A CMLL program is a sequence of statements.

The basic structure is:

```cmll
statement;
statement;
statement;

```

Statements may include:

* `project`
* `set`
* assignment
* `if` / `else`
* `while`
* `for`
* `func`
* `task`
* `return`
* `say`
* `show`
* `run`
* `read file`
* `write file`
* `list urls from`
* `env`

Expressions may include:

* numbers
* strings
* `true`
* `false`
* `null`
* variables
* arrays
* objects
* function calls
* object property access
* arithmetic
* comparisons
* logical operators
* unary `!` and `-`

---

# Important Syntax Rule

The lexer ignores whitespace and newlines.

A newline is **not** a statement separator.

Use semicolons:

```cmll
set name = "CMLL";
set version = 1;
show name;
show version;

```

Do not rely on this:

```cmll
set name = "CMLL"
set version = 1

```

The parser does not preserve newlines as separators.

Inside blocks, statements should also be separated with semicolons:

```cmll
if true {
    say "first";
    say "second";
}

```

---

# Comments

CMLL uses `#` for single-line comments.

```cmll
# This is a comment
set value = 10;
show value;

```

Everything from `#` to the end of the line is ignored by the lexer.

---

# Variables

## Creating variables

Use `set`:

```cmll
set name = "Dominex";
set age = 20;
set active = true;

```

The syntax is:

```text
set <identifier> = <expression>;

```

There is no `define` keyword in the current CMLL lexer/parser.

Correct:

```cmll
set total = 0;

```

Not:

```cmll
define total = 0;

```

## Reassigning variables

After a variable exists, use normal assignment:

```cmll
set total = 10;
total = total + 5;
show total;

```

Output:

```text
15

```

Assignment can also create a variable if the name does not already exist:

```cmll
value = 100;
show value;

```

However, `set` is the clearest form for declaring a new variable.

---

# Values

CMLL has these runtime value types:

```text
null
number
string
boolean
array
object
function

```

## Numbers

Numbers are represented internally as C `double` values.

```cmll
set integer = 10;
set decimal = 10.5;
set negative = -25;

```

Arithmetic:

```cmll
set a = 20;
set b = 6;

show a + b;
show a - b;
show a * b;
show a / b;
show a % b;

```

---

# Strings

Both double quotes and single quotes are supported:

```cmll
set a = "Hello";
set b = 'World';

show a;
show b;

```

Supported string escapes include:

```text
\n
\t
\r
\\
\"
\'

```

Example:

```cmll
say "Hello\nCMLL";

```

---

# String Concatenation

The `+` operator adds numbers when both operands are numbers.

If either side is not a number, CMLL stringifies both values and concatenates them.

```cmll
set name = "CMLL";
set version = 1;

show "Language: " + name;
show "Version: " + version;

```

This makes `+` useful for building command output and text.

---

# Booleans and null

Boolean literals:

```cmll
set enabled = true;
set disabled = false;

```

Null:

```cmll
set value = null;

```

Output:

```text
true
false
null

```

---

# Truthiness

CMLL evaluates values as follows when a condition is required:

| Value | Truthy when |
| --- | --- |
| `null` | never |
| boolean | value is `true` |
| number | not `0` |
| string | not empty |
| array | contains at least one item |
| object | contains at least one property |
| function | truthy |

Examples:

```cmll
if 1 {
    say "number is true";
}

if 0 {
    say "this is not printed";
}

if "hello" {
    say "string is true";
}

if "" {
    say "this is not printed";
}

```

---

# Operators

## Arithmetic

```text
+
-
*
/
%

```

Example:

```cmll
set result = (10 + 5) * 2;
show result;

```

## Comparison

```text
==
!=
<
<=
>
>=

```

Example:

```cmll
set age = 20;

if age >= 18 {
    say "adult";
} else {
    say "under 18";
}

```

## Logical operators

```text
&&
||
!

```

Example:

```cmll
set age = 20;
set active = true;

if age >= 18 && active {
    say "condition matched";
}

```

Negation:

```cmll
if !false {
    say "true";
}

```

---

# Operator Precedence

Expressions are parsed approximately in this order, from highest precedence to lowest:

1. primary values, variables, arrays, objects, parentheses
2. property access `.`
3. function calls `(...)`
4. unary `!` and `-`
5. `*`, `/`, `%`
6. `+`, `-`
7. `<`, `<=`, `>`, `>=`
8. `==`, `!=`
9. `&&`
10. `||`

Use parentheses when clarity is important:

```cmll
set total = (price * quantity) + shipping;

```

---

# Arrays

Arrays use square brackets:

```cmll
set numbers = [1, 2, 3, 4, 5];
show numbers;

```

Output:

```text
[1, 2, 3, 4, 5]

```

Arrays can contain different value types:

```cmll
set data = [
    "CMLL",
    1,
    true,
    null
];

show data;

```

Arrays can also contain objects:

```cmll
set products = [
    {name: "Phone", price: 500},
    {name: "Laptop", price: 1000},
    {name: "Tablet", price: 300}
];

```

The current implementation supports iterating arrays with `for`.

It does **not** implement general array indexing such as:

```cmll
numbers[0]

```

Do not generate that syntax for the current implementation.

---

# Objects

Objects use braces and `key: value` entries:

```cmll
set user = {
    name: "Dominex",
    age: 20,
    active: true
};

```

Keys can be identifiers or strings:

```cmll
set data = {
    name: "CMLL",
    "description": "Command Language Made Legible"
};

```

Properties are accessed with `.`:

```cmll
show user.name;
show user.age;
show user.active;

```

Nested objects work:

```cmll
set server = {
    host: "localhost",
    config: {
        port: 8080,
        debug: true
    }
};

show server.host;
show server.config.port;
show server.config.debug;

```

A missing property evaluates to `null`.

---

# Conditions

Basic `if`:

```cmll
set value = 10;

if value > 5 {
    say "value is greater than five";
}

```

`if` with `else`:

```cmll
set value = 3;

if value > 5 {
    say "large";
} else {
    say "small";
}

```

Nested conditions:

```cmll
set score = 85;

if score >= 90 {
    say "excellent";
} else {
    if score >= 70 {
        say "good";
    } else {
        say "needs improvement";
    }
}

```

The parser also supports an `else if` style through:

```cmll
if score >= 90 {
    say "excellent";
} else if score >= 70 {
    say "good";
} else {
    say "needs improvement";
}

```

---

# While Loops

Syntax:

```text
while <expression> {
    ...
}

```

Example:

```cmll
set count = 0;

while count < 5 {
    show count;
    count = count + 1;
}

```

Output:

```text
0
1
2
3
4

```

The runtime has a safety guard that stops a single `while` loop after more than one million iterations.

---

# For Loops

CMLL uses:

```text
for <variable> in <array> {
    ...
}

```

Example:

```cmll
set numbers = [10, 20, 30];

for number in numbers {
    show number;
}

```

The collection must be an array.

This is invalid for the current runtime:

```cmll
set value = 10;

for item in value {
    show item;
}

```

The runtime reports:

```text
'for' requires an array

```

---

# Nested Loops

Loops can be nested:

```cmll
set groups = [
    [1, 2],
    [3, 4],
    [5, 6]
];

for group in groups {
    for number in group {
        show number;
    }
}

```

---

# Functions

Functions use `func`:

```cmll
func add(a, b) {
    return a + b;
}

```

Call the function:

```cmll
set result = add(10, 20);
show result;

```

Output:

```text
30

```

Functions are first-class runtime values and can capture their surrounding environment.

---

# Return

Use `return` inside a function:

```cmll
func multiply(a, b) {
    return a * b;
}

show multiply(4, 5);

```

A bare return is also accepted:

```cmll
func stop() {
    return;
}

```

A bare return produces `null`.

---

# Functions Can Be Declared After Their Use

CMLL registers top-level functions and tasks before executing ordinary top-level statements.

Therefore this works:

```cmll
set result = add(5, 7);
show result;

func add(a, b) {
    return a + b;
}

```

Output:

```text
12

```

This behavior applies to top-level `func` and `task` declarations.

---

# Tasks

`task` has the same function declaration/runtime representation as `func` in the current implementation.

Example:

```cmll
task greet(name) {
    say "Hello, " + name;
}

greet("CMLL");

```

A task can also return a value:

```cmll
task square(value) {
    return value * value;
}

show square(9);

```

For the current runtime, `func` and `task` should be understood as callable named declarations with the same execution mechanism.

---

# Scope and Closures

Function calls create a child environment whose parent is the function's captured environment.

Example:

```cmll
set prefix = "CMLL";

func greet(name) {
    return prefix + ": " + name;
}

show greet("developer");

```

The function can read the captured `prefix`.

Function parameters are local to the call environment.

---

# Output

CMLL provides two output statements:

```cmll
say "Hello";
show "World";

```

Both evaluate their expression, convert the result to text, and print it followed by a newline.

They are currently equivalent at runtime.

Examples:

```cmll
show 123;
show true;
show null;
show [1, 2, 3];

set user = {name: "Dominex"};
show user;

```

---

# Project

`project` evaluates an expression and prints it with a `Project:` prefix.

```cmll
project "My CMLL Project";

```

Output:

```text
Project: My CMLL Project

```

A variable can be used:

```cmll
set name = "Command Tool";

project name;

```

---

# Running Shell Commands

`run` executes a command through the operating system shell.

Example:

```cmll
run "echo Hello from the shell";

```

CMLL prints the command before executing it:

```text
→ echo Hello from the shell
Hello from the shell

```

A command can be built with expressions:

```cmll
set name = "CMLL";
run "echo " + name;

```

The command is passed to the C runtime's `system()` function.

Therefore `run` should only be used with commands that are trusted.

A failing command causes the CMLL program to fail and reports the process exit status.

---

# File Operations

## Read a file

Syntax:

```text
read file <filename> as <variable>

```

Example:

```cmll
read file "message.txt" as content;
show content;

```

The complete file contents are stored in the specified variable.

## Write a file

Syntax:

```text
write file <filename> from <expression>

```

Example:

```cmll
write file "message.txt" from "Hello from CMLL";

```

A variable can be written:

```cmll
set content = "CMLL file content";
write file "output.txt" from content;

```

Expressions are allowed:

```cmll
set name = "CMLL";
write file "output.txt" from "Language: " + name;

```

---

# Environment Variables

CMLL has an `env` statement.

Syntax:

```text
env <expression>

```

Example:

```cmll
env "HOME";
env "PATH";

```

If the environment variable exists, its value is printed.

For example:

```text
/home/user

```

## Important: `env` is a statement, not a function

The current lexer makes `env` a keyword and the parser handles it as a statement.

Therefore do not write:

```cmll
set project = env("GOOGLE_CLOUD_PROJECT");

```

That syntax is **not supported by the current implementation**.

Use:

```cmll
env "GOOGLE_CLOUD_PROJECT";

```

The current `env` implementation prints the environment variable; it does not return the environment value as an expression.

---

# URL Listing

CMLL has a `list urls from` statement.

Syntax:

```text
list urls from <expression>

```

Example:

```cmll
list urls from "Visit [https://example.com](https://example.com) and [https://github.com](https://github.com)";

```

The runtime scans the resulting text and prints detected `http://` and `https://` URLs.

It is useful for extracting URLs from text:

```cmll
set text = "Docs: [https://example.com/docs](https://example.com/docs) API: [https://example.com/api](https://example.com/api)";
list urls from text;

```

---

# Expressions and Function Calls

Function calls are expressions:

```cmll
func add(a, b) {
    return a + b;
}

set value = add(2, 3) * 10;
show value;

```

Output:

```text
50

```

Function calls can be nested:

```cmll
func add(a, b) {
    return a + b;
}

func double(value) {
    return value * 2;
}

show double(add(3, 4));

```

---

# Real Installation & Deployment Examples

Below are concrete, real-world examples showing how CMLL can automate software installations and system configuration tasks on Linux systems.

### Real Installation Example 1: Web Server & Nginx Setup

Automate installing Nginx, creating a custom configuration file, writing a sample web index file, and enabling the systemd service:

```cmll
project "Nginx Web Server Installation";

say "Starting Nginx installation workflow...";

# Update system repositories and install Nginx via apt
run "sudo apt-get update -y";
run "sudo apt-get install -y nginx";

# Prepare a custom virtual host configuration
set nginx_config = "server {\n    listen 80;\n    server_name localhost;\n\n    location / {\n        root /var/www/cmll-site;\n        index index.html;\n    }\n}\n";

# Deploy the configuration to Nginx sites-available
write file "/tmp/cmll-site.conf" from nginx_config;
run "sudo mv /tmp/cmll-site.conf /etc/nginx/sites-available/cmll-site.conf";
run "sudo ln -sf /etc/nginx/sites-available/cmll-site.conf /etc/nginx/sites-enabled/";

# Create the web root directory and populate index.html
run "sudo mkdir -p /var/www/cmll-site";
set html_content = "<html><body><h1>Deployed via CMLL Automator</h1></body></html>";
write file "/tmp/index.html" from html_content;
run "sudo mv /tmp/index.html /var/www/cmll-site/index.html";

# Test configuration and restart Nginx service
run "sudo nginx -t";
run "sudo systemctl restart nginx";

say "Nginx installation and configuration complete!";

```

---

### Real Installation Example 2: Node.js Environment Provisioning

Download, unpack, and install a specific Node.js binary release into `/usr/local`, verify the installation, and set up global project directories:

```cmll
project "Node.js Environment Provisioner";

set node_version = "v20.11.0";
set arch = "linux-x64";
set tar_filename = "node-" + node_version + "-" + arch + ".tar.xz";
set download_url = "[https://nodejs.org/dist/](https://nodejs.org/dist/)" + node_version + "/" + tar_filename;

say "Provisioning Node.js " + node_version + "...";

# Download official tarball
run "curl -O " + download_url;

# Extract directly into /usr/local
run "sudo tar -xJvf " + tar_filename + " -C /usr/local --strip-components=1";

# Clean up downloaded archive
run "rm -f " + tar_filename;

# Verify bin executables exist and print versions
say "Verifying installation:";
run "node --version";
run "npm --version";

# Ensure global directory exists with correct permissions
run "sudo mkdir -p /usr/local/lib/node_modules";

say "Node.js environment provisioned successfully.";

```

---

### Real Installation Example 3: Multi-Package Developer Toolchain Setup

Iterate through a list of essential developer tools, install missing packages, and confirm setup complete with system path output:

```cmll
project "Developer Toolchain Setup";

task install_package(pkg) {
    say "Installing package: " + pkg;
    run "sudo apt-get install -y " + pkg;
}

set core_tools = [
    "build-essential",
    "git",
    "curl",
    "wget",
    "htop",
    "jq"
];

say "Updating system package indices...";
run "sudo apt-get update -y";

# Loop through each required package
for tool in core_tools {
    install_package(tool);
}

say "Printing PATH environment configuration:";
env "PATH";

say "All developer tools installed successfully!";

```

---

# Complete Example: Basic Program

```cmll
project "CMLL Example";

set name = "Developer";
set version = 1;

say "Hello, " + name;
show "CMLL version: " + version;

```

---

# Complete Example: Calculator

```cmll
func add(a, b) {
    return a + b;
}

func subtract(a, b) {
    return a - b;
}

func multiply(a, b) {
    return a * b;
}

func divide(a, b) {
    return a / b;
}

set a = 20;
set b = 5;

show add(a, b);
show subtract(a, b);
show multiply(a, b);
show divide(a, b);

```

Output:

```text
25
15
100
4

```

---

# Complete Example: Product Processing

This example demonstrates objects, arrays, `for`, property access, `if`/`else`, functions, and `while`.

```cmll
set total = 0;

set products = [
    {name: "Phone", price: 500},
    {name: "Laptop", price: 1000},
    {name: "Tablet", price: 300}
];

func add(a, b) {
    return a + b;
}

for product in products {
    if product.price > 400 {
        total = add(total, product.price);
    } else {
        total = total + 1;
    }
}

while total < 2000 {
    total = total + 100;
}

show total;

```

Output:

```text
2100

```

---

# Complete Example: Nested Data

```cmll
set company = {
    name: "CMLL",
    owner: {
        name: "Developer",
        active: true
    },
    tools: [
        "lexer",
        "parser",
        "runtime"
    ]
};

show company.name;
show company.owner.name;
show company.owner.active;
show company.tools;

```

---

# Complete Example: File Workflow

```cmll
project "File Example";

set message = "Hello from CMLL";

write file "example.txt" from message;

read file "example.txt" as content;

show content;

```

---

# Complete Example: Shell Workflow

```cmll
project "Command Example";

set name = "CMLL";

say "Running a shell command...";
run "echo " + name;

run "pwd";

```

---

# Complete Example: Environment and URLs

```cmll
project "System Information";

say "Home directory:";
env "HOME";

say "Path:";
env "PATH";

set documentation = "CMLL: [https://github.com/dominexmacedon-docs/CMLL](https://github.com/dominexmacedon-docs/CMLL)";
list urls from documentation;

```

---

# Complete Example: Nested Control Flow

```cmll
set values = [1, 2, 3, 4, 5];
set total = 0;

for value in values {
    if value % 2 == 0 {
        total = total + value;
    } else {
        if value > 3 {
            total = total + 10;
        } else {
            total = total + 1;
        }
    }
}

show total;

```

---

# Complete Example: Function + Array + Object

```cmll
func priceWithTax(price, tax) {
    return price + (price * tax);
}

set products = [
    {name: "Phone", price: 500},
    {name: "Laptop", price: 1000}
];

for product in products {
    set finalPrice = priceWithTax(product.price, 0.05);
    show product.name + ": " + finalPrice;
}

```

---

# Grammar-Oriented Reference for AI Code Generation

The following simplified grammar describes the syntax that an AI should generate for the current implementation.

```text
program
    := statement*

statement
    := project_statement
     | set_statement
     | assignment_statement
     | if_statement
     | while_statement
     | for_statement
     | function_statement
     | task_statement
     | return_statement
     | run_statement
     | read_statement
     | write_statement
     | say_statement
     | show_statement
     | list_urls_statement
     | env_statement
     | expression_statement

project_statement
    := "project" expression ";"

set_statement
    := "set" identifier "=" expression ";"

assignment_statement
    := identifier "=" expression ";"

if_statement
    := "if" expression block
       ("else" (if_statement | block))?

while_statement
    := "while" expression block

for_statement
    := "for" identifier "in" expression block

function_statement
    := "func" identifier "(" parameters? ")" block

task_statement
    := "task" identifier "(" parameters? ")" block

parameters
    := identifier ("," identifier)*

return_statement
    := "return" expression? ";"

run_statement
    := "run" expression ("|" expression)*

read_statement
    := "read" "file" expression "as" identifier ";"

write_statement
    := "write" "file" expression "from" expression ";"

say_statement
    := "say" expression ";"

show_statement
    := "show" expression ";"

list_urls_statement
    := "list" "urls" "from" expression ";"

env_statement
    := "env" expression ";"

expression_statement
    := expression ";"

block
    := "{" statement* "}"

expression
    := logical_or

logical_or
    := logical_and ("||" logical_and)*

logical_and
    := equality ("&&" equality)*

equality
    := comparison (("==" | "!=") comparison)*

comparison
    := addition (("<" | "<=" | ">" | ">=") addition)*

addition
    := multiplication (("+" | "-") multiplication)*

multiplication
    := unary (("*" | "/" | "%") unary)*

unary
    := ("!" | "-") unary
     | postfix

postfix
    := primary ("." identifier | "(" arguments? ")")*

primary
    := number
     | string
     | "true"
     | "false"
     | "null"
     | identifier
     | array
     | object
     | "(" expression ")"

array
    := "[" (expression ("," expression)*)? "]"

object
    := "{" (object_key ":" expression
             ("," object_key ":" expression)*)? "}"

object_key
    := identifier
     | string

arguments
    := expression ("," expression)*

```

This is a simplified grammar for AI/code-generation purposes; the C parser is the authoritative implementation.

---

# Lexical Reference

## Identifiers

Identifiers can begin with:

```text
A-Z
a-z
_

```

After the first character, the lexer accepts:

```text
A-Z
a-z
0-9
_
-

```

Examples:

```cmll
set user_name = "Dominex";
set build-version = 1;
set _internal = true;

```

Avoid using reserved keywords as variable/function names.

## Keywords

The current lexer recognizes:

```text
project
set
task
if
else
while
for
in
func
return
true
false
null
say
show
run
read
write
file
exists
list
urls
from
env
get

```

Some recognized keywords are only partially implemented or are currently unused by the parser/runtime. See the compatibility section below.

---

# CMLL Runtime Model

CMLL is implemented as a tree-walking interpreter.

The source passes through these stages:

```text
.cmll source
     |
     v
   Lexer
     |
     v
   Tokens
     |
     v
   Parser
     |
     v
   AST / CMLLNode tree
     |
     v
   Runtime
     |
     v
   Output / file operations / shell commands

```

The implementation is divided into:

```text
common.c
common.h

lexer.c
lexer.h

parser.c
parser.h

runtime.c
runtime.h

main.c

```

## `common.*`

Contains shared utilities such as:

* positions
* diagnostics
* string duplication
* file reading
* file writing
* common helper functionality

## `lexer.*`

Converts source text into tokens.

It handles:

* identifiers
* keywords
* strings
* numbers
* operators
* punctuation
* `#` comments

## `parser.*`

Converts tokens into an AST represented by `CMLLNode`.

It handles:

* statements
* expressions
* functions
* loops
* conditions
* arrays
* objects
* property access
* calls

## `runtime.*`

Evaluates the AST.

It provides:

* environments
* values
* functions
* closures
* arithmetic
* conditions
* loops
* file operations
* shell commands
* environment lookup
* URL extraction

## `main.c`

Provides the command-line executable:

```bash
cmll program.cmll

```

and:

```bash
cmll --version
cmll --help

```

---

# Runtime Value Model

The interpreter uses these CMLL value types:

```text
VALUE_NULL
VALUE_NUMBER
VALUE_STRING
VALUE_BOOLEAN
VALUE_ARRAY
VALUE_OBJECT
VALUE_FUNCTION

```

A function stores:

```text
function declaration
captured closure environment

```

A function call creates a new environment whose parent is the captured closure.

---

# Declaration Execution Order

At program startup, top-level `func` and `task` declarations are registered before ordinary top-level statements execute.

This means:

```cmll
show add(2, 3);

func add(a, b) {
    return a + b;
}

```

works because `add` is registered before `show add(2, 3)` executes.

---

# Current Implementation Limitations

The source archive contains token/node definitions for some features that are not fully connected through the complete parser/runtime pipeline.

For accurate AI-generated CMLL code, distinguish **implemented features** from **reserved/partial features**.

| Feature | Current status |
| --- | --- |
| `project` | Implemented |
| `set` | Implemented |
| variable assignment | Implemented |
| numbers | Implemented |
| strings | Implemented |
| booleans | Implemented |
| `null` | Implemented |
| arrays | Implemented |
| objects | Implemented |
| object property access | Implemented |
| arithmetic | Implemented |
| comparisons | Implemented |
| `&&`, ` |  |
| `if` / `else` | Implemented |
| `while` | Implemented |
| `for ... in` arrays | Implemented |
| `func` | Implemented |
| `task` | Implemented |
| `return` | Implemented |
| function calls | Implemented |
| `say` | Implemented |
| `show` | Implemented |
| `run` | Implemented |
| `read file ... as ...` | Implemented |
| `write file ... from ...` | Implemented |
| `list urls from ...` | Implemented |
| `env ...` | Implemented as a printing statement |
| array indexing `a[0]` | Not implemented |
| `env(...)` expression/function | Not implemented |
| `exists` statement | Not connected to a working runtime operation |
| `get` HTTP operation | Declared in lexer/parser structures but not implemented in runtime execution |
| `run ... | ...` pipeline |
| interactive REPL | Not implemented by this executable |

Do not invent syntax for the unimplemented features.

For example, the current language should not be documented as supporting:

```cmll
set value = env("NAME");

```

or:

```cmll
show array[0];

```

or:

```cmll
get "[https://example.com](https://example.com)";

```

unless the interpreter is first extended to implement those features.

---

# AI Authoring Rules

When an AI writes CMLL code for this implementation, use these rules:

1. Use `.cmll` source files.
2. Use `set` for normal variable declarations.
3. Use `=` for reassignment.
4. End statements with `;`.
5. Use `#` for comments.
6. Use `if condition { ... }` and optional `else`.
7. Use `while condition { ... }`.
8. Use `for item in array { ... }`.
9. Use `func name(parameters) { ... }` for functions.
10. Use `task name(parameters) { ... }` when a task declaration is desired.
11. Use `return expression;` inside callable declarations.
12. Use `say expression;` or `show expression;` for output.
13. Use `run expression;` for trusted shell commands.
14. Use `read file expression as variable;` for reading files.
15. Use `write file expression from expression;` for writing files.
16. Use `env expression;` to print an environment variable.
17. Use `list urls from expression;` to extract URLs from text.
18. Use object property access such as `user.name`.
19. Do not use array indexing.
20. Do not treat `env` as a function.
21. Do not assume `get`, `exists`, or pipelines are fully implemented.
22. Do not invent `define`, `import`, or module syntax because those are not part of this implementation.
23. Keep commands and file paths as expressions supported by the parser.
24. Use parentheses for grouping complicated expressions.

---

# Recommended AI Prompt Context

When asking an AI to generate CMLL code for this implementation, the following context is sufficient:

```text
Write code for the CMLL language implemented by the CMLL C interpreter.

CMLL uses .cmll files.

Variable declaration:
set name = expression;

Assignment:
name = expression;

Output:
say expression;
show expression;

Conditions:
if expression {
    ...
} else {
    ...
}

While:
while expression {
    ...
}

For arrays:
for item in array {
    ...
}

Functions:
func name(a, b) {
    return a + b;
}

Tasks:
task name(a, b) {
    return a + b;
}

Arrays:
[1, 2, 3]

Objects:
{name: "CMLL", version: 1}

Object property:
object.name

File read:
read file "input.txt" as content;

File write:
write file "output.txt" from content;

Shell:
run "echo hello";

Environment variable printing:
env "HOME";

URL extraction:
list urls from text;

Operators:
+ - * / %
== != < <= > >=
&& || !

Comments:
# comment

Statements require semicolons because newlines are ignored by the lexer.

Do not use:
define
import
array[index]
env(...)
or assume get/exists/pipeline execution is implemented.

```

---

# Project Layout

The CMLL interpreter source is organized as:

```text
CMLL/
├── common.c
├── common.h
├── lexer.c
├── lexer.h
├── parser.c
├── parser.h
├── runtime.c
├── runtime.h
└── main.c

```

A project that contains CMLL programs can additionally contain:

```text
project/
├── programs/
│   ├── hello.cmll
│   ├── calculator.cmll
│   └── app.cmll
└── ...

```

Run a program with:

```bash
cmll programs/app.cmll

```

---

# Uninstall

Remove the installed executable:

```bash
make uninstall

```

This removes:

```text
/usr/local/bin/cmll

```

---

# Clean Installation Files

To remove the downloaded ZIP and local executable:

```bash
make clean

```

---

# Release

CMLL Linux x86_64 release:

```text
[https://github.com/dominexmacedon-docs/CMLL/releases/download/cmll-v1.0.0/cmll-linux-x86_64.zip](https://github.com/dominexmacedon-docs/CMLL/releases/download/cmll-v1.0.0/cmll-linux-x86_64.zip)

```

Version:

```text
CMLL 1.0.0

```

---

# Quick Reference

```cmll
# Project
project "My Program";

# Variables
set name = "CMLL";
set count = 0;

# Assignment
count = count + 1;

# Output
say "Hello";
show name;

# Arrays
set numbers = [1, 2, 3];

# Objects
set user = {
    name: "Dominex",
    active: true
};

show user.name;

# Condition
if count > 0 {
    say "positive";
} else {
    say "zero";
}

# While
while count < 5 {
    count = count + 1;
}

# For
for number in numbers {
    show number;
}

# Function
func add(a, b) {
    return a + b;
}

show add(10, 20);

# Task
task greet(name) {
    say "Hello, " + name;
}

greet("CMLL");

# File
write file "output.txt" from "Hello";
read file "output.txt" as content;
show content;

# Environment
env "HOME";

# URLs
list urls from "[https://example.com](https://example.com)";

# Shell command
run "echo CMLL";

```

CMLL is intentionally small: the lexer recognizes the language vocabulary, the parser builds an AST, and the C runtime walks that AST and performs the requested operations. This README should be treated as the language-generation reference for the current CMLL 1.0.0 implementation.
