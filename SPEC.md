# Circle Companion: Version 1 Specification

Nov 25, 2026 · @Walid Abu Saifan

## Goal and principles

**Goal.** Cohort zero of the Technology, Society & Strategic Foresight Learning Circle opens on the app it will keep, so no learner is ever migrated mid-cohort. Version 1 must serve the circle, not only individual progress tracking.

**Principles**

1. **Learning first.** The app supports the weekly rhythm (learn, apply, write, circle) and never becomes the point. Walid's own Week 1 starts now on the current companion, regardless of build progress.
2. **Freirean by design.** Quests, praxis badges and paths stay; no streaks, leaderboards, penalties or public ranking.
3. **Private by default.** A member's notes and reflections are theirs alone unless they choose to share.
4. **Files are the source of truth.** Every member can export everything they wrote as plain Markdown at any time.
5. **Open and portable.** Open-source front end and back end (PocketBase, MIT licence), self-hosted from day one on the circle's own small server, so there is never a migration.
6. **Multilingual-ready.** All interface text lives in one language file from day one; English ships first, Arabic (right to left) and French follow without a rebuild.
7. **No app stores.** An installable web app (PWA) served from the circle's own site at `/app`, working on Android, iPhone and laptop.

## Users and the two spaces

| Role | Who | Can |
| --- | --- | --- |
| **Member** | Anyone admitted to the circle | Use their private space; read and contribute to the circle space |
| **Convenor** | Walid, and later any member he names | Everything a member can, plus publish weekly content, manage members and set session roles |

Sign-in is by email (a one-time link), so no Google or other platform account is needed. New members join only by invitation from a convenor.

**Private space.** Visible only to the member: learning notes, signals, reflections and journal, review cards, quest progress and badges, placement result.

**Circle space.** Visible to all members of the circle: the week's guide and library, session agendas and shared notes, the rotating role roster, the dialogue log, and any signal or note a member chooses to share. Members can comment on shared items; only the author can edit or withdraw their own.

## Version 1 scope

**In version 1**

- Email sign-in by invitation; profile with name, languages and placement code.
- Weekly guide and library for every week the convenor has published, with core and deepening tracks shown per member.
- Private learning notes with the 250 to 400 word guide, signals with the diversity tags, journal (energy, focus, what the AI flattened), questions, and spaced-review cards.
- Quests, praxis badges and paths, exactly as in the current companion.
- Circle sessions: agenda, shared notes, attendance (in the room or online), and the call link.
- Role roster that rotates per module.
- Dialogue log, and sharing a signal or note into the circle with comments.
- Convenor tools: publish week content, invite and remove members, set roles.
- Export of everything a member wrote as Markdown files in one download.
- Installable on phone and laptop; readable offline for the current week.

**Deliberately left out of version 1**

- Arabic and French interfaces (prepared for, shipped in 1.1).
- AI features inside the app; members keep using the assistant of their choice outside it.
- Notifications and email digests.
- Several circles in one installation, which is the Mycelium step.
- Native app-store apps.

Each exclusion keeps the build small enough to finish before the opening circle.

## Screens

The bottom navigation keeps the five tabs members already know from the current companion, plus one for the circle.

| Tab | What it shows |
| --- | --- |
| **Home** | Greeting, path and weekly progress ring, the next task, the 13-week trail, quick actions (time, signal, question, dialogue) |
| **Learn** | The week's core question, the steps, the library with the member's track marked, and the learning note editor |
| **Circle** | Next session (time, room, call link), agenda, shared notes, this module's role roster, shared signals and notes with comments |
| **Quests** | Open, on-the-road and completed quests; pose your own |
| **Badges** | Praxis badges and the path from Seeker to Gardener |
| **Reflect** | Review cards, weekly journal, my questions, export |

**Convenor view.** A separate screen, visible only to convenors, for publishing a week (question, steps, resources), scheduling a session, inviting members and assigning roles.

The visual language carries over from the website: deep indigo, saffron and embroidery red, Bricolage Grotesque and Instrument Sans, and the living mycelium background that grows with the member's activity.

## Data model and privacy rules

Every table carries a `circle_id`, even though version 1 runs one circle. This one column is what later lets the same installation host many circles (the Mycelium step) without migrating anyone's data.

