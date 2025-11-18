# MedAxis² Design Guidelines

## Design Approach

**System-Based Approach**: Material Design principles adapted for medical/clinical applications, emphasizing clarity, professionalism, and trust. Drawing inspiration from healthcare platforms like Epic MyChart, Zocdoc, and modern medical portals that balance clinical precision with user-friendly interfaces.

**Core Design Principles**:
1. Clinical Trust: Professional, clean aesthetic that inspires confidence
2. Clarity Over Decoration: Information hierarchy optimized for medical data
3. Accessibility First: WCAG 2.1 AA compliance for all users
4. Mobile-Critical: Patient interface prioritizes mobile experience

---

## Typography System

**Font Family**: Inter (already established in existing HTML)
- Primary: Inter Regular (400) for body text
- Emphasis: Inter SemiBold (600) for labels, UI elements
- Headings: Inter Bold (700) for section headers

**Scale**:
- H1 (Dashboard titles): 28px / 1.75rem, Bold
- H2 (Section headers): 22px / 1.375rem, Bold  
- H3 (Card titles): 18px / 1.125rem, SemiBold
- Body (primary): 15px / 0.9375rem, Regular
- Body (secondary): 14px / 0.875rem, Regular
- Small/Meta: 13px / 0.8125rem, Regular
- Captions: 12px / 0.75rem, Regular

**Line Height**: 1.5 for body text, 1.3 for headings

---

## Layout System

**Spacing Scale**: Use Tailwind units of **2, 3, 4, 6, 8, 12, 16** for consistent rhythm
- Micro spacing (form elements): p-2, gap-3
- Standard spacing (cards, sections): p-4, p-6, gap-4
- Large spacing (page sections): p-8, py-12, gap-8
- Extra large (section breaks): py-16

**Container Strategy**:
- Operator dashboard: max-w-7xl (1280px) for wide data tables
- Patient questionnaire: max-w-2xl (672px) for optimal readability
- Forms: max-w-xl (576px) for focused input
- Reports: max-w-4xl (896px) for document viewing

**Grid System**:
- Dashboard: 12-column grid for complex layouts
- Cards: 2-3 columns on desktop (grid-cols-1 md:grid-cols-2 lg:grid-cols-3)
- Forms: Single column with responsive widths

---

## Component Library

### Navigation
**Operator Dashboard Sidebar** (Desktop):
- Fixed left sidebar, width: 256px (w-64)
- Vertical navigation with icons + labels
- Sections: Dashboard, Patients, Questionnaires, Archive, Studio Settings, Billing (Pro)
- Active state: Subtle background fill, bold text
- Collapsed mobile: Hamburger menu overlay

**Patient Interface Header**:
- Minimal top bar with logo centered
- Progress indicator for multi-step questionnaire
- No navigation (linear flow)

### Cards & Containers
**Data Cards**:
- Rounded corners: rounded-lg (8px)
- Shadow: Subtle elevation (shadow-sm for resting, shadow-md for interactive)
- Padding: p-6 for content cards
- Border: 1px solid border when needed for definition

**Stat Cards** (Dashboard):
- Compact design: p-4
- Large number display: text-3xl, Bold
- Label below: text-sm
- Icon in top-right corner: 24x24px

### Forms & Inputs
**Input Fields**:
- Height: h-11 (44px for touch targets)
- Padding: px-4
- Border: 1px solid, rounded-md
- Focus: 2px outline, offset
- Labels: Above input, text-sm, SemiBold

**Radio Buttons** (Questionnaire):
- Custom styled with clear hit areas (min 44x44px)
- Full-width selectable rows on mobile
- Score value + descriptive text layout
- Selected state: Filled background

**Buttons**:
- Primary (CTA): h-11, px-6, rounded-lg, Bold text
- Secondary: h-10, px-4, rounded-md, SemiBold
- Text-only: No background, underline on hover
- Icon buttons: 40x40px minimum

### Tables (Archive, Patient History)
**Structure**:
- Zebra striping for row distinction
- Sticky header on scroll
- Responsive: Cards on mobile, table on desktop (md:table)
- Row actions: Right-aligned icon menu
- Sortable columns with indicator

**Cells**:
- Padding: px-4 py-3
- Vertical borders only when needed for data clarity
- Text alignment: Left for text, right for numbers

### Questionnaire Components
**Question Card**:
- One question per screen on mobile
- Question text: text-lg, SemiBold, mb-4
- Options: Vertical stack with spacing-3
- Navigation: Fixed bottom bar with Prev/Next

