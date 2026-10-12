# eManuals Hub — starter

A custom Next.js + TypeScript starter for a digital manuals marketplace. This is a foundation, not a live production store: payment processing, verified webhooks, authentication, private downloads, memberships, admin permissions and hosting must be configured before accepting money.

## Budget-conscious stack
- Next.js + TypeScript for the storefront and server routes
- Supabase for managed PostgreSQL, authentication and private object storage
- PayPal Sandbox checkout with server-side order verification and email delivery scaffolding
- Branded Help Centre and email support are configured; no personal WhatsApp number is displayed publicly

Check each provider's current fees, business eligibility and country support before committing.

## Local development
1. Install Node.js LTS (Node 20+ recommended).
2. Extract this project and run `npm install`.
3. Copy `.env.example` to `.env.local`.
4. Create a Supabase project and set the Supabase URL and anon key.
5. Run `supabase/schema.sql` in the Supabase SQL Editor.
6. Run `npm run dev` and open `http://localhost:3000`.

## Environment variables
Never commit `.env.local`. Never use `NEXT_PUBLIC_` for private keys. `SUPABASE_SERVICE_ROLE_KEY`, payment secrets and webhook secrets must be server-only.

## PayPal Sandbox checkout (new)
1. In PayPal Developer Dashboard, use Sandbox > Apps & Credentials and copy the Sandbox Client ID and Secret.
2. In Netlify environment variables, set `NEXT_PUBLIC_PAYPAL_CLIENT_ID` and `PAYPAL_CLIENT_ID` to the Sandbox Client ID, `PAYPAL_CLIENT_SECRET` to the Sandbox Secret, and `PAYPAL_BASE_URL=https://api-m.sandbox.paypal.com`.
3. Set `NEXT_PUBLIC_SITE_URL` to the deployed URL. Configure Supabase `NEXT_PUBLIC_SUPABASE_URL` and `SUPABASE_SERVICE_ROLE_KEY`.
4. Run `supabase/paypal-deliveries.sql` in Supabase SQL Editor.
5. Configure Resend with a verified sender domain and set `RESEND_API_KEY` and `MANUALS_FROM_EMAIL`.
6. Test only with PayPal Sandbox buyer accounts and test funds. The server creates an order from the server-side catalogue price, verifies the PayPal order/capture, binds the original email to the order, and emails the included PDF attachment.
7. This is a starter integration, not production approval. Before Live mode, add PayPal webhooks and reconciliation, rate limiting/abuse protection, robust retry queue and admin refunds/support workflow. Never use Live credentials until all requirements are reviewed.

**Important file limitation:** included PDF files are currently under `public/manuals`, so they are publicly reachable by URL regardless of payment. This integration emails the sample PDF after verified Sandbox capture but does not make those files private. Before selling paid manuals, move licensed PDFs to a private Supabase Storage bucket and implement authenticated, short-lived download URLs. Do not advertise model-specific manuals until their exact files and distribution rights are verified.

## Production checklist — do not skip
- [ ] Buy and configure a domain; set HTTPS and `NEXT_PUBLIC_SITE_URL`.
- [ ] Configure Supabase Auth; require verified identity before accessing purchased downloads.
- [ ] Create a private Storage bucket for licensed PDF files; do not make it public.
- [ ] Build server-side checkout/order creation with prices read from the database.
- [ ] Verify Razorpay/Stripe signatures and webhook events on the server; make webhook handling idempotent.
- [ ] Mark an order paid only from a verified provider event, never from a client redirect.
- [ ] Create entitlement records only after verified payment; check entitlement before issuing a short-lived signed download URL.
- [ ] Implement subscriptions and cancellation/expiry handling through verified provider webhooks.
- [ ] Implement admin roles, MFA, audit logging and server-side authorization.
- [ ] Add privacy policy, terms, refund/download policy, support SLA and business identity details.
- [ ] Confirm rights to distribute each manual before publishing it.
- [ ] Test failed payments, duplicate webhooks, refunds, expired memberships, unauthorized downloads and mobile checkout.
- [ ] Set up backups, monitoring, rate limits, email delivery and abuse controls.

## Important current limitations
- The homepage and a placeholder search page are implemented.
- Search is not yet connected to PostgreSQL.
- No payment provider is wired in; no real payment can be accepted.
- No admin UI or file download route is wired in yet.
- The example support links use the supplied WhatsApp number and support email.
- The catalogue should contain only manuals with verified distribution rights and model compatibility.


## Private WhatsApp support
The starter homepage no longer links directly to a personal `wa.me` URL. The `/support` page explains that a private WhatsApp inbox is not connected yet. To receive WhatsApp messages without publishing a personal number, set up the WhatsApp Business Platform (or a reputable shared team inbox provider), complete business verification and approved templates where required, then connect its webhook/API to a staff-only inbox. Do not put WhatsApp API tokens in browser code. A standard `wa.me` link should not be used if keeping the receiving number private is a requirement.


## Support workflow status
- Floating branded Help button: implemented in the starter UI.
- Help Centre page with email support and a support-request form preview: implemented.
- Support ticket database schema: included in `supabase/schema.sql`.
- Ticket submission is deliberately not connected to a backend yet; the UI clearly tells users it has not been sent.
- Private WhatsApp: not connected. Set up WhatsApp Business Platform/shared inbox, then implement authenticated server-side message handling and webhook verification. Do not use a personal `wa.me` link or expose WhatsApp access tokens in frontend code.
- Before production: add server-side validation, rate limiting/CAPTCHA, ticket ownership and staff role checks, email notifications, retention policy and audit logs.


