# SimRadio

**SimRadio** is a distributed Discord and Microsoft Flight Simulator (2020 / 2024) integration that seamlessly syncs your in-game radio frequencies with Discord voice channels. 

When you tune your COM1 radio in MSFS, the SimRadio client tracks your location and frequency, looks up the nearest airport using the openAIP database, and the central SimRadio Discord bot automatically moves you into a dynamically created Discord Voice Channel named `FREQ(ICAO)` (e.g., `122.800(EHAM)`).

All seamlessly created Discord channels utilize Discord's DAVE end-to-end encryption for maximum security and quality.

---

## 🏛 Architecture

The tool is split into components to allow a **Single Discord Bot** to handle multiple MSFS players safely without distributing bot tokens:

1. **SimRadio Server (`server.py`)**: The central Discord Bot. It runs 24/7 on a host machine, handles Discord commands, performs the openAIP airport lookups, creates voice channels, and moves users.
2. **SimRadio Client (`client.py`)**: A lightweight local program run by players alongside Microsoft Flight Simulator. It connects to MSFS using `SimConnect`, reads radio frequencies and GPS coordinates, and pushes that data securely to the Server using a unique "Magic Code".

---

## 🛠️ Setup Instructions

### 1. Host Setup (Server)

If you are hosting the Bot for your Discord Server:
1. Install Python 3.10+.
2. Install requirements: `pip install -r requirements.txt`.
3. Copy `server_config.example.json` to `server_config.json`.
4. Fill in:
   - `discord_bot_token`: Your Discord Bot Token (from the Discord Developer Portal). Ensure Privileged Gateway Intents (Message Content, Server Members, Presence) are enabled.
   - `openaip_api_key`: Your API key from openAIP (used to resolve airports).
   - `external_url`: The public IP or domain of your server so clients can reach it.
5. Create an `airports.csv` local cache via the script or download it.
6. Run the server: `python server.py`.

### 2. Player Setup (Client)

Players just need the Client executable or Python script running on the same PC as MSFS.
1. In the Discord Server where the SimRadio bot is running, join *any* Voice Channel.
2. Type `!link` in any text channel.
3. The Bot will DM you a unique **Magic Code** configuration file (`client_config.json`).
4. Place `client_config.json` next to your SimRadio Client executable.
5. Start MSFS and jump into a flight.
6. Start the `SimRadio Client`. 
7. The Client will detect MSFS, read your location, and pipe it to the Bot. The bot will instantly move you into the correct radio frequencies!
8. When finished, use `!unlink` in Discord.

---

## 📦 Building Executables (Optional)

If you wish to distribute compiled `.exe` files to your users so they don't have to install Python:
1. Ensure you have `pyinstaller` installed (`pip install pyinstaller`).
2. Run `python build.py`.
3. You will find `SimRadioClient.exe` and `SimRadioServer.exe` in the `dist/` directory. Send `SimRadioClient.exe` to your users!

---

## Technical Features
* **Built in API rate-limit protections** to avoid Discord spam.
* **Instance Locking**: Prevents players from running multiple clients that fight with each other.
* **Auto-Cleanup**: The bot dynamically cleans up unused frequency voice channels after users depart.
* **MSFS 2024 Ready**: Detects "Main Menu" states by listening to MSFS title bar changes and unlinks gracefully to avoid creating "Main Menu" Discord channels.