| Table | Holds | Who can read | Who can write |
| --- | --- | --- | --- |
| `circles` | Name, current week, languages | Members of the circle | Convenors |
| `members` | Name, role (member or convenor), languages, placement code, tracks | Members of the same circle | The member (own profile); convenors (role) |
| `weeks` | Week number, module, core question, steps | Members | Convenors |
| `resources` | Week, title, type, link, access note, track (core or deepening), language | Members | Convenors |
| `notes` | Learning notes, with a shared flag | Author; whole circle only if shared | Author |
| `signals` | Signal, source, why it matters, domain, region, strength, shared flag | Author; whole circle only if shared | Author |
| `journal` | Energy, focus, what worked, what the AI flattened | Author only | Author |
| `questions` | Member's own questions and status | Author only | Author |
| `review_cards` | Concept, explanation, review dates | Author only | Author |
| `quests` / `quest_progress` | Quest definitions; each member's status and findings | Definitions: members. Progress: author | Definitions: convenors. Progress: author |
| `badges` / `badge_claims` | Badge definitions; each member's claims and stories | Definitions: members. Claims: author, shared if chosen | Author |
| `sessions` | Date, room, call link, agenda, shared notes | Members | Convenors (agenda); members (shared notes) |
| `roles` | Module, member, role | Members | Convenors |
| `dialogue` | Who, topic, disagreement, what was learned | Author; circle if shared | Author |
| `comments` | Comment on a shared item | Members | Author |

**How privacy is enforced.** PocketBase API rules on every collection, not the app's screens: the server itself refuses to return another member's private records, so a bug in the interface cannot leak them. Every rule above becomes a policy written and tested before any screen is built.

**Content.** The weekly guide, resources, quests and badges are seeded from the current dashboard sheet, so nothing written so far is retyped.

## Server, backups and running costs

| Item | Choice | Cost |
| --- | --- | --- |
| Server | The smallest plan at a provider such as Hetzner or Linode, Ubuntu LTS, in a European data centre (GDPR protection, low latency to the region) | About USD 4 to 6 a month |
| Back end | PocketBase: one open-source program providing sign-in, database, file storage and live updates | Free |
| Domain | A short domain for the circle, used for the app's server address and optionally the site | About USD 10 to 15 a year |
| Email | A transactional email service for sign-in links | Free tiers cover a circle |
| Code | Codeberg repository, mirrored to GitHub | Free |

**Backups.** The whole database is a single folder. A nightly job copies it off the server to a second location (Gerrit's server or another storage space), and a weekly copy is kept for a month. Restoring means copying the folder back.

**Maintenance.** Updating PocketBase is replacing one file; HTTPS certificates renew automatically. A one-page runbook covers updates, backups and restores.

## Build plan and timeline

The dates are proposals, set by "as soon as possible within real limits": a few focused build sessions, then a pilot with one user before anyone else depends on the app.

&#91;embedded content: build plan · 4 phases, 1 gate\]

**Build order.** The database and its privacy rules come first and are tested before any screen exists; then sign-in and the private space; then the circle space; then the convenor tools and the export. Each step is usable on its own, so a slip still leaves a working app.

**Where it is built.** In a public repository on Codeberg, mirrored to GitHub, with this specification saved in it as `SPEC.md` so every tool reads the same document. The interface is plain HTML, CSS and JavaScript with no build tools, so it stays readable to anyone who knows the web. The finished app is published to `/app` on the circle's Codeberg site; PocketBase runs on the circle's server.

## Who builds what

One lead builder, one source of truth. The other tools work on separate branches, and only Walid merges into the main line.

| Tool | Subscription | Role | Typical tasks |
| --- | --- | --- | --- |
| Claude Code | DOT's Claude plan, included usage only (DOT has agreed, provided it adds no cost) | Lead builder | Architecture, collections and API rules, main screens, deployment |
| Codex | ChatGPT Plus | Independent reviewer | Reviews the privacy rules and security against `SPEC.md`, writes tests; reports findings in `REVIEW.md` rather than changing code |
| Gemini CLI | Google AI Pro | Supporting builder | Self-contained branches: Markdown export, Arabic and French text files, documentation |

Spreading the work across three subscriptions keeps each within its included limits, and having different models check each other is the Module 9 principle in practice: no single AI is the authority.

## What Walid needs to do

Kept deliberately small, about an hour in total before the pilot.

- [ ] Rent the small server and register the circle's domain, with your **personal** email and payment, not DOT's.
- [ ] Create the Codeberg account and repository (see the site walkthrough), so the app and the site live together.
- [ ] Install the build tools on your laptop: Claude Code, Codex and Gemini CLI (see the step-by-step guide).
- [ ] Choose an email sending service for sign-in links before inviting anyone (the build session will set it up with you).
- [ ] Put the Framaforms and Crab.fit links into the website now, so interest gathers while the app is built.
- [ ] Start your own Week 1 on the current companion this week.
- [ ] During the pilot, use the app for your real learning and note every friction point.

**Decisions already made.** PocketBase on the circle's own server in Europe; Codeberg with a GitHub mirror; Claude Code leads, Codex reviews, Gemini CLI supports; one circle in version 1, modelled for many; English first, Arabic and French in 1.1; no AI inside the app; the circle opens only after the pilot gate passes.