## Support ticket API (starter implementation)
- `POST /api/support` validates fields and stores tickets in `support_tickets` using the server-only Supabase service-role key.
- Optional email notification can be enabled with `RESEND_API_KEY` and `SUPPORT_FROM_EMAIL`.
- Apply `supabase/schema.sql` before using the endpoint.
- The public form includes a basic honeypot. This is not a complete anti-abuse system: add provider-backed rate limiting/CAPTCHA before production.
- No staff admin inbox UI is included yet, so ticket records are stored but staff need database/admin tooling to review them until the admin panel is implemented.


## Catalogue and sample PDF guides (new)
- Open `/catalog` to browse the included original starter guides, prices and details pages.
- Six original general guides are included under `public/manuals/`. They are not official OEM/model-specific manuals.
- Model/brand families are listed as a request directory; exact model-specific OEM PDFs must be supplied or licensed and checked for distribution rights before being advertised as available.
- The listed prices are editable examples only. The current purchase CTA sends an email enquiry; it does **not** take payment.
- Before accepting money, connect a payment provider, implement and verify server-side checkout/webhooks, authenticate customers, store paid files in a private Supabase Storage bucket, and grant downloads only after verified payment. Never put private PDFs in a public folder.

## Worldwide currency display
- The site-wide currency selector is shown above each page and remembers the visitor's selection in their browser.
- Catalogue and product-detail prices convert from the store's USD base price into the selected currency.
- The selector includes many commonly used worldwide currencies. It attempts to load current indicative USD exchange rates from open.er-api.com and falls back to approximate rates if that service is unavailable.
- Converted prices are display estimates only. Configure PayPal checkout separately and clearly state the actual checkout currency before payment; exchange rates and provider conversion/fees can differ.


## Car catalogue navigation index (added)
- The Catalogue dropdown supports desktop hover/focus and click/tap toggling.
- It includes a broad car brand index, commercial vehicle brands, and common manual document types.
- This is navigation metadata only, not an inventory of PDFs. A product must not be marked purchasable until its exact make/model/year file and digital distribution rights are verified.
- The index can later be connected to a licensed supplier feed/API or a verified admin-managed product database.
\n## Navigation/page cleanup (2026-10)\n- Removed duplicate top-level Categories link so the Catalogue dropdown is the single category-menu entry point.\n- Added dedicated Categories, Blog and FAQs routes; linked Vehicle Manuals, Equipment, Phone Repair and Support in the shared header.\n- The dropdown includes links to all categories and the available-manual catalogue.\n
## Hierarchical catalogue behavior
Category cards link to the selected category only. `/catalog` provides cascading category, brand, model, year, and document-type filters, plus an exact-manual request action if no verified PDF exists. Filters are based on the current product inventory; the UI intentionally does not fabricate model-specific PDFs.


## Competitive pricing policy

For an exact same make/model/year/manual-type match, add `competitorReferencePriceUSD`, `competitorReferenceURL`, and `competitorPriceCheckedAt` to the relevant product only after checking the listing manually. `getCompetitivePriceUSD()` prices the item at $2.50 USD below that verified reference. If any verification field is missing, the current catalogue price remains unchanged. Do not use a broad or unrelated competitor listing as a comparison. Re-check reference prices regularly because competitor prices can change.


## All-category manual request workflow
- Customer request form: `/request-manual` covers cars, motorbikes, trucks, tractors, construction/heavy equipment, cranes, marine, engines, phones, tablets, computers, laptops/Mac, appliances, printers and other equipment.
- Apply `supabase/custom-manual-requests.sql` in Supabase SQL Editor. The migration permits a request to be stored before a price is set and adds request/payment statuses. No public RLS policies are created; server routes use the service-role key.
- A new request is saved and emails the admin and customer a receipt. No payment is taken at request time.
- Admin workflow: open `/admin/manual-requests`, enter `ADMIN_ACTION_TOKEN`, load requests, prepare the authorized PDF, set the price, and upload the PDF (maximum 4 MB). The server creates a PayPal order and emails the PDF attachment and payment link to the customer.
- Payment status is tracked in `custom_manual_requests`. The PayPal return page `/manual-payment` verifies/captures the order server-side and updates the status to `paid`.
- Configure `ADMIN_ACTION_TOKEN` with a long random secret, PayPal Sandbox `PAYPAL_CLIENT_ID` and `PAYPAL_CLIENT_SECRET`, `PAYPAL_BASE_URL`, `RESEND_API_KEY`, a verified `SUPPORT_FROM_EMAIL` or `MANUALS_FROM_EMAIL`, `ADMIN_EMAIL`, `NEXT_PUBLIC_SITE_URL`, `NEXT_PUBLIC_SUPABASE_URL`, and `SUPABASE_SERVICE_ROLE_KEY`. Never put admin or PayPal secret tokens in `NEXT_PUBLIC_` variables or share the admin token.
- Before production, test the complete flow in PayPal Sandbox, add provider webhooks/reconciliation, rate limiting/CAPTCHA and staff authentication beyond a shared token, refund/cancellation handling, and a secure delivery process. Supply only manuals and source material you own or are authorized to distribute. Category listings are not a claim that every manual is in stock.
