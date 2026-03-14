---
name: dev-skill-creator
description: Create technical development skills for software engineers. Use when developers want to build skills for code generation, debugging workflows, test automation, CI/CD integration, architecture patterns, or technical process documentation. Focuses on showing rich diagnostic output, automated verification, and giving developers full control over the skill behavior.
---

# Dev Skill Creator

A skill for creating technical skills targeted at software developers and engineers.

**Target audience:** Software developers who understand software development processes, testing, CI/CD, and need skills that provide detailed technical output, automated validation, and programmatic verification.

## High-level Process

Creating a dev-skill follows this workflow:

- **Specification**: Define the technical problem, expected inputs/outputs, edge cases, and success criteria
- **Draft implementation**: Write the skill with emphasis on:
  - Detailed diagnostic output (logs, intermediate states, validation results)
  - Automated verification (scripts, assertions, programmatic checks)
  - Error detection and recovery guidance
  - Developer control points (configuration, override mechanisms)
- **Test harness**: Create test cases that exercise edge cases, error paths, and performance characteristics
- **Automated evaluation**: Run test cases with quantitative assertions and performance metrics
- **Iteration**: Refine based on test failures, performance bottlenecks, and verification gaps
- **Scale testing**: Expand test coverage and validate at scale

**Key differences from general skill creation:**
- Developers expect to see HOW things work, not just results
- Skills should expose diagnostic information, intermediate states, and decision points
- Automated verification is preferred over manual review where possible
- Error cases and recovery paths are first-class concerns

Your job is to meet developers where they are in this process and help them build technically rigorous skills.

## Communication Style

**Developers expect:**
- Technical precision without hand-holding
- JSON schemas, API contracts, and data structures presented directly
- Detailed error messages with root causes
- Performance metrics (time, tokens, pass rates) upfront
- Code examples that are production-ready, not simplified

**Don't:**
- Explain basic programming concepts (JSON, assertions, regex, shell commands)
- Apologize for technical complexity
- Simplify error messages to be "friendlier"

**Do:**
- Surface all diagnostic information available
- Show command output, logs, and intermediate states
- Provide exact file paths, line numbers, and technical specifics
- Assume familiarity with git, testing frameworks, CI/CD, and development workflows

---

## Creating a skill

### Capture Technical Specification

Extract the technical requirements from the conversation or probe for them:

1. **Problem definition**: What technical problem does this skill solve? What are the inputs, outputs, and transformations?
2. **Trigger conditions**: What code patterns, error messages, or development contexts should invoke this skill?
3. **Output contracts**: What are the exact output formats? (JSON schemas, file types, exit codes, stdout/stderr expectations)
4. **Edge cases and error paths**: What are the failure modes? How should errors surface to developers?
5. **Performance requirements**: Are there latency, token usage, or resource constraints?
6. **Verification strategy**: What assertions, scripts, or checks validate correct behavior?

For dev-skills, testing is not optional — these skills need programmatic verification.

### Technical Research

Research the problem space:
- Check for existing patterns in similar tools (linters, formatters, test frameworks)
- Search documentation for relevant APIs, libraries, or frameworks
- Identify reusable scripts or utilities that should be bundled
- Look for common pitfalls and anti-patterns to avoid

Use parallel subagents if the research is independent (e.g., API docs + existing tools + error patterns).

### Write the SKILL.md

Structure for developer-focused skills:

- **name**: Kebab-case identifier (e.g., `zero-downtime-deploy`)
- **description**: Trigger conditions (error patterns, code contexts, symptoms). Be specific with technical terms, stack traces, and tool names.
- **compatibility**: Required tools, minimum versions, environment constraints
- **skill body**: See structure below

### Dev-Skill Structure

```markdown
# Skill Name

## Overview
Technical problem and solution approach. Include failure modes this prevents.

## When to Use
- Specific error messages or stack traces
- Code patterns or anti-patterns
- Development phases (pre-commit, CI, deploy)
- Performance symptoms (high latency, memory leaks)

When NOT to use (technical scenarios where this skill doesn't apply)

## Diagnostic Output

This skill will show:
- Intermediate states (parsed data, validation results)
- Decision points (why X was chosen over Y)
- Performance metrics (time, memory, API calls)
- Error details (root cause, recovery options)

## Quick Reference
| Operation | Command/Pattern | Notes |
|-----------|----------------|-------|

## Implementation
Step-by-step with:
- Code examples (production-ready, with error handling)
- Shell commands (exact, with flags explained)
- API contracts (request/response schemas)
- Verification commands (how to check each step succeeded)

## Verification
Scripts or assertions to validate correct behavior:
```bash
# Example verification script
./scripts/verify_output.sh <path>
```

## Error Recovery
| Error Pattern | Root Cause | Fix |
|---------------|------------|-----|

## Performance Characteristics
- Time complexity: O(n) for...
- Token usage: ~X tokens per invocation
- Memory: Y MB peak

## Common Mistakes
Technical pitfalls with root causes and fixes. Include:
- Race conditions
- Off-by-one errors
- Resource leaks
- Configuration errors
```

