# Claude Code Instructions

This file contains project-specific instructions for Claude Code to help with development workflow.

## Project Overview

This is a Jekyll-based personal blog/portfolio site hosted on GitHub Pages. The site uses:
- Jekyll 4.x with kramdown markdown processor
- Custom SCSS styling with modular architecture
- jekyll-paginate for article pagination
- Ruby 3.4.1 managed via chruby

## Development Setup

### Prerequisites
- Ruby 3.4.1 (managed via chruby)
- Bundler gem

### Local Development Server

To run the Jekyll development server locally:

```bash
# Ensure you're using the correct Ruby version
source /opt/homebrew/opt/chruby/share/chruby/chruby.sh
chruby ruby-3.4.1

# Install dependencies (first time only)
bundle install

# Start the development server
bundle exec jekyll serve
```

The site will be available at `http://127.0.0.1:4000/`

### Common Commands

```bash
# Build the site without serving
bundle exec jekyll build

# Serve with live reload and drafts
bundle exec jekyll serve --livereload --drafts

# Clean generated files
bundle exec jekyll clean
```

## Project Structure

- `_posts/` - Blog posts in markdown format
- `_pages/` - Static pages (about, research, etc.)
- `_layouts/` - HTML templates
- `_includes/` - Reusable HTML components
- `_sass/` - SCSS partials for styling
- `assets/css/` - Main SCSS entry point
- `_config.yml` - Jekyll configuration

## Notes for Claude Code

- Always use Ruby 3.4.1 via chruby when running Jekyll commands
- The site uses kramdown for markdown processing
- Tables automatically get proper styling via `_page.scss`
- Use `bundle exec` prefix for all Jekyll commands to ensure correct gem versions
