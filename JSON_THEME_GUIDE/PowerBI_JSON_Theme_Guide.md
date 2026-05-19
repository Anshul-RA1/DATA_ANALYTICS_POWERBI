# Power BI JSON Theme — Complete Guide
## Anshul-RA1 | DATA_ANALYTICS_POWERBI

> Learn how to create, customize, and apply professional JSON themes in Power BI

---

## Table of Contents

1. [What is a JSON Theme?](#what-is-a-json-theme)
2. [Why JSON Theme is Important](#why-json-theme-is-important)
3. [JSON Theme Structure](#json-theme-structure)
4. [Complete Parameter Reference](#complete-parameter-reference)
5. [Color System](#color-system)
6. [Visual-Specific Formatting](#visual-specific-formatting)
7. [How to Apply a Theme](#how-to-apply-a-theme)
8. [Our Project Theme — Explained](#our-project-theme-explained)
9. [Interview Questions](#interview-questions)

---

## What is a JSON Theme?

```
JSON = JavaScript Object Notation
     = A text-based data format using
       key-value pairs

Power BI JSON Theme = A configuration file
that defines the visual appearance of your
ENTIRE report in one place.

File extension: .json
Example name:  Anshul_RA1_Theme.json
```

**Without JSON Theme:**
```
Manual approach:
→ Select Card 1 → Format → Background → #252B3B
→ Select Card 2 → Format → Background → #252B3B
→ Select Card 3 → Format → Background → #252B3B
→ Select Line Chart → Format → Background → #252B3B
→ Select Bar Chart → Format → Background → #252B3B
... 50+ manual steps for one page!
... 250+ steps for 5 pages!
❌ Time consuming
❌ Human error prone
❌ Inconsistent results
```

**With JSON Theme:**
```
→ Create theme.json file (one time!)
→ View → Themes → Browse → Upload
→ DONE! ✅

ALL visuals across ALL pages:
✅ Same background colors
✅ Same fonts
✅ Same border styles
✅ Same chart colors
✅ 100% consistent!
```

---

## Why JSON Theme is Important

### 1. Consistency Across Entire Report
```
Without theme:
Page 1 cards → Background #252B3B
Page 2 cards → Background #253040 (slightly different!)
Page 3 cards → Background white (forgot!)

This looks unprofessional! ❌

With theme:
Every card on every page → #252B3B automatically ✅
```

### 2. Branding Compliance
```
Real companies have brand guidelines:
→ Primary color: #0078D4 (Microsoft Blue)
→ Font: Segoe UI
→ Never use red for positive values

JSON theme ENFORCES these rules automatically!
New team members cannot accidentally
use wrong colors! ✅
```

### 3. Easy Maintenance
```
Without theme:
Client says: "Change primary color from 
blue to teal"
→ Update 50+ visuals manually
→ 2-3 hours of work ❌

With theme:
→ Open JSON file
→ Change "#4FACFE" to "#00D4B4" (1 line!)
→ Re-upload theme
→ ALL visuals updated instantly! ✅
→ 2 minutes of work!
```

### 4. Reusability
```
Once you build a good theme:
→ Use it for Project 1 ✅
→ Use it for Project 2 ✅
→ Use it for Project 3 ✅
→ Share with team ✅
→ Use in client projects ✅

Build once → Use forever!
```

### 5. Interview Value
```
Interviewer: "How do you ensure design 
consistency across a large Power BI report?"

Strong Answer:
"I create a custom JSON theme file that 
defines all colors, fonts, borders, and 
visual defaults centrally. This ensures 
100% consistency across all pages and 
visuals, makes brand updates trivial — 
just one file change — and significantly 
reduces development time compared to 
manual formatting."
```

---

## JSON Theme Structure

```json
{
  "name": "Theme Name",          ← Theme identifier
  
  "dataColors": [],              ← Chart/data colors array
  
  "background": "#1E2130",       ← Page background
  "foreground": "#FFFFFF",       ← Default text color
  "tableAccent": "#4FACFE",      ← Table highlight color
  
  "visualStyles": {              ← Per-visual formatting
    "*": { "*": {} },            ← Applies to ALL visuals
    "card": { "*": {} },         ← Only Card visuals
    "slicer": { "*": {} },       ← Only Slicer visuals
    "lineChart": { "*": {} },    ← Only Line Charts
    ...
  },
  
  "page": {                      ← Page-level settings
    "background": {},
    "outspace": {}               ← Canvas outer area
  }
}
```

---

## Complete Parameter Reference

### Top Level Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| `name` | String | Theme display name | `"MyTheme"` |
| `dataColors` | Array | Chart colors (order matters!) | `["#4FACFE", "#68D391"]` |
| `background` | Hex Color | Default page background | `"#1E2130"` |
| `foreground` | Hex Color | Default text color | `"#FFFFFF"` |
| `tableAccent` | Hex Color | Table/matrix highlight | `"#4FACFE"` |

---

### dataColors — Chart Colors Explained

```json
"dataColors": [
  "#4FACFE",   ← Color 1 (first data series)
  "#00D4B4",   ← Color 2 (second data series)
  "#68D391",   ← Color 3 (third data series)
  "#F0B429",   ← Color 4 (fourth data series)
  "#FC8181",   ← Color 5 (fifth data series)
  "#B794F4",   ← Color 6
  "#F687B3",   ← Color 7
  "#FBB040",   ← Color 8
  "#76E4F7",   ← Color 9
  "#9AE6B4"    ← Color 10
]
```

**How dataColors work:**
```
Bar chart with 3 categories:
Category 1 → Uses dataColors[0] → #4FACFE (Blue)
Category 2 → Uses dataColors[1] → #00D4B4 (Teal)
Category 3 → Uses dataColors[2] → #68D391 (Green)

Line chart with 4 lines (years):
2014 line → #4FACFE
2015 line → #00D4B4
2016 line → #68D391
2017 line → #F0B429

Order matters! Most important data
should use your primary color (index 0)!
```

---

### visualStyles — The Core Section

**Structure:**
```json
"visualStyles": {
  "VISUAL_TYPE": {
    "STATE": {
      "PROPERTY": [{ "value": "..." }]
    }
  }
}
```

**Visual Type Options:**
```
"*"              → ALL visuals (global default)
"card"           → Card visuals
"slicer"         → Slicer visuals
"lineChart"      → Line charts
"barChart"       → Bar charts
"columnChart"    → Column (vertical bar) charts
"donutChart"     → Donut charts
"pieChart"       → Pie charts
"tableEx"        → Table visuals
"matrix"         → Matrix visuals
"map"            → Map visuals
"waterfallChart" → Waterfall charts
"gauge"          → Gauge visuals
"kpiVisual"      → KPI visuals
"textbox"        → Text boxes
"scatterChart"   → Scatter plots
"filledMap"      → Filled maps
```

**State Options:**
```
"*"      → Applies to ALL states of that visual
"hover"  → When mouse hovers over visual
"select" → When visual is selected/clicked
```

---

### Common Properties Reference

#### Background Property
```json
"background": [{
  "color": {
    "solid": {
      "color": "#252B3B"
    }
  }
}]
```

#### Border Property
```json
"border": [{
  "color": { "solid": { "color": "#4FACFE" } },
  "show": true,
  "radius": 8
}]
```

#### Font Color Property
```json
"fontColor": [{ "solid": { "color": "#FFFFFF" } }]
```

#### Font Size Property
```json
"fontSize": [{ "value": 13 }]
```

#### Font Family Property
```json
"fontFamily": [{ "value": "Segoe UI Semibold" }]
```

#### Padding Property
```json
"padding": [{ "top": 16, "right": 16, "bottom": 16, "left": 16 }]
```

---

## Visual-Specific Formatting

### Card Visual — Sub-sections

```json
"card": {
  "*": {
    "background": [...],    ← Card background
    "border": [...],        ← Card border
    "padding": [...]        ← Inner spacing
  },
  "labels": {
    "color": [...],         ← The BIG number color
    "fontSize": [...],      ← Big number size
    "fontFamily": [...]     ← Big number font
  },
  "categoryLabels": {
    "color": [...],         ← Label text color (e.g. "Total Sales")
    "fontSize": [...],      ← Label text size
    "fontFamily": [...]     ← Label text font
  }
}
```

**Visual Reference:**
```
┌────────────────────────┐
│                        │
│       $2.3M            │ ← "labels" (big number)
│      Total Sales       │ ← "categoryLabels"
│                        │
└────────────────────────┘
```

---

### Slicer Visual — Sub-sections

```json
"slicer": {
  "*": {
    "background": [...],    ← Slicer background
    "border": [...]         ← Slicer border
  },
  "header": {
    "fontColor": [...],     ← "Year" / "Region" label color
    "fontSize": [...],      ← Header text size
    "background": [...]     ← Header background
  },
  "items": {
    "fontColor": [...],     ← Dropdown option text color
    "fontSize": [...],      ← Option text size
    "background": [...]     ← Options background
  }
}
```

---

### Line Chart — Sub-sections

```json
"lineChart": {
  "*": {
    "background": [...],    ← Chart background
    "border": [...]         ← Chart border
  },
  "title": {
    "fontColor": [...],     ← Chart title color
    "fontSize": [...],      ← Chart title size
    "fontFamily": [...]     ← Chart title font
  },
  "valueAxis": {
    "gridlineColor": [...], ← Horizontal gridlines
    "labelColor": [...]     ← Y-axis labels
  },
  "categoryAxis": {
    "labelColor": [...]     ← X-axis labels
  },
  "legend": {
    "labelColor": [...]     ← Legend text
  }
}
```

---

### Matrix Visual — Sub-sections

```json
"matrix": {
  "header": {
    "fontColor": [...],           ← Column header text
    "background": [...],          ← Column header background
    "fontFamily": [...]           ← Header font
  },
  "values": {
    "fontColorPrimary": [...],    ← Odd row text color
    "backgroundColorPrimary": [...], ← Odd row background
    "fontColorSecondary": [...],  ← Even row text color
    "backgroundColorSecondary": [...] ← Even row background
  },
  "totals": {
    "fontColor": [...],           ← Total row text color
    "background": [...]           ← Total row background
  }
}
```

---

### Waterfall Chart — Sentiment Colors

```json
"waterfallChart": {
  "sentimentColors": {
    "totalBar": "#4FACFE",      ← Total/starting bar color
    "increasedBar": "#68D391",  ← Positive/increase bar color
    "decreasedBar": "#FC8181"   ← Negative/decrease bar color
  }
}
```

---

## Color System — Our Project Theme

### Complete Color Palette

| Color Name | Hex Code | Usage |
|------------|----------|-------|
| Dark Navy | `#1E2130` | Page background |
| Card Background | `#252B3B` | All visual backgrounds |
| Deep Navy | `#141820` | Wallpaper/outer area |
| Dark Border | `#2D3748` | Subtle borders |
| Primary Blue | `#4FACFE` | Main accent, KPI numbers, borders |
| Teal | `#00D4B4` | Secondary data color |
| Green | `#68D391` | Positive values, growth |
| Gold | `#F0B429` | Warning, attention |
| Red | `#FC8181` | Negative values, decline |
| Purple | `#B794F4` | Additional data series |
| White | `#FFFFFF` | Primary text |
| Light Grey | `#A0AEC0` | Secondary labels |

### Color Usage Rules

```
PRIMARY ACCENT (#4FACFE Blue):
→ KPI card numbers
→ Visual borders
→ Chart primary data series
→ Table accents
→ Slicer borders

POSITIVE VALUES (#68D391 Green):
→ YoY (Year over Year) growth positive
→ Profit positive
→ Target achieved

NEGATIVE VALUES (#FC8181 Red):
→ YoY growth negative
→ Profit negative
→ Target not met

TEXT HIERARCHY:
→ #FFFFFF = Primary text (titles, KPI labels)
→ #A0AEC0 = Secondary text (axis labels, subtitles)
→ #718096 = Tertiary text (footnotes, captions)

BACKGROUNDS:
→ #1E2130 = Page canvas
→ #252B3B = Visual containers
→ #141820 = Outer wallpaper
→ #2D3748 = Subtle separators
```

---

## How to Apply a Theme

### Step 1 — Save the JSON file
```
Save: Anshul_RA1_Theme.json
Location: 
01_Sales_Analytics/templates/
```

### Step 2 — Apply in Power BI Desktop
```
View tab (top ribbon)
→ Themes section
→ Dropdown arrow next to theme thumbnails
→ "Browse for themes"
→ Navigate to your .json file
→ Select and Open
→ Theme applied instantly! ✅
```

### Step 3 — Verify
```
Check:
→ All card backgrounds changed? ✅
→ Chart colors updated? ✅
→ Font sizes consistent? ✅
→ Page background dark? ✅
```

### Step 4 — Fine-tune if needed
```
If any visual needs minor adjustment:
→ Select that visual only
→ Format pane → Override specific setting
→ This does NOT affect the theme file
→ Just overrides for that one visual
```

---

## Our Project Theme — Explained

### Section by Section

```json
"name": "Anshul_RA1_SalesAnalytics"
```
→ This is how the theme appears in Power BI's theme list

```json
"dataColors": ["#4FACFE", "#00D4B4", "#68D391", ...]
```
→ 10 colors for data series. Line chart with 4 years gets first 4 colors

```json
"background": "#1E2130"
"foreground": "#FFFFFF"
"tableAccent": "#4FACFE"
```
→ Global defaults for all visuals before visual-specific overrides

```json
"*": { "*": { "fontFamily": "Segoe UI", "fontSize": 11 } }
```
→ Wildcard — applies Segoe UI font and size 11 to EVERY single visual

```json
"card": { "labels": { "color": "#4FACFE", "fontSize": 28 } }
```
→ Only KPI cards get blue 28pt numbers

```json
"waterfallChart": { "sentimentColors": { "increasedBar": "#68D391", "decreasedBar": "#FC8181" } }
```
→ Waterfall bars: green for increases, red for decreases automatically

```json
"page": { "background": { "color": "#1E2130" } }
```
→ Every new page automatically gets dark navy background

---

## Advanced Tips

### Tip 1 — Using "transparent" for Textboxes
```json
"textbox": {
  "*": {
    "background": [{ "color": { "solid": { "color": "transparent" } } }],
    "border": [{ "show": false }]
  }
}
```
→ Text boxes (titles, labels) have no background or border
→ They float cleanly over the dark canvas

### Tip 2 — Wildcard vs Specific
```
Priority order (most specific wins):
1. Individual visual manual override (highest)
2. Visual-type specific in theme (e.g. "card")
3. Global wildcard "*" in theme (lowest)

Example:
"*" sets fontSize: 11
"card" → "labels" sets fontSize: 28
→ Card numbers show 28pt
→ Everything else shows 11pt ✅
```

### Tip 3 — Testing Colors Before Applying
```
Use these free tools to test colors:
→ coolors.co — generate color palettes
→ contrast-ratio.com — check readability
→ htmlcolorcodes.com — hex color picker

Rule: Background and text colors must have
contrast ratio of at least 4.5:1 for 
readability (WCAG accessibility standard)

Our theme:
#252B3B background + #FFFFFF text
= Contrast ratio 14.7:1 ✅ Excellent!
```

### Tip 4 — Radius for Modern Look
```json
"border": [{
  "show": true,
  "radius": 8   ← Rounded corners (0 = square, 16 = very round)
}]
```
→ radius: 0 = sharp corners (classic look)
→ radius: 4 = subtle rounding (modern)
→ radius: 8 = clearly rounded (friendly)
→ radius: 16 = very rounded (pill-like)

Our theme uses radius: 8 for a modern
professional look! ✅
```

### Tip 5 — Alternating Row Colors in Tables
```json
"values": {
  "fontColorPrimary": [{ "solid": { "color": "#FFFFFF" } }],
  "backgroundColorPrimary": [{ "solid": { "color": "#252B3B" } }],
  "fontColorSecondary": [{ "solid": { "color": "#A0AEC0" } }],
  "backgroundColorSecondary": [{ "solid": { "color": "#1E2A3B" } }]
}
```
→ Primary = odd rows
→ Secondary = even rows
→ Subtle alternating shade improves readability
→ This is called "zebra striping" ✅
```

---

## Interview Questions

**Q: What is a Power BI JSON theme file?**
> A JSON theme file is a configuration file that defines the visual appearance of an entire Power BI report centrally. It covers colors, fonts, borders, backgrounds, and chart defaults for every visual type. Applying a theme file is a one-time action that ensures 100% consistency across all pages and visuals without manual formatting.

---

**Q: Why would you use a JSON theme instead of manually formatting each visual?**
> Manual formatting is time-consuming, error-prone, and inconsistent — especially across multiple pages and team members. A JSON theme file establishes a single source of truth for all design decisions. If a brand color needs to change, updating one line in the JSON file instantly updates every visual in the report. This is significantly more efficient and maintainable in production environments.

---

**Q: How does the visualStyles section in a JSON theme work?**
> The visualStyles section uses a hierarchy — the wildcard "*" applies to all visual types, while specific visual type keys like "card" or "lineChart" override the defaults for those types. Within each visual type, you can target sub-components like "title", "legend", "valueAxis", and "categoryLabels" independently. The most specific rule always wins — manual formatting on an individual visual overrides the theme, which overrides the global wildcard.

---

**Q: What is dataColors in a JSON theme and why does order matter?**
> dataColors is an array of hex color codes that Power BI cycles through when displaying multiple data series. The first color goes to the first series, second to the second, and so on. Order matters because it determines which color appears most prominently. The primary brand color should be at index 0 so it appears on the most important data series first.

---

**Q: How do you apply a JSON theme in Power BI?**
> In Power BI Desktop, go to the View tab in the top ribbon, click the dropdown arrow in the Themes section, select "Browse for themes", navigate to your JSON file, and open it. The theme applies instantly to all pages and visuals. Individual visuals can still be manually overridden after the theme is applied if specific adjustments are needed.

---

**Q: What is the difference between background and outspace in the page section?**
> Background refers to the canvas area where visuals are placed — the actual report page. Outspace refers to the area outside the canvas boundary — the grey border area visible in edit mode. Setting both creates a cohesive dark environment in development mode. In presentation mode, only the background color is visible to end users.

---

## Common Mistakes to Avoid

```
❌ MISTAKE 1: Using too many colors
   More than 6-7 data colors looks chaotic
   → Stick to 5-6 primary colors ✅

❌ MISTAKE 2: Low contrast text
   Dark background + dark text = unreadable!
   → Always check contrast ratio ✅

❌ MISTAKE 3: Inconsistent border radius
   Some visuals rounded, some sharp = messy
   → Use same radius value everywhere ✅

❌ MISTAKE 4: Not testing in presentation mode
   Edit mode shows selection handles
   Preview mode shows actual final look
   → Always Ctrl+Shift+F11 to preview ✅

❌ MISTAKE 5: Hardcoding colors in measures
   "🟢 Excellent" emoji colors in DAX
   vs actual chart colors from theme
   → Keep them in sync! ✅
```

---

## Our Theme Color Map — Visual Summary

```
PAGE CANVAS:         #1E2130  ████ Dark Navy
VISUAL BACKGROUNDS:  #252B3B  ████ Card Dark
OUTER WALLPAPER:     #141820  ████ Deepest Navy
SUBTLE BORDERS:      #2D3748  ████ Border Dark

PRIMARY ACCENT:      #4FACFE  ████ Blue
SECONDARY ACCENT:    #00D4B4  ████ Teal
POSITIVE:            #68D391  ████ Green
ATTENTION:           #F0B429  ████ Gold
NEGATIVE:            #FC8181  ████ Red
ADDITIONAL:          #B794F4  ████ Purple

TEXT PRIMARY:        #FFFFFF  ████ White
TEXT SECONDARY:      #A0AEC0  ████ Light Grey
TEXT TERTIARY:       #718096  ████ Medium Grey
```

---

*Anshul-RA1 | DATA_ANALYTICS_POWERBI*
*Power BI JSON Theme — Complete Teaching Guide*
*Project 1 — Sales Analytics Dashboard*