**Progress Indicator**:
- Stepper with current/total (e.g., "Question 5 of 39")
- Visual progress bar: Full width, height-1
- Section indicators: PII, MFS, MBS, ARI, TMFA labels

### Report Viewer
**Layout**:
- PDF-like document container (A4 aspect ratio)
- White content area on neutral background
- Print-optimized styles
- Section headers with dividers

**Score Display**:
- Module scores: Grid layout, 2 columns on mobile, 5 on desktop
- Total score: Prominent display with level indicator
- Level badge: Pill-shaped, large text, centered
- Bar charts for module comparison

### Modals & Overlays
**Dialog Boxes**:
- Max-width: max-w-md for alerts, max-w-2xl for forms
- Backdrop: Semi-transparent overlay
- Close button: Top-right, 32x32px hit area
- Actions: Bottom-aligned, right-justified

**Confirmation Dialogs**:
- Icon + message + actions
- Destructive actions: Visually distinct
- Cancel always available

---

## Page-Specific Layouts

### Operator Dashboard (Home)
- Top bar: Studio name, notifications, user menu (h-16)
- Left sidebar: Navigation (w-64)
- Main content: Responsive grid of stat cards
- Recent activity: Table below stats
- Quick actions: Floating action button (bottom-right on mobile)

### Patient Archive
- Search/filter bar at top
- Results table with sortable columns
- Patient cards on mobile with key info
- Click row to view patient detail page
- Comparison tool: Select multiple questionnaires

### Studio Settings
- Tabbed interface: Studio Info, Subscription, Customization
- Form sections with clear labels
- Logo upload: Drag-drop zone, 200x200px preview
- Save button: Sticky at bottom on scroll

### Patient Questionnaire Flow
1. **Landing Page**: Privacy notice, accept button, logo at top
2. **Demographics**: Single form, all fields visible
3. **Questionnaire**: One question per screen, progress bar
4. **Completion**: Thank you message, save PDF button

### Report Generation
- Two-column layout on desktop: Parameters (left), Preview (right)
- Single column on mobile: Stack vertically
- Export buttons: Fixed top bar (PDF, Print, Share)
- Encrypted indicator: Lock icon + explanation text

---

## Responsive Breakpoints

- Mobile: Base styles (320px+)
- Tablet: md: (768px+) - Two-column layouts
- Desktop: lg: (1024px+) - Full sidebar, multi-column
- Wide: xl: (1280px+) - Maximum content width

**Mobile-First Adjustments**:
- Hide sidebar, show hamburger menu
- Stack cards vertically
- Full-width form inputs
- Bottom navigation for patients
- Larger touch targets (44px minimum)

---

## Accessibility Requirements

- All interactive elements: Minimum 44x44px
- Focus indicators: 2px outline with offset, never remove
- Color contrast: Minimum 4.5:1 for text
- Keyboard navigation: Logical tab order, visible focus
- Screen reader: Semantic HTML, ARIA labels where needed
- Form errors: Inline, descriptive, associated with fields

---

## Animations

**Minimal, Purposeful Motion**:
- Page transitions: 200ms ease-out
- Hover states: 150ms ease
- Loading states: Subtle spinner, no elaborate animations
- Success confirmations: Simple checkmark fade-in
- Avoid: Parallax, excessive motion, auto-playing animations

---

## Images

**Hero Image**: Not applicable for this medical application - focus on clean, data-driven interface

**Required Images**:
- **Logo**: MedAxis² logo in header (Logo_MediAxis2.png), max-height: 40px
- **Empty States**: Simple illustrations for empty patient list, no questionnaires yet (use Heroicons or similar)
- **Success States**: Checkmark icons for completed actions
- **Icons**: Use Heroicons library throughout for consistency (16px, 20px, 24px sizes)

**Image Guidelines**:
- No decorative images - maintain clinical, professional aesthetic
- Icons only when they add functional clarity
- Studio logo upload: Accept PNG/JPG, square format preferred

---

## Special Considerations

**Privacy/GDPR Compliance**:
- Consent flows clearly designed
- Privacy notices: Easily accessible, scannable
- Data handling explanations: Plain language

**Multi-Language Support** (Future):
- Design flexible for text expansion (Italian to English ~20% longer)
- Use flex/grid for dynamic content

**Offline Capability**:
- Visual indicators for sync status
- Cached data notifications

This comprehensive system ensures MedAxis² presents a professional, trustworthy interface appropriate for medical contexts while remaining accessible and user-friendly for both operators and patients.