# Context Management System

## Overview
This directory contains the context management system for the claude-agents project. The system optimizes agent execution by loading only necessary files and tracking usage patterns.

## Directory Structure

```
context/
├── registry.json       # Module and dependency mapping
├── state.json          # Current execution state and statistics
├── cache/              # Cached context slices
└── README.md           # This file
```

## Files

### registry.json
**Purpose**: Maps modules to their files, dependencies, and agents.

**Structure**:
- `project_info`: Project metadata
- `modules`: Module definitions with file patterns, dependencies, and agents
- `common_patterns`: Pre-defined patterns for common tasks
- `file_patterns`: Glob patterns for file types
- `exclude_patterns`: Files to always exclude

**Usage**: Context Manager reads this file to determine which files to load for a given task.

### state.json
**Purpose**: Tracks execution state, statistics, and performance metrics.

**Structure**:
- `last_execution`: Most recent task execution details
- `module_stats`: Per-module usage statistics
- `hot_modules`: Frequently accessed modules
- `cache_stats`: Cache hit/miss rates
- `performance_metrics`: Overall system performance
- `recent_tasks`: History of recent executions

**Usage**: Updated after each agent execution to track patterns and optimize future runs.

### cache/
**Purpose**: Stores cached context slices for common task patterns.

**Contents**: JSON files named by pattern (e.g., `add-ui-component.json`)

**Structure** of cache files:
```json
{
  "pattern": "add-ui-component",
  "modules": ["frontend-engineering"],
  "files": ["src/components/Button.tsx", "..."],
  "tokens": 1200,
  "last_used": "2025-11-17T10:30:00Z",
  "use_count": 23,
  "success_rate": 0.96
}
```

**Usage**: Context Manager checks cache before building context from scratch.

**Invalidation**: Cache entries are invalidated when:
- Files in the cache are modified
- Module dependencies change
- Entry is older than 7 days
- LRU eviction (when cache > 100 entries)

## How It Works

### 1. Task Request
User or orchestrator requests a task (e.g., "Add logout button").

### 2. Context Manager Analyzes
Context Manager analyzes the request:
- Determines task type (add component, fix bug, etc.)
- Identifies affected modules
- Determines context level (minimal, module, full)

### 3. Check Cache
Context Manager checks `cache/` for similar previous tasks:
- If cache hit: Reuse cached context
- If cache miss: Build context from registry

### 4. Load Context
Context Manager uses `registry.json` to:
- Find modules matching the task
- Build file list from module patterns
- Apply exclusion patterns
- Return minimal file set

### 5. Execute Agent
Specialized agent executes with focused context:
- Loads only the provided files
- Implements the task
- Requests expansion if needed

### 6. Update State
After execution, `state.json` is updated:
- Record files accessed
- Update module statistics
- Track token usage
- Cache successful pattern

### 7. Cache Result
If execution was successful:
- Create/update cache entry
- Record pattern for future reuse

## Usage Examples

### Example 1: Add Button Component

**Request**: "Add a ShareButton component"

**Context Manager Process**:
1. Analyze: Task type = "add-ui-component"
2. Check cache: Found `add-ui-component.json` (cache hit)
3. Load context from cache:
   - `src/components/Button.tsx` (similar component)
   - `src/theme/index.ts` (design system)
   - `src/types/component.ts` (types)
4. Return: 3 files, ~800 tokens
5. Agent executes: Frontend Engineer creates ShareButton
6. Update state: Record success, update cache usage count

**Result**: Task completed in 5 minutes with 800 tokens (98% savings vs full context).

### Example 2: Fix Authentication Bug

**Request**: "Fix error: Token validation failing"

**Context Manager Process**:
1. Analyze: Task type = "fix-bug", module = "backend-engineering"
2. Check cache: No cache for this specific error (cache miss)
3. Build context from registry:
   - Load backend-engineering module entry points
   - Identify auth-related files
   - Return minimal trace-focused context
