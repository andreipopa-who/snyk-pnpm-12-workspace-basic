# 12-workspace-basic

**Feature:** pnpm workspaces support with `pnpm-workspace.yaml`

## Structure

```
12-workspace-basic/
├── package.json            # Root package
├── pnpm-workspace.yaml     # Workspace config
├── pnpm-lock.yaml          # Generated - single lockfile for all packages
└── packages/
    ├── package-a/
    │   └── package.json    # @test/package-a with lodash
    └── package-b/
        └── package.json    # @test/package-b with express, minimist (vulnerable)
```

## Setup

```bash
# Generate lockfile for entire workspace
npx pnpm@9 install
```

## Snyk CLI Tests

### Scan All Workspace Projects

```bash
# Scan all projects in workspace
snyk test --all-projects --org=$SNYK_ORG

# With detection depth
snyk test --all-projects --detection-depth=3 --org=$SNYK_ORG

# Allow out-of-sync lockfiles (useful during development)
snyk test --all-projects --strict-out-of-sync=false --org=$SNYK_ORG
```

### Monitor All Workspace Projects

```bash
snyk monitor --all-projects --org=$SNYK_ORG
```

### Scan Individual Package

```bash
cd packages/package-b
snyk test --org=$SNYK_ORG
```

## Expected Results

- `@test/package-a`: No vulnerabilities expected
- `@test/package-b`: Should detect minimist@1.2.5 vulnerability

## SCM Import Behavior

When imported via SCM:

- Root lockfile should be used for all workspace packages
- Each package may appear as separate project
- **Known Limitation:** Fix PRs only update `package.json`, NOT workspace lockfile

## Verification Checklist

### CLI

- [ ] `--all-projects` discovers both packages
- [ ] Single lockfile is used for resolution
- [ ] Vulnerabilities in package-b are detected
- [ ] `--detection-depth` works correctly

### SCM

- [ ] Workspace is imported successfully
- [ ] Both packages appear in dashboard
- [ ] Correct dependencies per package
