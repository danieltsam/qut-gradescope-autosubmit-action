# QUT Gradescope Autosubmission Action
*This action is built on top of the [QUT Gradescope Auto Submitter CLI tool](https://github.com/danieltsam/qut-gradescope-autosubmitter). For local development and testing, consider installing the CLI tool directly.*

A GitHub Action that automatically submits your code to QUT Gradescope when you push changes. No more manual clicking through submission forms - just push your code and let the action handle the rest.

## Features

- **Automatic submission** on every push to main/master branches
- **Manual trigger** with workflow dispatch for on-demand submissions
- **Smart caching** of Playwright browsers for faster execution
- **Artifact preservation** of submission files for audit trail
- **System diagnostics** to ensure everything is working correctly
- **Flexible configuration** via repository variables and secrets

## Quick Setup

### 1. Add Repository Secrets

Go to your repository Settings → Secrets and variables → Actions and add:

- `GRADESCOPE_USERNAME` - Your QUT student number (e.g., n12345678)
- `GRADESCOPE_PASSWORD` - Your QUT password

### 2. Add Repository Variables

In the same location, add these variables:

- `GRADESCOPE_COURSE` - Your course code (e.g., cab201)
- `GRADESCOPE_ASSIGNMENT` - Assignment name as it appears on Gradescope

### 3. Add the Workflow
From GitHub Marketplace, just add the workflow!

## Usage

### Automatic Submission

Every time you push code to the main or master branch:

```bash
git add solution.py
git commit -m "Implement sorting algorithm"
git push origin main
# → GitHub Action automatically submits to Gradescope
```

### Manual Submission

1. Go to the Actions tab in your repository
2. Select "Auto Submit to Gradescope"
3. Click "Run workflow"
4. Optionally check "Force submission" to submit even without changes

## Configuration

The action automatically creates a `gradescope.yml` configuration file using your repository variables:

```yaml
course: ${{ vars.GRADESCOPE_COURSE }}
assignment: ${{ vars.GRADESCOPE_ASSIGNMENT }}
file: submission.zip
bundle: ['*']  # Includes all files, respects .gitignore
notify_when_graded: false
headless: true
```

## File Bundling

By default, the action includes all files in your repository while respecting `.gitignore`. Files like `README.md`, `.gitignore`, and `.github/` directories are automatically excluded from triggering submissions.

## Security Features

- **Headless mode** - Runs browser automation in the background
- **Fresh login** - Uses new authentication session for each run
- **No grade waiting** - Submits and exits without waiting for grading results
- **Secure credential storage** - Uses GitHub's encrypted secrets

## Outputs and Artifacts

- **Submission artifact** - The actual ZIP file submitted to Gradescope is saved as an artifact
- **System diagnostics** - Logs show system health checks and validation
- **Execution logs** - Detailed progress tracking of each submission step

## Troubleshooting

### Common Issues

**"Course/Assignment not found"**
- Verify `GRADESCOPE_COURSE` and `GRADESCOPE_ASSIGNMENT` variables match what appears on Gradescope
- Try using partial names (e.g., "cab201" instead of "CAB201_24se2")

**"Authentication failed"**
- Check that `GRADESCOPE_USERNAME` and `GRADESCOPE_PASSWORD` secrets are correct
- Ensure your QUT account has access to the specified course

**"No files to submit"**
- Check that your repository contains files not excluded by `.gitignore`
- Verify the workflow isn't being triggered by documentation-only changes

### Debug Mode

For troubleshooting, you can enable debug output by adding this to your workflow:

```yaml
env:
  ACTIONS_RUNNER_DEBUG: true
  ACTIONS_STEP_DEBUG: true
```

## Rate Limiting

The action uses `--fresh-login` to ensure reliable authentication, but be mindful that excessive submissions may trigger QUT SSO rate limiting. The action automatically skips runs when only documentation files are changed to minimize unnecessary submissions.

## Requirements

- QUT student account with Gradescope access
- Repository with code files to submit
- Proper repository secrets and variables configuration
