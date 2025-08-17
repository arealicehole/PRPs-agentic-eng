# Working with Existing Projects in the PRP Framework

## Overview

The PRP (Product Requirement Prompt) Framework can be used with existing codebases in three different ways. This guide explains each approach with examples, pros/cons, and when to use each.

## The Three Approaches

### Approach 1: Nested Project (Simplest)
**Setup**: Place your existing project as a subdirectory within the PRP framework.

```
PRPs-agentic-eng/                    # PRP Framework root
├── .claude/                         # Claude commands available here
│   └── commands/
├── PRPs/                           # PRP storage
│   ├── templates/
│   └── your-project/              # Project-specific PRPs
├── your-existing-project/         # YOUR PROJECT HERE
│   ├── src/
│   ├── package.json
│   └── ...
└── CLAUDE.md                      # Framework instructions
```

#### How to Use:
```bash
# Always work from PRP root
cd ~/PRPs-agentic-eng

# Commands available immediately
/prime-core                        # Loads framework context
/prp-task-create "Fix your-existing-project bug"

# Reference nested files in PRPs
# Path: your-existing-project/src/index.js
```

#### Pros:
- ✅ Zero configuration needed
- ✅ All PRP commands immediately available
- ✅ Can work on multiple projects
- ✅ Framework updates automatic

#### Cons:
- ❌ Paths are always relative to framework root
- ❌ Git management more complex (nested repos)
- ❌ May include unnecessary framework files

#### Best For:
- Quick experiments
- Learning the PRP framework
- Multiple small projects
- Temporary analysis work

---

### Approach 2: Framework Import (Balanced)
**Setup**: Copy essential PRP framework files into your existing project.

```
your-existing-project/              # YOUR PROJECT ROOT
├── .claude/                        # COPIED from framework
│   └── commands/
├── PRPs/                          # COPIED structure
│   ├── templates/                # COPIED templates
│   └── features/                 # Your project PRPs
├── src/                          # Your existing code
├── package.json                  # Your existing config
└── CLAUDE.md                     # MERGED instructions
```

#### How to Copy Framework:
```bash
# From your project root
cd ~/your-existing-project

# Copy essential framework files
cp -r ~/PRPs-agentic-eng/.claude .
cp -r ~/PRPs-agentic-eng/PRPs/templates PRPs/templates
cp ~/PRPs-agentic-eng/PRPs/README.md PRPs/

# Merge CLAUDE.md files
cat ~/PRPs-agentic-eng/CLAUDE.md >> CLAUDE.md
echo "\n# Project-Specific Instructions\n" >> CLAUDE.md
echo "Project root: ./" >> CLAUDE.md
```

#### How to Use:
```bash
# Work from your project root
cd ~/your-existing-project

# Commands work on your project
/prime-core                        # Loads YOUR project context
/prp-base-create "New feature"

# Natural file paths
# Path: src/index.js (not nested!)
```

#### Pros:
- ✅ Natural file paths
- ✅ Single Git repository
- ✅ Project-specific customization
- ✅ Portable (framework travels with project)

#### Cons:
- ❌ Manual framework updates
- ❌ Initial setup required
- ❌ Potential file conflicts

#### Best For:
- Production projects
- Team collaboration
- Long-term development
- CI/CD integration

---

### Approach 3: Symlink Strategy (Advanced)
**Setup**: Keep framework and project separate, connect via symlinks.

```
~/development/
├── PRPs-agentic-eng/              # PRP Framework
│   ├── .claude/
│   ├── PRPs/
│   └── CLAUDE.md
└── your-project/                  # Your actual project

# Create bidirectional symlinks
PRPs-agentic-eng/
├── project -> ~/development/your-project
└── ...

your-project/
├── .claude -> ~/development/PRPs-agentic-eng/.claude
├── PRPs -> ~/development/PRPs-agentic-eng/PRPs
└── ...
```

#### How to Setup Symlinks:
```bash
# Setup from framework side
cd ~/PRPs-agentic-eng
ln -s ~/development/your-project project

# Setup from project side
cd ~/development/your-project
ln -s ~/PRPs-agentic-eng/.claude .claude
ln -s ~/PRPs-agentic-eng/PRPs PRPs

# Create project-specific PRP directory
mkdir ~/PRPs-agentic-eng/PRPs/your-project
```

#### How to Use:
```bash
# Can work from either location

# From framework:
cd ~/PRPs-agentic-eng
/prime-core
# Reference: project/src/index.js

# From project:
cd ~/development/your-project
/prime-core
# Reference: src/index.js
```

#### Pros:
- ✅ Clean separation of concerns
- ✅ Framework updates automatic
- ✅ Multiple projects easy
- ✅ Flexible working directory

#### Cons:
- ❌ Complex setup
- ❌ Symlinks can break
- ❌ OS-specific issues possible
- ❌ IDE confusion with symlinks

#### Best For:
- Multiple active projects
- Framework development
- Advanced users
- Monorepo structures

---

## Decision Matrix