### Skill Writing Guide

#### Bundled Resources

Dev-skills often need executable validation:

```
skill-name/
├── SKILL.md (instructions)
├── scripts/
│   ├── verify.sh          - Automated verification
│   ├── setup_env.sh       - Environment setup
│   └── benchmark.py       - Performance measurement
├── references/
│   ├── api-reference.md   - Complete API documentation
│   └── error-catalog.md   - Known errors and fixes
└── templates/
    └── config.template.json - Configuration template
```

**When to bundle scripts:**
- Verification logic that's complex or error-prone to replicate
- Setup/teardown that needs precise sequencing
- Performance benchmarks that should be consistent
- Parsing logic that's reused across test cases

**Scripts should:**
- Have clear exit codes (0 = success, non-zero = failure with specific meanings)
- Output structured data (JSON) for programmatic parsing
- Log diagnostic information to stderr
- Be idempotent where possible

#### Progressive Disclosure for Dev Skills

1. **Metadata** (name + description) - Always in context. Include technical keywords: error messages, stack trace patterns, tool names, API endpoints.
2. **SKILL.md body** - Core workflow, decision logic, verification steps (<500 lines)
3. **Bundled resources** - Detailed API docs, scripts, templates (unlimited)

**Optimize for:**
- Developers scanning quickly for relevant patterns
- Copy-paste ready commands and code
- Clear success/failure indicators
- Diagnostic output for debugging

**Key patterns:**
- Keep SKILL.md under 500 lines; if you're approaching this limit, add an additional layer of hierarchy along with clear pointers about where the model using the skill should go next to follow up.
- Reference files clearly from SKILL.md with guidance on when to read them
- For large reference files (>300 lines), include a table of contents

**Domain organization**: When a skill supports multiple domains/frameworks, organize by variant:
```
cloud-deploy/
├── SKILL.md (workflow + selection)
└── references/
    ├── aws.md
    ├── gcp.md
    └── azure.md
```
Claude reads only the relevant reference file.

#### Principle of Lack of Surprise

This goes without saying, but skills must not contain malware, exploit code, or any content that could compromise system security. A skill's contents should not surprise the user in their intent if described. Don't go along with requests to create misleading skills or skills designed to facilitate unauthorized access, data exfiltration, or other malicious activities. Things like a "roleplay as an XYZ" are OK though.

#### Writing for Developers

**Show your work:**
```markdown
## Validation Steps
1. Parse input: `jq '.data' < input.json`
2. Verify schema: `./scripts/validate_schema.py --input parsed.json`
3. Transform: `./scripts/transform.py < parsed.json > output.json`
4. Verify output: `./scripts/verify_output.sh output.json`

Each step should produce diagnostic output. If step 2 fails, examine:
- Schema validation errors (stderr from validate_schema.py)
- Input structure (diff expected vs actual with `jq -S`)
- Type mismatches (check validation.log)
```

**Expose decision points:**
```markdown
## Choosing the Right Strategy

| Condition | Strategy | Why |
|-----------|----------|-----|
| `data.size < 1MB` | In-memory processing | Fast, no I/O overhead |
| `data.size > 1MB` | Streaming | Prevents OOM, slight latency increase |
| `data.format == "nested"` | Recursive parser | Handles arbitrary depth |
| `data.format == "flat"` | Direct mapping | 10x faster for simple cases |

The skill will log which strategy was selected and why.
```

**Production-ready code:**
```python
#!/usr/bin/env python3
import sys
import json
import logging

logging.basicConfig(level=logging.INFO, format='%(levelname)s: %(message)s')
logger = logging.getLogger(__name__)

def transform(input_data: dict) -> dict:
    """
    Transform input data structure to output format.

    Args:
        input_data: Validated input matching input_schema.json

    Returns:
        Output matching output_schema.json

    Raises:
        ValueError: If required fields missing
        TypeError: If field types incorrect
    """
    try:
        # Implementation with error handling
        logger.info(f"Processing {len(input_data.get('items', []))} items")
        # ...
        return output
    except KeyError as e:
        logger.error(f"Missing required field: {e}")
        raise ValueError(f"Invalid input structure: {e}")

if __name__ == "__main__":
    try:
        input_data = json.load(sys.stdin)
        result = transform(input_data)
        json.dump(result, sys.stdout, indent=2)
        sys.exit(0)
    except Exception as e:
        logger.error(f"Transformation failed: {e}")
        sys.exit(1)
```

### Test Cases for Dev-Skills

