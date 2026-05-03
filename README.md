# Medicare Advantage Plan Lookup Website (MVP Blueprint)

This repository now documents a production-style blueprint for building a **Medicare Advantage plan lookup website** with real plan data, ZIP/county selection, plan filters, benefit details, and lead capture.

## What You Want to Build

A user should be able to:
1. Enter ZIP code.
2. Select county.
3. See real Medicare Advantage plans available in that county.
4. Filter plans by type:
   - Dual (Medicare + Medicaid / D-SNP)
   - Giveback
   - Chronic illness (C-SNP)
   - General MA plans
5. View plan details such as:
   - Food/grocery benefit (when applicable)
   - Dental / Vision / Hearing
   - PCP and specialist copays
   - Part B giveback amount (when applicable)
6. Click **Enroll Me** and submit:
   - Name
   - ZIP code
   - Date of birth
   - Selected plan
   - Phone (required)
   - Email
7. See consent prompt:
   - “A licensed agent will call you within 24 hours. Do we have permission to call you back?” (Yes/No)
8. If Yes: save lead automatically to Google Sheets.

---

## Important Compliance Requirements (Must-Have)

Because this is Medicare marketing, your site must follow CMS Medicare rules and TCPA/state call-consent requirements.

### Core safeguards
- Store a consent timestamp and consent text version.
- Track page/session source for audit trail.
- Do not claim enrollment is guaranteed.
- Show legal disclaimer that a licensed agent will review eligibility and plan availability.
- Keep plan data current (monthly updates at minimum, ideally automated).
- Add Privacy Policy + Terms + Contact + Disclaimer pages.

---

## Recommended Tech Stack

- **Frontend:** Next.js + Tailwind CSS
- **Backend/API:** Next.js API routes (or a Kotlin/Spring API if you prefer JVM)
- **Database:** PostgreSQL
- **Data ingestion:** ETL scripts from Medicare public plan files
- **Lead delivery:** Google Sheets API webhook/integration
- **Hosting:** Vercel (frontend/API) + managed Postgres (Neon/Supabase/RDS)

---

## Data Model (High Level)

### `counties`
- id
- state
- county_name
- fips
- zip_codes[]

### `plans`
- id
- contract_id
- plan_id
- year
- organization_name
- plan_name
- state
- county_fips
- plan_type (MA, D-SNP, C-SNP, Giveback, etc.)
- part_b_giveback
- pcp_copay
- specialist_copay
- dental_benefit
- vision_benefit
- hearing_benefit
- grocery_benefit
- last_verified_at

### `leads`
- id
- full_name
- zip_code
- dob
- selected_plan_id
- phone
- email
- consent_call_back (boolean)
- consent_text_version
- consent_timestamp
- created_at

---

## User Flow

1. **ZIP input**
   - Validate 5-digit ZIP.
   - Fetch counties mapped to ZIP.
2. **County selection**
   - If one county, auto-select.
   - If multiple counties, user picks one.
3. **Plan list + filters**
   - Show cards with premium-style UI.
   - Filters: D-SNP, C-SNP, Giveback, General MA.
4. **Plan details**
   - Expand/collapse or modal with full benefits.
5. **Enroll Me form**
   - Capture required fields.
   - Validate phone strongly.
6. **Consent modal**
   - Explicit Yes/No callback permission.
   - On Yes, submit lead.
7. **Google Sheets push**
   - Append row including consent metadata.

---

## API Endpoints (Example)

- `GET /api/zip/:zip/counties`
- `GET /api/plans?zip=XXXXX&countyFips=YYYYY&filter=dsnp|csnp|giveback|ma`
- `POST /api/leads`
- `POST /api/integrations/google-sheets`

---

## Google Sheets Integration Plan

Use a service account and Google Sheets API.

Append columns:
- Timestamp
- Name
- ZIP
- DOB
- Plan Name + Contract/Plan ID
- Phone
- Email
- Consent Yes/No
- Consent Timestamp
- Landing URL / UTM Source

---

## UI/Brand Direction (Premium Look)

- Clean white base + trustworthy blue/green accents
- Large readable typography (senior-friendly)
- High contrast and accessibility-first
- Card-based plan layout with clear “Compare” and “Enroll Me” CTAs
- Sticky filter bar on mobile

---

## Next Build Steps

1. Scaffold Next.js app with Tailwind.
2. Build ZIP → County lookup API + UI.
3. Build plans listing/filter API + UI.
4. Create plan detail modal and CTA.
5. Implement lead form + callback consent modal.
6. Wire Google Sheets integration.
7. Add legal pages + analytics + error monitoring.
8. Add monthly Medicare dataset import pipeline.

---

## Notes

- This README is a practical implementation blueprint.
- Before going live, have your compliance/legal team review wording, disclaimers, and consent flows.

## License

This project remains licensed under the [MIT License](LICENSE).
