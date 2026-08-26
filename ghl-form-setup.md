# GHL Form Setup — WorkwidKirk GHL Portfolio

Everything here happens inside GoHighLevel. Nothing in this file touches the site, except the two
places marked **site-side** where a number has to be copied back into `index.html`.

The site is ready for the embed. `index.html` → `<!-- CONTACT -->` holds a `.formslot` div with the
embed instructions in an HTML comment and a working email fallback until the form lands.

Do not reuse `PRE-BOOKING FORM` (`3A04WWn7tWtH8dCdM6QD`) from the PPC portfolio. That form asks about
monthly ad spend and Amazon product category, which means nothing to someone buying a CRM build.

---

## 1. Build the form

**Sites → Forms → Builder → New Form.** Name it `Portfolio — GHL Build Enquiry`.
The name matters: the workflow trigger filters on it, and that filter fails silently when it misses.

| Field | Type | Required | Custom field |
|---|---|---|---|
| First name | text | yes | built-in |
| Last name | text | **no** | built-in |
| Email | email | yes | built-in |
| Phone | phone | **no** | built-in |
| What do you run? | dropdown | yes | `business_type` |
| Already on HighLevel? | radio | yes | `on_highlevel` |
| What is breaking? | textarea | yes | `whats_breaking` |
| Budget for the build | dropdown | no | `build_budget` |

**What do you run?** — Agency · Coach or consultant · Local service business · Ecommerce · Something else

**Already on HighLevel?** — Yes · No · Migrating from another CRM

**Budget for the build** — Under $500 · $500 – $1,500 · $1,500 – $3,000 · $3,000+ · Not sure yet

Two deliberate choices:

- **Phone and last name are optional.** Someone who found the site five minutes ago will not hand
  over a phone number to get a build plan. You need the number once they agree to the call, not
  before. Every required field costs completions.
- **"Not sure yet" is a real budget option.** Forcing a number out of someone who does not have one
  produces a made-up answer, which is worse than an honest blank.

Submit button label: **Send my enquiry**. Not "Submit" — that describes the mechanic, not the
outcome, and first-person possessives outperform second-person on CTA labels.

A custom field's object (Contact vs Opportunity) **cannot be changed after creation**. All four
above belong on the Contact: each would still be true if the same person enquired a year from now.

---

## 2. Style it

**Styles → Custom CSS.** Two traps, both of which look like "the CSS half worked":

### 2.1 The card background is an inline style

The form card is `#_builder-form` and ships with `style="background-color:#F2F7EFFF; border:1px solid
#FFFFFFFF; border-radius:3px; …"`. Its class is **`.form-builder--wrap`** (plus
`.form-builder--wrap-full`). There is no `.form-builder--wrapper` — that selector does nothing.

Inline styles need `!important` from the stylesheet to beat. Kill the backgrounds down the whole
chain, or the form renders as a cream card floating on the page's cream card:

```css
html, body,
.ghl-form-wrap, .hl_wrapper--inner, .hl_form-builder--main,
.form-side, .form-builder, #_builder-form, .form-builder--wrap {
  background: transparent !important;
  border: none !important;
  box-shadow: none !important;
}
```

### 2.2 Field colours come from the Styles panel, not from your CSS

GHL injects `#_builder-form .form-builder--item input[type=text][class=form-control] { … !important }`
at specificity (1,3,1). A plain `input[type="text"]` rule is (0,1,1) and loses silently — labels and
the submit button will style correctly while the fields stay white.

**Set Field Background, Field Text, and Field Border in the Styles panel.** That is what generates
those rules, so it is the fix rather than a fight. If you must do it in CSS, double the id
(`#_builder-form#_builder-form …`) for (2,3,1), which wins regardless of source order.

Dropdowns are Vue multiselects, not `<select>`. Cover `.multiselect`, `.multiselect__tags`,
`.multiselect__input`, `.multiselect__single`, `.multiselect__placeholder` too.

