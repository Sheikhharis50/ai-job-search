# Search Queries for Job Scraper

<!-- SETUP: Customize these queries based on your skills, target roles, and location -->

## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first. Shipped country-agnostic CLIs include `linkedin-search` and `freehire-search`; Danish demos and any skill you add with `/add-portal` are included the same way. You do **not** need a matching `site:` line below for those CLIs to run.

The `site:` query templates in this file are the **WebSearch fallback** — for portals without a CLI, company career pages, or when a CLI fails.

**Language scope:** write every query category in every language listed in your CLAUDE.md Languages table (typically 1-2, sometimes more). A posting requiring a language you have *not* declared, as a job condition, is excluded before scoring; a posting requiring a *higher level* than you declared in a language you *do* work in is flagged for your own judgment, not excluded — see `04-job-evaluation.md`'s Language Gate, the single source of truth for this rule. Translate each category's keywords rather than machine-translating word-for-word (e.g. "Frontend Developer" -> "Desarrollador Frontend", not a literal word-for-word translation) if you work in more than one language.

## Search Sites

Primary (remote-first job boards; enabled CLIs cover the country-agnostic case):
- **linkedin.com/jobs** - LinkedIn job listings (filter: Remote); also covered by `linkedin-search` CLI
- **freehire.com** - covered by `freehire-search` CLI
- Danish portal demos (Jobindex, Jobbank, Jobdanmark, Jobnet) are shipped but left **disabled** - not relevant to a Pakistan-based remote job search targeting UK/EU/UAE/US employers. Re-run `/setup --section search` to enable if this changes.

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for known target companies

## Query Categories

Queries are grouped by priority. Write **each category in every language from your Languages table** (see Language scope above). Combine each query with your location terms (e.g. your city, region, or metro area) where the site supports it.

**Organize by function, not job title.** The same underlying work carries different titles across companies and markets (a "Data Scientist" role at one employer may be posted as "Insights Analyst" or "Data Consultant" at another). Name each priority category after the function it covers, and list several plausible job titles as query variants within that category rather than betting an entire priority tier on one exact title string.

### Priority 1: AI Product Frontend Engineering

These match your strongest and most desired career direction: senior frontend roles building AI-powered product features.

```
site:linkedin.com/jobs "Senior Frontend Engineer" "AI" Remote
site:linkedin.com/jobs "AI Product Engineer" Remote
site:linkedin.com/jobs "LLM Frontend Engineer" Remote
site:linkedin.com/jobs "Senior React Developer" Remote
```

### Priority 2: Frontend / Product Engineering (general)

Broader senior frontend and product engineering roles.

```
site:linkedin.com/jobs "Frontend Engineer" React Next.js Remote
site:linkedin.com/jobs "Product Engineer" React TypeScript Remote
site:linkedin.com/jobs "Senior Frontend Developer" Remote
```

### Priority 3: Full Stack (React-leaning)

Adjacent roles you could pivot into.

```
site:linkedin.com/jobs "Full Stack Developer" React Node Remote
site:linkedin.com/jobs "Full Stack Engineer" TypeScript Remote
```

### Priority 4: Broader Technical / Consulting

Wider net for general technical roles.

```
site:linkedin.com/jobs "React developer" Remote
site:linkedin.com/jobs "Frontend Consultant" Remote
```

## Location Filter

This is a remote-only search - no commute-distance filter applies. Instead, verify working-hours overlap:
- **Ideal**: UK, EU, UAE, or US-based teams (full or majority overlap with Lahore, Pakistan working hours per the candidate's stated availability)
- **Acceptable**: Any fully async / flexible-hours remote role
- **Borderline**: Teams requiring overlap outside UK/EU/UAE/US that still leave a reasonable working window
- **Too far**: On-site or hybrid roles requiring relocation to a specific city (deal-breaker - see CLAUDE.md Deal-breakers)

## Employer Exclusion Filter

Exclude postings from companies, staffing agencies, or recruiters headquartered in or primarily
hiring out of **India, Bangladesh, Sri Lanka, or Pakistan** — this is a candidate preference
(pay and work-culture concerns with South Asian outsourcing/body-shop firms), not an eligibility
issue, since the candidate is themselves Pakistan-based.

- Applies to the **employer/agency's base**, not the job's remote-eligibility region. A South
  Asian outsourcing firm placing developers on Western client teams is still excluded even if the
  role itself is "100% remote, work from anywhere."
- Judge from what's visible in the posting/company profile: company HQ location, "About us"
  text, staffing/outsourcing self-description, or a LinkedIn company page listing India/
  Bangladesh/Sri Lanka/Pakistan as the primary office. Don't exclude on a weak signal alone (e.g.
  a generic "global talent" phrase) — exclude when the employer's base is reasonably clear from
  the posting or company page.
- This does **not** exclude direct-hire Western/global companies that happen to have an
  engineering office in one of these countries (e.g. a US company with a Bangalore R&D center) —
  the filter targets outsourcing/staffing agencies and India/Bangladesh/Sri Lanka/Pakistan-based
  employers of record, not every company with any presence there.
- `/scrape` Step 3 (Quick Fit Assessment) applies this before assigning a fit level: an employer
  matching this filter gets excluded from the presented results entirely (not just downgraded),
  the same way a Language Gate FAIL is excluded, and the reason is available if the user asks why
  a result is missing.
- `/rank` applies this as part of the Location & Logistics dimension: an employer matching this
  filter gets `location_verdict: FAIL` regardless of remote-eligibility wording, with the reason
  noted alongside the veto.

## Language Filter

Your working languages and levels are in CLAUDE.md's Languages table. When filtering scraped results, apply `04-job-evaluation.md`'s Language Gate: a posting requiring a language you haven't declared at all is excluded; a posting requiring a higher level than you declared in a language you do work in is not excluded, flag it clearly instead (see `job-scraper/SKILL.md`'s Step 3 "Quick Fit Assessment" for how the flag surfaces in `/scrape` output). Postings simply *written* in a language you don't work in, that don't require it on the job, are fine.

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
