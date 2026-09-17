# Enterprise CloudPage Preference Center (SFMC / SSJS)

A production-grade, two-way subscriber preference center built for **Salesforce Marketing Cloud (SFMC)** CloudPages.

## Architecture Overview
* **Backend Processing Engine:** Server-Side JavaScript (SSJS) running on the Rhino engine.
* **Security Layer:** 
  * Reversible base64 tokenized URL parsing (simulating encrypted `CloudPagesURL()` parameters).
  * Strict regular expression validation for identifiers and email formatting.
  * Context-aware character escaping protecting against Stored and Reflected Cross-Site Scripting (XSS).
* **Persistence & Deduplication:** Direct `UpsertData` / `Rows.Upsert` transaction targeting an enterprise sendable Data Extension keyed uniquely on `SubscriberKey`.
* **Error Containment:** Global `try/catch` failure boundary logging execution stack traces and presenting customer-friendly error interfaces without dumping raw system state.

---

## Technical Solutions & Interview Talking Points

### 1. Preventing Duplicate Records (`SubscriberKey` Primary Key Locking)
* **Primary Key Enforcement:** The target Data Extension defines `SubscriberKey` as the single non-nullable Primary Key.
* **Upsert vs. Insert:** The backend executes `UpsertData()` instead of standard insert functions. If a record with that `SubscriberKey` exists, it applies an update in place; otherwise, it inserts a new record.
* **Read/Write Idempotency:** When the page loads via an email link (`CloudPagesURL(pageID, 'sk', _subscriberkey)`), it retrieves the subscriber's current states via `LookupOrderedRows()`. Any state submitted during POST maps strictly to that existing `SubscriberKey`, preventing multiple entries for the same contact.

### 2. Defending Against Cross-Site Scripting (XSS)
* **Reflected XSS Defense:** Query-string and form inputs are never printed directly into the DOM. Every user-controlled string passes through a custom sanitization sanitizer (`sanitizeString()`) that converts characters like `<`, `>`, `"`, `'`, and `&` into standard HTML entities (`&lt;`, `&gt;`, etc.) prior to rendering.
* **Stored XSS Defense:** Values written into Data Extensions are cleaned and trimmed.
* **State Tampering:** Checkbox controls evaluate explicit Boolean strings (`"true"` vs `"false"`). Arbitrary injected script injections into form fields fail strict validation checks.