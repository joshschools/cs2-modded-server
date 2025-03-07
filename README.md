# Counter-Strike 2 (CS2) Modded Dedicated Server

If you are looking for the CS:GO version you can still access that [here](https://github.com/kus/csgo-modded-server/tree/csgo).

## About

A single modded Counter-Strike 2 (CS2) Modded Dedicated Server that you can [change the active mod](#changing-game-modes) on the server from chat or server console. [Maps are preconfigured per game mode](#what-maps-are-preconfigured-with-each-mode) and change when the game mode changes.

Each game mode has a hand full of maps preset so you are ready to go and it's [easy to add more](#setting-maps-for-different-game-modes).

- 1v1 (with arenas) ([Steam API key](#playing-workshop-mapscollections) required)
- Deathmatch ([Steam API key](#playing-workshop-mapscollections) required)
- Competitive (using [MatchZy](https://github.com/shobhit-pathak/MatchZy#usage-commands))
- Practice (record grenade throws etc)
- GunGame
- Retake
- Wingman (allows more than 4 players) ([Steam API key](#playing-workshop-mapscollections) required)
- KZ ([Steam API key](#playing-workshop-mapscollections) required)
- BHop ([Steam API key](#playing-workshop-mapscollections) required)
- Surf ([Steam API key](#playing-workshop-mapscollections) required)
- ScoutzKnivez ([Steam API key](#playing-workshop-mapscollections) required)
- Mini Games ([Steam API key](#playing-workshop-mapscollections) required)
- Course format (tests players with different traps, kz, surf, bhop) ([Steam API key](#playing-workshop-mapscollections) required)
- Hide n Seek ([Steam API key](#playing-workshop-mapscollections) required)
- Soccer ([Steam API key](#playing-workshop-mapscollections) required)

Every time you want to boot the server, you should run `gcp.sh` (if on Google Cloud) or `install.sh` (on Linux) and it will ensure your OS is up to date, CS2 is up to date, and pull down the latest patches from this mod (any updates that I push up).

Obviously, any changes you have made to the files in this mod will be overwritten so I have created a "[custom files](#custom-files)" folder where you mirror the contents of the `game/csgo/` folder, and any files you want to tweak, you put in there in the same spot and they will always overwrite the mods default files. Read more about it [here](#custom-files).

The simple quick setup:

1. [Create your firewall rules](#create-firewall-rule)
2. [Provision your server on Google Cloud](#create-instance)
3. [SSH into server](#ssh-to-server)
4. [Install mod](#install-mod)
5. [Create your custom files for hostname, admins etc](#custom-files)
6. Ensure you have followed the steps for creating an [online server](#creating-an-online-server) or [LAN server](#creating-a-lan-server)
7. Kill server if running `./stop.sh` and start again `gcp.sh` (if on Google Cloud) or `install.sh` (on Linux)

Your server should be up and running!

To check everything is working correctly run the following commands in the server console:

- `meta list` and you should see `CounterStrikeSharp` in the output
- `css_plugins list` and you should see a few plugins in the output

If you see content in both; everything is working.

> [!IMPORTANT]
> Using RCON whilst connected to the server does not work. See discussion [here](https://www.reddit.com/r/GlobalOffensive/comments/167spzi/cs2_rcon/).
> The current work arounds are:
>
> - I have included [CS2Rcon](https://github.com/LordFetznschaedl/CS2Rcon) which allows admins to use !rcon in chat.
> - You can disconnect from the server and use `rcon_address IP:PORT` in console and you can use rcon commands.
> - Use an external RCON program which has implemented the RCON protocol such as [this](https://github.com/fpaezf/CS2-RCON-Tool-V2).

Useful things to know:

- [Access admin menu](#acessing-admin-menu)
- [Changing game mode](#changing-game-modes)

Getting up and running:

- [Running on Google Cloud](#running-on-google-cloud)
- [Running on Linux](#running-on-linux)

## Mods installed

Mod | Version | Why
--- | --- | ---
[Metamod:Source](http://www.sourcemm.net/downloads.php?branch=master) | `2.0.0-1278` | Sits between the Game and the Engine, and allows plugins to intercept calls that flow between
[CounterStrikeSharp](https://github.com/roflmuffin/CounterStrikeSharp) | `142` | Attempts to implement a .NET Core scripting layer on top of a Metamod Source Plugin, allowing developers to create plugins that interact with the game server in a modern language (C#)
[CS2_ExecAfter](https://github.com/kus/CS2_ExecAfter) | `1.0.0` | Executes a command after server event (i.e. OnMapStart) or a delay.
[CS2_DamageInfo](https://github.com/K4ryuu/CS2_DamageInfo) | `1.3.3` | Displays the amount of damage players have inflicted on the victim's HP and Armor, as well as the hit groups they have hit.
[MatchZy](https://github.com/shobhit-pathak/MatchZy) | `0.6.1` | MatchZy is a plugin for CS2 for running and managing practice/pugs/scrims/matches with easy configuration!
[MapConfigurator](https://github.com/ManifestManah/MapConfigurator)| `1.0.2` | Allows you to quick and easily create unique configuration files for each map on your server.
[SimpleAdmin](https://github.com/connercsbn/SimpleAdmin/)| `0.1.2` | Adds basic administrator functions
[CS2Rcon](https://github.com/LordFetznschaedl/CS2Rcon)| `1.2.0` | This is a rudimentary implementation of a RCON plugin for CS2 using CounterStrikeSharp as RCON does not work whilst connected to the server
[SharpTimer](https://github.com/DEAFPS/SharpTimer/)| `0.1.9` | SharpTimer is a simple Surf/KZ/Bhop/MG/Deathrun/etc CS2 Timer plugin
[GunGame](https://github.com/ssypchenko/cs2-gungame)| `1.0.3` | GunGame mode on Counter Strike Sharp
[CS2-Multi-1v1](https://github.com/rockCityMath/CS2-Multi-1v1)| `1.0.0` | A CS2 plugin that allows players to fight 1v1 in ranked arenas.
[CS2 Retakes](https://github.com/B3none/cs2-retakes)| `1.2.4` | CS2 implementation of retakes. Based on the version for CS:GO by Splewis.
[CS2 Instadefuse](https://github.com/B3none/cs2-instadefuse)| `1.3.1` | Allows a CT to instantly defuse the bomb when nothing can prevent defusal. Written in C# for CounterStrikeSharp.
[CS2 Instaplant](https://github.com/B3none/cs2-instaplant)| `1.0.0` | Allows terrorists to plant the bomb instantly
[CS2 Retakes Allocator](https://github.com/yonilerner/cs2-retakes-allocator)| `1.0.8` | Advanced weapon allocation for B3none/cs2-retakes
[CS2 Whitelist](https://github.com/PhantomYopta/CS2_WhiteList)|`2.0`| Restricts access to the server for SteamID members/employees listed in the whitelist

## Custom files

> [!NOTE]  
> Any reference to a path is always the root of the installation. Which on Linux will typically be `/home/steam/cs2/` and on Windows where ever you extracted the zip.
>
> For example on Linux:
> `/custom_files/addons/counterstrikesharp/configs/admins.json` full path is `/home/steam/cs2/custom_files/addons/counterstrikesharp/configs/admins.json`
> `/game/csgo/addons/counterstrikesharp/configs/admins.json` full path is `/home/steam/cs2/game/csgo/addons/counterstrikesharp/configs/admins.json`

Any changes you have made to the files in this mod will be overwritten when the update scripts are ran. I have created a folder `/custom_files/` in the root of the project, where you mirror the contents of the `csgo/` folder, and any files you want to tweak, you put in there in the same spot and they will always overwrite the mods default files.

So this can be used to set the server hostname to something you want, set the RCON or serverpassword or set the admins of the server.

You can see an example of what I use on my server in the `/custom_files_example/` directory, which sets the hostname, server image and admins.

For example; if you want to add yourself as an admin, that file is located `/game/csgo/addons/counterstrikesharp/configs/admins.json`. So to make your tweak to it, you would copy that file to `/custom_files/addons/counterstrikesharp/configs/admins.json` and add yourself as an admin at the bottom. Then when the update scripts run, it will copy your custom file at `/custom_files/addons/counterstrikesharp/configs/admins.json` and overwrite the default mod file at `/game/csgo/addons/counterstrikesharp/configs/admins.json`.

If you want to change the server name, or make any changes to any mod settings use the `/cfg/custom_MOD.cfg` as it executes at the end and can overwrite any setting. So if you wanted to change the server name for GunGame, you would copy `/game/csgo/cfg/custom_dm.cfg` to `/custom_files/cfg/custom_dm.cfg` and and write `hostname "shipREKT GunGame +Deathmatch +Turbo"` and any other settings you want and this file will overwrite `/game/csgo/cfg/custom_dm.cfg` each time the `gcp.sh`/`install.sh`/`win.bat` script is ran, and these settings will run at the end when you load the GunGame mod.

To generate this directory, you can run the `gcp.sh` script (if on Google Cloud), `install.sh` script on Linux once or on `win.bat` script on Windows where you extracted the mod zip and this is where you would put your custom modifications.

## Creating an online server

If you are hosting an online server, you need to create a Steam [Game Login Token](https://steamcommunity.com/dev/managegameservers), your server will not run online without this. Put this value in the `STEAM_ACCOUNT` environment variable or create a custom file for `/game/csgo/cfg/secrets.cfg` following the [custom files](#custom-files) steps (`/custom_files/cfg/secrets.cfg`) and set it in `sv_setsteamaccount`.

You also need to create an [authorization key](http://steamcommunity.com/dev/apikey) which will allow your server to download maps from the workshop. Put this value in the `API_KEY` environment variable.

**You must connect to the server from the public IP, not the LAN IP even if you are on the same network. The script logs the public IP `Starting server on XXX.XXX.XXX.XXX:27015`**

## Creating a LAN server

Create a custom file for `/game/csgo/cfg/env.cfg` following the [custom files](#custom-files) steps (`/custom_files/cfg/env.cfg`) and set `sv_lan` to `1`, `sv_downloadurl` to `""` and `sv_allowdownload` to `1`.

## Environment variables

### Available via environment variable only

Key | Default value | What is it
--- | --- | ---
`API_KEY` | `changeme` | To download maps from the workshop, your server needs access to the steam web api. To allow this you'll need an authorization key which you can generate [here](http://steamcommunity.com/dev/apikey)
`IP` | `` | Not required. Allows the server IP to be set. Useful if a CS2 server needs to be bound to a specific IP address.
`PORT` | `27015` | Server port
`TICKRATE` | `128` | Server tickrate MM is 64, Faceit is 128
`MAXPLAYERS` | `32` | Max player limit
`DUCK_DOMAIN` | `` | [Duck DNS](https://www.duckdns.org/) domain if you want to utalise the free service to get a domain for your server instead of IP
`DUCK_TOKEN` | `` | [Duck DNS](https://www.duckdns.org/) access token to update domain when server boots
`CUSTOM_FOLDER` | `custom_files` | Folder of your own modifications to the mod that mirror the csgo/ structure and overwrite the mode files. More on that [here](#custom-files)

### Can be configured via config file in custom files directory

These values can be set via environment variable or a config file in the custom files directory.
Copy `/game/csgo/cfg/secrets.cfg` to `/custom_files/cfg/secrets.cfg` and write the values you want and this file will overwrite `/game/csgo/cfg/secrets.cfg` each time the `gcp.sh`/`install.sh` script is ran.

Key | Value | What is it
--- | --- | ---
`RCON_PASSWORD` | `changeme` | RCON password to control server from console also remotely configure
`STEAM_ACCOUNT` | `` | To host a server online, you need to create a Steam [Game Login Token](https://steamcommunity.com/dev/managegameservers). Your server will not run online without this
`SERVER_PASSWORD` | `` | If you want a password protected server

### Playing workshop maps/collections

To download maps from the workshop, your server needs access to the steam web api. To allow this you'll need an authorization key which you can generate [here](http://steamcommunity.com/dev/apikey) and set `API_KEY` to the key.

The console command for hosting a workshop map is `host_workshop_map fileid` where `fileid` is the number that comes after `?id=` in the workshop URL for example: [https://steamcommunity.com/sharedfiles/filedetails/?id=2433686680](https://steamcommunity.com/sharedfiles/filedetails/?id=2433686680)

The console command for hosting a workshop collection is `host_workshop_collection collectionid` where `collectionid` is the number that comes after `?id=` in the workshop URL for example: [https://steamcommunity.com/sharedfiles/filedetails/?id=1092904694](https://steamcommunity.com/sharedfiles/filedetails/?id=1092904694). This command will then download all maps in the collection and create a mapgroup out of them, then host it.

### Setting maps for different game modes

Copy the file `/game/csgo/gamemodes_server.txt` following the [custom files](#custom-files) steps (`/custom_files/gamemodes_server.txt`) and add the maps you want per gamemode. Most gamemodes fall under casual, but I have created unique groups for each mode so adding your own maps is easy by updating this one file.

## Running on Google Cloud

### Create firewall rule

```
gcloud compute firewall-rules create source \
--allow tcp:27015-27020,tcp:80,udp:27015-27020
```

### Create instance

Ensure you have all the settings for your [environment variables](#environment-variables).

If you have issues with the server not handling load, you may want to consider [compute-optimized](https://cloud.google.com/compute/vm-instance-pricing#compute-optimized_machine_types) machine `c2-standard-4`.

```
gcloud beta compute instances create <instance-name> \
--maintenance-policy=TERMINATE \
--project=<project> \
--zone=australia-southeast1-c \
--machine-type=n2-standard-2 \
--network-tier=PREMIUM \
--metadata=RCON_PASSWORD=changeme,STEAM_ACCOUNT=changeme,API_KEY=changeme,DUCK_DOMAIN=changeme,DUCK_TOKEN=changeme,startup-script="echo \"Delaying for 30 seconds...\" && sleep 30 && cd / && /gcp.sh" \
--no-restart-on-failure \
--scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/compute.readonly,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append \
--tags=source \
--image-family=ubuntu-2204-lts \
--image-project=ubuntu-os-cloud \
--boot-disk-size=60GB \
--boot-disk-type=pd-standard \
--boot-disk-device-name=<instance-name>
```

### SSH to server

```
gcloud compute ssh <instance-name> \
--zone=australia-southeast1-c
```

### Install mod

```
sudo su
cd / && curl --silent --output "gcp.sh" "https://raw.githubusercontent.com/kus/cs2-modded-server/master/gcp.sh" && chmod +x gcp.sh && bash gcp.sh
```

If the installation has paused for a long time, restart the server and do it again.

### Stop server

```
gcloud compute instances stop <instance-name> \
--zone australia-southeast1-c
```

### Start server

```
gcloud compute instances start <instance-name> \
--zone australia-southeast1-c
```

### Delete server

```
gcloud compute instances delete <instance-name> \
--zone australia-southeast1-c
```

### Push file to server from local machine

For example a map:

```
On local:
gcloud config set project <project>
cd /path/to/folder
gcloud compute scp de_kus.vpk root@<instance-name>:/home/steam/cs2/game/csgo/maps --zone australia-southeast1-c

On server SSH:
cd /home/steam/cs2/game/csgo/maps
chown steam:steam de_kus.vpk
chmod 644 de_kus.vpk
```

### Download from server

`gcloud compute scp root@<instance-name>:/home/steam/cs2/gamecsgo/cfg/comp.cfg  ~/Desktop/`

### Turn VM off at 3:30AM every day

SSH into the VM

Switch to root `sudo su`

Check the timezone your server is running in `sudo hwclock --show`

Open crontab file `nano /etc/crontab`

Append to the end of the crontab file `30 3    * * *   root    shutdown -h now`

Save `CTRL + X`

## Running on Linux

Make sure you have **60GB free space**.

Ensure you have all the settings for your [environment variables](#environment-variables).

- **If setting up internet server:**

   Set environment variable `STEAM_ACCOUNT` to your [Game Server Login Token](https://steamcommunity.com/dev/managegameservers)

   Make sure you [port forward](https://portforward.com/router.htm) on your router TCP: `27015` and UDP: `27015` & `27020` so players can connect from the internet.

   **You must connect to the server from the public IP, not the LAN IP even if you are on the same network. The script logs the public IP `Starting server on XXX.XXX.XXX.XXX:27015`**

- **If setting up LAN server:**

   Set environment variable `LAN` to `1`

```
sudo su
export RCON_PASSWORD="changeme"
export API_KEY="changeme"
export STEAM_ACCOUNT=""
export SERVER_PASSWORD=""
export PORT="27015"
export TICKRATE="128"
export MAXPLAYERS="32"
cd / && curl --silent --output "install.sh" "https://raw.githubusercontent.com/kus/cs2-modded-server/master/install.sh" && chmod +x install.sh && bash install.sh
```

- **If running for the first time**

To check everything is working correctly run the following commands in the server console:

- `meta list` and you should see `CounterStrikeSharp` in the output
- `css_plugins list` and you should see a few plugins in the output

If you see content in both; everything is working.

When you join the server you can [change game modes](#changing-game-modes).

## Running on Windows

Make sure you have **60GB free space**.

[Download this repo](https://github.com/kus/cs2-modded-server/archive/master.zip) and extract it to where you want your server (i.e. `C:\Server\cs2-modded-server`). All the following instructions will use this as the root.

Edit `\game\csgo\gameinfo.gi` and search for `Game_LowViolence    csgo_lv` it should be under `GameInfo > FileSystem > SearchPaths` and below it add `Game    csgo/addons/metamod` and save the file. You can find detailed instructions [here](https://cs2.poggu.me/metamod/installation/).

Create a folder `steamcmd` and [download SteamCMD](https://steamcdn-a.akamaihd.net/client/installer/steamcmd.zip) and extract it inside `steamcmd` so you should have `\steamcmd\steamcmd.exe`.

- **If setting up internet server:**

   Open `\csgo\cfg\env.cfg`

   Set `sv_setsteamaccount` to your [Game Server Login Token](https://steamcommunity.com/dev/managegameservers)

   Open `\win.ini`

   Set `ip_internet` to your [public ip](http://checkip.amazonaws.com/)

   Make sure you [port forward](https://portforward.com/router.htm) on your router TCP: `27015` and UDP: `27015` & `27020` so players can connect from the internet.

   **You must connect to the server from the public IP, not the LAN IP even if you are on the same network.**

- **If setting up LAN server:**

   Open `\csgo\cfg\env.cfg`

   Set `sv_lan` to `1`

[Add admins](#acessing-admin-menu)

Run `win.bat`

Accept both Private and Public connections on Windows Firewall.

- **If running for the first time**

   Once the CS2 server has started close it

   If you want to use my bundled maps and this server setup you can use `https://raw.githubusercontent.com/kus/cs2-modded-server-assets/master/game/csgo` for your your `sv_downloadurl` in `cfg/env.cfg`. Make sure you change `sv_allowdownload` to `0`.

   Run `win.bat` again

To check everything is working correctly run the following commands in the server console:

- `meta list` and you should see `CounterStrikeSharp` in the output
- `css_plugins list` and you should see a few plugins in the output

If you see content in both; everything is working.

When you join the server you can [change game modes](#changing-game-modes).

## FAQ

### What maps are preconfigured with each mode?

#### mg_active

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_ancient.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_ancient<br><sup><sub>changelevel de_ancient</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_anubis.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_anubis<br><sup><sub>changelevel de_anubis</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_inferno.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_inferno<br><sup><sub>changelevel de_inferno</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_mirage.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_mirage<br><sup><sub>changelevel de_mirage</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_nuke.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_nuke<br><sup><sub>changelevel de_nuke</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_overpass.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_overpass<br><sup><sub>changelevel de_overpass</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_vertigo.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_vertigo<br><sup><sub>changelevel de_vertigo</sub></sup></td></tr></table></td></tr></table>

#### mg_comp

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_italy.jpg?raw=true&sanitize=true"></td></tr><tr><td>cs_italy<br><sup><sub>changelevel cs_italy</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_office.jpg?raw=true&sanitize=true"></td></tr><tr><td>cs_office<br><sup><sub>changelevel cs_office</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_vertigo.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_vertigo<br><sup><sub>changelevel de_vertigo</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_ancient.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_ancient<br><sup><sub>changelevel de_ancient</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_anubis.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_anubis<br><sup><sub>changelevel de_anubis</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_dust2.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_dust2<br><sup><sub>changelevel de_dust2</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_inferno.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_inferno<br><sup><sub>changelevel de_inferno</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_mirage.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_mirage<br><sup><sub>changelevel de_mirage</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_nuke.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_nuke<br><sup><sub>changelevel de_nuke</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_overpass.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_overpass<br><sup><sub>changelevel de_overpass</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_vertigo.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_vertigo<br><sup><sub>changelevel de_vertigo</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_assembly.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3071005299">de_assembly</a><br><sup><sub>host_workshop_map 3071005299</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_cbble.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070212801">de_cbble</a><br><sup><sub>host_workshop_map 3070212801</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_cache.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070596702">de_cache</a><br><sup><sub>host_workshop_map 3070596702</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_pipeline.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3079872050">de_pipeline</a><br><sup><sub>host_workshop_map 3079872050</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_biome.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3075706807">de_biome</a><br><sup><sub>host_workshop_map 3075706807</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mp_raid.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070346180">mp_raid</a><br><sup><sub>host_workshop_map 3070346180</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_mutiny.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070766070">de_mutiny</a><br><sup><sub>host_workshop_map 3070766070</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_assault.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070594412">cs_assault</a><br><sup><sub>host_workshop_map 3070594412</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_ruins_d_prefab.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3072352643">de_ruins_d_prefab</a><br><sup><sub>host_workshop_map 3072352643</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_militia.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3089953774">cs_militia</a><br><sup><sub>host_workshop_map 3089953774</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_aztec.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070960099">de_aztec</a><br><sup><sub>host_workshop_map 3070960099</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_akiba.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3108513658">de_akiba</a><br><sup><sub>host_workshop_map 3108513658</sub></sup></td></tr></table></td></tr></table>

#### mg_wingman

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_italy.jpg?raw=true&sanitize=true"></td></tr><tr><td>cs_italy<br><sup><sub>changelevel cs_italy</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_office.jpg?raw=true&sanitize=true"></td></tr><tr><td>cs_office<br><sup><sub>changelevel cs_office</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_vertigo.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_vertigo<br><sup><sub>changelevel de_vertigo</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_ancient.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_ancient<br><sup><sub>changelevel de_ancient</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_anubis.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_anubis<br><sup><sub>changelevel de_anubis</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_dust2.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_dust2<br><sup><sub>changelevel de_dust2</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_inferno.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_inferno<br><sup><sub>changelevel de_inferno</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_mirage.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_mirage<br><sup><sub>changelevel de_mirage</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_nuke.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_nuke<br><sup><sub>changelevel de_nuke</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_overpass.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_overpass<br><sup><sub>changelevel de_overpass</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_vertigo.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_vertigo<br><sup><sub>changelevel de_vertigo</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/gd_rialto.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3085490518">gd_rialto</a><br><sup><sub>host_workshop_map 3085490518</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_safehouse.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070550406">de_safehouse</a><br><sup><sub>host_workshop_map 3070550406</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/ar_shoots.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070552133">ar_shoots</a><br><sup><sub>host_workshop_map 3070552133</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_lake.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070563536">de_lake</a><br><sup><sub>host_workshop_map 3070563536</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_bank.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070581293">de_bank</a><br><sup><sub>host_workshop_map 3070581293</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_shortdust.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070612859">de_shortdust</a><br><sup><sub>host_workshop_map 3070612859</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_assembly.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3071005299">de_assembly</a><br><sup><sub>host_workshop_map 3071005299</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_cbble.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070212801">de_cbble</a><br><sup><sub>host_workshop_map 3070212801</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_cache.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070596702">de_cache</a><br><sup><sub>host_workshop_map 3070596702</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_pipeline.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3079872050">de_pipeline</a><br><sup><sub>host_workshop_map 3079872050</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_biome.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3075706807">de_biome</a><br><sup><sub>host_workshop_map 3075706807</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mp_raid.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070346180">mp_raid</a><br><sup><sub>host_workshop_map 3070346180</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_mutiny.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070766070">de_mutiny</a><br><sup><sub>host_workshop_map 3070766070</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_assault.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070594412">cs_assault</a><br><sup><sub>host_workshop_map 3070594412</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_ruins_d_prefab.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3072352643">de_ruins_d_prefab</a><br><sup><sub>host_workshop_map 3072352643</sub></sup></td></tr></table></td></tr></table>

#### mg_dm

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_italy.jpg?raw=true&sanitize=true"></td></tr><tr><td>cs_italy<br><sup><sub>changelevel cs_italy</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_office.jpg?raw=true&sanitize=true"></td></tr><tr><td>cs_office<br><sup><sub>changelevel cs_office</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_vertigo.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_vertigo<br><sup><sub>changelevel de_vertigo</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_ancient.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_ancient<br><sup><sub>changelevel de_ancient</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_anubis.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_anubis<br><sup><sub>changelevel de_anubis</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_dust2.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_dust2<br><sup><sub>changelevel de_dust2</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_inferno.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_inferno<br><sup><sub>changelevel de_inferno</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_mirage.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_mirage<br><sup><sub>changelevel de_mirage</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_nuke.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_nuke<br><sup><sub>changelevel de_nuke</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_overpass.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_overpass<br><sup><sub>changelevel de_overpass</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_vertigo.jpg?raw=true&sanitize=true"></td></tr><tr><td>de_vertigo<br><sup><sub>changelevel de_vertigo</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/gd_rialto.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3085490518">gd_rialto</a><br><sup><sub>host_workshop_map 3085490518</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_safehouse.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070550406">de_safehouse</a><br><sup><sub>host_workshop_map 3070550406</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/ar_shoots.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070552133">ar_shoots</a><br><sup><sub>host_workshop_map 3070552133</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_lake.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070563536">de_lake</a><br><sup><sub>host_workshop_map 3070563536</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_bank.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070581293">de_bank</a><br><sup><sub>host_workshop_map 3070581293</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_shortdust.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070612859">de_shortdust</a><br><sup><sub>host_workshop_map 3070612859</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fy_pool_day.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070923343">fy_pool_day</a><br><sup><sub>host_workshop_map 3070923343</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fy_iceworld.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070238628">fy_iceworld</a><br><sup><sub>host_workshop_map 3070238628</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/daymare.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3072640420">daymare</a><br><sup><sub>host_workshop_map 3072640420</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_theorem.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070348309">aim_theorem</a><br><sup><sub>host_workshop_map 3070348309</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_assembly.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3071005299">de_assembly</a><br><sup><sub>host_workshop_map 3071005299</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_cbble.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070212801">de_cbble</a><br><sup><sub>host_workshop_map 3070212801</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_cache.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070596702">de_cache</a><br><sup><sub>host_workshop_map 3070596702</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_pipeline.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3079872050">de_pipeline</a><br><sup><sub>host_workshop_map 3079872050</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_biome.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3075706807">de_biome</a><br><sup><sub>host_workshop_map 3075706807</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/dm_desk.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3077599381">dm_desk</a><br><sup><sub>host_workshop_map 3077599381</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fun_bounce.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3088183343">fun_bounce</a><br><sup><sub>host_workshop_map 3088183343</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/1v1aim_map_longdustversion_d.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082605693">1v1aim_map_longdustversion_d</a><br><sup><sub>host_workshop_map 3082605693</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/ar_churches_s2r.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070291913">ar_churches_s2r</a><br><sup><sub>host_workshop_map 3070291913</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_ag_texture_city_advanced.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082113929">aim_ag_texture_city_advanced</a><br><sup><sub>host_workshop_map 3082113929</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/traningoutside.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3080973179">traningoutside</a><br><sup><sub>host_workshop_map 3080973179</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/shipment_version_1_0.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3086555291">shipment_version_1_0</a><br><sup><sub>host_workshop_map 3086555291</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_ag_texture2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3074961197">aim_ag_texture2</a><br><sup><sub>host_workshop_map 3074961197</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_ag_texture_jungle.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3095778105">aim_ag_texture_jungle</a><br><sup><sub>host_workshop_map 3095778105</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs2_bloodstrike.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3071890065">cs2_bloodstrike</a><br><sup><sub>host_workshop_map 3071890065</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/gg_simpsons_vs_flanders_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3109232789">gg_simpsons_vs_flanders_v2</a><br><sup><sub>host_workshop_map 3109232789</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_akiba.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3108513658">de_akiba</a><br><sup><sub>host_workshop_map 3108513658</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_facingworlds-99.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3112806723">cs_facingworlds-99</a><br><sup><sub>host_workshop_map 3112806723</sub></sup></td></tr></table></td></tr></table>

#### mg_gg

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fy_pool_day.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070923343">fy_pool_day</a><br><sup><sub>host_workshop_map 3070923343</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fy_iceworld.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070238628">fy_iceworld</a><br><sup><sub>host_workshop_map 3070238628</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/daymare.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3072640420">daymare</a><br><sup><sub>host_workshop_map 3072640420</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_theorem.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070348309">aim_theorem</a><br><sup><sub>host_workshop_map 3070348309</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_safehouse.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070550406">de_safehouse</a><br><sup><sub>host_workshop_map 3070550406</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/ar_shoots.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070552133">ar_shoots</a><br><sup><sub>host_workshop_map 3070552133</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_lake.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070563536">de_lake</a><br><sup><sub>host_workshop_map 3070563536</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/de_bank.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070581293">de_bank</a><br><sup><sub>host_workshop_map 3070581293</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fun_bounce.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3088183343">fun_bounce</a><br><sup><sub>host_workshop_map 3088183343</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/1v1aim_map_longdustversion_d.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082605693">1v1aim_map_longdustversion_d</a><br><sup><sub>host_workshop_map 3082605693</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/ar_churches_s2r.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070291913">ar_churches_s2r</a><br><sup><sub>host_workshop_map 3070291913</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_ag_texture_city_advanced.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082113929">aim_ag_texture_city_advanced</a><br><sup><sub>host_workshop_map 3082113929</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/traningoutside.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3080973179">traningoutside</a><br><sup><sub>host_workshop_map 3080973179</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/shipment_version_1_0.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3086555291">shipment_version_1_0</a><br><sup><sub>host_workshop_map 3086555291</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_ag_texture2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3074961197">aim_ag_texture2</a><br><sup><sub>host_workshop_map 3074961197</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_ag_texture_jungle.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3095778105">aim_ag_texture_jungle</a><br><sup><sub>host_workshop_map 3095778105</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs2_bloodstrike.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3071890065">cs2_bloodstrike</a><br><sup><sub>host_workshop_map 3071890065</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/gg_simpsons_vs_flanders_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3109232789">gg_simpsons_vs_flanders_v2</a><br><sup><sub>host_workshop_map 3109232789</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cs_facingworlds-99.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3112806723">cs_facingworlds-99</a><br><sup><sub>host_workshop_map 3112806723</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/dm_desk.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3077599381">dm_desk</a><br><sup><sub>host_workshop_map 3077599381</sub></sup></td></tr></table></td></tr></table>

#### mg_1v1

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_map.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070549948">aim_map</a><br><sup><sub>host_workshop_map 3070549948</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_newagewip.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070308285">aim_newagewip</a><br><sup><sub>host_workshop_map 3070308285</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_movement.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070423167">aim_movement</a><br><sup><sub>host_workshop_map 3070423167</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fy_pool_day.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070923343">fy_pool_day</a><br><sup><sub>host_workshop_map 3070923343</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fy_iceworld.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070238628">fy_iceworld</a><br><sup><sub>host_workshop_map 3070238628</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/daymare.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3072640420">daymare</a><br><sup><sub>host_workshop_map 3072640420</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_theorem.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070348309">aim_theorem</a><br><sup><sub>host_workshop_map 3070348309</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/fun_bounce.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3088183343">fun_bounce</a><br><sup><sub>host_workshop_map 3088183343</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/1v1aim_map_longdustversion_d.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082605693">1v1aim_map_longdustversion_d</a><br><sup><sub>host_workshop_map 3082605693</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/ar_churches_s2r.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070291913">ar_churches_s2r</a><br><sup><sub>host_workshop_map 3070291913</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/aim_ag_texture_city_advanced.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082113929">aim_ag_texture_city_advanced</a><br><sup><sub>host_workshop_map 3082113929</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/traningoutside.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3080973179">traningoutside</a><br><sup><sub>host_workshop_map 3080973179</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/shipment_version_1_0.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3086555291">shipment_version_1_0</a><br><sup><sub>host_workshop_map 3086555291</sub></sup></td></tr></table></td></tr></table>

#### mg_bhop

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_at_night.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3077211069">bhop_at_night</a><br><sup><sub>host_workshop_map 3077211069</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_asiimov.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3076417347">bhop_asiimov</a><br><sup><sub>host_workshop_map 3076417347</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_ragnarok.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3077153735">bhop_ragnarok</a><br><sup><sub>host_workshop_map 3077153735</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_zunron.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3077475505">bhop_zunron</a><br><sup><sub>host_workshop_map 3077475505</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_1derland.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3077596014">bhop_1derland</a><br><sup><sub>host_workshop_map 3077596014</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_whiteshit.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3078523849">bhop_whiteshit</a><br><sup><sub>host_workshop_map 3078523849</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_cherryblossom.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082038560">bhop_cherryblossom</a><br><sup><sub>host_workshop_map 3082038560</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_arcturus.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3088973190">bhop_arcturus</a><br><sup><sub>host_workshop_map 3088973190</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/bhop_kiwi_cwfx.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3095219437">bhop_kiwi_cwfx</a><br><sup><sub>host_workshop_map 3095219437</sub></sup></td></tr></table></td></tr></table>

#### mg_kz

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/only_up.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3074758439">only_up</a><br><sup><sub>host_workshop_map 3074758439</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_hub.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070220367">kz_hub</a><br><sup><sub>host_workshop_map 3070220367</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_checkmate.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070194623">kz_checkmate</a><br><sup><sub>host_workshop_map 3070194623</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_victoria.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3086304337">kz_victoria</a><br><sup><sub>host_workshop_map 3086304337</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_rc_stonehenge.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3072219045">kz_rc_stonehenge</a><br><sup><sub>host_workshop_map 3072219045</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_sxb2_cxz.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3083714192">kz_sxb2_cxz</a><br><sup><sub>host_workshop_map 3083714192</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_rc_twotowers.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3083509404">kz_rc_twotowers</a><br><sup><sub>host_workshop_map 3083509404</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_simplyhard.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3078311932">kz_simplyhard</a><br><sup><sub>host_workshop_map 3078311932</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_nomibo.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3077122656">kz_nomibo</a><br><sup><sub>host_workshop_map 3077122656</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_sxb2_biewan.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3076000218">kz_sxb2_biewan</a><br><sup><sub>host_workshop_map 3076000218</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_ggsh.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3072744536">kz_ggsh</a><br><sup><sub>host_workshop_map 3072744536</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/kz_ltt.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3072699538">kz_ltt</a><br><sup><sub>host_workshop_map 3072699538</sub></sup></td></tr></table></td></tr></table>

#### mg_surf

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/surf_kitsune.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3076153623">surf_kitsune</a><br><sup><sub>host_workshop_map 3076153623</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/surf_utopia_njv.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3073875025">surf_utopia_njv</a><br><sup><sub>host_workshop_map 3073875025</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/surf_beginner.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070321829">surf_beginner</a><br><sup><sub>host_workshop_map 3070321829</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/surf_mesa_revo.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3076980482">surf_mesa_revo</a><br><sup><sub>host_workshop_map 3076980482</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/surf_deathstar.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3080544577">surf_deathstar</a><br><sup><sub>host_workshop_map 3080544577</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/surf_rookie.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082548297">surf_rookie</a><br><sup><sub>host_workshop_map 3082548297</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/surf_benevolent.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3098972556">surf_benevolent</a><br><sup><sub>host_workshop_map 3098972556</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/surf_ace.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3088413071">surf_ace</a><br><sup><sub>host_workshop_map 3088413071</sub></sup></td></tr></table></td></tr></table>

#### mg_minigames

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_skeet_multigames_v7.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3082120895">mg_skeet_multigames_v7</a><br><sup><sub>host_workshop_map 3082120895</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_warmcup_headshot.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3076765511">mg_warmcup_headshot</a><br><sup><sub>host_workshop_map 3076765511</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/deathrun_iceworld_cs2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3083325292">deathrun_iceworld_cs2</a><br><sup><sub>host_workshop_map 3083325292</sub></sup></td></tr></table></td></tr></table>

#### mg_course

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cr_devisland_p1_v1.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3076483842">cr_devisland_p1_v1</a><br><sup><sub>host_workshop_map 3076483842</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_switch_course_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070439729">mg_switch_course_v2</a><br><sup><sub>host_workshop_map 3070439729</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/cr_minecraft_jb_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070896876">cr_minecraft_jb_v2</a><br><sup><sub>host_workshop_map 3070896876</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_metro_course_v1.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070463151">mg_metro_course_v1</a><br><sup><sub>host_workshop_map 3070463151</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_alley_course_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070455802">mg_alley_course_v2</a><br><sup><sub>host_workshop_map 3070455802</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_glave_course_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070445185">mg_glave_course_v2</a><br><sup><sub>host_workshop_map 3070445185</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_office_course_v3.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070459211">mg_office_course_v3</a><br><sup><sub>host_workshop_map 3070459211</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_metal_course_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070464208">mg_metal_course_v2</a><br><sup><sub>host_workshop_map 3070464208</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_acrophobia_run_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070463620">mg_acrophobia_run_v2</a><br><sup><sub>host_workshop_map 3070463620</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_metro_course_s2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3071040020">mg_metro_course_s2</a><br><sup><sub>host_workshop_map 3071040020</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_circle_course_v3.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070434475">mg_circle_course_v3</a><br><sup><sub>host_workshop_map 3070434475</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_simpsons_course_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070447697">mg_simpsons_course_v2</a><br><sup><sub>host_workshop_map 3070447697</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_sonic_course_v2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070452642">mg_sonic_course_v2</a><br><sup><sub>host_workshop_map 3070452642</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/mg_sky_realm_v3.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070451616">mg_sky_realm_v3</a><br><sup><sub>host_workshop_map 3070451616</sub></sup></td></tr></table></td></tr></table>

#### mg_scoutzknivez

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/scoutzknivez_pure_cs2.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3073929825">scoutzknivez_pure_cs2</a><br><sup><sub>host_workshop_map 3073929825</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/ar_dizzy.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070553020">ar_dizzy</a><br><sup><sub>host_workshop_map 3070553020</sub></sup></td></tr></table></td></tr></table>

#### mg_hns

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/infernohideandseek.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3097563690">infernohideandseek</a><br><sup><sub>host_workshop_map 3097563690</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/seek_town_bs.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3074479691">seek_town_bs</a><br><sup><sub>host_workshop_map 3074479691</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/winterday_bs.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070951079">winterday_bs</a><br><sup><sub>host_workshop_map 3070951079</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/minus_denhet.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070541369">minus_denhet</a><br><sup><sub>host_workshop_map 3070541369</sub></sup></td></tr></table><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/hs_lake.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3071094345">hs_lake</a><br><sup><sub>host_workshop_map 3071094345</sub></sup></td></tr></table></td></tr></table>

#### mg_soccer

<table><tr><td><table align="left"><tr><td><img height="112" src="https://github.com/kus/cs2-modded-server/blob/assets/images/ka_soccer_2009.jpg?raw=true&sanitize=true"></td></tr><tr><td><a href="https://steamcommunity.com/sharedfiles/filedetails/?id=3070198374">ka_soccer_2009</a><br><sup><sub>host_workshop_map 3070198374</sub></sup></td></tr></table></td></tr></table>

### How do I add more bots?

By default bots are enabled in deathmatch, gungame, gungame ffa, retakes, scoutsknives and wingman.

The default is set to add 1 bot if only 1 human is in the server, and then if there is 2 or more humans there will be no bots.

You can overwrite the settings for the bots by creating a "[custom file](#custom-files)" for this file [custom_bots.cfg](https://github.com/kus/cs2-modded-server/blob/master/game/csgo/cfg/custom_bots.cfg).

If you copy [custom_bots.cfg](https://github.com/kus/cs2-modded-server/blob/master/game/csgo/cfg/custom_bots.cfg) and put it in the `custom_files/cfg/` directory (`/home/steam/cs2/custom_files/cfg/` on default Linux setup) and you can modify it and change say `bot_quota` to `10` if you want 10 players at all times. When the server starts (on Linux and Windows) it will merge this file into the game cfg and it will execute every time `bots.cfg` executes.

You can also just login to RCON `rcon_password yourpassword` and use `rcon bot_add_ct` and `rcon bot_add_t`.

If you want to remove bots you use `rcon bot_kick`.

### Failed to open libtier0.so

`Failed to open libtier0.so (/home/steam/cs2/bin/libgcc_s.so.1: version 'GCC_7.0.0' not found (required by /lib/i386-linux-gnu/libstdc++.so.6))`

This is because Valve ships their own copies of those libraries. As modern systems will have newer versions, you can safely delete the listed file from the server install. Do not delete the file in the system path (usually lib or lib32)[*](https://wiki.alliedmods.net/Installing_metamod:source).

`cd /home/steam/cs2/bin/` and `rm libgcc_s.so.1` and restart the server.

### How do I connect to RCON remotely?

[Download SourceAdminTool](https://nightly.link/Drifter321/admintool/workflows/build/master) ([source](https://github.com/Drifter321/admintool)) for your OS (you can read about it [here](https://forums.alliedmods.net/showthread.php?t=289370)) and click `Servers > Add Servers` and put in the `<IP>:27015` and when you see the server show in the list, down the bottom left type in your RCON password and click `Login` and you should be able to execute commands from the bottom text box i.e. `exec dm.cfg`

**You must connect to the server from the public IP if hosting an online server, not the LAN IP even if you are on the same network. The script logs the public IP `Starting server on XXX.XXX.XXX.XXX:27015`**

### Why can't I set the server to start automatically with a mod loaded

Because the way the server is setup with several mods it's not possible. You can't use `+exec` in the server launcher as that executes to quick before SourceMod is loaded. You can monitor the server once it's started (via RCON) and then load a mod i.e. `exec dm.cfg`.

## Acessing admin menu

Admins are managed by [CounterStrikeSharp](https://github.com/roflmuffin/CounterStrikeSharp) using the [Admin Framework](https://docs.cssharp.dev/admin-framework/defining-admins/). You define admins and their flags and most plugins now utilise this framework.

To see an example of my admins you can look at this file [/custom_files_example/addons/counterstrikesharp/configs/admins.json](https://github.com/kus/cs2-modded-server/blob/master/custom_files_example/addons/counterstrikesharp/configs/admins.json). To set your admins on your own server use this file as a reference and use the [custom files](#custom-files) system to have your own version.

## Changing game modes

There is no "menu" feature in CS2, so it's all via the chat window or server console.

The easiest way to manage the server is to use the Rcon commands via chat i.e. `!rcon exec dm` will change to deathmatch.

These are all the available chat commands to change the game mode:

| Command                | Game mode                                                                         |
| ---------------------- | --------------------------------------------------------------------------------- |
| `!rcon exec 1v1`     | 1v1 (allows more than 2 players)                                                    |
| `!rcon exec bhop`    | Bunny hop maps                                                                    |
| `!rcon exec comp`    | Competitive using [MatchZy](https://github.com/shobhit-pathak/MatchZy#usage-commands) |
| `!rcon exec course`  | Tests players with different traps, kz, surf, bhop                                |
| `!rcon exec dm`      | Deathmatch                                                                        |
| `!rcon exec gg`      | Gun Game                                                                          |
| `!rcon exec hns`     | Hide n Seek                                                                       |
| `!rcon exec kz`      | Kreedz Climbing                                                                   |
| `!rcon exec minigames` | Mini Games                                                                      |
| `!rcon exec prac`    | Practice (grenade lineups etc)                                                    |
| `!rcon exec retake`  | Retakes                                                                           |
| `!rcon exec scoutzknivez` | ScoutzKnivez                                                                 |
| `!rcon exec soccer`  | Soccer                                                                            |
| `!rcon exec surf`    | Surf                                                                              |
| `!rcon exec wingman` | Wingman (allows more than 4 players)                                              |

Changing between gamemodes multiple times is not recommended, and it is better if you restart the CS2 server in-between.

To view what other commands are available view the plugins at the top of the page.

## RCON doesn't work

Using RCON whilst connected to the server does not work. See discussion [here](https://www.reddit.com/r/GlobalOffensive/comments/167spzi/cs2_rcon/).
The current work arounds are:

- I have included [CS2Rcon](https://github.com/LordFetznschaedl/CS2Rcon) which allows admins to use !rcon in chat.
- You can disconnect from the server and use `rcon_address IP:PORT` in console and you can use rcon commands.
- Use an external RCON program which has implemented the RCON protocol such as [this](https://github.com/fpaezf/CS2-RCON-Tool-V2).

If it still doesn't work, make sure you try connect from CS2 outside of a game via console:

**You must connect to the server from the public IP if hosting an online server, not the LAN IP even if you are on the same network. The script logs the public IP `Starting server on XXX.XXX.XXX.XXX:27015`**

```bash
rcon_address ip:port
rcon_password "password"
rcon say "hi"
```

And check the ports cs2 is using on your OS i.e. on Ubuntu `sudo lsof -i -P -n | head -n 1; sudo lsof -i -P -n | grep cs2`.

## License

See `LICENSE` for more details.
