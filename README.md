# Quickfire Trivia Party

A real-time multiplayer trivia game for 2–8 players. One player hosts a room and shares a 4-letter code; everyone else joins from their own phone, tablet, or laptop — no downloads, no accounts, no sign-up. Five rapid-fire questions, a live leaderboard after every round, and a podium at the end.

**▶ Play it live: https://quickfire-trivia-party.netlify.app/**

## Features

- **Instant rooms** — create a room, get a 4-letter code, share it (or copy the invite link, which pre-fills the code for anyone who opens it)
- **True real-time sync** — every player's screen advances together: lobby, question, answer reveal, leaderboard, and final standings
- **Speed-based scoring** — a correct answer is worth 1,000 points plus up to 500 bonus points for answering fast, so the leaderboard can flip on any question
- **Zero setup** — a single HTML file, no backend server, no database, no API keys, nothing to install
- **Play anywhere** — works in any modern browser, on phones and desktops alike

## How to play

**Hosting**

1. Enter your name under **Create Room** and tap **Create Room**.
2. You land in the lobby with a 4-letter room code. Share the code — or use **Copy invite link** so friends join with the code already filled in.
3. When at least 2 players are in, tap **Start Game**.

**Joining**

1. Enter your name and the room code under **Join Room** (or open the host's invite link), and tap **Join Room**.
2. Wait in the lobby until the host starts the game.

**The game**

- 5 questions per game, drawn at random from a bank of 20 general-knowledge questions.
- 12 seconds to answer each question — tap one of the four options (A–D).
- After each question there is a 4-second reveal showing the correct answer, then the updated leaderboard.
- After the last question, the final podium shows the winner and full standings. The host can run it back with **Play Again**.

## Scoring

| Result | Points |
| --- | --- |
| Correct answer | 1,000 + speed bonus |
| Speed bonus | Up to 500, scaled by how fast you answered (full bonus instantly, decaying to 0 at the 12-second limit) |
| Wrong answer or time out | 0 |

The exact formula used by the host: `1000 + round(500 × (12 − secondsToAnswer) / 12)`.

## How it works

Quickfire Trivia Party is a **host-authoritative peer-to-peer** app built on [PeerJS](https://peerjs.com/) (WebRTC):

- There is no game server. The host's browser *is* the server: it owns the player list, the question sequence, everyone's answers, and the scores.
- When a host creates a room, the game registers the peer ID `qtp-<roomcode>` with the free public PeerJS cloud broker. Joiners connect directly to that ID, which is why a room code is all you need.
- The host broadcasts every state change — lobby updates, the current question, the reveal, the leaderboard, and the final standings — to all connected players, and players send their answers back to the host, which timestamps and scores them.
- Because the host is authoritative, all screens stay in sync and scoring is consistent, with nothing to deploy or pay for beyond static hosting.

```
Host browser ──registers qtp-CODE──▶ PeerJS cloud broker
Joiner browser ──connects to qtp-CODE──▶ Host browser
Host ──broadcast: question / reveal / leaderboard──▶ All players
Player ──send: answer + elapsed time──▶ Host (scores it)
```

## Tech stack

- **Plain HTML, CSS, and JavaScript** — the entire game (markup, styles, question bank, networking, and game logic) lives in one `index.html` file (~17 KB)
- **PeerJS 1.5.4** (loaded from the unpkg CDN) over WebRTC for peer-to-peer connections, using the free PeerJS cloud broker
- **Hosting:** Netlify (static, deployed from this repository's `main` branch)
- No frameworks, no build step, no environment variables

## Run it locally

Option 1 — just open the file:

```bash
git clone https://github.com/KakoozaJerry/quickfire-trivia-party.git
cd quickfire-trivia-party
# open index.html in your browser
```

Option 2 — serve it (recommended, closest to production):

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then open the local address in two different browsers (or a browser and a phone on the same network via your machine's LAN address) to test hosting and joining. An internet connection is still required so the browsers can reach the PeerJS cloud broker.

## Project structure

```
.
├── index.html   # The whole game: UI screens, styles, 20-question bank,
│                # PeerJS connection/lobby logic, game flow, timers, scoring
└── README.md
```

## Roadmap

- More question packs (and themed packs)
- Sound effects and background music
- A more resilient sync layer (dedicated relay option for networks where peer-to-peer connections are restricted)
- Custom round counts and question timers

## About this project

Built by **Abraham Kakooza** for Handshake's AI Skills Studio **"Create a Multiplayer Game"** mission — an AI-assisted build, designed and assembled with an AI assistant and published as a [Handshake Showcase project](https://app.joinhandshake.com/ai-showcase/projects/3701950).