4. Return: 4 files, ~700 tokens
5. Agent executes: Backend Engineer fixes token validation
6. Update state: Record success
7. Cache pattern: Save "fix-auth-error.json" for future

**Result**: Bug fixed in 5 minutes with 700 tokens (98.5% savings vs full context).

### Example 3: Add Comments Feature

**Request**: "Add comments feature to posts"

**Context Manager Process**:
1. Analyze: Task type = "add-feature", modules = "backend-engineering" + "frontend-engineering"
2. Check cache: Found `add-feature.json` template (partial hit)
3. Build context:
   - Backend: posts API, comments model, types
   - Frontend: comment components, hooks, API client
4. Use context diff:
   - Phase 1: Backend files (1,800 tokens)
   - Phase 2: Frontend files (1,600 tokens, unload backend)
   - Phase 3: Integration (1,200 tokens, unload implementation)
5. Agents execute: Backend Engineer → Frontend Engineer → QA
6. Update state: Record multi-phase execution
7. Cache pattern: Update "add-feature.json" with actual files used

**Result**: Feature completed in 60 minutes with 2,800 tokens peak (94% savings vs full context).

## Optimization Strategies

### Strategy 1: Context Caching
- Common patterns cached for instant reuse
- Target: >60% cache hit rate
- Saves 5-10 seconds per task

### Strategy 2: Module Boundaries
- Modules are isolated units
- Don't cross boundaries unless required
- Keeps context minimal

### Strategy 3: Context Diff
- Multi-phase tasks use context diff
- Unload completed files
- Load new files as needed
- Keeps peak tokens low

### Strategy 4: Hot Module Tracking
- Track frequently accessed modules
- Pre-cache hot modules
- Optimize access patterns

### Strategy 5: LRU Cache Eviction
- Keep cache size manageable
- Evict least recently used entries
- Maintain top 100 patterns

## Maintenance

### Daily
- No maintenance required
- System self-manages

### Weekly
- Review `hot_modules` in `state.json`
- Optimize frequently used patterns

### Monthly
- Clean old cache entries (>7 days unused)
- Review `performance_metrics`
- Update patterns if success rate < 90%

### As Needed
- Update `registry.json` when:
  - New modules added
  - File structure changes
  - New agent types created

## Performance Metrics

### Target Metrics
- Token savings: >90% vs full context
- Cache hit rate: >60%
- Avg execution time: <15 minutes per task
- Success rate: >95%

### Current Metrics
Check `state.json` for current performance metrics:
- `performance_metrics.total_token_savings`
- `cache_stats.hit_rate`
- `performance_metrics.avg_execution_time_seconds`

## Troubleshooting

### Issue: Low Cache Hit Rate (<30%)
**Cause**: Tasks too varied or cache not populated
**Solution**:
- Review task patterns
- Create cache entries manually for common tasks
- Update pattern definitions

### Issue: High Token Usage (>5,000 per task)
**Cause**: Loading too much context
**Solution**:
- Review `registry.json` module definitions
- Ensure exclusion patterns are correct
- Break large tasks into smaller sub-tasks

### Issue: Cache Misses for Common Tasks
**Cause**: Pattern not recognized or cache invalidated
**Solution**:
- Check `cache/` directory for entries
- Verify cache files are valid JSON
- Update pattern matching logic

### Issue: Outdated Registry
**Cause**: Project structure changed
**Solution**:
- Update `registry.json` with new file patterns
- Run registry validation
- Clear cache if major changes

## Integration

### With Context Manager Agent
Context Manager reads `registry.json` to provide minimal context.

### With Workflow Optimizer Agent
Workflow Optimizer uses `state.json` to identify patterns and optimize workflows.

### With All Agents
All agents benefit from focused context provided by this system.

## Future Enhancements

- Automatic registry updates on file structure changes
- Machine learning for pattern recognition
- Real-time performance monitoring
- A/B testing for context strategies
- Distributed caching for team usage