| Criteria | Nested | Import | Symlink |
|----------|--------|--------|---------|
| Setup Speed | ⭐⭐⭐ | ⭐⭐ | ⭐ |
| Path Simplicity | ⭐ | ⭐⭐⭐ | ⭐⭐ |
| Git Management | ⭐ | ⭐⭐⭐ | ⭐⭐ |
| Framework Updates | ⭐⭐⭐ | ⭐ | ⭐⭐⭐ |
| Team Friendly | ⭐⭐ | ⭐⭐⭐ | ⭐ |
| Multiple Projects | ⭐⭐⭐ | ⭐ | ⭐⭐⭐ |

---

## Common Patterns

### Creating Project-Specific PRPs

Regardless of approach, organize PRPs by project:

```
PRPs/
├── templates/           # Framework templates (don't modify)
├── project-name/       # Your project PRPs
│   ├── features/      # Feature PRPs
│   ├── refactors/     # Refactoring PRPs
│   └── tasks/         # Task PRPs
└── shared/            # Cross-project PRPs
```

### Path References in PRPs

#### Nested Approach:
```yaml
# In your PRP
Files to modify:
  - your-project/src/components/Header.tsx
  - your-project/api/routes.js
```

#### Import Approach:
```yaml
# In your PRP
Files to modify:
  - src/components/Header.tsx
  - api/routes.js
```

#### Symlink Approach:
```yaml
# In your PRP (from framework root)
Files to modify:
  - project/src/components/Header.tsx
  - project/api/routes.js

# OR (from project root)
Files to modify:
  - src/components/Header.tsx
  - api/routes.js
```

### CLAUDE.md Customization

Always add project-specific context:

```markdown
# Project-Specific Context

## Working Directory
- Nested: Use `your-project/` prefix for all paths
- Import: Direct paths from project root
- Symlink: Use `project/` when from framework root

## Key Files
- Entry: src/index.js
- Config: webpack.config.js
- Tests: tests/**/*.test.js

## Conventions
- Style: ESLint + Prettier
- Testing: Jest + React Testing Library
- Git: Conventional commits
```

---

## Migration Paths

### From Nested to Import
```bash
# 1. Copy framework files to project
cp -r ~/PRPs-agentic-eng/.claude ~/your-project/
cp -r ~/PRPs-agentic-eng/PRPs ~/your-project/

# 2. Update paths in existing PRPs
find PRPs -name "*.md" -exec sed -i 's|your-project/||g' {} \;

# 3. Move project to new location
mv ~/PRPs-agentic-eng/your-project ~/development/

# 4. Update CLAUDE.md
echo "Project is now standalone with embedded PRP framework" >> CLAUDE.md
```

### From Import to Symlink
```bash
# 1. Move framework files back
mv .claude ~/PRPs-agentic-eng/
mv PRPs ~/PRPs-agentic-eng/

# 2. Create symlinks
ln -s ~/PRPs-agentic-eng/.claude .claude
ln -s ~/PRPs-agentic-eng/PRPs PRPs

# 3. Update CLAUDE.md
echo "Using symlinked PRP framework" >> CLAUDE.md
```

---

## Troubleshooting

### Issue: Commands not found
**Solution**: Ensure you're in a directory with `.claude/commands/`

### Issue: Paths not resolving
**Solution**: Check your approach and adjust path prefixes

### Issue: Git conflicts with nested repos
**Solution**: Add inner repo to `.gitignore` or use symlinks

### Issue: Framework updates needed
**Solution**: 
- Nested/Symlink: Pull framework repo
- Import: Manually copy updated files

---

## Best Practices

1. **Choose based on project lifecycle**:
   - Experimentation → Nested
   - Production → Import
   - Multi-project → Symlink

2. **Document your approach**:
   ```markdown
   # In project README
   This project uses the PRP Framework (Import approach)
   See PRPs/guides/working-with-existing-projects.md
   ```

3. **Maintain PRP organization**:
   - One PRP per feature/task
   - Clear naming: `feature-name.md`
   - Include validation gates

4. **Version control strategy**:
   - Nested: Consider submodules
   - Import: Single repo
   - Symlink: Separate repos

5. **Team onboarding**:
   - Document chosen approach
   - Include setup scripts
   - Provide examples

---

## Quick Start Templates

### Nested Project Setup
```bash
#!/bin/bash
git clone https://github.com/yourorg/PRPs-agentic-eng.git
cd PRPs-agentic-eng
git clone https://github.com/yourorg/your-project.git
echo "your-project/" >> .gitignore
```

### Import Setup Script
```bash
#!/bin/bash
curl -L https://github.com/yourorg/PRPs-agentic-eng/archive/main.tar.gz | tar xz
mv PRPs-agentic-eng-main/.claude .
mv PRPs-agentic-eng-main/PRPs .
rm -rf PRPs-agentic-eng-main
```

### Symlink Setup Script
```bash
#!/bin/bash
FRAMEWORK_PATH="$HOME/PRPs-agentic-eng"
ln -s "$FRAMEWORK_PATH/.claude" .claude
ln -s "$FRAMEWORK_PATH/PRPs" PRPs
mkdir -p "$FRAMEWORK_PATH/PRPs/$(basename $PWD)"
```

---

## Conclusion

Choose your approach based on:
- **Nested**: Quick start, learning, experiments
- **Import**: Production, teams, long-term
- **Symlink**: Power users, multiple projects

The PRP framework adapts to your needs - start simple and evolve as required.
