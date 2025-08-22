# Write or Die - Deployment Guide

This guide will help you deploy the Write or Die web application to GitHub Pages for public access.

## Prerequisites

- GitHub account
- Git installed on your computer
- Basic familiarity with GitHub

## Deployment Steps

### Step 1: Create GitHub Repository

1. Go to [GitHub](https://github.com) and sign in to your account
2. Click the "+" icon in the top right corner and select "New repository"
3. Name your repository (e.g., `write-or-die` or `writing-productivity-tool`)
4. Make sure the repository is **Public** (required for GitHub Pages)
5. Initialize with a README if desired
6. Click "Create repository"

### Step 2: Upload Project Files

#### Option A: Using GitHub Web Interface
1. In your new repository, click "uploading an existing file"
2. Drag and drop all files from the `write-or-die-web/dist/` folder
3. Commit the files with a message like "Initial deployment"

#### Option B: Using Git Command Line
```bash
# Clone your repository
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY_NAME.git
cd YOUR_REPOSITORY_NAME

# Copy the built files
cp -r /path/to/write-or-die-web/dist/* .

# Add and commit files
git add .
git commit -m "Deploy Write or Die application"
git push origin main
```

### Step 3: Enable GitHub Pages

1. In your GitHub repository, go to **Settings**
2. Scroll down to **Pages** in the left sidebar
3. Under **Source**, select "Deploy from a branch"
4. Choose **main** branch and **/ (root)** folder
5. Click **Save**

### Step 4: Access Your Deployed Application

1. GitHub will provide a URL like: `https://YOUR_USERNAME.github.io/YOUR_REPOSITORY_NAME/`
2. It may take a few minutes for the site to become available
3. Visit the URL to access your deployed Write or Die application

## Important Files for Deployment

The following files from the `dist/` folder are required:

- `index.html` - Main application file
- `assets/` folder - Contains CSS and JavaScript files
- `favicon.ico` - Application icon

## Updating Your Deployment

To update your deployed application:

1. Make changes to the source code in `write-or-die-web/src/`
2. Build the project: `npm run build`
3. Copy the new files from `dist/` to your GitHub repository
4. Commit and push the changes
5. GitHub Pages will automatically update your site

## Troubleshooting

### Site Not Loading
- Check that your repository is public
- Verify that GitHub Pages is enabled in repository settings
- Ensure all files from the `dist/` folder are in the repository root

### Features Not Working
- Check browser console for errors
- Verify that all asset files are properly uploaded
- Ensure the `index.html` file is in the repository root

### AI Features Not Working
- AI features require user-provided API keys
- Users must configure their own OpenAI or Hugging Face API keys
- No server-side configuration is needed for the basic application

## Security Notes

- The deployed application contains no sensitive information
- All API keys are provided by users and stored locally in their browsers
- No user data is sent to your servers

## Custom Domain (Optional)

To use a custom domain:

1. Add a `CNAME` file to your repository root with your domain name
2. Configure your domain's DNS to point to GitHub Pages
3. Enable HTTPS in the Pages settings

## Support

If you encounter issues with deployment:

1. Check the GitHub Pages documentation
2. Verify all files are properly uploaded
3. Test the application locally first using `npm run dev`
4. Check browser developer tools for error messages

---

Your Write or Die application is now ready for public use! Share the GitHub Pages URL with others to help them improve their writing productivity.

