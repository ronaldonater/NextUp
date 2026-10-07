<p align="center">
  <img src="assets/NextUp.svg" width="148" alt="NextUp!">
</p>

<h1 align="center">osu! Auto Host Rotate Bot</h1>

<p align="center">
  A self-hosted Bancho multiplayer referee bot with fair local ELO, automatic host rotation, map regulations, and a local lobby dashboard.
</p>

<p align="center">
  <a href="https://ronaldonater.com/osu-ahr">Command reference</a>
  &nbsp;•&nbsp;
  <a href="https://github.com/ronaldonater/osu-ahr-bot/issues">Report an issue</a>
  &nbsp;•&nbsp;
  <a href="https://ko-fi.com/ronaldonater">Support the project</a>
</p>

---

## What it does

The bot creates and manages osu! multiplayer lobbies through Bancho. It rotates host turns automatically, keeps local competitive records for every game mode, validates selected beatmaps against configurable regulations, and provides a browser-based control panel at <code>http://localhost:3000</code>.

| Lobby flow | Competitive play | Dashboard |
| --- | --- | --- |
| Queue-based host rotation | Per-mode ELO and player statistics | Create and manage multiple lobbies |
| Five-minute map-pick and start timers | Fractional placement ELO with dynamic K-factor | Configure regulations before or after creation |
| Voting for skip, start, abort, and kick | Global best-score and leaderboard notices | Browse player stats and activity logs |
| Optional random Team VS events | Local and global leaderboards | Edit names, passwords, ranked state, and more |

## Quick start

### Requirements

- Node.js 22 or newer
- An osu! OAuth application
- A dedicated osu! account for the Bancho bot
- A legacy osu! API key, required by bancho.js

### Install and configure

~~~sh
npm install
Copy-Item .env.example .env
~~~

Open <code>.env</code> and set your credentials:

~~~dotenv
DATABASE_URL="file:./dev.db"
OSU_CLIENT_ID="your_client_id"
OSU_CLIENT_SECRET="your_client_secret"
BANCHO_USERNAME="your_bot_username"
BANCHO_PASSWORD="your_bot_password"
BANCHO_API_KEY="your_legacy_api_key"
DASHBOARD_TOKEN="a_long_random_secret"
ADMIN_OSU_IDS="your_osu_profile_id"
PORT=3000
~~~

<code>ADMIN_OSU_IDS</code> uses numeric osu! profile IDs, separated by commas when you need more than one administrator.

### Start the bot

~~~sh
npm run db:generate
npm run db:migrate
npm run dev
~~~

Then open [http://localhost:3000](http://localhost:3000), enter the <code>DASHBOARD_TOKEN</code> from your <code>.env</code>, and create a lobby. For a production-style build, use:

~~~sh
npm run build
npm start
~~~

## Local dashboard

The dashboard is the simplest way to create a room and apply its starting rules before anybody joins. It supports:

- Lobby title, optional password, game mode, team mode, and win condition
- Map-star, length, BPM, AR, HP, OD, CS, map-status, and year regulations
- Ranked or unranked lobbies, Free Mod, map conversion settings, and random-event chance
- Active-lobby editing, room closure, optional inactive-room recreation, and a per-lobby activity console
- Per-mode player lookup, player-stat editing, and paginated ELO leaderboards

The dashboard HTML, CSS, and client script live in [public/](public).

## Commands

Players can run <code>!cmds</code> in a lobby to receive the complete, current command reference:

> [ronaldonater.com/osu-ahr](https://ronaldonater.com/osu-ahr)

| Command | Purpose |
| --- | --- |
| <code>!queue</code> | Show the current host queue |
| <code>!skip</code> | Pass the host turn, or vote to skip when you are not host |
| <code>!start</code> | Start the match as host, or vote to start |
| <code>!ostats [username] [mode]</code> | Show local ELO and match statistics |
| <code>!rank [username] [mode]</code> | Show a player's local ranking |
| <code>!top [local] [mode]</code> | View the local or global ELO leaderboard |
| <code>!bestscore [username]</code> | Show a global best score for the selected map |
| <code>!autoskip on/off</code> | Automatically skip your host turn |
| <code>!donate</code> | Share the project support link |

Administrative commands begin with <code>*</code>; permissions are controlled by <code>ADMIN_OSU_IDS</code>. The command reference includes the complete regulation and administrator command list.

## ELO and match records

Ranked Head-to-Head games use fractional placement ELO. All finishers receive a result based on placement, tied scores share an averaged rank, and disconnects tie for last. Ratings are compared with the lobby's average ELO.

| Matches played before the game | K-factor |
| --- | ---: |
| 0–10 | 40 |
| 11–100 | 24 |
| 101+ | 16 |

Each game mode maintains its own ELO and stats. A player must complete at least three matches to appear on ELO leaderboards, though ratings continue updating before then. One-player games and random events are unranked and do not change ELO or competitive statistics.

## Project structure

~~~text
src/
  adapters/       Bancho IRC and osu! API integrations
  services/       ELO, map regulations, team balancing, and votes
  lobby-controller.ts
  index.ts        Express API and dashboard server
prisma/
  schema.prisma   SQLite data model
public/
  index.html      Local dashboard
  app.js          Dashboard behavior
  old-osu-theme.css
~~~

## Scripts

| Script | Description |
| --- | --- |
| <code>npm run dev</code> | Run the bot with watch mode |
| <code>npm run build</code> | Compile TypeScript to <code>dist/</code> |
| <code>npm start</code> | Run the compiled build |
| <code>npm test</code> | Build and run ELO tests |
| <code>npm run db:generate</code> | Generate the Prisma client |
| <code>npm run db:migrate</code> | Create or apply Prisma migrations |

## Notes for public deployment

Use a dedicated bot account and keep its credentials and dashboard token private. For a remote deployment, use a durable database such as PostgreSQL, serve the dashboard behind TLS, and configure reconnect and recovery behavior appropriate for your host.

## Credits

The local dashboard theme is adapted from [Stern](https://github.com/osuTitanic/titanic/tree/main/services/stern), inspired by the classic osu! website.
