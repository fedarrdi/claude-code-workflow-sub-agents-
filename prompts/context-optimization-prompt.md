Please create a comprehensive context management system for the existing agents in /agents directory. 

Execute the following tasks:

1. Create a new file `/agents/context-manager.md` with a Context Manager Agent that maintains relevant context slices. Include a context registry structure that maps modules to their files, dependencies, agents, and MCP tools.

2. Create `/agents/mcp-coordinator.md` for managing MCP server tools like Playwright. Include a registry of MCP tools with their purpose, which agents use them, and what context they need.

3. Create `/agents/workflow-optimizer.md` that implements smart context switching, context caching, and workflow patterns for feature addition and bug fixes. Include context diff strategies to load only changed or required files.

4. Update ALL existing agents in /agents directory to add the following sections:
   - Context Requirements (required_files, optional_context, exclude_patterns)
   - Context Loading Strategy 
   - MCP Tools Used
   - State Management
   Keep all their existing content and just add these new sections.

5. Create `/prompts/smart-context-prompt.md` with instructions for context-aware execution, including how to start with minimal context, expand as needed, use context markers, and integrate MCP tools efficiently.

6. Create `/agents/context-templates/` directory with these templates:
   - `add-api-endpoint.md` - for adding API endpoints with minimal context
   - `add-ui-component.md` - for creating UI components with focused context
   - `fix-bug.md` - for bug fixes with trace-focused context
   - `add-feature.md` - for new features with module-level context
   - `run-tests.md` - for testing with Playwright using only affected components

7. Create `/agents/execute-with-context.md` that provides a system for executing agents with different context levels (minimal, module, full) with examples and optimization rules.

8. Create `/context/` directory structure:
   - `/context/registry.json` - initialize with the current project's module structure
   - `/context/cache/` - empty directory for context caching
   - `/context/state.json` - for tracking last execution state

9. Add a `/agents/quick-commands.md` file with short commands for common operations like:
   - Loading specific module context
   - Running Playwright on changed components only
   - Executing agents with minimal context
   - Updating context after changes

10. Analyze the current codebase structure and populate the context registry with actual modules, their file patterns, and dependencies found in the project.

Make all agents context-aware so they don't scan the entire codebase unnecessarily. Ensure the context management system integrates with MCP tools like Playwright for focused testing.

After creating all files, provide a summary of:
- Files created
- How the context system works
- Example command to test the new system
- How this improves agent efficiency