Selectors that work unmodified: `label`, `.field-label`, `button[type="submit"]`, `.button-element`
(the submit button's real class).

### 2.3 Colours to match this site

```
Page background behind the form   #F5F0E8   (cream — the slot is cream, not navy)
Field background                  #FFFFFF
Field text                        #0D1F3C
Field border                      rgba(13,31,60,.18)
Label text                        #0D1F3C
Submit button background          #2ECC71
Submit button text                #081527
Font                              Inter
```

The submit button must be emerald, not GHL's default green. If it looks close but wrong, it is the
default and the style did not apply.

### 2.4 Delete the footer legal line or point it somewhere real

GHL ships Privacy Policy and Terms of Service links in the form footer pointing at
`https://www.example.com`. Either set real URLs or delete the line.

---

## 3. Embed it — site-side

**Integrate → Embed**, copy the snippet, and replace the `.formslot__fallback` block in
`index.html`. Keep `form_embed.js` — it auto-sizes the iframe height.

The slot only reserves height once an iframe is actually inside it (`.formslot:has(iframe)`), so the
fallback does not render as a tall empty box before the form exists.

**Copy the snippet's `data-height` into the `.formslot:has(iframe)` rule's `min-height`.** That floor
stops the layout jumping on first paint; `form_embed.js` handles growth past it on its own.
Adding or removing a field changes the real height, so re-copy the snippet and update the number
whenever the form changes.

---

## 4. Wire the workflow

New workflow. **Trigger: Form Submitted**, filtered to `Form is any of "Portfolio — GHL Build Enquiry"`.

Five things that each fail quietly:

1. **The form-name filter is silent when it misses.** Name the wrong form and the contact is created
   normally while the workflow never enrols. No error appears anywhere. The filter is "any of", so
   add forms rather than swapping them.
2. **A Draft workflow fires for nothing.** Check the Draft/Publish toggle top-right, not just Save.
3. **Internal alerts must use Send Internal Notification → type Email → pick the *user*.** Never
   `Send Email` with your own address typed into the To field. `Send Email` is contact-scoped, so
   GHL mints a junk contact per alert address on *every* submission — subscribed contacts that then
   receive bulk sends and skew your conversion numbers. The tell: the junk contact's Activity tab
   reads "No activities yet" while Conversations holds the alert, and that alert carries an
   unsubscribe footer.
4. **Turn off Settings → Business Profile → Allow Duplicate Contact.** Otherwise a fresh junk pair
   appears on each run instead of being reused.
5. **Settings → Allow Re-entry**, if you plan to test more than once with the same email. Without it
   a contact that already entered is skipped, which looks exactly like a broken trigger.

Actions, in order:

1. Add tag `ghl-portfolio-lead` — keeps this site's leads separable from the PPC portfolio's.
2. Create Opportunity in your sales pipeline at stage **New Lead**. This is an entry-point workflow,
   so it does need Create Opportunity. Stage-triggered workflows do not.
3. Send Internal Notification to yourself.
4. Send the confirmation email to the contact.

Confirmation email, matching what the page promises:

> Subject: Got it — here is what happens next
>
> Thanks for sending that over. I read every one of these myself, and I will reply within one
> business day with either a build plan or the questions I need answered before I can write one.
> If you have not heard from me by then, check your spam folder and then reply to this email.

---

## 5. On-submit destination

Point On Submit at **the calendar page**, not at `workwidkirk.com/thank-you`. That URL 404s — the
whole root domain does, because it resolves to HighLevel with nothing published on it.

There is **no way to intercept this from the site.** `form_embed.js` handles only `iframeLoaded`,
`fetch-query-params`, `fetch-sticky-contacts`, `set-sticky-contacts` and `modify-parent-url`. There
is no `formSubmitted` event, despite what GHL blog posts repeat, and `modify-parent-url` only runs
`history.replaceState` — address bar, no navigation. The redirect is a top-level navigation fired
from inside the iframe, and the parent cannot convert it into an in-place swap.

Options, best first:

1. A published funnel step or page carrying a confirmation line and the booking calendar embedded
   below it. One click from form to booked call.
2. The calendar's booking URL directly.
3. A thank-you *message* in the form settings, if neither of the above exists yet.

---

## 6. Verify after deploy

- Submit a real test entry from the live page. The contact lands in HighLevel tagged
  `ghl-portfolio-lead`.
- All four custom fields carried through, not just name and email.
- The workflow shows the contact under **Enrollment History**, and **Execution Logs** shows each
  action completing. Those two tabs separate an enrolment problem from an action problem — do not
  debug by watching the contact record.
- The confirmation email arrived in a real inbox. `Delivered` + `Accepted` in the logs means it left
  GHL, so check Promotions and Spam before touching the workflow.
- No junk contact appeared from the internal alert.
- On Submit landed on the calendar, not a 404.
- No scrollbar *inside* the form. If there is one, `form_embed.js` did not load.
- The submit button is emerald `#2ECC71`, not GHL's default green.
- Load it on a phone. Under 900px the form stacks below the copy.
