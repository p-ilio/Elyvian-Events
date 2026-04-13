# Elyvian Events

> A full-stack bilingual event management platform built for an events company in Athens, Greece.

**Live site:** [elyvianevents.com](https://elyvianevents.com)

<img width="1918" height="946" alt="Image" src="https://github.com/user-attachments/assets/ae6c0436-b0bf-41ec-a1a0-1355d49ec692" />
---

## What this is

A client project. An events company in Athens needed a custom platform to manage and publish their events, handle registrations, collect newsletter subscribers, and present everything bilingually (English and Greek). I built the whole thing solo: design, frontend, backend, CMS, deployment.

It's also my primary portfolio project, and the thing I learned the most from.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React + Vite |
| Routing | React Router |
| Backend / CMS | Django + Wagtail (headless) |
| Database | MySQL |
| Styling | Custom CSS — no Tailwind, no CSS Modules |
| SEO | react-helmet-async |
| Payments | Stripe Checkout (Sessions + Webhooks) |
| Email | SMTP |
| Deployment | cPanel shared hosting |

---

## Design System — Neo-Brutalism

<img width="447" height="244" alt="Image" src="https://github.com/user-attachments/assets/8ec3907a-a6dc-4e5c-8820-b8920ea36e8c" />
<img width="357" height="246" alt="Image" src="https://github.com/user-attachments/assets/22db8bdd-b3ef-4bc9-a1fe-5005bf09105f" />

The client wanted something that stood out. I went with a neo-brutalist / comic-book aesthetic: hard offset shadows, halftone dot patterns on every section, no border-radius, no blur, a strict four-color palette.

```
Deep Aegean Navy   #002244   — primary
Comic Yellow       #FFD700   — accent / badges / underlines
Comic Cyan         #00D4FF   — secondary accent
Comic Red          #FF3366   — danger / hover states
```

Typography pairs **Cormorant Garamond** (headings) with **Space Grotesk** (body) — elegant meets technical, which fit the brand well.

**Why no Tailwind?** I wanted to actually understand CSS at a deep level, not assemble utility classes. Writing every shadow, transition, and layout by hand was slower, but I came out the other side knowing exactly why everything looks the way it does. I'd make the same choice again.

**Why no CSS Modules?** The project didn't need the overhead. I went with component-scoped class prefixes that are simple, readable, and collision-free without any build-time magic.

---

## Features

### Public site

<img width="1912" height="945" alt="Image" src="https://github.com/user-attachments/assets/16d4f4c6-4996-4f4e-9e4c-3ea5cbe31ea0" />


- **Bilingual (EN / GR)** — language toggled via React context, persisted client-side, all API content served in both languages
- **Event listings** — category pill filters, pagination, loading/error/empty states
- **Event detail** — photo gallery, pricing tiers, custom registration form fields per event
- **Event registration** — four modes: free (full form), interest (name + email only), paid (Stripe Checkout), invite-only (locked with no form)
- **News / editorial** — magazine-style grid, featured articles, full article view
- **Newsletter** — subscribe/unsubscribe, merged into the news section rather than a separate component
- **Contact form** — SMTP delivery
- **Testimonials** — auto-scrolling marquee
- **Partner logos** — auto-scrolling carousel
- **Stripe** — Checkout Session creation, webhook handler, payment confirmation page

<img width="1916" height="945" alt="Image" src="https://github.com/user-attachments/assets/5769e356-8c8e-4c91-ad51-bc2fc6e8c0b9" />


<img width="1916" height="945" alt="Image" src="https://github.com/user-attachments/assets/eca824c2-a9cd-4a65-8ce2-8d493a0f3593" />
<img width="1916" height="945" alt="Image" src="https://github.com/user-attachments/assets/b6da980d-35d6-4463-acdc-012ab299f232" />

<img width="1916" height="945" alt="Image" src="https://github.com/user-attachments/assets/409d576d-b80e-4f66-80ef-f14edb7c7c7e" />


### Floating UI

- `Navbar` — transparent → white on scroll, hamburger opens a split overlay with gallery images pulled from the CMS
- `EventCountdownTicker` — fixed left panel, slides in from the side, live countdown to the next upcoming event
- `ScrollSubscribeBar` — fixed right panel, appears after scroll threshold, dismissible with a cooldown period
- `ClipPathTransition` — halftone curtain wipe between routes, the Elyvian logo assembles in the center mid-transition
- `BackToTop` — fixed bottom-right

<img width="328" height="280" alt="Image" src="https://github.com/user-attachments/assets/0bdb2bf9-db0f-435f-951c-1a31f7851795" />


**The transition animation** was one of the things I'm proudest of. Getting the clip-path wipe, the halftone overlay, and the logo assembly to all play together at the right timing took a lot of iteration but it gave the site a distinct personality that I haven't seen elsewhere.

### Admin (Wagtail CMS)


- Custom dashboard that overrides Wagtail's default, styled with the full Elyvian design system
- Live stats row: events, articles, subscribers, registrations, contact messages
- All content: events, news, testimonials, partners, site settings are fully manageable without touching code

**Why Wagtail?** I needed a CMS flexible enough to support per-event custom form fields, gallery images, registration types, and pricing all from the admin UI. Wagtail gave me that flexibility out of the box. The tradeoff is a steeper learning curve than something like WordPress, but the result is a proper headless CMS that the client can actually use.

---

## Architecture

Frontend and backend live on separate subdomains. The React app talks to the Django REST API, Wagtail is fully headless, no server-rendered templates on the public side.

The backend exposes REST endpoints covering event listings, registration, Stripe checkout, news, newsletter, contact, and CMS-managed content. The API uses Django REST Framework serializers with full pagination, filtering, and error handling.

---

## Security

This is the area I underestimated going in. The first version had issues I only found during a proper code review ( input sanitization gaps, header injection vectors, etc.). None of them were obvious until I went looking.

What's in place now covers input validation, output sanitization, header protection, webhook signature verification, error message separation (validation errors surface to the user, server internals stay internal), and admin access hardening.

The lesson: security review shouldn't be a phase you do after launch. I moved it earlier in my process on every project since.

---

## Deployment — The Hard Part

The client wanted cPanel hosting. That ruled out Docker, SSH tooling, and most of the deployment workflows I'd read about. Everything had to go through cPanel's File Manager and web UI.

Some specific things that bit me:

- **Database driver** — the default MySQL driver requires C compilation, which isn't available on shared hosting. Had to switch to a pure-Python alternative.
- **Database key length limit** — hit a migration failure caused by the hosting provider's byte limit on index keys. Fixed by adjusting the character encoding across all tables.
- **Media files in production** — the standard Django approach for serving media doesn't work outside of development mode. Had to write a custom URL routing solution.
- **Stripe SDK breaking change** — a major version upgrade changed how the SDK returns event objects. Spent longer than I'd like to admit debugging a server error that only appeared on live webhooks, not in local testing.

---

## What I'd Do Differently

- **Start the security review earlier.** Treating it as a late phase meant retrofitting fixes instead of building them in from the start.
- **Pin dependency versions from day one.** The Stripe SDK breaking change was avoidable.
- **Plan the deployment environment earlier.** I designed the project assuming a standard dev setup and hit the shared hosting constraints late. Knowing about the driver limitations, the WSGI configuration, and the subdomain architecture upfront would have saved hours.

---

## About

Built by **Panagiotis Iliopoulos**, sole developer and full stack. Professional client deliverable.
Design, architecture, backend, frontend, deployment have all been done independently.
