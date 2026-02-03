# Guide to Writing AGENTS.md

## Overview
This guide provides a systematic approach to writing `AGENTS.md` files for future projects, ensuring clarity, simplicity, and adherence to established rules.

---

## Why AGENTS.md is Needed

The `AGENTS.md` file serves as a crucial guide for ensuring agents operate effectively within a project. It defines clear rules, simplifies communication, and ensures consistency in contributions—all while preventing unintended or unauthorized changes.

## New Agent Staging Process

### Rule for Staging Changes
- When an agent is writing, creating, or amending files, all changes **must remain staged within the conversation** until explicit user approval is provided.
- Only after the user "green lights" the action should the agent execute the actual changes in the repository.

## Steps to Write an AGENTS.md File

### Step 1: Understand the Repository's Purpose
- Identify the key purpose of the repository (e.g., structured notes, code, documentation).
- Tailor the rules in `AGENTS.md` to suit this purpose.

### Step 2: Specify Rules for Agents
Include the following fundamental rules:

#### 1. Approval Before Changes
- Agents must **always** seek explicit approval before making changes.
- Clearly state that no file should be deleted, modified, or created without explicit written approval.

#### 2. Simplicity and Clarity
- Avoid excessive explanations, filler words, or unnecessary jargon.
- Provide direct and actionable instructions:
  - ✅ "Initialize using `command`."
  - 🚫 "We highly recommend initializing using this simple command: `command`."

#### 3. Markdown Usage
- Ensure all documentation adheres to markdown standards:
  - **Headings**: Use `#`, `##`, and `###` for structure.
  - **Lists**: Use `-` for unordered lists and `1.` for ordered lists.
  - **Code Blocks**:
    ```markdown
    ```
    Example code block
    ```
    ```

#### 4. Repository Style Rules
- Define file-naming conventions:
  - ✅ Use lowercase-hyphenated names (e.g., `example-notes.md`).
  - 🚫 Avoid camelCase or underscores (e.g., `exampleNotes.md`, `example_notes.md`).
- Break long text into readable sections with blank lines.

### Step 3: Identify Potential Weaknesses
- Review the proposed document for ambiguities or missing elements.
- Address these by:
  - Adding examples for clarity.
  - Providing additional context, if necessary (e.g., file-naming rules).

### Step 4: Validate Against Guiding Principles
- Ensure the `AGENTS.md` aligns with the following principles:
  - Absolute clarity of rules (e.g., "no exceptions" for approval processes).
  - Conciseness to avoid overloading users with information.
  - Markdown-first approach to maintain consistency.

```
# AGENTS.md

## Overview
This repository is dedicated to structured markdown notes. Agents must strictly follow these rules and seek explicit approval before making any file changes.

---

## Rules for Agents

### Rule 1: Approval Before Changes
- Agents must **never** delete, modify, or create files without explicit written (e.g., in chat) user approval. This rule is absolute, with no exceptions.
- All changes must remain **staged in the conversation** until the user explicitly approves. Only apply changes to the repository after receiving explicit confirmation.

### Rule 2: Keep It Simple
- Avoid filler, agreeing words, or decorative language when editing or writing notes. Clarity is the only goal. Redundant explanations are prohibited.
- Example:
  - ✅ "Run this command to initialize."  
  - 🚫 "It is strongly recommended you run this simple command to initialize because it's very helpful."

### Rule 3: Markdown First
- All documentation must be written in Markdown. Formatting conventions:
  1. **Headings**: Use `#`, `##`, `###` in order. Example:
     ```markdown
     # Main Title
     ## Section Header
     ### Subsection Header
     ```
  2. **Lists**:
     - Unordered: `- Item`
     - Ordered: `1. Step`
     - Example:
       ```markdown
       - Bullet 1
         - Sub-bullet
       1. Step 1
       2. Step 2
       ```
  3. **Code**:
     - Inline: `` `example` ``
     - Multiline:
       ```markdown
       ```
       block of code
       ```
       ```

### Rule 4: Repository Style Rules
- **File Names**:
  - Follow lowercase-hyphenated convention:
    - ✅ `example-notes.md`
    - ✅ `part-1-summary.md`
    - 🚫 Avoid: `exampleNotes.md`, `Example_Notes.md`
- Use blank lines between sections and avoid long paragraphs:
  - Break text into readable chunks, with one idea per paragraph.
```
---

## Summary
To write effective `AGENTS.md` files:
1. Understand the repository and its specific needs.
2. Focus on clarity, simplicity, and concise formatting.
3. Rigorously validate with user feedback for any weak points.

Maintain consistency with these principles, and your `AGENTS.md` will effectively guide agentic work for any project.