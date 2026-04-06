---
name: design-check
description: Check frontend design consistency. Detect discrepancies between design data (Figma, etc.) and frontend implementation, and propose fixes.
---

# Frontend Design Check Skill

Compare design data with the implemented screen to detect inconsistencies in margins, padding, colors, sizes, etc., and propose fixes.

## How to Execute

When this skill is invoked, immediately use the Agent tool to spawn a sub-agent. Pass the workflow below as the sub-agent's instructions. **Do NOT execute the workflow in the main context.** This ensures the design check is performed with a fresh context, free from implementation bias.

Provide the sub-agent with only the essential context needed for checking (e.g., URLs to check, design specs or Figma links), without passing along implementation details.

## Prerequisites

Design information for comparison (descriptions in the prompt, documents, Figma URLs, etc.) is required. For taking screenshots, use the following tools in priority order:

1. **agent-browser** (MCP tool) — use this if available
2. **Playwright** — use as fallback if agent-browser is not available

## Workflow

Make a todo list for all the tasks in this workflow and work on them one after another.

### 1. Identify Design Data References

Investigate where the design information for comparison is located:

- **Information in the prompt**: If the user provides design information directly in the prompt, use it as the top priority.
- **Documents and source code**: If the prompt does not contain the information, investigate design documents and style definition files within the project.
- **External tool references**: If external references such as Figma URLs are provided, attempt to access them in the next step.

### 2. Access Design Data (If Possible)

Attempt to retrieve data from external design tools:

- **Access via MCP or API**: If Figma or similar tools are accessible via MCP tools or APIs, retrieve the design data directly.
- **If inaccessible**: If the connection cannot be established, skip this step and proceed with the available information.

### 3. Consistency Validation Based on Design Data

Match the browser width and compare the markup with the design data:

- **Validation targets**: Strictly compare margins, padding, background colors, borders, colors, gradients, sizes, fonts, etc.
- **Exclude text differences**: Do not flag text differences that can be inferred as data fetched from an API.
- **Consider variable-width elements**: Even if an element has a fixed width in the design data, validate it assuming variable width if it can be inferred as a variable-width element (lists, text containers, etc.).
- **Report inconsistencies**: When discrepancies are found, clearly report the affected area, expected value, and actual value.

### 4. Image-Based Validation

If possible, perform a visual comparison of the design data and the implemented screen:

- **Obtain design images**: Download or export the design data as images.
- **Take screenshots**: Capture screenshots of the implemented screen at the same width. Use **agent-browser** (MCP tool) if available; otherwise fall back to **Playwright**.
- **Compare images**: Place the two images side by side and identify visual differences.
- **Fix proposal priority**: Prioritize fixes based on matching the actual screen appearance rather than matching the design data exactly.

### 5. Report Results

Report the validation results to the user:

- **List of discrepancies**: Present a list of detected inconsistencies, including the affected area, expected value, and actual value.
- **Fix proposals**: Propose specific fix methods for each discrepancy (e.g., CSS property value changes).
- **Priority levels**: Assign fix priority (High, Medium, Low) based on visual impact.
- **Next actions**: Confirm with the user whether to proceed with fixes, and move on to code modifications if needed.
