# Kinetic Email: Interactive CSS Tabbed Product Showcase (SFMC)

An advanced kinetic email template built for **Salesforce Marketing Cloud (SFMC)**. Demonstrates progressive enhancement by delivering an interactive in-email colorway switcher for supporting WebKit clients (Apple Mail, iOS Mail), alongside an automatic static fallback for strict clients (Gmail, Outlook Desktop, Yahoo).

## Architecture Overview
* **Interactive Layer (Progressive Enhancement):** Hidden radio inputs (`<input type="radio">`) paired with sibling combinator selectors (`#tab:checked ~ .content`) to toggle imagery, specs, and CTAs without JavaScript.
* **Fallback Layer (Graceful Degradation):** Clean, accessible static multi-item grid displayed by default on non-interactive clients.
* **Targeting & Reset:** CSS `@media screen and (-webkit-min-device-pixel-ratio: 0)` and class-targeted declarations reveal the interactive viewport while hiding the fallback.

---

## Technical Solutions & Interview Talking Points

### 1. The Kinetic Technique ("The Radio Hack")
* Native email clients strictly strip `<script>` tags, preventing standard JavaScript interactions.
* To achieve interactive state changes, we use hidden HTML `<input type="radio" name="colorway">` tags placed before the visual containers.
* Visible `<label for="...">` elements act as clickable swatch triggers.
* When clicked, CSS general sibling selectors (`#color-1:checked ~ .view-1`) manipulate `display`, `opacity`, and `max-height` to swap the displayed product image and color specs dynamically in real time.

### 2. Why the Fallback Strategy Is Just as Important as the Interactive Layer
* **Market Realities:** Gmail strips `<style>` tags containing complex attribute selectors and sibling combinators (`~`), while desktop Outlook renders using Microsoft Word (which has zero support for kinetic CSS or form tags).
* **The "Zero Broken Inboxes" Rule:** Interactive emails fail if non-supporting clients see half-rendered radio buttons or broken layouts.
* **Progressive Enhancement Pattern:** We code the template **fallback-first**. By default, the kinetic interactive block is hidden (`display: none; max-height: 0;`), and the rock-solid static fallback grid renders. Only when a client passes specific WebKit/CSS engine feature tests does the interactive interface trigger open.