# Product Design Specification

## 1. Product experience

An internal administration application for managing multiple software products and their offline licenses. Administrators communicate with customers outside the application and record customer information, requests, approvals, and license operations inside it. There is no customer-facing portal.

The experience should prioritize operational clarity, safe execution of sensitive licensing actions, efficient record management, and learnability through an embedded User Guide.

## 2. Design principles

- Classic Enterprise: calm, structured, information-dense, and predictable.
- Very lean interfaces: show the information and actions needed for the current task; avoid decorative UI and unnecessary charts.
- Consistent patterns across modules for tables, filters, forms, status indicators, confirmations, and activity history.
- Make destructive or security-sensitive operations explicit and difficult to trigger accidentally.
- Responsive behavior must adapt interaction patterns, not merely shrink desktop layouts.
- Accessibility is a design requirement, not a final polish task.

## 3. Brand, themes, and typography

### 3.1 Color direction
- Primary theme: Navy Blue.
- Supporting surfaces: white and cool-neutral grays in Light Mode; navy/slate neutrals in Dark Mode.
- Semantic colors are reserved for state communication: success, warning, danger, and informational states.
- Exact color tokens, contrast pairs, and semantic palettes are TBD and must be validated against WCAG 2.2 AA.

### 3.2 Appearance
- Support Light and Dark themes.
- Users can switch themes manually regardless of operating-system preference.
- Persist the user's theme preference locally where appropriate; storage mechanism is an implementation detail.
- All components, charts, tables, dialogs, overlays, and status indicators must support both themes.

### 3.3 Typography
- Use dedicated font families for Arabic and English.
- Candidate Arabic fonts: IBM Plex Sans Arabic or Cairo. Candidate English font: Inter.
- Final font selection, weights, and scale are TBD.
- Support RTL/LTR layout, mirrored directional affordances, locale-aware dates/numbers, and mixed-language technical identifiers.

### 3.4 Icons
- Use outline icons consistently.
- Icons that convey actions must have accessible names/tooltips; do not rely on icon shape or color alone.

## 4. Responsive behavior

Support Desktop, Tablet, and Mobile.

- Desktop: full grouped navigation, compact data tables, multi-column dashboard and forms where useful.
- Tablet: preserve navigation and table usability while reducing columns and spacing as needed.
- Mobile: sidebar becomes a temporary drawer; prioritize key record fields and actions; use hybrid compact list/card presentation and open record details for secondary fields.
- Avoid forcing users to horizontally scan a wide desktop table on mobile as the primary interaction.
- Exact breakpoints are TBD and should follow content-driven testing rather than device-brand assumptions.

## 5. Application shell and navigation

### 5.1 Main shell
- Collapsible sidebar with expanded and icon-only states.
- On narrow screens, navigation opens as a drawer.
- Group navigation items by business capability; each group can expand/collapse independently.
- Top application bar should provide page context and global utilities such as notifications and account controls. Exact topbar composition is TBD.

### 5.2 Proposed navigation groups
These are an initial grouping, subject to final route and permission review:

- **Dashboard**
- **Business Management:** Customers, Products
- **Licensing Management:** Licenses, Devices, Dongles
- **Operations:** Requests, Notifications
- **Administration:** Users, Roles/Permissions, Audit Log, System Settings, Backup & Restore
- **Help:** User Guide

The visibility of navigation items must respect authorization. Hiding a menu item is not a substitute for backend authorization.

## 6. Dashboard

Use a hybrid dashboard combining high-level status with operational work queues.

### 6.1 KPI summary
- Products.
- Customers.
- Active, expired, and pending licenses.
- Pending requests.
- Dongle inventory/status summary.

### 6.2 Operational panels
- Requests awaiting review or approval.
- Licenses approaching expiration.
- Recent important activity.
- In-app notifications and alerts.
- A small number of meaningful charts where they improve understanding.

No general reporting/export module is required. Avoid charts that duplicate the same information already shown in tables or KPI cards.

## 7. Data tables and record browsing

