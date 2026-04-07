# CLAUDE.md - Project Context

## Project Overview
CIRCULATION Enterprise Dashboard - an enterprise-level business dashboard for CIRCULATION Inc.
Single-page HTML application displaying KPIs, revenue charts, company metrics, and organizational data.

## Tech Stack
- **Frontend**: Vanilla HTML/CSS/JavaScript (no build tools or frameworks)
- **Language**: Japanese UI labels and content
- **Styling**: CSS custom properties, grid/flexbox layout, responsive design
- **Charts**: Custom bar charts rendered via inline JavaScript (no external charting library)

## Repository Structure
```
index.html               # Main dashboard (latest version)
acplan_unified_51.html   # Previous/alternate version of the dashboard
```

## Key Architecture
- Self-contained HTML files with embedded `<style>` and `<script>` sections
- CSS variables defined in `:root` for theming (--blue, --grn, --org, --red, etc.)
- Sidebar navigation with icon-based menu items
- KPI cards with cumulative values and delta indicators
- Revenue panel with bar chart visualization and drill-down capability
- Company grid cards with per-company metrics
- Summary row, layer panel, and organizational structure views

## Development Notes
- No package manager or build step required - open HTML files directly in a browser
- All styles are inlined within `<style>` tags
- All scripts are inlined within `<script>` tags
- Data is embedded directly in the JavaScript (no external API calls)
- Design follows Rakuten-style KPI patterns and MoneyForward-style card layouts

## Conventions
- CSS class names use short abbreviations (e.g., `.ni` for nav item, `.co-m` for company metric)
- Font stack prioritizes SF Mono (monospace) for numeric values
- Color scheme: Blue (#0079C0) primary, Green (#1a7f37) positive, Red (#cf222e) negative, Orange (#bf8700) warning