After drafting the skill, create comprehensive test cases covering:

**Functional coverage:**
- Happy path (expected inputs, correct outputs)
- Edge cases (empty inputs, max sizes, boundary conditions)
- Error cases (invalid inputs, missing dependencies, corrupted data)
- Performance cases (large inputs, stress testing, resource limits)

**Test case format** - save to `evals/evals.json`:
```json
{
  "skill_name": "zero-downtime-deploy",
  "evals": [
    {
      "id": 1,
      "name": "successful-deploy-with-health-check",
      "prompt": "Deploy version 2.1.0 to production cluster. Application: api-server, deployment: rolling update with health checks enabled. Current traffic: 10k req/s.",
      "expected_output": "Deployment completed with 0 dropped requests. Health checks passed. Old pods terminated after traffic drained.",
      "files": ["k8s-config.yaml", "health-check.sh"],
      "assertions": []
    },
    {
      "id": 2,
      "name": "rollback-on-health-check-failure",
      "prompt": "Deploy version 2.1.1 to production. Health check endpoint: /health. Expected: rollback automatically if health checks fail for 30s.",
      "expected_output": "Health checks failed after 15s. Initiated automatic rollback. Deployment reverted to version 2.1.0.",
      "files": ["k8s-config.yaml"],
      "assertions": []
    }
  ]
}
```

**Assertion strategy:**
- Use scripts over manual checks where possible
- Test exit codes, file existence, content validation
- Check performance metrics (time bounds, token usage)
- Verify error messages contain root cause information

See `references/schemas.md` for the complete schema.

## Running and evaluating test cases

This section is one continuous sequence — don't stop partway through. Do NOT use `/skill-test` or any other testing skill.

Put results in `<skill-name>-workspace/` as a sibling to the skill directory. Within the workspace, organize results by iteration (`iteration-1/`, `iteration-2/`, etc.) and within that, each test case gets a directory (`eval-0/`, `eval-1/`, etc.). Don't create all of this upfront — just create directories as you go.

### Step 1: Spawn all runs (with-skill AND baseline) in the same turn

For each test case, spawn two subagents in the same turn — one with the skill, one without. This is important: don't spawn the with-skill runs first and then come back for baselines later. Launch everything at once so it all finishes around the same time.

**With-skill run:**

```
Execute this task:
- Skill path: <path-to-skill>
- Task: <eval prompt>
- Input files: <eval files if any, or "none">
- Save outputs to: <workspace>/iteration-<N>/eval-<ID>/with_skill/outputs/
- Outputs to save: <what the user cares about — e.g., "the .docx file", "the final CSV">
```

**Baseline run** (same prompt, but the baseline depends on context):
- **Creating a new skill**: no skill at all. Same prompt, no skill path, save to `without_skill/outputs/`.
- **Improving an existing skill**: the old version. Before editing, snapshot the skill (`cp -r <skill-path> <workspace>/skill-snapshot/`), then point the baseline subagent at the snapshot. Save to `old_skill/outputs/`.

Write an `eval_metadata.json` for each test case (assertions can be empty for now). Give each eval a descriptive name based on what it's testing — not just "eval-0". Use this name for the directory too. If this iteration uses new or modified eval prompts, create these files for each new eval directory — don't assume they carry over from previous iterations.

```json
{
  "eval_id": 0,
  "eval_name": "descriptive-name-here",
  "prompt": "The user's task prompt",
  "assertions": []
}
```

### Step 2: Draft quantitative assertions (while runs execute)

Write programmatic assertions that can be automatically verified. Focus on:

**Output verification:**
```json
{
  "type": "file_exists",
  "path": "output/result.json",
  "description": "Output file created"
}
```

**Content validation:**
```json
{
  "type": "json_schema",
  "path": "output/result.json",
  "schema": "schemas/output.schema.json",
  "description": "Output matches expected schema"
}
```

**Performance bounds:**
```json
{
  "type": "max_duration",
  "threshold_seconds": 30,
  "description": "Completes within 30 seconds"
},
{
  "type": "max_tokens",
  "threshold": 50000,
  "description": "Uses less than 50k tokens"
}
```

**Behavioral checks:**
```json
{
  "type": "script",
  "script": "./scripts/verify_no_data_loss.sh",
  "expected_exit_code": 0,
  "description": "No data loss during transformation"
}
```