### 7.1 Desktop/tablet
- Compact density is the default: restrained row height and padding while preserving readable text and accessible hit targets.
- Clear column labels, sorting/filter affordances where supported, pagination, and consistent action placement.
- Use status badges for meaningful lifecycle states; include text labels so color is never the only signal.
- Provide loading skeletons, empty states, error states, and permission states.

### 7.2 Mobile
- Hybrid presentation: show the most important fields in a compact list/card row.
- Secondary fields and full record context are available through a detail view/expansion.
- Keep primary actions discoverable and avoid dense multi-column tables as the default mobile layout.

### 7.3 Filtering
- Filters should be discoverable and consistent between modules.
- Preserve established query/filter serialization behavior if an existing implementation is present; do not change it without an explicit requirement.
- Filter details, saved filters, and advanced filter builders are not currently specified.

## 8. Forms, dialogs, and operations

- Default create, edit, and routine operation forms use modal dialogs.
- Dialogs must be responsive, keyboard accessible, have clear titles and actions, and prevent accidental data loss when closing with unsaved changes.
- Use inline validation messages near the relevant fields.
- Use confirmation dialogs for irreversible or high-impact actions such as revoke, archive, restore, and sensitive key operations.
- Multi-step wizard pages are not part of the approved default. If a real workflow proves too complex for a modal, raise it as an explicit design decision rather than silently changing the pattern.

## 9. Feedback and system states

### 9.1 Feedback pattern
- Toasts: short success confirmations and transient operational messages.
- Inline feedback: field validation, actionable errors, and context-specific failures.
- Persistent notifications: important events remain available in the in-app notification center.
- Avoid displaying secrets or sensitive values in notifications and error messages.

### 9.2 Comprehensive states
Design explicit states for:
- Initial loading and skeleton loading.
- Empty collection and no filter matches.
- Validation failure and recoverable operation failure.
- Unexpected error.
- Access denied.
- Server unavailable, connection lost, reconnecting, and disconnected.
- Background operation pending/failed/completed where relevant.
- File scanning pending, rejected, or failed.
- Offline client licensing conditions where surfaced in administration workflows.

The administration dashboard requires connectivity to its on-premises server. Offline licensing refers to the licensed software's activation/validation/renewal/revocation process, not to an offline-capable admin dashboard.

## 10. User Guide

- Provide static embedded documentation pages shipped with the application; no CMS or database-backed content management.
- Organize by module and end-to-end scenarios, not only by individual controls.
- Include step-by-step instructions, prerequisites, expected outcomes, and relevant warnings for sensitive operations.
- Link to relevant guide pages from related screens and workflows where useful.
- Support Arabic and English, RTL/LTR, and responsive layouts.
- Guide content is versioned with application releases. Exact content format, navigation/search behavior, and ownership workflow are TBD.

## 11. Accessibility

Target WCAG 2.2 AA as far as practical and testable.

- Full keyboard navigation and visible focus indicators.
- Semantic headings, labels, landmarks, and accessible form errors.
- Screen-reader names and state announcements for interactive controls and dynamic updates.
- Sufficient text and non-text contrast in both themes.
- Do not communicate status through color alone.
- Respect reduced-motion preferences.
- Dialog focus management, escape behavior, and focus restoration.
- Ensure compact tables do not create inaccessible targets or clipped content.

## 12. Localization

- Arabic and English are supported throughout the UI and embedded guide.
- All user-facing frontend strings must use centralized `translationKeys`; no hard-coded UI copy in components.
- RTL/LTR must be supported at layout and component level.
- Localize dates, numbers, and formatting; preserve technical identifiers and license payload fields exactly where required by protocol.

## 13. UX details still TBD

- Final color hex tokens and semantic palettes.
- Final font families and weights.
- Exact breakpoints and mobile navigation behavior details.
- Topbar controls and global search scope.
- Dashboard widget order and chart definitions.
- Per-module table columns, filters, sorting, and action menus.
- Modal sizing rules and form-specific field layouts.
- User Guide content format and in-app search/navigation.
- Notification center grouping, read/unread behavior, and preferences UI.
- Accessibility verification tooling and acceptance checklist.
