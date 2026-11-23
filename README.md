# Keepsake

**Remember the dates that matter.**

Keepsake is a private, offline-first personal occasions calendar designed to help you remember birthdays, anniversaries, milestones, and other meaningful dates.

Built as a cross-platform desktop application, Keepsake combines a beautiful calendar experience with personal occasion management, countdowns, and timely notifications.

No accounts. No cloud dependencies. No unnecessary complexity. Just the dates that matter, organized in one place.

---

## Overview

Keepsake is primarily a date-oriented application, not a task manager or productivity tool.

Its central purpose is to help users organize important dates, associate them with people, visualize upcoming occasions, and receive reminders at appropriate times.

Although birthdays are the primary inspiration behind the project, Keepsake supports a broader range of personal occasions and general calendar events.

The application is designed to work entirely offline, keeping personal information on the user's own device.

## Core Features

### 1\. Calendar

The calendar is the central part of Keepsake.

- Monthly calendar view.
- Weekly calendar view.
- Agenda view.
- Year overview.
- Navigate between dates and months.
- Quickly return to the current date.
- Visual indicators for scheduled occasions.
- Multiple occasions on the same date.
- Filter calendar entries by occasion type or person.
- Regional calendar preferences, including the first day of the week.

### 2\. People Management

Maintain a personal directory of people associated with important occasions.

- Add, edit, and remove people.
- Store first and last names.
- Optional birth dates and birth years.
- Relationship labels, such as family, friend, or colleague.
- Optional profile pictures.
- Personal notes.
- Associate multiple occasions with a person.
- View a person's upcoming and previous occasions.
- Search and organize the people directory.

### 3\. Occasion Management

Create and manage meaningful dates independently or associate them with one or more people.

Initial occasion types include:

- Birthdays.
- Wedding anniversaries.
- Relationship anniversaries.
- Weddings.
- Memorial days.
- Graduation anniversaries.
- Personal milestones.
- General events.
- Custom occasions.

Additional capabilities:

- One-time and annually recurring occasions.
- Custom occasion types.
- Optional descriptions and locations.
- Multiple people associated with an occasion.
- Individual notification preferences.
- Optional birth years for age calculations.

### 4\. Upcoming Occasions

A chronological overview of important dates.

- Today's occasions.
- Tomorrow's occasions.
- Upcoming occasions this week.
- Upcoming occasions this month.
- Future occasions.
- Days remaining until an occasion.
- Upcoming birthdays and anniversaries.
- Relevant age information when a birth year is available.

### 5\. Notifications

Keepsake supports both native operating-system notifications and an in-app notification center.

- Configurable advance notifications.
- Multiple notification schedules per occasion.
- Default notification preferences.
- Custom notification times.
- Enable or disable notifications individually.
- Notification history.
- Read and unread notification states.
- Missed notification reconciliation.

Example: A birthday can have notifications scheduled seven days before, one day before, and on the actual date.

### 6\. Settings and Personalization

- Light and dark themes.
- Warm, customizable visual appearance.
- Regional date formatting.
- 12-hour and 24-hour time formats.
- Monday or Sunday as the first day of the week.
- Local timezone preferences.
- Default notification settings.
- Application startup preferences.
- Window state restoration.

### 7\. Data Management

Users retain control over their information.

- Local SQLite database.
- JSON data export and import.
- SQLite database backups.
- Data restoration.
- ICS calendar import and export.
- Local profile pictures and attachments.
- Database migration support.

---

## Design Philosophy

Keepsake should feel warm, personal, elegant, and approachable.

Rather than looking like a conventional enterprise calendar, the interface should communicate the personal nature of the information it contains.

### Visual Direction

The preferred visual identity is warm and understated.

Suggested colors include:

- Warm ivory.
- Dusty beige.
- Muted terracotta.
- Sage green.
- Dusty rose.
- Forest charcoal.

Occasion types may use subtle distinguishing colors.

The interface should prioritize:

- Generous spacing.
- Clear visual hierarchy.
- Rounded surfaces and subtle borders.
- Gentle transitions and animations.
- Readable typography.
- Minimal visual clutter.
- Consistent iconography.

### Iconography

All application icons should use Lucide icons, imported from [lucide.dev](<https://lucide.dev/>).

Icons should be consistent in size, stroke weight, and visual treatment.

### User Experience Principles

1. Dates should always take priority over tasks.
2. Adding an occasion should be quick and straightforward.
3. People and occasions should be naturally interconnected.
4. Important upcoming dates should be immediately visible.
5. Notifications should be useful rather than intrusive.
6. The application should remain easy to understand.
7. Personal information should remain private.

---

## Technology Stack

Keepsake is built using the following technologies:

| Technology | Purpose |
| --- | --- |
| Tauri v2 | Cross-platform desktop application framework |
| React | User interface |
| TypeScript | Type-safe application development |
| Tailwind CSS | Styling and design system |
| Rust | Backend logic and native desktop integration |
| SQLite | Local relational database |
| Lucide | Application iconography |

### Architectural Principles

- React handles the user interface and presentation.
- Rust handles application logic, database operations, and native integrations.
- SQLite is the primary persistent data store.
- Tauri provides communication between the frontend and backend.
- Application data is stored locally.
- No external backend or cloud service is required.

---

## Privacy and Offline Operation

Privacy is a fundamental part of Keepsake.

The application is intended to operate completely offline.

It will not require:

- User accounts.
- Online authentication.
- Cloud synchronization.
- External APIs.
- Analytics or telemetry.
- Remote image hosting.
- External font services.

Personal information, occasions, notification preferences, and application settings remain on the user's device.

Backups and exports are controlled by the user.

---

## Supported Platforms

Keepsake aims to support:

- Windows
- macOS
- Linux

Native notifications, system tray functionality, application startup, and other operating-system integrations will be implemented and tested separately for each platform.

---

## Development Priorities

Development will follow a progressive approach.

1. Project foundation and database architecture.
2. People management.
3. Occasion management and recurrence.
4. Date calculations and calendar functionality.
5. Dashboard and upcoming occasions.
6. Notification scheduling and delivery.
7. Native desktop integration.
8. Backup, restoration, and calendar interoperability.
9. Visual refinement, accessibility, and cross-platform testing.

Reliability, date correctness, privacy, and usability take priority over feature quantity.

---

## Future Possibilities

Potential features for future releases include:

- Advanced recurrence options.
- Calendar widgets.
- Additional calendar import formats.
- Custom notification sounds.
- More extensive personalization.
- Enhanced occasion statistics.
- Optional database encryption.
- Advanced native notification scheduling.
- Additional occasion categories.

These features are not prerequisites for the initial release.

---

## Project Philosophy

Keepsake is built around a simple idea:

**Some dates deserve to be remembered.**

It should make remembering those dates effortless, while remaining private, dependable, and pleasant to use.

No unnecessary productivity features. No online dependencies. No distractions.

Just a personal place for the dates that matter.

---