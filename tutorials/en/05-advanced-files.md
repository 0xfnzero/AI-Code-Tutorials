# Lesson 5: Advanced File Operations

> 🎯 **Learning Objectives**: Master complex file operations, cross-file refactoring, and batch processing techniques

## 1. Complex File Structure Management

### Create Complete Project Structure

```
You: Create a standard front-end project structure

Claude will create:
my-app/
├── src/
│   ├── components/
│   │   ├── Header.js
│   │   ├── Footer.js
│   │   └── Sidebar.js
│   ├── styles/
│   │   ├── main.css
│   │   ├── variables.css
│   │   └── components/
│   ├── utils/
│   │   ├── helpers.js
│   │   └── api.js
│   └── index.js
├── public/
│   ├── index.html
│   └── assets/
├── tests/
├── .gitignore
├── README.md
└── package.json
```

### Project Template Management

```
You: Save current project structure as template "webapp-template"

Later use:
You: Create new project based on "webapp-template"
```

## 2. Cross-file Search and Replace

### Global Search

```
You: Search for "API_KEY" in all files

Claude: Found 3 uses:
- src/utils/api.js:15
- src/config.js:8
- tests/api.test.js:22
```

### Cross-file Replace

```
You: Replace "oldFunction" with "newFunction" in all files

Claude:
✅ src/utils/helpers.js - 3 occurrences
✅ src/components/Header.js - 1 occurrence
✅ src/index.js - 2 occurrences
Replaced 6 occurrences total
```

### Regular Expression Search

```
You: Find all function calls starting with "fetch"

You: Find all console.log statements

You: Search for all TODO and FIXME comments
```

## 3. Code Refactoring

### Extract Components

```
You: Extract the navigation bar in index.html as a separate component

Claude:
1. Create components/Navbar.js
2. Extract navigation bar code
3. Update index.html reference
4. Keep functionality unchanged
```

### Extract Functions

```
You: Extract this repeated code as a function

Claude:
Created utils/formatDate.js
And updated all places using this logic:
- src/components/Post.js
- src/components/Comment.js
- src/utils/display.js
```

### Modular Refactoring

```
You: Split app.js into multiple modules

Claude:
Split into:
- modules/auth.js - Authentication related
- modules/ui.js - UI operations
- modules/data.js - Data processing
- app.js - Main entry
```

## 4. File Import/Export Management

### Analyze Dependencies

```
You: Show which files index.js depends on

Claude:
index.js dependency tree:
├── utils/helpers.js
├── components/Header.js
│   └── styles/header.css
├── components/Footer.js
└── utils/api.js
    └── config.js
```

### Update Import Paths

```
You: Change all relative paths to absolute paths (using @ alias)

Before: import Header from '../components/Header'
After: import Header from '@/components/Header'
```

### Clean Unused Imports

```
You: Find and delete all unused import statements

Claude:
Cleaned redundant imports in 5 files:
- src/index.js: removed 2
- src/utils/helpers.js: removed 1
...
```

## 5. Batch File Operations

### Batch Create

```
You: Create 5 page components: Home, About, Contact, Services, Blog
Each should have corresponding JS and CSS files

Claude:
Created:
components/
├── Home/
│   ├── Home.js
│   └── Home.css
├── About/
│   ├── About.js
│   └── About.css
...
```

### Batch Rename

```
You: Rename all .jsx files to .js

Claude:
Renamed 8 files:
Header.jsx → Header.js
Footer.jsx → Footer.js
...
```

### Batch Add Content

```
You: Add copyright notice to the top of all JavaScript files

Claude:
Added to 12 files:
/**
 * Copyright (c) 2024 Your Company
 * All rights reserved.
 */
```

## 6. Configuration File Management

### Create Configuration Files

```
You: Create project configuration file config.js, including:
- API address
- Environment variables
- Application settings

Claude:
Created src/config.js with reasonable default values
```

