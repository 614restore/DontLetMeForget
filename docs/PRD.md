# DontLetMeForget — Product Requirements (MVP v1)

## 1) Product vision
**Say it, snap it, and never forget where you put it.**

DontLetMeForget helps people quickly log and recover where important things are stored, using voice capture, photos, and structured location breadcrumbs.

## 2) Final MVP scope (must-have)

1. Item creation with **dual-photo capture** (close-up + wider context).
2. **Hierarchical location tree** (Home → Room → Container → Spot).
3. **Voice-to-structure logging** with editable suggestions.
4. **Shared household sync** with member roles.
5. **Private Vault** protected by biometrics or PIN.
6. Search by item name, notes, tags, and location.
7. Item move history with last-known location + timestamp.
8. Basic manual/date reminders.

### Voice logging behavior (core workflow)
Voice is in scope as a first-class input, but as an **assisted** flow:

1. User taps **Add by Voice**.
2. App records and transcribes speech.
3. App parses transcript into suggested fields:
   - Item name
   - Container
   - Room/area/location node
   - Tags
   - Notes
4. User can confirm or edit before saving.
5. Raw transcript remains visible/editable.

**Non-goal for MVP:** fully autonomous voice commands that save without user confirmation.

## 3) Should-have (post-MVP / v1.1)

- AI title/category/tag suggestions from photos and transcript.
- QR labels for bins and storage containers.
- Advanced filters, saved searches, richer exports (including PDF/export improvements).
- Item-level share permissions.
- Better onboarding and guided first-use examples.

## 4) Ignore for now

- Calendar-aware reminders tied to travel/flights.
- Estate/executor mode.
- NFC launch support.
- Insurance-grade inventory workflows.
- Fully automatic AI actions with no confirmation step.
- Heavy offline conflict resolution beyond local cache + queued sync.

## 5) Target users and positioning

### Users
- Couples, families, roommates, and collectors.
- People tracking keys, gifts, documents, keepsakes, and storage bin contents.

### Differentiation
- Voice-first logging for fast capture.
- Visual breadcrumbs with dual photos.
- Household sharing and private vault controls.

## 6) Screen list (MVP)

1. **Home / Search**
   - Quick search bar
   - Recent items / recent moves
   - Actions: Add Item, Add by Voice
2. **Add Item (manual/camera)**
   - Fields: name, notes, tags, location
   - Dual-photo capture flow
3. **Add by Voice**
   - Recording state + transcript preview
   - Parsed suggestions editor
   - Confirm-and-save step
4. **Item Detail**
   - Photos, structured location, notes, tags
   - Move item action
   - Reminder controls
5. **Move History**
   - Chronological location changes + timestamps
6. **Location Tree**
   - Browse/edit hierarchy
7. **Household & Roles**
   - Invite/manage members and roles
8. **Private Vault**
   - Lock/unlock settings (biometric/PIN)
9. **Settings**
   - Sync status
   - Export/reminder basics
   - Permission prompts and privacy controls

## 7) Data model (MVP)

## Core entities

- **users**
  - id, auth_id, display_name, created_at
- **households**
  - id, name, owner_user_id, created_at
- **household_members**
  - household_id, user_id, role (`owner`, `editor`, `viewer`)
- **locations**
  - id, household_id, parent_location_id (nullable), name, level, created_at
- **items**
  - id, household_id, created_by_user_id, name, notes, is_vaulted, created_at, updated_at
- **item_photos**
  - id, item_id, kind (`closeup`, `context`), storage_url, captured_at
- **item_locations**
  - id, item_id, location_id, is_current, moved_at, moved_by_user_id
- **tags**
  - id, household_id, name
- **item_tags**
  - item_id, tag_id
- **voice_logs**
  - id, item_id (nullable until save), user_id, transcript_raw, transcript_edited, created_at
- **voice_suggestions**
  - id, voice_log_id, suggested_name, suggested_container, suggested_location_text, suggested_tags_json
- **reminders**
  - id, item_id, reminder_at, type (`manual`, `date_based`), created_by_user_id, status

## Local-first sync shape

- App writes to local store immediately for capture speed.
- Mutations are queued and synced in background.
- Conflict policy for MVP: last-write-wins + visible edit history where relevant.

## 8) Free vs Pro paywall (recommended)

### Free
- Core item logging (manual + voice-assisted).
- Dual-photo capture.
- Search and location tree.
- Basic household sharing.
- Basic reminders.

### Pro
- Higher limits (items, photos, voice logs).
- Advanced AI parsing quality/features.
- Advanced filters, saved searches, richer exports.
- Enhanced permission controls and vault enhancements.

**Pricing principle:** keep basic voice capture free, because voice-first entry is the main adoption hook.

## 9) Technical direction

- Cross-platform mobile with local-first UX and background sync.
- Supabase for auth, Postgres, storage, and realtime.
- Device-level local database/cache for offline capture reliability.
- Voice implementation:
  - iOS: Apple Speech framework
  - Android: Google speech recognition path (or cross-platform abstraction)
- UX requirements for trust:
  - clear mic permissions
  - visible recording state
  - transcript preview
  - easy correction before save
