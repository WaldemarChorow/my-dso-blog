# Docusaurus Blog Configuration

## Brief Overview

This project documents the complete setup and configuration of a Docusaurus blog from an existing template. The configuration includes customizing the site metadata, updating theme settings, configuring environment variables for deployment, and setting up GitHub Pages for automatic deployment. This guide demonstrates how to personalize a Docusaurus template while maintaining clean code structure and proper configuration management.

## Table of Contents

1. [Project Initialization](#project-initialization)
2. [Core Configuration](#core-configuration)
3. [Environment Setup](#environment-setup)
4. [Deployment Configuration](#deployment-configuration)

import GithubLinkAdmonition from '@site/src/components/GithubLinkAdmonition';

<GithubLinkAdmonition 
    link="https://github.com/spmse/dev-blog-template"
    title="Github Template" 
    type="tip"
>
This project was created from the dev-blog-template repository.
</GithubLinkAdmonition>

## Project Initialization

### Clone the Template Repository

The Docusaurus blog template was cloned from GitHub to serve as the base for this project:

```bash
git clone https://github.com/spmse/dev-blog-template.git my-dso-blog
cd my-dso-blog
```

### Install Dependencies

After cloning, all project dependencies were installed using pnpm:

```bash
pnpm install
```

This command reads the `package.json` file and installs all required packages. The development server can be started with:

```bash
pnpm start
```

## Core Configuration

### docusaurus.config.ts

The main configuration file `docusaurus.config.ts` was customized with the following changes:

**1. Site Metadata**

```typescript
const config: Config = {
  title: 'Learn diary',
  tagline: 'by Waldemar Chorow',
  // ...
};
```

**2. Environment-Based Configuration**

Created a TypeScript variable to read the Git repository URL from environment variables:

```typescript
const gitRepositoryUrl = process.env.GIT_REPOSITORY_URL ?? 'https://github.com/WaldemarChorow/my-dso-blog';
```

This variable is used for:
- Documentation edit URLs
- Blog post edit URLs
- GitHub repository links in the navbar and footer

**3. Navbar Customization**

```typescript
navbar: {
  title: 'Waldemar Chorow',
  items: [
    {
      href: gitRepositoryUrl,
      label: 'Github',
      position: 'right',
    },
  ],
},
```

**4. Footer Updates**

The footer was restructured to include:
- Docs section with a link to projects documentation
- More section with links to the personal repository and template repository
- Updated copyright message: "Built with Docusaurus and 💚, extended from the developer-akademie-starter"

## Environment Setup

### example.env Configuration

Environment variables were added to support flexible deployment:

```env
DEPLOYMENT_URL=https://WaldemarChorow.github.io
DEPLOYMENT_BRANCH=main
BASE_URL=/my-dso-blog/
GITHUB_ORG=WaldemarChorow
GITHUB_PROJECT=my-dso-blog
GIT_REPOSITORY_URL=https://github.com/WaldemarChorow/my-dso-blog
BLOG_ENABLED=false
```

These variables allow the configuration to be adjusted for different deployment environments without modifying the code.

## Deployment Configuration

### GitHub Pages Setup

The project is configured to deploy automatically to GitHub Pages through a GitHub Actions workflow:

1. **Repository Settings**: In the GitHub repository settings, the Pages source was changed to "GitHub Actions"
2. **Automatic Deployment**: When a commit is pushed to the main branch, the workflow automatically builds and deploys the website
3. **Base URL**: The `BASE_URL` environment variable determines the deployment path on GitHub Pages

### Building the Project

To build the static site locally:

```bash
pnpm build
```

This generates the static content into the `build/` directory, ready for deployment.

## Key Configuration Steps Summary

- ✓ Cloned the template repository and installed dependencies
- ✓ Updated site title and tagline to reflect personal branding
- ✓ Created environment variable for Git repository URL
- ✓ Updated all GitHub links to point to the personal repository
- ✓ Configured footer with projects link and template reference
- ✓ Added deployment configuration for GitHub Pages
- ✓ Updated copyright and attribution messages

## Further References

- [Docusaurus Official Documentation](https://docusaurus.io/)
- [Docusaurus Configuration Reference](https://docusaurus.io/docs/api/docusaurus-config)
- [GitHub Pages Documentation](https://pages.github.com/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Environment Variables Best Practices](https://12factor.net/config)
