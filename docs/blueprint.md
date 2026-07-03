# Private Emotional Support Bot — Bot specification

**Archetype:** support

**Voice:** empathetic and supportive — write every user-facing message, button label, error, and empty state in this voice.

A Telegram bot offering on-demand private emotional support through compassionate messages, coping suggestions, and optional scheduled check-ins. Focuses on encouragement and brief interactions without replacing professional mental health services.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- Individuals seeking private emotional support
- Users needing quick encouragement or check-ins

## Success criteria

- Users receive contextual support messages within 2 seconds
- Scheduled check-ins delivered at user-selected times
- Crisis keywords trigger safety response with hotlines

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Open main menu with support options
- **Get support now** (button, actor: user, callback: support:now) — Request immediate support message
  - inputs: user ID
  - outputs: support message with coping suggestion
- **Set daily check-in** (button, actor: user, callback: checkin:setup) — Configure recurring check-in schedule
  - inputs: time, frequency
  - outputs: confirmation message

## Flows

### Onboarding
_Trigger:_ /start

1. Display welcome message
2. Offer language selection (RU/EN)
3. Present main menu options

_Data touched:_ user profile

### Immediate Support
_Trigger:_ support:now or text message

1. Generate contextual support message
2. Add coping suggestion
3. Offer follow-up question with Yes/No buttons

_Data touched:_ conversation log

### Scheduled Check-in
_Trigger:_ cron schedule

1. Send check-in message at user-selected time
2. Log interaction
3. Store response if user replies

_Data touched:_ scheduled check-ins

### Crisis Handling
_Trigger:_ detected crisis keywords

1. Send safety message
2. Display crisis hotline list
3. Log incident without storing content

_Data touched:_ conversation log

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **user_profile** _(retention: persistent)_ — Telegram user metadata
  - fields: user ID, display name, language (RU/EN), timezone, check-in schedule
- **support_messages** _(retention: session)_ — Template categories for responses
  - fields: encouragement, grounding, breathing, tips, quotes
- **conversation_log** _(retention: persistent)_ — Last 10 messages per user
  - fields: message text, timestamp
- **scheduled_checkins** _(retention: persistent)_ — User-configured check-in settings
  - fields: frequency, time, active status

## Integrations

- **Telegram** (required) — Private chat messaging and scheduled delivery
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Configure crisis keyword list
- Set admin error notification ID
- Adjust check-in message templates

## Notifications

- Admin alerts for system errors
- Optional check-in messages to users

## Permissions & privacy

- No message content stored beyond 30 days
- Crisis responses don't log user content
- User can unsubscribe at any time

## Edge cases

- User sends crisis keywords in group chat (ignored)
- User changes mind after scheduling check-ins
- Message templates needing localization

## Required tests

- Verify check-in delivery at user-selected time
- Test crisis response triggers
- Validate language toggle functionality

## Assumptions

- Users understand this isn't professional therapy
- Default RU language handles localization nuances
- 30-day data retention meets privacy requirements
