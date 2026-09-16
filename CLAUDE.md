# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a GitHub Action template repository for creating composite actions. The project demonstrates a reusable composite workflow that interacts with the GitHub API and includes automated release management using semantic-release.

## Architecture

### Core Structure
- **`action.yaml`**: Defines the composite GitHub Action's interface (inputs, outputs, and steps). This is the entry point for the action's behavior.
- **`.github/workflows/`**: Contains CI/CD workflows:
  - `release.yaml`: Triggers semantic-release on pushes to main, creating tags and releases
  - `create-branch.yaml`: Example workflow that demonstrates using this action
- **`scripts/plugins/`**: Custom semantic-release plugins that extend the release process:
  - `analyze-commits.js`: Analyzes commit messages for versioning
  - `verify-conditions.js`: Pre-release validation
  - `prepare.js`: Pre-release preparation steps
  - `generate-notes.js`: Generates release notes
  - `publish.js`: Publishes release artifacts
  - `release.config.js`: Main semantic-release configuration loader

### Release Process
The project uses semantic-release with a plugin-based architecture. Key files:
- `.releaserc.json`: Semantic-release configuration (branches, plugins, and assets)
- `package.json`: Contains dev dependencies for semantic-release and commitizen
- `CHANGELOG.md`: Auto-generated changelog by semantic-release

## Common Commands

```bash
# Install dependencies
npm install

# Create a release (analyzes commits and creates versioned release)
npm run release

# Lint commits (using Commitizen)
npx cz

# View available npm scripts
npm run
```

## Development Workflow

1. **Creating Commits**: Use conventional commit format (enforced by Commitizen) for automated version bumping:
   - `feat: ...` → minor version bump
   - `fix: ...` → patch version bump
   - `BREAKING CHANGE: ...` → major version bump

2. **Releasing**: Pushes to `main` automatically trigger semantic-release workflow, which:
   - Analyzes commit history since last release
   - Determines version bump based on commits
   - Updates CHANGELOG.md
   - Creates a git tag and GitHub release
   - Uses custom plugins to extend this process

3. **Action Development**: Modify `action.yaml` to change the action's interface or steps. The action currently:
   - Sets up Python
   - Posts comments on GitHub issues using the GitHub API
   - Takes inputs for customization via `inputs` section

## Key Configuration Points

- **Node.js Version**: `22.14.0` (specified in release.yaml workflow)
- **Python Version**: `3.x` (in action.yaml)
- **Deployment**: Semantic-release publishes to GitHub (releases, tags, changelog)
- **Branching**: Only `main` branch triggers releases (.releaserc.json)

## GitHub Workflows Context

- **Release Workflow**: Requires GitHub token with `contents:write`, `issues:write`, `pull-requests:write` permissions
- **Branch Creation Workflow**: Example usage showing how to call this action in another workflow (uses another action `create-branch-action`)
- **Dependabot**: Configured for automated dependency updates (.github/dependabot.yaml)

## Testing & Validation

Currently, no automated tests are configured. To add tests:
- Add test script to package.json
- Tests should validate:
  - Commit analysis (version bumping logic)
  - GitHub API interaction in action.yaml
  - Custom semantic-release plugins

## Notes for Future Changes

- When modifying the action, remember it's a **composite action** (not a Docker action or JavaScript action)—updates go in `action.yaml`
- The custom semantic-release plugins in `scripts/plugins/` extend the standard release flow; changes here affect version determination and release process
- The action is published via GitHub releases; versions are automatically managed by semantic-release based on commit messages
- Any changes to action inputs/outputs need to be reflected in both `action.yaml` and `README.md` (Inputs table)
