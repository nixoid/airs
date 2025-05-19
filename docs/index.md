---
title: AIRS - AI Editor Rules Standard
version: 1.0.0

---

# AIRS: AI Editor Rules Standard

## 1\. Introduction

This document outlines the AI Editor Rules Standard (AIRS), a company-agnostic standard for defining custom rules, instructions, and prompts for AI coding assistants and language models integrated into development environments. The goal is to foster interoperability, reusability, and clarity when guiding AI behavior across different tools and projects.

**Project Repository on GitHub: [https://github.com/nixoid/airs](https://github.com/nixoid/airs)**

The official version of the AI Editor Rules Standard (AIRS) is published at [https://www.useairs.dev](https://www.useairs.dev). This document serves as its primary specification. We encourage community participation for its evolution; suggestions and improvements can be submitted via pull requests to the repository hosting this standard.

This standard draws inspiration from systems like Cursor's Rules and VS Code's Instructions/Prompt Files.

## 2\. Core Concepts

### What are AI Rules?

AI Rules are persistent sets of instructions, context, and guidelines provided to an AI model to tailor its responses, behavior, and output to specific project needs, coding standards, user preferences, or task requirements.

### Purpose of Rules

*   **Consistency**: Ensure AI-generated code or text adheres to specific styles and patterns.
*   **Contextual Awareness**: Provide the AI with relevant domain-specific knowledge, boilerplate, or existing code snippets.
*   **Efficiency**: Automate repetitive prompting or instruction-giving.
*   **Accuracy**: Improve the relevance and correctness of AI suggestions.
*   **Workflow Automation**: Define pre-set steps or considerations for common tasks.

## 3\. File Naming and Hierarchy

Rules are defined in Markdown files (`.md`) with YAML frontmatter.

### 3.1. Directory-Specific Rules

*   **Filename**: `.prompt.md`
*   **Location**: Placed directly within any directory.
*   **Scope**: The rule applies to activities within that specific directory and its subdirectories (unless overridden by a more specific `.prompt.md` in a subdirectory).

### 3.2. Project-Scoped Rules

*   **Directory Name**: `.prompts/`
*   **Location**: At the root of the project.
*   **Structure**: This directory can contain multiple `.md` rule files. Each file represents a distinct rule or a collection of related rules.
*   **Scope**: Rules in this directory can apply to the entire project or be conditionally activated based on their frontmatter configuration.

### 3.3. User-Scoped (Global) Rules

*   **Location**: Managed by the specific AI tool or editor, often through a settings UI.
*   **File-based (Recommendation)**: If a file-based approach is supported or desired for portability, user-specific rules could reside in a dedicated directory within the user's configuration folder (e.g., `~/.config/ai-rules-profile/` on Linux/macOS or `%APPDATA%/ai-rules-profile/` on Windows).
*   **Scope**: These rules apply globally across all projects for the user.

## 4\. Rule File Structure

Each rule file consists of two parts:

1.  **YAML Frontmatter**: Enclosed by `---` at the beginning of the file. Contains metadata that defines the rule's properties and behavior.
2.  **Markdown Body**: The content of the rule, written in standard Markdown, providing instructions to the AI.

```
---
# YAML Frontmatter
id: "my-example-rule"
name: "Example Rule"
description: "This is an example rule to demonstrate the structure."
activation: conditional
conditions:
  path_patterns:
    - "src/components/**/*.tsx"
context:
  include_files:
    - "./related-template.tsx"
# ... other frontmatter fields
---

# Markdown Body

This is where you write the actual instructions for the AI.
You can use Markdown formatting, including:

- Lists
- Code blocks
- Headings

## Specific Guidelines
1. Always use functional components.
2. Props should be destructured.
```

## 5\. Frontmatter Fields

The following fields are defined for the YAML frontmatter:

`id` (string, optional): A unique, machine-readable identifier for the rule (e.g., `"typescript-style-guide"`). Useful for referencing or logging.

`name` (string, optional): A human-readable name for the rule (e.g., `"TypeScript Style Guide"`). Displayed in UIs.

`description` (string, required): A concise explanation of the rule's purpose and content. Crucial for `selectable` rules or when browsing available rules.

`version` (string, optional): Version of the rule itself (e.g., `"1.2.0"`).

`author` (string, optional): Creator of the rule.

`tags` (array of strings, optional): Keywords for categorizing, filtering, or organizing rules (e.g., `["typescript", "style", "frontend"]`).

`activation` (enum, required): Defines how the rule becomes active.

*   `always`: The rule is always active if its inherent scope (determined by its file location or global status) is relevant to the current context. For a `.prompt.md` file, this means it's always active for its directory.
*   `conditional`: The rule is activated only if all specified `conditions` are met.
*   `selectable`: The rule is made available for explicit selection by the user or for the AI to choose based on its `description` and relevance to the query.

`conditions` (object, optional): Required if `activation` is `conditional`. Defines criteria for activation.

*   `path_patterns` (array of strings, optional): An array of glob patterns. The rule activates if the currently active file path, or files relevant to the current AI task, match any of these patterns. Example: `["src/api/**/*.ts", "tests/api/**.test.ts"]`.
*   `language_ids` (array of strings, optional): An array of language identifiers (e.g., `["typescript", "python"]`). The rule activates if the active file's language matches.
*   `active_symbols` (array of strings, optional): Rule activates if specific code symbols (e.g., `function:getUser`, `class:ApiService`) are present in the active context. (Advanced)

`context` (object, optional): Specifies additional information to be dynamically included and made available to the AI when the rule is active.

*   `include_files` (array of strings, optional): An array of file paths or glob patterns. The content of these files will be provided to the AI.
    *   Paths relative to the rule file: `["./base-component.template.tsx"]`
    *   Paths relative to project root (convention): `["project://src/global-types.d.ts"]`
*   `include_variables` (array of strings, optional): Names of predefined environment variables that the AI tool should resolve and inject. Examples: `["selection", "clipboard_content", "current_file_name", "current_branch"]`. The exact set of available variables may be editor-specific.
*   `embeddings_search` (array of objects, optional): Defines semantic searches to perform and include results. (Advanced)
    *   `query` (string): The semantic search query.
    *   `limit` (integer, optional): Number of results to include.

`priority` (integer, optional): An integer indicating the rule's precedence. Higher values typically mean higher priority. If multiple rules are active, priority can help resolve conflicts or determine the order of application. Defaults to `0`.

`output_format` (string, optional): A hint to the AI about the desired output format if the rule is about generation (e.g., `"json"`, `"markdown"`, `"typescript"`).

## 6\. Rule Body (Markdown)

The body of the rule file is written in standard Markdown. This is where you provide the actual instructions, guidelines, context, or examples to the AI.

### Content Guidelines:

*   **Clarity**: Be explicit and unambiguous.
*   **Conciseness**: Avoid unnecessary verbosity, but provide sufficient detail.
*   **Actionability**: Instructions should be actionable by the AI.
*   **Examples**: Use fenced code blocks for code examples, templates, or snippets.
*   **Persona/Tone**: You can instruct the AI on the tone or persona to adopt.
*   **Constraints**: Specify "do's and don'ts."

### Placeholders and Variables:

*   **Generic Placeholder**: Use `{{placeholder_name}}` for values that should be dynamically filled in by the user or context.
*   **Editor-Specific Variables**: Acknowledge that AI tools may provide their own syntax for dynamic variables (e.g., VS Code's `#selection` or `@workspace` commands). The rule body can incorporate these where known.

## 7\. Examples

### Example 1: Directory-Specific Rule (`.prompt.md`)

File: `src/api/.prompt.md`

```
---
name: "API Development Guidelines for this Directory"
description: "Standard practices for developing API endpoints in this directory. Focuses on Node.js with Express."
activation: always
context:
  include_files:
    - "project://src/core/api-error-handler.ts"
    - "./base-controller.template.ts"
tags: ["api", "nodejs", "express"]
---

## API Endpoint Development (Node.js/Express)

When creating new API endpoints in this directory (`src/api`):

1.  **Framework**: Use Express.js.
2.  **Error Handling**: Utilize the central `api-error-handler.ts`. (Content included for your reference).
3.  **Structure**: Follow the pattern in `base-controller.template.ts` if applicable.
4.  **Validation**: Use `zod` for request body and parameter validation.
5.  **Response Format**: Ensure responses are JSON and follow the standard `{ "data": ..., "error": ... }` structure.
6.  **Logging**: Implement detailed logging for request lifecycle and errors.
```

### Example 2: Project-Scoped Conditional Rule

File: `.prompts/frontend-tailwind-standards.md`

```
---
id: "tailwind-css-usage"
name: "Tailwind CSS Best Practices"
description: "Ensures consistent and optimal use of Tailwind CSS in frontend components."
activation: conditional
conditions:
  path_patterns:
    - "src/frontend/components/**/*.tsx"
    - "src/frontend/views/**/*.tsx"
  language_ids:
    - "typescriptreact"
tags: ["frontend", "tailwind", "css", "style"]
priority: 10
---

## Tailwind CSS Usage Guide

When working with Tailwind CSS in `.tsx` files within `src/frontend/components/` or `src/frontend/views/`:

-   **Utility-First**: Prioritize using utility classes directly in the JSX.
-   **Avoid `@apply`**: Minimize the use of `@apply` in CSS files. Prefer component encapsulation or utility composition.
-   **Customization**: If custom styles are needed, add them to `tailwind.config.js` under `theme.extend`.
-   **Readability**: For long lists of classes, consider line breaks or helper functions if it improves readability.
-   **Purging**: Be mindful of class names that might be purged (e.g., dynamically generated class strings). Ensure they are safelisted if necessary.
-   **Responsive Design**: Use Tailwind's responsive prefixes (e.g., `sm:`, `md:`) for adapting layouts.
```

### Example 3: Selectable Rule for Code Refactoring

File: `.prompts/refactor-to-async-await.md`

```
---
id: "refactor-promise-to-async"
name: "Refactor Promises to Async/Await"
description: "Provides guidance and steps for refactoring Promise-based asynchronous JavaScript/TypeScript code to use async/await syntax."
activation: selectable
tags: ["refactor", "async", "javascript", "typescript"]
---

## Refactoring to Async/Await

When asked to refactor Promise-chains to async/await:

1.  **Identify Functions**: Target functions that return Promises or use `.then()` and `.catch()`.
2.  **`async` Keyword**: Add the `async` keyword before the function declaration (`async function myFunction() {...}`).
3.  **`await` Keyword**: Replace `.then(value => ...)` with `const value = await promise;`.
4.  **Error Handling**: Convert `.catch(err => ...)` blocks to `try...catch` blocks surrounding the `await` expressions.
    ```javascript
    // Before
    function fetchData(url) {
      return fetch(url)
        .then(response => response.json())
        .catch(error => console.error('Error:', error));
    }

    // After
    async function fetchData(url) {
      try {
        const response = await fetch(url);
        const data = await response.json();
        return data;
      } catch (error) {
        console.error('Error:', error);
        // Re-throw or handle as appropriate
        throw error; 
      }
    }
    ```
5.  **Parallel Operations**: For multiple independent promises, use `await Promise.all([...])` to maintain concurrency.
6.  **Return Values**: Ensure the refactored async function implicitly or explicitly returns a Promise with the correct resolved value.
```

## 8\. Interoperability and Editor-Specific Extensions

This standard provides a common baseline. Specific AI tools and editors may:

*   Support additional, non-standard frontmatter fields. These can coexist with the standard fields.
*   Implement unique syntaxes within the Markdown body (e.g., VS Code's `@workspace` or `#file` chat variables, Cursor's `@file` references). Where possible, rules can be written to leverage these if the target environment is known, or provide alternatives for broader compatibility.
*   Offer richer UI integrations for managing and selecting rules.

The goal is for a rule file written according to this standard to be largely understandable and useful across different platforms, even if some advanced or editor-specific features are gracefully ignored or require minor adaptation.

## 9\. Contributing & Official Source

The AI Editor Rules Standard (AIRS) is an open standard, and community contributions are highly encouraged to help it evolve and improve.

*   **Official Publication**: The canonical version of this standard is hosted at [https://www.useairs.dev](https://www.useairs.dev).
*   **Contributions**: Suggestions, improvements, and discussions are welcome. Please submit them as pull requests or issues to the GitHub repository where this standard is maintained: [https://github.com/nixoid/airs](https://github.com/nixoid/airs).

```typescript
// Example of a good component structure
import React from 'react';

interface MyComponentProps {
  title: string;
}

export const MyComponent: React.FC<MyComponentProps> = ({ title }) => {
  return <div>{title}</div>;
};
``` 