**Where possible, write verification scripts** instead of manual inspection. Scripts are:
- Faster to run across iterations
- More reliable (no human error)
- Reusable for regression testing
- Self-documenting (code shows exactly what's checked)

Update `eval_metadata.json` and `evals/evals.json` with assertions.

**Explain to the developer:**
- What each assertion validates
- How to run assertions manually (`./scripts/verify.sh eval-0/with_skill/outputs/`)
- Expected failure modes (what would cause each assertion to fail)
- Performance baselines (time/token expectations)

### Step 3: As runs complete, capture timing data

When each subagent task completes, you receive a notification containing `total_tokens` and `duration_ms`. Save this data immediately to `timing.json` in the run directory:

```json
{
  "total_tokens": 84852,
  "duration_ms": 23332,
  "total_duration_seconds": 23.3
}
```

This is the only opportunity to capture this data — it comes through the task notification and isn't persisted elsewhere. Process each notification as it arrives rather than trying to batch them.

### Step 4: Grade, aggregate, and show results

Once all runs complete:

1. **Automated grading** — For each assertion:
   ```bash
   # Script-based assertions
   ./scripts/verify_output.sh eval-0/with_skill/outputs/
   echo $? # 0 = pass, non-zero = fail

   # Schema validation
   jq empty output.json && echo "Valid JSON"
   jsonschema -i output.json schema.json

   # Performance checks
   jq '.duration_ms < 30000' timing.json
   ```

   Save results to `grading.json`. **CRITICAL:** Use exact field names `text`, `passed`, `evidence` (not `name`/`met`/`details`):
   ```json
   {
     "assertions": [
       {
         "text": "Output file exists",
         "passed": true,
         "evidence": "File found at output/result.json, size: 1.2MB"
       },
       {
         "text": "Schema validation passed",
         "passed": false,
         "evidence": "Missing required field 'timestamp'. Schema: output.schema.json"
       }
     ]
   }
   ```

2. **Benchmark aggregation**:
   ```bash
   python -m scripts.aggregate_benchmark <workspace>/iteration-N --skill-name <name>
   ```
   Produces `benchmark.json` + `benchmark.md` with pass rates, timing, token usage, and statistical significance.

3. **Technical analysis** — identify:
   - Assertions with 100% pass rate (possibly too weak)
   - Assertions with 0% pass rate (skill not working or assertion too strict)
   - High variance in performance (flaky tests or non-deterministic behavior)
   - Token usage patterns (where are tokens spent?)
   - Time bottlenecks (which steps are slow?)
   - Error patterns (common failure modes)

4. **Launch viewer**:
   ```bash
   nohup python <skill-creator>/eval-viewer/generate_review.py \
     <workspace>/iteration-N \
     --skill-name "dev-skill-name" \
     --benchmark benchmark.json \
     > /dev/null 2>&1 &
   VIEWER_PID=$!
   ```

**Tell the developer:**
```
Results at http://localhost:8000

**Outputs tab:** Review each test case output with:
- Generated files (rendered inline)
- Logs and diagnostic output
- Assertion results (expand "Formal Grades")
- Performance metrics (timing.json)

**Benchmark tab:**
- Pass rates by config (with_skill vs baseline)
- Time/token distributions (mean ± stddev)
- Per-eval breakdown
- Statistical analysis

**What to look for:**
- Unexpected failures (check logs for root cause)
- Performance regressions (compare timing across iterations)
- Missing assertions (edge cases not covered)
- Verification gaps (manual checks that should be automated)
```

### What the user sees in the viewer

The "Outputs" tab shows one test case at a time:
- **Prompt**: the task that was given
- **Output**: the files the skill produced, rendered inline where possible
- **Previous Output** (iteration 2+): collapsed section showing last iteration's output
- **Formal Grades** (if grading was run): collapsed section showing assertion pass/fail
- **Feedback**: a textbox that auto-saves as they type
- **Previous Feedback** (iteration 2+): their comments from last time, shown below the textbox

The "Benchmark" tab shows the stats summary: pass rates, timing, and token usage for each configuration, with per-eval breakdowns and analyst observations.

Navigation is via prev/next buttons or arrow keys. When done, they click "Submit All Reviews" which saves all feedback to `feedback.json`.

### Step 5: Read the feedback

When the user tells you they're done, read `feedback.json`:

```json
{
  "reviews": [
    {"run_id": "eval-0-with_skill", "feedback": "the chart is missing axis labels", "timestamp": "..."},
    {"run_id": "eval-1-with_skill", "feedback": "", "timestamp": "..."},
    {"run_id": "eval-2-with_skill", "feedback": "perfect, love this", "timestamp": "..."}
  ],
  "status": "complete"
}
```

Empty feedback means the user thought it was fine. Focus your improvements on the test cases where the user had specific complaints.

Kill the viewer server when you're done with it:

```bash
kill $VIEWER_PID 2>/dev/null
```

---

## Improving Dev-Skills

Use test results and developer feedback to refine the skill.

### Analysis Priorities

1. **Automated verification gaps** — Where are you relying on manual review that could be scripted?
   - Add verification scripts to `scripts/`
   - Convert visual checks to programmatic assertions
   - Make flaky tests deterministic

2. **Error handling** — Do error messages provide actionable information?
   - Include root cause, not just symptoms
   - Provide recovery suggestions
   - Show diagnostic commands
   - Log context (state at failure time)

3. **Performance bottlenecks** — Where is time/token budget spent?
   - Profile which steps are slow
   - Identify unnecessary work
   - Consider caching or streaming for large inputs
   - Bundle frequently-used scripts

4. **Developer control** — Where does the skill make decisions that developers should control?
   - Add configuration options
   - Expose override mechanisms
   - Provide escape hatches for edge cases
   - Document decision rationale

5. **Diagnostic visibility** — Can developers debug when things go wrong?
   - Show intermediate states
   - Log decision points
   - Surface validation results
   - Provide inspection commands

### Iteration Principles

**For developers:**
- Favor explicit over implicit
- Expose don't hide (show diagnostic info)
- Fail loud with context (better than silent failure)
- Optimize for debugging (rich error messages)
- Automate verification (scripts over human review)

**Bundle reusable code:** If test transcripts show subagents repeatedly writing similar helper scripts, bundle them:
```
scripts/
├── parse_config.py      - Used by all 3 test cases
├── validate_schema.sh   - Common validation logic
└── generate_report.py   - Standard output formatting
```

**Example: Before bundling**
```markdown
## Step 2: Parse configuration
Use Python to parse the YAML config file and extract the deployment parameters.
```
(Result: Each subagent writes their own parser, slightly different, bugs in each)

**After bundling:**
```markdown
## Step 2: Parse configuration
Run the bundled parser:
```bash
./scripts/parse_config.py config.yaml > parsed.json
```

The parser validates:
- Required fields present (name, version, replicas)
- Types correct (replicas is int, not string)
- Value ranges (replicas between 1-100)

Exits with:
- 0 if valid (parsed JSON to stdout)
- 1 if invalid (error details to stderr)
```
(Result: Consistent, tested, reusable parsing logic)

### Technical Test Case Examples

Dev-skills need comprehensive test coverage:

**Happy path:**
```json
{
  "id": 1,
  "name": "standard-deployment-success",
  "prompt": "Deploy api-server v2.1.0 to prod cluster with rolling update. 3 replicas, health check: /health returns 200. Expected: zero downtime.",
  "expected_output": "Deployment successful. 3/3 pods healthy. 0 requests dropped.",
  "files": ["k8s-deployment.yaml"],
  "assertions": [
    {"type": "file_exists", "path": "deployment-log.json"},
    {"type": "script", "script": "./scripts/verify_zero_downtime.sh"},
    {"type": "max_duration", "threshold_seconds": 300}
  ]
}
```

**Edge case:**
```json
{
  "id": 2,
  "name": "deployment-with-max-replicas",
  "prompt": "Deploy with 100 replicas (cluster max). Test resource allocation and pod scheduling under load.",
  "expected_output": "All 100 pods scheduled successfully. No resource quota exceeded. Deployment complete.",
  "files": ["k8s-deployment-large.yaml"],
  "assertions": [
    {"type": "script", "script": "./scripts/verify_all_pods_running.sh", "args": ["100"]},
    {"type": "json_field", "path": "result.json", "field": "successful_pods", "expected": 100}
  ]
}
```

**Error case:**
```json
{
  "id": 3,
  "name": "deployment-fails-health-check",
  "prompt": "Deploy v2.2.0. Health check will fail (returns 500). Expected: automatic rollback to v2.1.0, no prod impact.",
  "expected_output": "Health check failed after 30s. Rolled back to v2.1.0. Production traffic unaffected.",
  "files": ["k8s-deployment-broken.yaml"],
  "assertions": [
    {"type": "script", "script": "./scripts/verify_rollback_occurred.sh"},
    {"type": "log_contains", "path": "deployment-log.json", "pattern": "Rollback initiated"},
    {"type": "max_dropped_requests", "threshold": 0}
  ]
}
```

**Performance:**
```json
{
  "id": 4,
  "name": "deployment-performance-baseline",
  "prompt": "Deploy to 50-node cluster. Measure total deployment time and resource usage.",
  "expected_output": "Deployment time recorded. Resource metrics collected.",
  "files": ["k8s-deployment.yaml"],
  "assertions": [
    {"type": "max_duration", "threshold_seconds": 600},
    {"type": "max_tokens", "threshold": 100000},
    {"type": "script", "script": "./scripts/check_resource_usage.sh", "expected_exit_code": 0}
  ]
}
```

---

## Verification-First Development

Dev-skills must be verifiable. Developers need confidence that the skill works correctly.

### Verification Strategy Hierarchy

1. **Automated scripts** (best) — deterministic, fast, reusable
   ```bash
   ./scripts/verify_output.sh <output_dir>
   exit $?  # 0 = pass, non-zero = specific failure mode
   ```

2. **Programmatic assertions** — JSON schema, file checks, performance bounds
   ```json
   {"type": "json_schema", "path": "output.json", "schema": "schema.json"}
   ```

3. **Manual review** (last resort) — only for truly subjective evaluation

### Building Verification Scripts

**Requirements:**
- Clear exit codes (0 = success, specific codes for different failures)
- Structured output (JSON for machine parsing)
- Diagnostic info (why did it pass/fail?)
- Idempotent (safe to run multiple times)

**Example:**
```bash
#!/usr/bin/env bash
# verify_deployment.sh - Verify zero-downtime deployment succeeded

set -euo pipefail

OUTPUT_DIR="$1"
DEPLOYMENT_LOG="$OUTPUT_DIR/deployment.json"

# Check required files exist
if [[ ! -f "$DEPLOYMENT_LOG" ]]; then
  echo "ERROR: deployment log not found" >&2
  exit 1
fi

# Parse and verify metrics
DROPPED_REQUESTS=$(jq -r '.metrics.dropped_requests' "$DEPLOYMENT_LOG")
if [[ "$DROPPED_REQUESTS" != "0" ]]; then
  echo "FAIL: $DROPPED_REQUESTS requests dropped during deployment" >&2
  exit 2
fi

PODS_HEALTHY=$(jq -r '.status.healthy_pods' "$DEPLOYMENT_LOG")
PODS_EXPECTED=$(jq -r '.config.replicas' "$DEPLOYMENT_LOG")
if [[ "$PODS_HEALTHY" != "$PODS_EXPECTED" ]]; then
  echo "FAIL: Only $PODS_HEALTHY/$PODS_EXPECTED pods healthy" >&2
  exit 3
fi

# All checks passed
echo "PASS: Zero-downtime deployment verified"
echo "{\"passed\": true, \"healthy_pods\": $PODS_HEALTHY, \"dropped_requests\": $DROPPED_REQUESTS}"
exit 0
```

### Performance Verification

Track and assert on performance characteristics:

```json
{
  "timing": {
    "total_duration_ms": 15234,
    "breakdown": {
      "parse_config": 120,
      "validate": 450,
      "deploy": 14500,
      "verify": 164
    }
  },
  "tokens": {
    "total": 45230,
    "by_phase": {
      "planning": 8500,
      "execution": 32000,
      "verification": 4730
    }
  },
  "resources": {
    "peak_memory_mb": 256,
    "api_calls": 12,
    "file_operations": 45
  }
}
```

**Assert on:**
- Total duration < threshold (detect regressions)
- Token usage < budget (control costs)
- Resource consumption (memory, API calls, file I/O)
- Breakdown by phase (identify bottlenecks)

### Error Documentation

Dev-skills must document error patterns comprehensively:

**Error catalog structure:**
```markdown
## Error: CrashLoopBackOff during deployment

**Symptom:**
```
kubectl get pods
NAME                    READY   STATUS             RESTARTS
api-server-abc123       0/1     CrashLoopBackOff   5
```

**Root cause:** Health check endpoint returns 503 during 60s warmup period. Kubernetes marks pod as unhealthy and restarts it before warmup completes.

**Solution:**
Increase `initialDelaySeconds` in health check probe:
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 90  # Was: 30
  periodSeconds: 10
```

**Verification:**
```bash
kubectl logs api-server-abc123 | grep "Server started"
# Should see startup complete before first health check
```

**Prevention:**
- Set `initialDelaySeconds` > application warmup time
- Use readiness probe separate from liveness probe
- Monitor warmup duration in metrics
```

**Include for each error:**
- Exact symptom (command output, log pattern, metrics)
- Root cause (technical explanation)
- Solution (precise fix with code/config)
- Verification command (how to confirm fix worked)
- Prevention strategies (how to avoid in future)

---

---

## Advanced: Blind comparison

For situations where you want a more rigorous comparison between two versions of a skill (e.g., the user asks "is the new version actually better?"), there's a blind comparison system. Read `agents/comparator.md` and `agents/analyzer.md` for the details. The basic idea is: give two outputs to an independent agent without telling it which is which, and let it judge quality. Then analyze why the winner won.

This is optional, requires subagents, and most users won't need it. The human review loop is usually sufficient.

---

## Description Optimization

The description field in SKILL.md frontmatter is the primary mechanism that determines whether Claude invokes a skill. After creating or improving a skill, offer to optimize the description for better triggering accuracy.

### Step 1: Generate trigger evaluation queries

Create 20 eval queries testing trigger accuracy:

**Should-trigger queries (8-10):** Realistic development scenarios with technical context
```json
[
  {
    "query": "our k8s deployment keeps timing out during rollouts. pods show CrashLoopBackOff after ~30s, health check endpoint is /api/health but it returns 503 for the first minute while the app warms up. how do i configure the deployment to wait longer before marking pods as failed?",
    "should_trigger": true
  },
  {
    "query": "the CI pipeline failed with 'Error: ENOENT: no such file or directory, open /app/dist/bundle.js' but locally `npm run build` works fine. dockerfile is:\nFROM node:18\nCOPY . /app\nRUN npm ci\nRUN npm run build\nCMD [\"npm\", \"start\"]\nwhat's wrong?",
    "should_trigger": true
  }
]
```

**Should-not-trigger queries (8-10):** Near-miss scenarios that share keywords but need different skills
```json
[
  {
    "query": "my k8s cluster monitoring shows high CPU but when i check the pods they're all under 50%. grafana dashboard: https://grafana.internal/d/cluster-metrics. what's using the CPU?",
    "should_trigger": false
  },
  {
    "query": "need to write a python script that deploys to k8s using the client library. should use apply() not create() so it's idempotent. any examples of this pattern?",
    "should_trigger": false
  }
]
```

**Requirements:**
- Concrete technical details (tool names, error messages, file paths, commands)
- Real stack traces, log snippets, or configuration fragments
- Specific version numbers, environment details, or system constraints
- Typos, abbreviations, casual phrasing (mirrors actual developer queries)
- Mix of lengths (50-300 words)

**Focus on edge cases:**
- Ambiguous phrasing where keywords match but intent differs
- Related domains (monitoring vs deployment vs debugging)
- Overlapping concerns where multiple skills could apply
- Technical terms with multiple meanings (e.g., "deploy" = CI/CD vs infrastructure)

### Step 2: Review with user

Present the eval set to the user for review using the HTML template:

1. Read the template from `assets/eval_review.html`
2. Replace the placeholders:
   - `__EVAL_DATA_PLACEHOLDER__` → the JSON array of eval items (no quotes around it — it's a JS variable assignment)
   - `__SKILL_NAME_PLACEHOLDER__` → the skill's name
   - `__SKILL_DESCRIPTION_PLACEHOLDER__` → the skill's current description
3. Write to a temp file (e.g., `/tmp/eval_review_<skill-name>.html`) and open it: `open /tmp/eval_review_<skill-name>.html`
4. The user can edit queries, toggle should-trigger, add/remove entries, then click "Export Eval Set"
5. The file downloads to `~/Downloads/eval_set.json` — check the Downloads folder for the most recent version in case there are multiple (e.g., `eval_set (1).json`)

This step matters — bad eval queries lead to bad descriptions.

### Step 3: Run the optimization loop

Tell the user: "This will take some time — I'll run the optimization loop in the background and check on it periodically."

Save the eval set to the workspace, then run in the background:

```bash
python -m scripts.run_loop \
  --eval-set <path-to-trigger-eval.json> \
  --skill-path <path-to-skill> \
  --model <model-id-powering-this-session> \
  --max-iterations 5 \
  --verbose
```

Use the model ID from your system prompt (the one powering the current session) so the triggering test matches what the user actually experiences.

While it runs, periodically tail the output to give the user updates on which iteration it's on and what the scores look like.

This handles the full optimization loop automatically. It splits the eval set into 60% train and 40% held-out test, evaluates the current description (running each query 3 times to get a reliable trigger rate), then calls Claude to propose improvements based on what failed. It re-evaluates each new description on both train and test, iterating up to 5 times. When it's done, it opens an HTML report in the browser showing the results per iteration and returns JSON with `best_description` — selected by test score rather than train score to avoid overfitting.

### How skill triggering works

Understanding the triggering mechanism helps design better eval queries. Skills appear in Claude's `available_skills` list with their name + description, and Claude decides whether to consult a skill based on that description. The important thing to know is that Claude only consults skills for tasks it can't easily handle on its own — simple, one-step queries like "read this PDF" may not trigger a skill even if the description matches perfectly, because Claude can handle them directly with basic tools. Complex, multi-step, or specialized queries reliably trigger skills when the description matches.

This means your eval queries should be substantive enough that Claude would actually benefit from consulting a skill. Simple queries like "read file X" are poor test cases — they won't trigger skills regardless of description quality.

### Step 4: Apply the result

Take `best_description` from the JSON output and update the skill's SKILL.md frontmatter. Show the user before/after and report the scores.

---

### Package and Present (only if `present_files` tool is available)

Check whether you have access to the `present_files` tool. If you don't, skip this step. If you do, package the skill and present the .skill file to the user:

```bash
python -m scripts.package_skill <path/to/skill-folder>
```

After packaging, direct the user to the resulting `.skill` file path so they can install it.

---

## Claude.ai-specific instructions

In Claude.ai, the core workflow is the same (draft → test → review → improve → repeat), but because Claude.ai doesn't have subagents, some mechanics change. Here's what to adapt:

**Running test cases**: No subagents means no parallel execution. For each test case, read the skill's SKILL.md, then follow its instructions to accomplish the test prompt yourself. Do them one at a time. This is less rigorous than independent subagents (you wrote the skill and you're also running it, so you have full context), but it's a useful sanity check — and the human review step compensates. Skip the baseline runs — just use the skill to complete the task as requested.

**Reviewing results**: If you can't open a browser (e.g., Claude.ai's VM has no display, or you're on a remote server), skip the browser reviewer entirely. Instead, present results directly in the conversation. For each test case, show the prompt and the output. If the output is a file the user needs to see (like a .docx or .xlsx), save it to the filesystem and tell them where it is so they can download and inspect it. Ask for feedback inline: "How does this look? Anything you'd change?"

**Benchmarking**: Skip the quantitative benchmarking — it relies on baseline comparisons which aren't meaningful without subagents. Focus on qualitative feedback from the user.

**The iteration loop**: Same as before — improve the skill, rerun the test cases, ask for feedback — just without the browser reviewer in the middle. You can still organize results into iteration directories on the filesystem if you have one.

**Description optimization**: This section requires the `claude` CLI tool (specifically `claude -p`) which is only available in Claude Code. Skip it if you're on Claude.ai.

**Blind comparison**: Requires subagents. Skip it.

**Packaging**: The `package_skill.py` script works anywhere with Python and a filesystem. On Claude.ai, you can run it and the user can download the resulting `.skill` file.

**Updating an existing skill**: The user might be asking you to update an existing skill, not create a new one. In this case:
- **Preserve the original name.** Note the skill's directory name and `name` frontmatter field -- use them unchanged. E.g., if the installed skill is `research-helper`, output `research-helper.skill` (not `research-helper-v2`).
- **Copy to a writeable location before editing.** The installed skill path may be read-only. Copy to `/tmp/skill-name/`, edit there, and package from the copy.
- **If packaging manually, stage in `/tmp/` first**, then copy to the output directory -- direct writes may fail due to permissions.

---

## Cowork-Specific Instructions

If you're in Cowork, the main things to know are:

- You have subagents, so the main workflow (spawn test cases in parallel, run baselines, grade, etc.) all works. (However, if you run into severe problems with timeouts, it's OK to run the test prompts in series rather than parallel.)
- You don't have a browser or display, so when generating the eval viewer, use `--static <output_path>` to write a standalone HTML file instead of starting a server. Then proffer a link that the user can click to open the HTML in their browser.
- For whatever reason, the Cowork setup seems to disincline Claude from generating the eval viewer after running the tests, so just to reiterate: whether you're in Cowork or in Claude Code, after running tests, you should always generate the eval viewer for the human to look at examples before revising the skill yourself and trying to make corrections, using `generate_review.py` (not writing your own boutique html code). Sorry in advance but I'm gonna go all caps here: GENERATE THE EVAL VIEWER *BEFORE* evaluating inputs yourself. You want to get them in front of the human ASAP!
- Feedback works differently: since there's no running server, the viewer's "Submit All Reviews" button will download `feedback.json` as a file. You can then read it from there (you may have to request access first).
- Packaging works — `package_skill.py` just needs Python and a filesystem.
- Description optimization (`run_loop.py` / `run_eval.py`) should work in Cowork just fine since it uses `claude -p` via subprocess, not a browser, but please save it until you've fully finished making the skill and the user agrees it's in good shape.
- **Updating an existing skill**: The user might be asking you to update an existing skill, not create a new one. Follow the update guidance in the claude.ai section above.

---

## Reference files

The agents/ directory contains instructions for specialized subagents. Read them when you need to spawn the relevant subagent.

- `agents/grader.md` — How to evaluate assertions against outputs
- `agents/comparator.md` — How to do blind A/B comparison between two outputs
- `agents/analyzer.md` — How to analyze why one version beat another

The references/ directory has additional documentation:
- `references/schemas.md` — JSON structures for evals.json, grading.json, etc.

---

Repeating one more time the core loop here for emphasis:

- Figure out what the skill is about
- Draft or edit the skill
- Run claude-with-access-to-the-skill on test prompts
- With the user, evaluate the outputs:
  - Create benchmark.json and run `eval-viewer/generate_review.py` to help the user review them
  - Run quantitative evals
- Repeat until you and the user are satisfied
- Package the final skill and return it to the user.

Please add steps to your TodoList, if you have such a thing, to make sure you don't forget. If you're in Cowork, please specifically put "Create evals JSON and run `eval-viewer/generate_review.py` so human can review test cases" in your TodoList to make sure it happens.

Good luck!
