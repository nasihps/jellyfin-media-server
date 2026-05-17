# Self Host Jellyfin Media Server - ARR Stack

This stack contains a fully automated, hardlink-optimized Docker setup. It features instant file moves (Atomic Moves)

## 🚀 1. Starting the Server

1. Open a terminal and navigate to the docker compose file directory:
   `cd ~/jellyfin-media-server`
2. Create the necessary directories for data and configuration:
   ```bash
   mkdir -p ~/jellyfin-media-server/data/torrents/{movies,tv}
   mkdir -p ~/jellyfin-media-server/data/media/{movies,tv}
   mkdir -p ~/jellyfin-media-server/config/{jellyfin,radarr,sonarr,prowlarr,qbittorrent}
   mkdir -p ~/jellyfin-media-server/config/homarr/{configs,icons,data}
   ```
3. Start the containers in the background:
   `docker compose up -d`
4. Check the logs for qBittorrent's temporary password:
   `docker logs qbittorrent`
5. To stop all containers:
   `docker compose down`

---

## ⚙️ 2. Web UI Configuration Guide

**Important:** When an app asks for an IP address of another app, use the Docker container name (e.g., `radarr`, `qbittorrent`), NOT `localhost`. Use `localhost` only in your web browser.

### Step A: qBittorrent (`http://localhost:8080`)
1. **If you prefer Zero Seeding:** Go to Options (Gear Icon) > BitTorrent. Check the box to limit share ratio to `0` and set the action to `Pause`. Check the box to limit seeding time to `0` minutes.
2. **Paths:** Go to Options > Downloads. Set the Default Save Path to `/data/torrents/`.

### Step B: Radarr (`http://localhost:7878`) & Sonarr (`http://localhost:8989`)
1. **Enable Hardlinks:** Go to Settings > Media Management. Click "Show Advanced". Check "Rename Movies/Episodes" and check "Use Hardlinks instead of Copy".
2. **Root Folders:** At the bottom of Media Management, add `/data/media/movies` (Radarr) and `/data/media/tv` (Sonarr).
3. **Link qBittorrent:** Go to Settings > Download Clients. Add qBittorrent. Use Host: `qbittorrent`, Port: `8080`, and enter your login. Ensure "Remove Completed" is checked. Test and Save.
4. **Get API Keys:** Go to Settings > General. Copy the API keys to a notepad.
5. **Enable Metadata:** In Radarr, go to Settings > Metadata > Emby/Kodi. Enable it, then check "Enable Movie Metadata" and "Enable Movie Images".

### Step C: Prowlarr (`http://localhost:9696`)
1. **Add Indexers:** Go to Indexers > Add. Search for and add your preferred public trackers (e.g., 1337x).
   - *Note: Enable magnetic URL in Prowlarr -> Indexer settings -> Enable magnetic URL.*
   - *Note: For trackers like 1337x, EZTV, etc., they require FlareSolverr. Enable it in Prowlarr -> Indexer Proxy settings -> configure FlareSolverr and create a tag. Use the tag in individual indexer settings (1337x, EZTV).*
2. **Link to Arr Apps:** Go to Settings > Apps. Add Radarr and Sonarr.
   - Prowlarr Server: `http://prowlarr:9696`
   - Radarr/Sonarr Server: `http://radarr:7878` or `http://sonarr:8989`
   - Paste the respective API keys. Test and Save.

### Step D: Jellyfin (`http://localhost:8096`)
1. Complete the initial setup wizard.
2. Add Library > Movies > Folder: `/data/media/movies`.
3. Add Library > Shows > Folder: `/data/media/tv`.

### Step E: Homarr (`http://localhost:7575`)
1. Enter edit mode (top right). Add App blocks for your services.
2. Use internal Docker addresses (e.g., `http://radarr:7878`) for API connections, and `http://localhost:7878` for external button links. Paste your API keys where prompted to enable dashboard widgets.

### Step F: Jellyseerr (`http://localhost:5055`)
1. **Connect to Jellyfin:** Connect the Jellyfin server with `http://jellyfin:8096`.
2. **Login:** Login with your Jellyfin username and password.
3. **Add Arr Apps:** Add Radarr & Sonarr connections using their respective API keys (e.g., `http://radarr:7878` and `http://sonarr:8989`).
