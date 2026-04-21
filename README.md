# waybar-jellyfin-tui-now-playing
A simple waybar custom module for displaying currently playing media from jellyfin-tui. Shows the artist name and provides a tooltip with detailed track information including artist, album, and title.

## Demo
<img width="694" height="264" alt="screenshot-2026-04-21_13-49-55" src="https://github.com/user-attachments/assets/f42e6e71-2a78-4c68-9250-2b09b9144463" />

## Requirements
- [waybar](https://github.com/Alexays/Waybar) - A highly customizable wayland bar
- [playerctl](https://github.com/acrisci/playerctl) - A command-line utility for controlling media players
- A playerctl-supported media player (e.g., [jellyfin-tui](https://github.com/dhonus/jellyfin-tui), spotify, mpc, vlc, etc.)

## Installation
1. Copy the `waybar-now-playing` script
2. Add it to your `$PATH` (e.g., ~/.local/bin/)
3. Make it executable:
  ```
  chmod +x waybar-now-playing
  ```
4. Add to your waybar config:
  ```jsonc
  "custom/now-playing": {
    "exec": "waybar-now-playing",
    "interval": 2,
    "format": "{text}",
    "return-type": "json",
    "tooltip": "{tooltip}"
  }
  ```

## How It Works
The script uses `playerctl` to query the currently playing track from your media player. It outputs JSON formatted for waybar, displaying the artist name in the bar and showing full track details (artist, album, title) in the tooltip on hover.

The module only displays output when media is actively playing—it exits silently when paused or stopped, keeping your bar clean.

## Adapting to other players
The script is currently hardcoded to use `jellyfin-tui`. To use a different player:

### Option 1: Change the player in the script
Edit the script and replace `jellyfin-tui` with your player name, for instance, `spotify`:
```bash
status=$(playerctl -p spotify status 2>/dev/null)
artist=$(playerctl -p spotify metadata --format '{{xesam:artist}}')
title=$(playerctl -p spotify metadata --format '{{xesam:title}}')
album=$(playerctl -p spotify metadata --format '{{xesam:album}}')
```

You can list all your players with `playerctl -l`.

### Option 2: Use the default player (no specific player)
Remove the `-p player-name` flag entirely to use whatever player is currently active:
```bash
status=$(playerctl status 2>/dev/null)
artist=$(playerctl metadata --format '{{xesam:artist}}')
title=$(playerctl metadata --format '{{xesam:title}}')
album=$(playerctl metadata --format '{{xesam:album}}')
```

## Customization
### Changing the icon
The script uses the "music note" icon. This appears in your waybar, next to the currently playing artist. To change it, edit the last line:
```bash
printf '{"text":"  %s","tooltip":"%s"}\n' "$artist" "$tooltip"
```

Refer to https://www.nerdfonts.com/cheat-sheet for other possible icons.

### Changing tooltip opacity
The tooltip uses alpha values (72% and 96%) to style secondary vs primary text. This makes it naturally adapt to other color schemes you may be using. Adjust these values as needed in the `tooltip` line:
```bash
tooltip="<span alpha='72%'>󰠃 Artist</span>   <span alpha='96%'>${artist}</span>\n<span alpha='72%'>󰀥 Album</span>    <span alpha='96%'>${album}</span>\n<span alpha='72%'> Song</span>     <span alpha='96%'>${title}</span>"
```