### Environment Configuration

```
You: Create configuration files for development and production environments

Claude:
Created:
.env.development
.env.production
config/dev.config.js
config/prod.config.js
```

### Configuration File Templates

```
You: Generate a .env.example file

Claude:
Created .env.example:
API_URL=https://api.example.com
API_KEY=your_api_key_here
DEBUG=false
```

## 7. Code Quality Tools

### ESLint Configuration

```
You: Configure ESLint using Airbnb standards

Claude:
1. Create .eslintrc.json
2. Install necessary packages
3. Add scripts to package.json
```

### Prettier Configuration

```
You: Add Prettier code formatting

Claude:
1. Create .prettierrc
2. Create .prettierignore
3. Configure recommended settings
```

### Code Checking

```
You: Check code quality issues in the project

Claude:
Found 5 issues:
1. src/utils/api.js:25 - Unused variable
2. src/components/Header.js:10 - Missing key property
...
```

## 8. File Monitoring and Automation

### Watch File Changes

```
You: When style.css changes, automatically refresh browser

Claude:
Set up live-server to watch CSS files
```

### Automated Tasks

```
You: Create a script that automatically on save:
1. Format code
2. Run lint check
3. Run tests

Claude:
Created scripts/pre-commit.sh
And configured git hook
```

## 9. File Backup and Versioning

### Create Backup

```
You: Backup current project state

Claude:
Created backup: backup-2024-10-08/
```

### Version Tagging

```
You: Tag current version as v1.0.0

Claude:
✅ Updated package.json version: "1.0.0"
✅ Created git tag v1.0.0
✅ Updated CHANGELOG.md
```

### Compare Versions

```
You: Compare differences between current version and previous version

Claude:
Main changes:
+ Added 3 files
- Deleted 1 file
~ Modified 8 files
```

## 10. Project Documentation Automation

### Generate Directory Structure Documentation

```
You: Generate project directory structure documentation

Claude:
Created docs/structure.md with complete directory tree
```

### Generate API Documentation

```
You: Analyze all functions and generate API documentation

Claude:
Created docs/api.md
Contains detailed documentation for 25 functions
```

### Update README

```
You: Update README, add new feature descriptions

Claude:
Updated README.md:
- Added new feature list
- Updated usage examples
- Updated dependency descriptions
```

## 11. Real-world Cases

### Case 1: Project Migration

**Scenario**: Migrate from JavaScript to TypeScript

```
You: Convert the entire project to TypeScript

Claude:
Executing migration plan:
1. Rename all .js to .ts
2. Add type annotations
3. Create tsconfig.json
4. Update package.json
5. Fix type errors

Migration complete!
- Converted 15 files
- Added 50+ type definitions
- 0 type errors
```

### Case 2: Code Standardization

**Scenario**: Unify project code style

```
You: Unify project code style:
- Use single quotes
- 2 space indentation
- Add semicolons at end

Claude:
1. Configure Prettier
2. Format all files
3. Update .editorconfig

Formatted 20 files
```

### Case 3: Performance Optimization

**Scenario**: Optimize static resources

```
You: Optimize all images and code files

Claude:
Optimization results:
Images:
- Compressed 12 images
- Saved 2.5 MB

Code:
- Compressed JavaScript
- Compressed CSS
- Removed comments
- Saved 150 KB
```

## 12. Advanced Search Techniques

### Use Wildcards

```
You: Find all test*.js files

You: Show list of components/**/*.js
```

### Combined Conditional Search

```
You: Find files containing both "fetch" and "async"

You: Search for comments containing TODO but not DONE
```

### Context Search

```
You: Find all places calling getUserData function, show 5 lines before and after

Claude:
Found 3 calls:

1. src/components/Profile.js:45
  40: function loadProfile() {
  41:   setLoading(true)
  42:   try {
  43:     const data = await getUserData(userId)
  44:     setProfile(data)
  45:   } catch (error) {
  46:     handleError(error)
  47:   }
  48: }
...
```

