# cs-server

Notes and setup guide for running a Counter-Strike dedicated server.

## Which game?

| Game | Steam App ID (dedicated server) | Notes |
|------|----------------------------------|-------|
| Counter-Strike 2 (CS2) | `730` | Current game (Source 2). Server binary is `cs2`. |
| CS:GO (legacy) | `740` | Source 1, superseded by CS2. |
| CS 1.6 | `90` (mod `cstrike`) | GoldSrc, uses HLDS. |

## Requirements

- Linux (Debian/Ubuntu recommended) or Windows, 64-bit
- ~60 GB disk for CS2, 2+ CPU cores (high clock speed matters), 4-8 GB RAM
- Public IP (or port forwarding) and a stable connection
- `steamcmd`
- A **Game Server Login Token (GSLT)** from https://steamcommunity.com/dev/managegameservers (app ID 730) for the server to be listed publicly

## Install (CS2, Linux)

```bash
sudo apt install steamcmd          # or download from Valve
steamcmd +force_install_dir ~/cs2 +login anonymous +app_update 730 validate +quit
```

## Run

```bash
cd ~/cs2/game/bin/linuxsteamrt64
./cs2 -dedicated +map de_dust2 +game_type 0 +game_mode 1 \
      +sv_setsteamaccount <GSLT> -port 27015 -maxplayers 10
```

Game type/mode: casual `0/0`, competitive `0/1`, wingman `0/2`, deathmatch `1/2`.

## Ports

| Port | Proto | Use |
|------|-------|-----|
| 27015 | UDP/TCP | Game traffic, RCON (TCP) |
| 27020 | UDP | SourceTV (if enabled) |
| 27005 | UDP | Client port |

## Configuration

Config lives in `game/csgo/cfg/`:

- `server.cfg` — hostname, rcon password, rates, gameplay cvars
- `autoexec.cfg` — executed at startup
- `gamemode_*_server.cfg` — per-mode overrides

Common cvars: `hostname`, `rcon_password`, `sv_password`, `sv_cheats 0`, `mp_roundtime`, `mp_freezetime`, `mp_maxrounds`, `mp_friendlyfire`, `sv_lan 0`.

## Plugins / Modding

- **Metamod:Source** — plugin loader (edit `gameinfo.gi` to load it)
- **CounterStrikeSharp** — C# plugin framework for CS2
- **CS 1.6**: AMX Mod X + Metamod
- Common plugin types: admin tools, stats, skins, retakes, match systems (e.g. MatchZy, Get5 for CS:GO)

## Security & Operations

- Use a strong `rcon_password`; never commit it or your GSLT to git
- Run as a non-root user; firewall everything except the game ports
- Rate-limit / use DDoS protection if public
- Keep updated: re-run `app_update 730 validate`
- Use `systemd` or `tmux`/`screen` to keep the server running

## Troubleshooting

- Server not listed: check GSLT validity, `sv_lan 0`, port forwarding
- Clients can't connect: verify UDP 27015 is open
- Version mismatch: update the server with SteamCMD

## Resources

- https://developer.valvesoftware.com/wiki/Counter-Strike_2/Dedicated_Servers
- https://docs.cssharp.dev
- https://www.metamodsource.net
