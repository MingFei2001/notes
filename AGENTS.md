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