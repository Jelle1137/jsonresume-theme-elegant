# JSON Resume Theme Modification Manual

This document outlines the general process for modifying jsonresume-theme-elegant templates and regenerating the compiled `index.js` file.

## Prerequisites

- Node.js and npm installed
- Git repository forked from the original theme
- Basic understanding of Pug templates
- PowerShell or command line access

## Understanding the Theme Structure

### Template Files
- **Pug Templates** (`.pug` files) - Define the HTML structure and layout
- **Compiled Template** (`tpl/index.js`) - Generated JavaScript file containing all templates
- **Main Template** (`index.pug`) - Master template that includes all components

### Key Concept
When you modify any `.pug` template file, you **must regenerate** the `tpl/index.js` file for changes to appear in the final HTML output.

## Step-by-Step Modification Process

### Step 1: Backup Original Files
```bash
# Create backups of files you plan to modify
cp pug/background/[template-name].pug pug/background/[template-name].pug.backup
cp tpl/index.js tpl/index.js.backup
```

### Step 2: Modify Template Files

Edit any `.pug` template files in:
- `pug/background/` - Individual resume sections
- `pug/` - Main components
- `index.pug` - Master template

Make your desired changes to the template structure and content.

### Step 3: Install Dependencies

```powershell
# Install project dependencies (only needed once)
npm install --legacy-peer-deps
```

### Step 4: Regenerate the Compiled Template (Critical Step)

This is the **most important step** - you must regenerate `tpl/index.js` after any template modifications:

```powershell
# Compile Pug templates to JavaScript
npx pug-cli -c index.pug --out tpl

# Add module exports (PowerShell)
Add-Content -Path "tpl\index.js" -Value "module.exports = { renderResume: template };"
```

**For Unix/Linux/Mac:**
```bash
# Compile templates
npx pug-cli -c index.pug --out tpl

# Add module exports (Bash)
echo "module.exports = { renderResume: template };" >> ./tpl/index.js
```

**Alternative using Grunt (if configured):**
```powershell
# If Grunt tasks are set up
npx grunt exec:compile_pug
```

### Step 5: Verify Changes Were Applied

Check that the compiled template includes your modifications:

```powershell
# Search for specific content in compiled template
Select-String -Path "tpl\index.js" -Pattern "[your-search-term]"

# Verify module exports line exists
Select-String -Path "tpl\index.js" -Pattern "module.exports"

# Check file size/modification date
Get-ChildItem tpl\index.js
```

**For Unix/Linux/Mac:**
```bash
# Search for content
grep "[your-search-term]" tpl/index.js

# Check file info
ls -la tpl/index.js
```

### Step 6: Test Your Changes

1. Use the theme with a test resume JSON
2. Generate HTML output
3. Verify your template changes appear correctly

### Step 7: Commit Changes

```bash
# Add your modified template files
git add pug/background/[modified-files].pug
git add pug/[any-other-modified-files].pug

# Add the regenerated compiled template
git add tpl/index.js

# Commit with descriptive message
git commit -m "Describe your template modifications"
git push origin main
```

## Understanding the Compilation Process

### What Happens During Compilation:
1. **Pug compiler reads** `index.pug` and all included templates
2. **Generates JavaScript code** that can render HTML
3. **Outputs to** `tpl/index.js` 
4. **Module exports** makes the template function available to the theme

### Why This Step is Critical:
- The theme uses the **compiled** `tpl/index.js`, not the `.pug` files directly
- Docker builds and resume generation read from `tpl/index.js`
- Changes to `.pug` files are **invisible** until compilation

## Template File Locations

### Common Template Files to Modify:

| File | Purpose |
|------|---------|
| `pug/background/work-experience.pug` | Work experience section |
| `pug/background/education.pug` | Education section |
| `pug/background/skills.pug` | Skills section |
| `pug/background/projects-experience.pug` | Projects section |
| `pug/background/about.pug` | About/summary section |
| `pug/profile-card.pug` | Left sidebar profile |
| `pug/background-card.pug` | Main content area |
| `index.pug` | Master template |

## Docker Integration

If using Docker builds from Git, ensure your Dockerfile includes the compilation step:

```dockerfile
# Install dependencies
RUN npm install --legacy-peer-deps

# Compile templates (critical step)
RUN npx pug-cli -c index.pug --out tpl

# Add module exports
RUN echo "module.exports = { renderResume: template };" >> ./tpl/index.js
```

**Your changes will be automatically included** when Docker builds from your updated Git repository.

## Troubleshooting

### Common Issues:

1. **"pug not found" error:**
   - Solution: Use `npx pug-cli` instead of `pug`
   - Or install globally: `npm install -g pug-cli`

2. **PowerShell syntax errors with `&&`:**
   - Solution: Use separate commands or PowerShell-specific syntax
   - Use `Add-Content` instead of `echo >>`

3. **Dependencies conflicts:**
   - Solution: Use `npm install --legacy-peer-deps`

4. **Changes not appearing in HTML:**
   - **Most common issue**: `tpl/index.js` was not regenerated
   - Solution: Re-run the compilation commands
   - Verify the compiled template contains your changes
   - Check file modification timestamps

5. **Module exports missing:**
   - Symptoms: Theme fails to load or render
   - Solution: Ensure the module.exports line is added after compilation

### Verification Commands:

```powershell
# Check if your changes exist in compiled template
Select-String -Path "tpl\index.js" -Pattern "[search-for-your-changes]"

# Verify module exports line exists
Select-String -Path "tpl\index.js" -Pattern "module.exports"

# Check file modification time
Get-ChildItem tpl\index.js | Select-Object Name, LastWriteTime
```

**For Unix/Linux/Mac:**
```bash
# Search for your changes
grep "[search-for-your-changes]" tpl/index.js

# Check file modification time
ls -la tpl/index.js
```

## Key Reminders

- ⚠️ **Always regenerate `tpl/index.js`** after modifying any `.pug` file
- ⚠️ **Both template files AND compiled file** must be committed to Git
- ⚠️ **Test your changes** before pushing to production
- ⚠️ **Backup files** before making modifications

## Expected Workflow

1. **Modify** `.pug` template files
2. **Compile** to regenerate `tpl/index.js`
3. **Verify** changes appear in compiled file
4. **Test** with sample resume data
5. **Commit** both template and compiled files
6. **Deploy** via Docker or other build process

---

## Notes

- The `tpl/index.js` file is **generated code** - don't edit it manually
- Always compile after template changes or modifications won't appear
- This process applies to **any template modification**, not just specific sections
- Keep backups of working versions before making changes

**Created:** July 2, 2025  
**Last Updated:** July 2, 2025  
**Version:** 1.0
