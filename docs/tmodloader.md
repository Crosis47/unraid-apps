# tModLoader on Unraid

Install **tModLoader-Web-Managed-Server** from Unraid Community Applications to
run a modded Terraria dedicated server with a web dashboard for worlds, Workshop
mods, players, settings, and backups.

## Install and set up

1. Open the **Apps** tab in Unraid, search for **tModLoader**, and install
   **tModLoader-Web-Managed-Server**.
2. Review the **Game port**, **Dashboard port**, **Server data**, and **Backup
   archives** fields. Choose available host ports and dedicated storage folders.
   For a new server, start with empty folders. The default data path is
   `/mnt/user/appdata/tmodloader-crosis47/data`; the backup path is
   `/mnt/user/appdata/tmodloader-crosis47/backups`.
3. Leave **Enable Dashboard** set to `1` and **Manage game settings with** set to
   `web`. Optional fields are available under **Show more settings**; the game
   settings can be managed in the dashboard after setup.
4. Apply the configuration and start the container. First startup needs internet
   access to download and install the game runtime.
5. Open the container's logs in Unraid and find the one-time setup code. Open
   **WebUI** from the container menu, enter the code, and create your administrator
   credential. The game waits until administrator setup is complete.
6. Use the dashboard to create or select a world, choose mods, and configure your
   server. Set a player password if needed; it is separate from the administrator
   credential. The default is no player password.

Set **Settings > Docker > Docker Stop Timeout** to **120 seconds**. The app also
requests a 120-second container stop timeout so the server has time to save and
exit. Increase both if your mod pack needs more time.

## Connect to the server

In your tModLoader client, join using the Unraid server's IP address and the
**Game port** selected during installation (default: `7777`). Use a compatible
tModLoader version and the server's mod set.

For players outside your LAN, forward the game TCP port on your router to the
Unraid host. See the [networking guide](https://github.com/Crosis47/tmodloader/wiki/Networking-and-Access#game-connections)
for connection details.

The default dashboard address is `http://<Unraid-IP>:8080`. The **WebUI** button
uses your configured dashboard host port. Keep HTTP dashboard access on a trusted
LAN. For a hostname, set **Dashboard origin** (`TMOD_WEB_ORIGIN`) to the exact
browser origin, including the scheme and any non-default port. For remote
access through an HTTPS reverse proxy, also configure **Trusted proxy**
(`TMOD_WEB_TRUSTED_PROXY`) and the required forwarding headers described in the
[networking and proxy guide](https://github.com/Crosis47/tmodloader/wiki/Networking-and-Access).
Do not expose the HTTP dashboard directly to the internet.

## Choose where to manage settings

With the default configuration, manage worlds, mods, passwords, autosave, backups,
and restart schedules in the dashboard. Saved dashboard values override matching
Unraid environment defaults and remain in effect when the container is recreated.

**Manage game settings with** controls this behavior:

| Value | Where to configure game settings |
| --- | --- |
| `web` (default) | Use the dashboard. Unraid values provide defaults for settings that have not been saved in the dashboard. |
| `env` | Use the Unraid container fields. Dashboard configuration editing is disabled. Set the intended world, mods, and password before switching. |

Setting **Enable Dashboard** to `0` also uses environment-managed game settings
and skips administrator setup. Expand **Show more settings** to configure the
server in Unraid.

Ports, storage paths, dashboard access, and runtime update options are configured
in Unraid. Change the **host port mappings** when needed; keep the internal
container ports at `7777` for the game and `8080` for the dashboard.

See [settings ownership and defaults](https://github.com/Crosis47/tmodloader/wiki/Configuration)
for the full configuration reference. Individual Unraid field descriptions also
include relevant wiki URLs, which you can copy into your browser.

## Storage, backups, and updates

**Server data** stores worlds, mods, the installed runtime, dashboard credentials,
settings, and logs. **Backup archives** stores server backups. Preserve both
folders when updating or recreating the container. Do not share them with another
server instance.

Startup repairs ownership of these dedicated folders to UID/GID `1000:1000`, then
runs the game and dashboard without root privileges. Do not add `PUID`/`PGID`
variables or override the container user; this image uses `1000:1000` rather than
Unraid's usual `99:100` convention.

Use the dashboard to create, schedule, verify, and restore backups. Backups
briefly stop the game and disconnect players. Create a backup before major world,
mod, or version changes.

Update the container through Unraid to receive dashboard and container fixes.
Game runtime updates are controlled separately by **Automatic runtime updates**:
`1` checks for runtime updates at startup, while `0` retains the installed runtime.
First startup always requires a download.

See [storage and updates](https://github.com/Crosis47/tmodloader/wiki/Updates-and-Storage)
and [backups and recovery](https://github.com/Crosis47/tmodloader/wiki/Backups-and-Recovery)
for more details.

## Advanced configuration

- Optional `*_FILE` settings need a matching read-only file mapping in Unraid.
  Entering a container path in a setting does not mount the host file.
- **Use custom server configuration** requires a read-only file mapping to
  `/terraria-server/customconfig.txt`, plus `env` mode or a disabled dashboard.
  Keep the custom configuration's game port at `7777`.
- Masked password and token fields hide input in the form, but their values are
  still stored in Docker and the saved Unraid configuration.

## Wiki guides

| Guide | What it covers |
| --- | --- |
| [Dashboard setup](https://github.com/Crosis47/tmodloader/wiki/Web-Dashboard#access-and-credentials) | First-run setup and administrator credentials |
| [Settings ownership](https://github.com/Crosis47/tmodloader/wiki/Configuration#choose-who-manages-settings) | Choosing between dashboard and environment-managed settings |
| [Networking and proxy access](https://github.com/Crosis47/tmodloader/wiki/Networking-and-Access) | Game connections, dashboard ports, and HTTPS reverse proxies |
| [Storage and updates](https://github.com/Crosis47/tmodloader/wiki/Updates-and-Storage) | Persistent folders, container updates, and runtime updates |
| [Worlds and Journey mode](https://github.com/Crosis47/tmodloader/wiki/Worlds-and-Playthroughs) | World creation, saved playthroughs, and Journey permissions |
| [Mods and Workshop](https://github.com/Crosis47/tmodloader/wiki/Mods-and-Workshop) | Workshop downloads, mod selections, and configuration files |
| [Backups and recovery](https://github.com/Crosis47/tmodloader/wiki/Backups-and-Recovery) | Backup schedules, archive verification, and restoration |
| [Autosave and restart schedules](https://github.com/Crosis47/tmodloader/wiki/Configuration#scheduling) | Autosave and scheduled server restarts |
| [All settings and defaults](https://github.com/Crosis47/tmodloader/wiki/Configuration#defaults-reference) | The complete settings reference |
| [Troubleshooting](https://github.com/Crosis47/tmodloader/wiki/Troubleshooting) | Diagnosing setup, startup, and connection problems |

## Help

Check the container logs and the
[troubleshooting guide](https://github.com/Crosis47/tmodloader/wiki/Troubleshooting)
if setup or startup fails. The [project wiki](https://github.com/Crosis47/tmodloader/wiki)
also covers worlds, Workshop mods, schedules, and dashboard use.

For issues with this container, [open a project issue](https://github.com/Crosis47/tmodloader/issues)
with the relevant logs and your configuration, removing passwords and tokens.
This container is independently maintained and is not affiliated with Re-Logic
or the tModLoader team.
