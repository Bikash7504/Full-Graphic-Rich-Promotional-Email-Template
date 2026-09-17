# Production Dynamic Abandoned Cart Email (SFMC / Litmus Tested)

A high-converting, fully responsive, and dynamic Abandoned Cart email built for **Salesforce Marketing Cloud (SFMC)**.

## Architecture Overview
* **Templating Engine:** SFMC AMPscript (`LookupOrderedRows`, string manipulation, math evaluations).
* **Grid Architecture:** Fluid/hybrid two-column layout using floating `inline-block` tables backed by Microsoft Word Conditional Comments (`<!--[if (gte mso 9)|(IE)]>`).
* **Resilience & Fallbacks:** Graceful degradation for zero-price items, missing names, broken image paths, and odd item counts.

## Key Technical Solutions

### 1. Outlook Desktop vs. Mobile Stack Mechanism
To guarantee clean single-column stacking on mobile while keeping rigid horizontal cells in desktop Outlook:
* Outlook's Word engine ignores CSS `max-width`, `display: inline-block`, and `@media` queries.
* We open an Outlook-only table (`<table width="600"><tr><td width="280">`) using `<!--[if (gte mso 9)|(IE)]>`.
* Modern mobile clients ignore the MSO comments and execute the HTML markup: `<table class="stack-column" width="280" align="left">`.
* On mobile screens (<600px), a media query enforces `width: 100% !important; display: block !important;`, collapsing the grid cleanly.

### 2. Multi-Item Dynamic Extraction
The template queries the `AbandonedCart_Items` Data Extension using `LookupOrderedRows()`, loops by `STEP 2` to construct structural table rows, and safely handles odd-numbered carts with a blank spacer cell to prevent table deformation.