## 13. File Template System

### Create File Templates

```
You: Create a React component template

Claude:
Create templates/ReactComponent.js:
import React from 'react'
import './{{ComponentName}}.css'

function {{ComponentName}}(props) {
  return (
    <div className="{{component-name}}">
      {/* Component content */}
    </div>
  )
}

export default {{ComponentName}}
```

### Use Templates to Generate Files

```
You: Use React component template to create UserCard component

Claude:
Created based on template:
- components/UserCard.js
- components/UserCard.css
- components/UserCard.test.js
```

## 14. Workspace Management

### Switch Between Multiple Projects

```
You: Switch to project "website"

Claude:
Switched to ~/projects/website
Current directory: website/
```

### Workspace Snapshots

```
You: Save current working state

Claude:
Saved snapshot:
- List of open files
- Current branch
- Staged changes
```

## 15. Practice Exercises

### Exercise 1: Project Refactoring

Tasks:
1. Create a messy single-file project
2. Refactor it into modular structure
3. Extract repeated code
4. Add documentation

### Exercise 2: Batch Operations

Tasks:
1. Create 10 similar files
2. Batch modify a property
3. Batch rename
4. Batch add comments

### Exercise 3: Configuration Management

Tasks:
1. Set up development environment configuration
2. Set up production environment configuration
3. Create configuration switching script
4. Test different environments

### Exercise 4: Documentation Generation

Tasks:
1. Generate complete documentation for existing project
2. Include directory structure, API, usage guide
3. Add code examples
4. Generate deployment instructions

## 16. Common Problem Solutions

### Problem 1: File Conflicts

```
You: There's a conflict during merge, can you help me resolve it?

Claude:
Analyzing conflict:
- index.js line 25
- Both versions modified the same function

Suggestion:
Keep version A's performance optimization
While merging version B's error handling
```

### Problem 2: Dependency Mess

```
You: Project import relationships are messy, can you clarify?

Claude:
I will:
1. Analyze all imports
2. Detect circular dependencies
3. Generate dependency graph
4. Provide optimization suggestions
```

### Problem 3: Lost Files

```
You: I accidentally deleted an important file, can you recover it?

Claude:
Checking git history...
Found: src/utils/helpers.js at commit abc123
Want to recover?
```

## 17. Best Practices

### File Organization Principles

1. **Group by feature**, not by type
   ```
   ✅ features/user/UserProfile.js
   ❌ components/UserProfile.js
   ```

2. **Keep flat structure**, avoid deep nesting
   ```
   ✅ src/components/Header.js
   ❌ src/app/views/components/ui/Header.js
   ```

3. **Naming consistency**
   ```
   ✅ UserProfile.js, UserProfile.css, UserProfile.test.js
   ❌ UserProfile.js, user-profile.css, testUserProfile.js
   ```

### Code Refactoring Strategy

1. **Small steps** - Refactor only one aspect at a time
2. **Keep tests** - Run tests before and after refactoring
3. **Commit frequently** - Commit each small change
4. **Sync documentation** - Update code and documentation together

### Automation Recommendations

Automate these repetitive tasks:
- Code formatting
- Lint checking
- Test running
- Documentation generation
- Deployment process

## 18. Next Steps

🎉 Congratulations on mastering advanced file operation techniques!

Now you have learned:
- ✅ Complex project structure management
- ✅ Cross-file refactoring
- ✅ Batch operations
- ✅ Automated tool configuration

**Ready to learn more advanced content?**

👉 Next Lesson: [Hands-on Project: Complete Web Application](./06-web-project.md)

There, we will:
1. Build a complete project from scratch
2. Implement front-end and back-end interaction
3. Add database integration
4. Deploy online

---

💡 **Tips**:
- Build your own project template library
- Regularly refactor code to keep it clean
- Use version control to track every change
- Create scripts for common operations
