Vanilla Tweaks™ Lite documentation<br>
As-of version: `1.16.2-rc.1`<br>
Last updated: `May 18, 2026`<br>

---

### Introduction

Vanilla Tweaks™ Lite (VT) is a native, Dynamic Link Library (DLL) mod for Minecraft Bedrock Edition for Windows, developed and owned by ambient (`@ambiennt` on Discord). It aims to make subtle, but powerful improvements to gameplay (specifically PVP), as well as add several quality of life mods.

VT is developed for the keyboard and mouse control scheme. Other schemes, such as controller, are not officially supported.

The VT DLL is built for the 64-bit version of Minecraft for Windows and requires an x86-64 CPU with AVX2 (or newer) instruction set support.

The current versions that VT supports are: `1.21.114`.

---

### Installation and Setup

At any given time, VT will likely NOT support the latest version of Minecraft Bedrock Edition (MCBE). Therefore, it is recommended that the end user turn off Microsoft Store auto-updates (if applicable), and use a 3rd party version manager, such as [mc-w10-version-launcher
](https://github.com/MCMrARM/mc-w10-version-launcher) with [this](https://raw.githubusercontent.com/ddf8196/mc-w10-versiondb-auto-update/master/versions.json.min) version list endpoint.

The mod (in `.dll` format) can be 'injected' into a running instance of MCBE through any DLL injector of choice. I recommend [this](https://github.com/ambiennt/MCBE-DLL-Injector/releases/) due to the simple UI and minimal dependencies.

A successful injection will show a watermark, i.e. `Vanilla Tweaks Lite by Ambient`, followed by a version tag in the MCBE window titlebar. The mod can be unloaded, or 'ejected' by pressing `Control` + `L` at the same time on your keyboard, or typing `.eject` in the in-game chat. See the [Commands](#commands) section for a full list of commands.

For the sake of simplicity, and so that more focus can be diverted to core features, this mod has no UI. All configurable settings are deserialized from `%LOCALAPPDATA%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\RoamingState\VanillaTweaks\config.json`. This directory can be accessed via the Windows `Run` application. The `VanillaTweaks` directory, and the corresponding `config.json` file are created upon inject if they do not already exist. The configuration file is read upon inject and execution of the in-game `.config reload` command.

---

### Commands

Commands are only executable through the in-game chat interface. The default command prefix is `.`, but is configurable via the `command_prefix` setting (see below). The syntax for running a command follows similarly to vanilla commands, i.e. \<prefix\> \<command\> \<args...\>. Command arguments are delimited by one or more space. String arguments with spaces can be escaped with quotes, e.g. "my spaced argument" -> `my spaced argument`.<br><br>

`config <reload/open/copy/paste>`<br>
Provides config utility functions. Aliases: `cfg`.<br><br>The `reload` enum reloads the current `config.json` data at runtime. This differs from ejecting, then re-injecting VT, because this will not serialize its config options back to the `config.json` and overwrite any tentative changes. Example usage: `.config reload`.<br><br>The `open` enum opens the current `config.json` file with the system-defined program. Example usage: `.config open`.<br><br>The `copy` enum copies the current `config.json` contents to the system clipboard. Example usage: `.config copy`.<br><br>The `paste` enum reads the contents of the system clipboard and applies it as the active config (same effect as `.config reload`). Only takes effect if the clipboard contains valid JSON; otherwise the current config is left unchanged. Example usage: `.config paste`.<br><br>

`customchat <enable/disable/connect/disconnect>`<br>
Controls the custom chat system for the `custom_chat` feature. Aliases: `cc`.<br><br> The `enable` enum activates custom chat mode. When enabled, messages will default to being routed through the configured external chat server. This does not establish a network connection by itself. Example usage: `.customchat enable`.<br><br> The `disable` enum deactivates custom chat mode. Messages will default to vanilla Minecraft chat instead. This also does not close an existing connection to the external chat server; it only changes message routing behavior. Example usage: `.customchat disable`.<br><br> The `connect` enum attempts to establish a connection to the configured `custom_chat` endpoint. If not currently connected, this must be called before messages can be sent through the external chat server. If already connected or currently connecting, the request is ignored. Example usage: `.customchat connect`.<br><br> The `disconnect` enum closes the current connection to the external chat server. If not currently connected, the request is ignored. Example usage: `.customchat disconnect`.<br><br>Note: `enable`/`disable` control how messages are routed, while `connect`/`disconnect` control the network connection state. You may remain connected to the external chat server even while custom chat is disabled; in that case, messages will simply not be routed to it unless explicitly prefixed.<br><br>

`eject`<br>
Unloads VT from MCBE. This process takes about 0.25 seconds. If successful (although this command should never fail), the watermark in the titlebar should disappear. Example usage: `.eject`.<br><br>

`help [pageNumber: int = 1]`<br>
Shows a paginated list of client commands with their aliases and usage. Displays up to 20 commands per page (1-indexed). If the requested page is out of range, the nearest valid page is shown. Example usage: `.help 1`.<br><br>

`ip`<br>
Prints numerical IP, DNS, and port information of the currently joined server. Example usage: `.ip`.<br><br>

`netstat [verbose: bool = false]`<br>
Describes various network statistics, such as ping and total throughput. Aliases: `.ping`. Example usage: `.netstat true`.<br><br>

`stealskin <playerName: string> [fetchFromWorld: bool = false]`<br>
Steals a player's skin, cape, and geometry data. Player name lookups are case insensitive. `fetchFromWorld` retrieves the skin data from the target player in the world as opposed to the pause menu's player list. Prefer this option if there is no skin data from the player list, or the skin data from the player list undesirably differs. Retrieved skin files are outputted in `%LOCALAPPDATA%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\RoamingState\VanillaTweaks\StolenSkins\`. Example usages: `.stealskin username123`, `.stealskin "spacedusername 456" true`.<br><br>

---

### Built-in Features

VT has certain features that are non-configurable due to the vast amount of internal changes required to implement. As such, supporting the ability to turn on/off said feature would be too difficult to maintain.

`Performance Improvements`<br>
VT is already optimized for minimal overhead. There are a few zero-cost optimizations implemented to offset any performance penalties one might incur when using this mod. In practice, overall performance is approximately unchanged, with variation depending on hardware and configuration.

---

### Syntax Legend

\* Note: The `Allowed values` field in the following documentation uses set/interval notation:<br>
- `{...}` = set notation (roster): specific allowed values, e.g. {0, 1, 2}<br>
- `[a, b]` = closed interval (endpoints included)<br>
- `(a, b)` = open interval (endpoints excluded)<br>
- `[a, b)` / `(a, b]` = half-open interval<br><br>

A server address should be specified using the `<address>:<port>` format, where the `address` may be a server DNS name or numerical IP, and port may be a number between 0-65535. Unless otherwise specified, the wildcard `*` can be used to represent to all ports for that address (e.g. `127.0.0.1:3000`, `play.example1.net:19132`, `play.example2.net:*`).<br><br>

---

### Configurable Features

`always_show_paper_doll`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Forces the paper doll to always show, provided that the vanilla setting is enabled. In other words, any timers that show the paper doll for a limited time (e.g. 1 second after un-sneaking) are disabled.<br><br>

`attack_while_using_item_anim`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Plays the right arm/right item swing animation when attacking while using an item. The first-person animation is modeled after MCJE 1.7. This is a strictly visual change, and does not actually relay an attack input to the server.<br><br>

`background_app_execution`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Allows MCBE to execute while minimized or 'tabbed out' from a fullscreen window. This prevents the client from reloading its resources and auto-disconnecting while logged into a server.<br><br>

`cache_xbox_authentication_token`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Caches the Xbox game server authentication token after a successful multiplayer join and reuses it for future server join attempts while the token remains valid. This can reduce server join latency by skipping the repeated Xbox authentication request normally performed before each remote server connection. Cached tokens are account-specific and are not reused after expiration or when signed into a different Xbox account.<br><br>

`center_cursor`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Centers the cursor in the window when it becomes visible, e.g. when opening a container screen. This differs from the vanilla behavior, where the cursor remains in the last absolute screen coordinates in which it was visible.<br><br>

`command_prefix`<br>
Type: `string`<br>
Allowed values: *all non-empty strings whose first character is not `/`*<br>
Sets the prefix by which client commands are executed. Messages beginning with this prefix are parsed as client commands; other messages are sent as normal chat.<br><br>

`cubecraft_tweaks`<br>
Type: `object`<br>
Various tweaks to improve the user experience on CubeCraft.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `disable_serverside_entity_culling`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - CubeCraft uses a server-side entity culling system to hide entities that it believes are out of a player's view. This system is only active when the server has acknowledged the player is in first-person perspective. In practice, it can cause visual artifacts such as entities popping in/out due to latency, appearing late, or remaining invisible.<br>

<br><br>
`custom_chat`<br>
Type: `object`<br>
Routes messages through a user-hosted external chat server instead of the vanilla Minecraft chat system. This enables private, uncensored conversations between VT users that persist across different Minecraft servers, and provides faster message delivery in situations where server-side chat throttling occurs. An example chat server implementation can be found [here](https://github.com/ambiennt/tcp-chat-server).<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `default_to_custom_chat`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Controls the default routing behavior when typing messages. A setting value of `true` means messages are sent to the external chat server by default, and vice versa.<br>
- `endpoint`<br>
    - Type: `array` of `string`<br>
    - Allowed values: *any server address with port*<br>
    - The address of the external chat server to connect to.<br>
- `route_custom_to_vanilla_prefix`<br>
    - Type: `string`<br>
    - Allowed values: *all non-empty strings whose first character is not `/`*<br>
    - When in custom chat mode, messages beginning with this prefix are routed to vanilla Minecraft chat instead of the external chat server. Example (prefix set to `;`): `;hello non-VT users!`.<br>
- `route_vanilla_to_custom_prefix`<br>
    - Type: `string`<br>
    - Allowed values: *all non-empty strings whose first character is not `/`*<br>
    - When NOT in custom chat mode, messages beginning with this prefix are routed to the external chat server instead of the vanilla Minecraft chat. Example (prefix set to `,`): `,hello VT users!`.<br>
- `use_route_custom_to_vanilla_prefix`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the `route_custom_to_vanilla_prefix` behavior.<br>
- `use_route_vanilla_to_custom_prefix`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the `route_vanilla_to_custom_prefix` behavior.<br>

<br><br>
`damage_tilt`<br>
Type: `object`<br>
Multiplies the speed of the damage tilt animation about the target forward/look axis (local Z). The vanilla MCBE multiplier is actually `4.0`, but this appears slower than that of MCJE. A value of `0.0` effectively disables the respective damage tilt animation. Here is a [visual representation](https://www.desmos.com/calculator/ruih0owh8a) of the animation over the domain `[0.0, 1.0]`, where the exponent is the configured multiplier.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `camera_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Controls the strength of the *camera* damage tilt animation. Higher values indicate a faster tilt, and vice versa.<br>
- `first_person_objects_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Controls the strength of the damage tilt animation applied to *first-person objects* (equipped mainhand/offhand items, hands, etc. rendered in the first-person perspective). Higher values indicate a stronger faster tilt, and vice versa.<br>

<br><br>
`disable_breathing_bob`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Disables the 'breathing' animation in which the first-person item(s) bob up and down slightly when the vanilla `View Bobbing` setting is enabled. The ability to disable this animation exists to be in parity with MCJE.<br><br>

`disable_chat_tts_hotkey`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Disables the hardcoded `Control` + `B` hotkey to toggle the vanilla `Text To Speech For Chat` setting.<br><br>

`disable_cobweb_waterlogging`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Prevents water buckets from waterlogging cobwebs client-side. 'Waterlogging' refers to placing a water source block inside another block. This feature is useful when the intended action is to break a cobweb with water, since a waterlogged cobweb remains intact. It also avoids server/client desyncs on servers such as CubeCraft, where cobweb waterlogging transactions are rejected and cause incorrect bucket state or ghost water blocks.<br><br>

`disable_projectile_throw_swing_anim`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Disables the first and third-person right arm/right item swing animation when throwing a projectile. The ability to disable this animation exists to be in parity with MCJE. This only disables the animation for the local player, as there is no way to filter on a swing animation triggered by other players throwing projectiles.<br><br>

`enable_legacy_play_and_create_world_screens`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Overrides the web-based Ore UI play and create world screens with the legacy JSON-based screens. The advantage of using the legacy screens is that they load much faster, and are configurable via resource packs.<br><br>

`enable_legacy_skin_picker_screen`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Overrides the 1.13+ persona dressing room screen with the 1.12.1 (and prior) legacy skin picker screen. The advantage of using the legacy screen is that it loads much faster (both screens are JSON-based, however). Additionally, the layout is arguably more user-friendly and does not require any workarounds to display user-made skin packs with custom geometry or capes. Note: this is a deprecated feature. `skin_sideloader` should be used for changing skins over through the legacy skin picker screen.<br><br>

`enchant_glint_color`<br>
Type: `object`<br>
Configures the enchant glint color.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `base_ui_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the base enchant glint color for UI items. The alpha channel of this setting overrides the vanilla `Glint strength` setting.<br>
- `base_world_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the base enchant glint color for world items and equipment. The alpha channel of this setting overrides the vanilla `Glint strength` setting.<br>
- `base_ui_color_fills_ui_slot_area`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Determines whether the base enchant glint for UI items fills the full inventory slot area instead of only the non-transparent pixels of the item sprite.<br>
- `smooth_world_uv`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Corrects how the enchant glint overlay samples UVs on 2D texture items rendered in the world. Instead of sampling from a single corner of each item texture pixel, the glint uses the full pixel area, fixing the pixelated appearance on flat item textures and bringing the appearance closer to MCJE. Enabling this setting may cause visual artifacts when used alongside the ray tracing graphics mode.<br>
- `ui_uv_rotation`<br>
    - Type: `Vec2`<br>
    - Allowed values: [`-180.0`, `180.0`)<br>
    - Sets the UV rotation, in degrees, for the enchant glint rendered on UI items. The X and Y components control the rotation of each glint layer.<br>
- `world_uv_rotation`<br>
    - Type: `Vec2`<br>
    - Allowed values: [`-180.0`, `180.0`)<br>
    - Sets the UV rotation, in degrees, for the enchant glint rendered on world items and equipment. The X and Y components control the rotation of each glint layer.<br>
- `ui_inverse_uv_scale_multiplier`<br>
    - Type: `int`<br>
    - Allowed values: *unbounded*<br>
    - Controls how densely the enchant glint texture is tiled on UI items. Higher values make the glint pattern appear smaller and repeat more frequently. Lower values make it appear larger and repeat less frequently.<br>
- `world_inverse_uv_scale_multiplier`<br>
    - Type: `int`<br>
    - Allowed values: *unbounded*<br>
    - Controls how densely the enchant glint texture is tiled on world items and equipment. Higher values make the glint pattern appear smaller and repeat more frequently. Lower values make it appear larger and repeat less frequently.<br>
- `ui_uv_anim_speed_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Scales the UV animation speed for the enchant glint rendered on UI items. Higher values make the glint move faster. Lower values make it move more slowly. This setting overrides the vanilla `Glint speed` setting.<br>
- `world_uv_anim_speed_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Scales the UV animation speed for the enchant glint rendered on world items and equipment. Higher values make the glint move faster. Lower values make it move more slowly. This setting overrides the vanilla `Glint speed` setting.<br>

<br><br>
`entity_hitboxes`<br>
Type: `object`<br>
Draws a wire frame along an entity's hitbox bounds.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `line_thickness`<br>
    - Type: `float`<br>
    - Allowed values: [`0.0`, `0.1`]<br>
    - Sets the thickless (in world units) for each hitbox 'line'. This setting is only applicable if `use_line_thickness` is enabled.<br>
- `primary_hitbox_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the primary hitbox.<br>
- `primary_hitbox_selected_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the primary hitbox when the local player is looking at the entity within pick distance.<br>
- `render_for_players_only`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Skips rendering hitboxes on non-player entities.<br>
- `sub_hitbox_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the sub-hitbox color of multi-part entities (e.g., the Ender Dragon). This setting has no effect on entities without sub-hitboxes.<br>
- `use_line_thickness`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the application of the `line_thickness` on hitbox rendering. When `true`, hitbox edges are rendered as quads using the thickness set by `line_thickness` (in world units). When `false`, edges are drawn as simple line strips and `line_thickness` is ignored. Thick lines are more expensive to render; turn this setting off for the more performant path if you don't need a custom thickness.<br>

<br><br>
`entity_overlay_color`<br>
Type: `object`<br>
Changes various context-specific overlay colors for entities.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `armor_hurt_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the overlay color on equipped armor when the wearing entity is taking damage.<br>
- `armor_on_fire_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the overlay color on equipped armor when the wearing entity is on fire, but NOT taking damage.<br>
- `oscillate_armor_on_fire_color`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Sets whether the `armor_on_fire_color` should oscillate its green and alpha color channels, (in partity with vanilla MCBE), while using `armor_on_fire_color` as a base. Keep this setting `false` if you want the `armor_on_fire_color` to appear solid.<br>

<br><br>
`first_person_sword_block_anim`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Plays the MCJE 1.7 sword block animation in first-person when interacting. This animation is strictly visual, of course, because MCBE does not have any network-bound notion of sword blocking.<br><br>

`force_crafting_tab`<br>
Type: `object`<br>
Forces the crafting tab to always default to a configured index. This is only applicable to the inventory and the crafting table UIs. This feature is also advantageous because the cached tab index is not differentiable between different containers, and always (frustratingly) resets to the expanded view upon rejoining a game.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `crafting_table_show_all_recipes`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Controls the recipe filter for the crafting table UI. When enabled, all recipes are shown; when disabled, only currently craftable recipes are shown.<br>
- `crafting_table_tab_index`<br>
    - Type: `int`<br>
    - Allowed values: [`1`, `3`]<br>
    - The tab index for the crafting table UI. A tab index of `1` is the minimized view that doesn't show recipes, `2` is the inventory + recipe view, and `3` is the creative menu, which is only applicable in the creative gamemode.<br>
- `inventory_show_all_recipes`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Controls the recipe filter for the inventory UI. When enabled, all recipes are shown; when disabled, only currently craftable recipes are shown.<br>
- `inventory_tab_index`<br>
    - Type: `int`<br>
    - Allowed values: [`1`, `3`]<br>
    - The tab index for the inventory UI.<br>

<br><br>
`force_time_of_day`<br>
Type: `int`<br>
Allowed values: [`0`, `23999`]<br>
Overrides the world's time of day. A tick value of 0 represents sunrise, 6000 for noon, 12000 for sunset, and 18000 for midnight. A total day in Minecraft is 24000 ticks, or 20 real-life minutes.<br>

<br><br>
`freelook`<br>
Type: `object`<br>
Detaches the camera orbit from the local player's look direction. When `enabled` is `true`, holding down the configured `key` activates this feature.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `key`<br>
    - Type: `int`<br>
    - Allowed values: *unbounded*; see [Key Codes](#key-codes) for a list of enumerated keys.<br>
    - The key code used to activate the feature. This key uses hold logic.<br>

<br><br>
`gui_scale`<br>
Type: `object`<br>
Overrides the vanilla `GUI Scale Modifier` setting, with added flexibility.
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `disable_adaptive_scaling`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Disables the automatic GUI scaling mechanism when the MCBE window resizes. When this setting is off, any active GUI scale will be overwritten by the result of the vanilla adaptive scaling algorithm.<br>
- `scale_factor`<br>
    - Type: `float`<br>
    - Allowed values: [`1.0`, `8.0`]<br>
    - The factor to scale the GUI by. Vanilla scale values are {`1.0`, `2.0`, `3.0`, `4.0`, `5.0`, `6.0`, `7.0`, `8.0`}. Any scale that isn't precisely one of these values is subject to 'pixel stepping', where some source pixels are mapped to one screen pixel while others are mapped to two or more, producing an uneven, jagged appearance. This occurs because MCBE's UI scaling is nearest-neighbor (no anti-aliasing), so non-integer scale factors cause irregular pixel sizes.<br>

<br><br>
`inventory_hotkeys`<br>
Type: `object`<br>
Allows moving, swapping, and adding items within/between/from containers with a single key press. The inspiration for this feature comes from MCJE, in which hovering over a container slot and pressing one of the hotbar slot keys will swap the item stack in the hovered slot with the item stack in the respective hotbar slot. This implementation differs from MCJE in that item stacks of the same item type are added instead of swapped, where applicable.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `drop_hotbar_selected_stack_key`<br>
    - Type: `int`<br>
    - Allowed values: *unbounded*; see [Key Codes](#key-codes) for a list of enumerated keys.<br>
    - The key code used to drop the entire hovered item stack while in HUD. This provides a more accessible alternative to the vanilla control + `Drop Item` keybind by allowing the action to be performed with a single key press.<br>
- `drop_hovered_stack_key`<br>
    - Type: `int`<br>
    - Allowed values: *unbounded*; see [Key Codes](#key-codes) for a list of enumerated keys.<br>
    - The key code used to drop the entire hovered item stack while in a container screen. This is also beneficial for the same reasons as `drop_hotbar_selected_stack_key`.<br>
- `flying_item_anim`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the 'flying item' animation when an item stack is being swapped. This is the same animation that plays in vanilla MCBE when an item stack is shift-clicked into another slot. Disabling this animation means that the swap appears 'instant'.<br>

<br><br>
`inventory_player_rotation_fix`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Fixes an animation bug with the paper doll in the inventory screen in which the model root x-axis rotation is inverted.<br><br>

`item_matrix_fix`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Restores the default hardcoded matrix transformations (position, rotation, and scale) for certain vanilla items (such as snowballs and arrows) and all custom items, bypassing their data-driven settings.<br><br>

`java_first_person_bow_anim`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Enables a custom reimplementation of the MCJE 1.7 first-person bow animation. This replaces the default JSON-based bow attachable (in both first and third-person). When enabled, it also allows `enchant_glint_color`'s `smooth_world_uv` feature to apply to bows.<br><br>

`java_first_person_crossbow_anim`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Enables a custom reimplementation of the MCJE 1.14+ first-person crossbow animation. This replaces the default JSON-based crossbow attachable (in both first and third-person). When enabled, it also allows `enchant_glint_color`'s `smooth_world_uv` feature to apply to crossbows.<br><br>

`java_view_bobbing`<br>
Type: `object`<br>
A faithful reimplementation of the MCJE view bobbing animation, with extra configurations. This feature only applies when the vanilla `View Bobbing` setting is enabled.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `camera_strength`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Controls the strength of the *camera* bobbing animation. Higher values indicate a stronger bob, and vice versa.<br>
- `first_person_objects_strength`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Controls the strength of the bobbing animation applied to *first-person objects* (equipped mainhand/offhand items, hands, etc. rendered in the first-person perspective). Higher values indicate a stronger bob, and vice versa.<br>
- `first_person_objects_velocity_damping_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Scales how quickly a first-person object's motion decays after the camera orbits. Lower values make it ease back more slowly and smoothly. Higher values make it settle faster.<br>
- `first_person_objects_velocity_stiffness_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Scales the spring strength pulling the first-person object back to its rest pose. Lower values appear more 'springy'. Higher values appear stiffer/tighter.<br>

<br><br>
`lighting_options`<br>
Type: `object`<br>
Various options to change terrain lighting.
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `brightness`<br>
    - Type: `float`<br>
    - Allowed values: [`0.0`, `+∞`)<br>
    - Sets the brightness applied to the terrain texture. This setting binds diectly to the vanilla `Brightness` slider setting, but is uncapped. Setting arbitrarily high values, e.g. `100.0` can mimic a traditional 'full-bright' mode.<br>
- `night_vision`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles whether the `night_vision_scale` setting applies.<br>
- `night_vision_scale`<br>
    - Type: `bool`<br>
    - Allowed values: [`0.0`, `1.0`]<br>
    - The normalized scale by which the terrain texture should apply the visibility boost used by the night vision effect. This differs from the `brightness` setting in that it more closely respects the shading of the current environment.<br>
- `overworld_sunrise_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color of the overworld sunrise on the terrain light texture. This color is less pronounced when higher brightness/night vision values are set.<br>

<br><br>
`limb_swing_anim_fix`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Fixes a longstanding animation bug in which the arm and leg movement swing for the local player appears 'stiff', and has a weaker amplitude when the jump key is pressed.<br><br>

`load_resource_pack_shader_materials`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Allows `*.material.bin` files to be loaded by resource packs. Overwritten files must be placed in the `./renderer/materials/` folder, relative to the current resource pack root.<br><br>

`movement_options`<br>
Type: `object`<br>
Various settings to control player movement.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `toggle_sprint`<br>
    - Type: `object`<br>
    - Automatically enables the sprint state without the end user having to press the sprint key.
        - `enabled`<br>
            - Type: `bool`<br>
            - Allowed values: {`true`, `false`}<br>
            - Toggles the parent object.<br>
        - `key`<br>
            - Type: `int`<br>
            - Allowed values: *unbounded*; see [Key Codes](#key-codes) for a list of enumerated keys.<br>
            - The key code used to activate the feature. This key uses toggle logic.<br>

<br><br>
`name_tag_options`<br>
Type: `object`<br>
A highly customizable from-scratch reimplementation of entity name tag rendering.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `background_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the nameplate.<br>
- `background_darken_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the 'darkened' nameplate. 'Darken' in the context of name tags refers to when the entity is sneaking, as the vanilla name tag is visually darkened in this scenario.<br>
- `billboard_rotation_fix`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Fixes billboarding calculations so name tags properly face the camera. In vanilla MCBE, name tags can appear skewed near the edges of the view frustum because their camera-facing rotation is affected by view-dependent distortion from effects such as FOV changes and view bobbing.<br>
- `render_for_local_player`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Enables name tag rendering for the local player.<br>
- `local_pos_offset`<br>
    - Type: `vec2`<br>
    - Allowed values: *unbounded*<br>
    - Applies a local offset to the name tag in *screen-space* relative to the camera orientation. Positive X moves the tag right (perpendicular to the camera's view direction), and positive Y moves it upward in the camera's local space.<br>
- `scale`<br>
    - Type: `float`<br>
    - Allowed values: [`0.0`, `4.0`]<br>
    - Applies a uniform scale factor to the name tag.<br>
- `text_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the name tag text. Only the alpha channel of this setting is applied when the text is color coded via `§`. Inversely, the RGB component of the text color is always superceded by whatever the active color code is, if applicable.<br>
- `text_darken_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the darkened name tag text. The same preconditions as `text_color` apply for this setting.<br>
- `text_shadow`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Enables a shadow effect for the name tag text.<br>
- `text_shadow_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the text shadow. This color is always applied, regardless of color format specifiers (e.g. `§a`). This setting is only applicable if `text_shadow` is enabled.<br>
- `text_shadow_darken_color`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Sets the color for the text shadow when the name tag is darkened. This color is always applied, regardless of color format specifiers (e.g. `§a`). This setting is only applicable if `text_shadow` is enabled.<br>
- `text_shadow_offset`<br>
    - Type: `vec2`<br>
    - Allowed values: *unbounded*<br>
    - Offsets the text shadow relative to the text positioning. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up. This setting is only applicable if `text_shadow` is enabled.<br>
- `world_pos_offset_y`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Offsets the name tag world position along the Y axis. Positive Y moves up, negative Y moves down.<br>

<br><br>
`override_game_sensitivity`<br>
Type: `object`<br>
Overrides MCBE's in-game sensitivity, allowing finer-grained control beyond the vanilla `Camera Sensitivity` slider setting.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `scale_to_window_size_x`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Scales the in-game sensitivity in linear proportion to the x dimension of the MCBE window size. The 'default' window size is fullscreen, i.e. the monitor size. For example, if you have both `scale_to_window_size_x` and `scale_to_window_size_y` enabled, and are playing on a 1920 x 1080 monitor, shrinking your window dimensions to 1920/2 x 1080/2 = 960 x 540 will halve your in-game sensitivity.<br>
- `scale_to_window_size_y`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Scales the in-game sensitivity in linear proportion to the y dimension of the MCBE window size. The same logic as `scale_to_window_size_x` applies, but for the y dimension.<br>
- `value`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - The raw in-game sensitivity value to be overridden. This value has the same units as the vanilla sensitivity slider. This field and the vanilla slider write to the same value-last write wins. If you adjust the vanilla slider, it overwrites this field (the override stops applying) until you set value again, either by re-injecting, or executing `.config reload`.<br>
- `weighted_scale_multiplier`<br>
    - Type: `Vec2`<br>
    - Allowed values: *unbounded*<br>
    - The relative weighting of the raw camera turn delta per axis. Increasing one component biases the turn toward that axis and can make the other axis feel proportionally slower.<br>

<br><br>
`perspective_fov`<br>
Type: `object`<br>
Controls the camera field of view (FOV) independently for each perspective. This allows fine-tuning the perceived zoom or spatial distortion when switching between first-person and third-person views. The valid range matches the vanilla `FOV` slider setting, typically `30.0` - `110.0` degrees.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `first_person`<br>
    - Type: `float`<br>
    - Allowed values: *dynamic*; see object description.<br>
    - Sets the FOV used while in first-person view.<br>
- `third_person_back`<br>
    - Type: `float`<br>
    - Allowed values: *dynamic*; see object description.<br>
    - Sets the FOV used while in third-person back view.<br>
- `third_person_front`<br>
    - Type: `float`<br>
    - Allowed values: *dynamic*; see object description.<br>
    - Sets the FOV used while in third-person front view.<br>

<br><br>
`premium_skin_spoof`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Spoofs your skin to add the same 'premium' tag that marketplace/paid skins have. Some servers may use this tag to determine whether to trust a player's skin. Although, in practice, this likely does not have an affect on anything.<br><br>

`protocol_proxy`<br>
Type: `object`<br>
Enables protocol translation between the client's native version and a different target protocol version. This allows the client to join servers enforcing a newer protocol version. Current target protocol version: `1.21.132`. This feature is not supported on all servers and is currently only tested on CubeCraft. Certain content introduced after the client's native game version, such as newer blocks and items, is not implemented and will not function correctly.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `servers`<br>
    - Type: `array` of `string`<br>
    - Allowed values: *any server address*<br>
    - Specifies a whitelist of servers on which protocol translation is applied. The proxy will only activate when connecting to a listed server.<br>

<br><br> 
`render_options`<br>
Type: `object`<br>
Various settings to change rendering visuals.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `disable_particle_rendering`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Disables all in-game particles from rendering. Particle rendering in MCBE is very computationally expensive on the GPU, so enabling this setting will net a very large performance improvement.
- `disable_sky_rendering`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Disables clouds, sun, and moon tessellation, and sky tinting. This is an override of disabling the vanilla `Render Clouds` and `Beautiful Skies` setting. Enabling this setting nets a minor performance improvement.
- `disable_weather_rendering`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Disables rain, snow, and thunder rendering. Enabling this setting nets a minor performance improvement when such weather is present.
- `force_disable_vsync`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Disables frame vertical synchronization (vsync) at the graphics backend level. This feature is useful if you are on a device in which the following do not work to disable vsync:<br>
        - Setting the internal vanilla `gfx_vsync` option to `0`, i.e. `gfx_vsync:0` in `%LOCALAPPDATA%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\LocalState\games\com.mojang\minecraftpe\options.txt`<br>
        - Disabling vsync for `Minecraft.Windows.exe` in your graphics card control panel<br>

<br><br>
`saturation_display`<br>
Type: `object`<br>
Displays the local player's saturation attribute value in the HUD. The display text has precision up to 2 decimal places.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `screen_anchor`<br>
    - Type: `int`<br>
    - Allowed values: [`0`, `8`]; see [Anchor IDs](#anchor-ids) for a list of enumerated IDs.<br>
    - Selects which screen anchor point the display text will be aligned to.<br>
- `screen_anchor_offset`<br>
    - Type: `vec2`<br>
    - Allowed values: *unbounded*<br>
    - Adjusts the position of the display relative to the chosen `screen_anchor`, measured in raw screen pixels. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up.<br>
- `text_anchor`<br>
    - Type: `int`<br>
    - Allowed values: [`0`, `8`]; see [Anchor IDs](#anchor-ids) for a list of enumerated IDs.<br>
    - Defines which point on the text's bounding box is aligned to the `screen_anchor`. By default, text is rendered from its top-left corner. Changing this allows you align the text relative to the screen anchor. In most cases, you will want `text_anchor` to match `screen_anchor` so that the chosen point on the text lines up with the chosen point on the screen.<br>
- `text_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the display text.<br>
- `text_shadow`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Enables a shadow effect for the display text.<br>
- `text_shadow_color`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the color for the text shadow. This setting is only applicable if `text_shadow` is enabled.<br>
- `text_shadow_offset`<br>
    - Type: `vec2`<br>
    - Allowed values: *unbounded*<br>
    - Sets offset of the text shadow relative to the text positioning. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up. This setting is only applicable if `text_shadow` is enabled.<br>
- `text_size`<br>
    - Type: `float`<br>
    - Allowed values: [`16.0`, `256.0`]<br>
    - Sets the size, in raw screen pixels, of the display text.<br>

<br><br>
`screen_anims_fix`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Changes the behavior of the main menu panorama and splash text to keep animating even when the vanilla `Screen Animations` setting is off.<br><br>

`skin_sideloader`<br>
Type: `object`<br>
Loads a custom player skin and optional cape directly from disk upon joining a server, bypassing the in-game skin menu. This can help resolve issues where skins fail to load or sync through the vanilla skin system. Skin and cape files are expected to be located relative to the configuration directory: `%LOCALAPPDATA%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\RoamingState\VanillaTweaks\`.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `cape_filename`<br>
    - Type: `string`<br>
    - Allowed values: *any valid `.png` filename*<br>
    - Specifies the filename of the cape texture to load. The path is relative to the configuration directory, and the file must be located in the configuration directory root. Supported texture dimensions: `64x32` pixels. Example: `my_cape.png`.<br>
- `is_slim_arm`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Determines whether the skin uses the slim (Alex) model with 3-pixel-wide arms (`true`) or the classic (Steve) model with 4-pixel-wide arms (`false`).<br>
- `load_cape`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Enables loading and applying the custom cape file on login.<br>
- `load_skin`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Enables loading and applying the custom skin file on login.<br>
- `show_cape_texture_on_elytra`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Controls whether the cape texture is displayed on the elytra when equipped. When disabled, the dedicated elytra texture is shown instead. This setting should be disabled if the cape texture does not include an elytra part.<br>
- `skin_filename`<br>
    - Type: `string`<br>
    - Allowed values: *any valid `.png` filename*<br>
    - Specifies the filename of the skin texture to load. The path is relative to the configuration directory, and the image file must be located in the configuration directory root. Supported texture dimensions: `64x32`, `64x64`, or `128x128` pixels. Example: `my_skin.png`.<br>

<br><br>
`sky_color`<br>
Type: `object`<br>
Overrides the sky color for different dimensions.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `nether`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the sky color for the Nether dimension.<br>
- `overworld`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the sky color for the Overworld dimension.<br>
- `overworld_is_brightness_dependent`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - When enabled, the `overworld` sky color is modulated by the time of day: darker at night, brighter during the day. This matches vanilla behavior.<br>
- `the_end`<br>
    - Type: `RGBA HTML color`<br>
    - Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
    - Sets the sky color for The End dimension.<br>

<br><br>
`theme_accent_color`<br>
Type: `RGBA HTML color`<br>
Allowed values: [`#00000000`, `#FFFFFFFF`]<br>
Sets the accent color used by VT in chat messages. Currently, a translucent alpha channel is not supported. This color is also accessible via the formatting code `§w`.<br><br>

`use_xbox_profile_pictures`<br>
Type: `bool`<br>
Allowed values: {`true`, `false`}<br>
Uses Xbox Live profile pictures for all players, overriding the default Marketplace/Persona-generated skin headshot system.<br><br>

`zoom`<br>
Type: `object`<br>
Dynamically sets the camera FOV to zoom in farther-away objects. When `enabled` is `true`, holding down the configured `key` activates this feature.<br>
- `enabled`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles the parent object.<br>
- `hide_first_person_objects`<br>
    - Type: `bool`<br>
    - Allowed values: {`true`, `false`}<br>
    - Toggles whether first-person objects are hidden while this feature is enabled.<br>
- `key`<br>
    - Type: `int`<br>
    - Allowed values: *unbounded*; see [Key Codes](#key-codes) for a list of enumerated keys.<br>
    - The key code used to activate the feature. This key uses hold logic.<br>
- `inverse_easing_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Scales how fast the zoom eases to its new level when you scroll with your mouse. This uses the [in out cubic](https://easings.net/#easeInOutCubic) easing function. Lower values correspond to a slower ease. higher values correspond to a faster ease. A multiplier of `1.0` is the baseline, indicating that the easing animation takes `1.0` seconds to elapse. Setting this value to `0.0` effectively disables the easing animation.<br>
- `inverse_fov_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Sets the initial FOV amount the moment you start zooming (before any scroll). Effective FOV = `<base fov>` / `inverse_fov_multiplier`. A multiplier of `1.0` is the baseline, indicating that the initial zoom amount is equal to: `1.0` * the current FOV (identical).<br>
- `inverse_sensitivity_multiplier`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - Scales mouse sensitivity while zooming. Effective sensitivity = `<base sensitivity>` / `inverse_sensitivity_multiplier`. A multiplier of `1.0` is the baseline, indicating that the sensitivity is equal to: `1.0` * the current sensitivity (identical).<br>
- `scroll_delta`<br>
    - Type: `float`<br>
    - Allowed values: *unbounded*<br>
    - The step size for scroll-based zoom changes. Smaller values equate to finer control (smaller jumps). Larger values equate to coarser control (bigger jumps). Scrolling up on the mouse adds the delta (zooms in if positive), scrolling down subtracts the delta (zooms out if positive).<br><br>

---

### Key Codes

| Key                | Value |
| ------------------ | ----: |
| Left Click         |   -99 |
| Right Click        |   -98 |
| Middle Click       |   -97 |
| Back Button        |   -95 |
| Forward Button     |   -94 |
| Unassigned         |     0 |
| Go Back            |     4 |
| Backspace          |     8 |
| Tab                |     9 |
| Enter              |    13 |
| Shift              |    16 |
| Control            |    17 |
| Alt                |    18 |
| Pause              |    19 |
| Caps Lock          |    20 |
| Escape             |    27 |
| Space              |    32 |
| Page Up            |    33 |
| Page Down          |    34 |
| End                |    35 |
| Home               |    36 |
| Left Arrow         |    37 |
| Up Arrow           |    38 |
| Right Arrow        |    39 |
| Down Arrow         |    40 |
| Insert             |    45 |
| Delete             |    46 |
| Zero               |    48 |
| One                |    49 |
| Two                |    50 |
| Three              |    51 |
| Four               |    52 |
| Five               |    53 |
| Six                |    54 |
| Seven              |    55 |
| Eight              |    56 |
| Nine               |    57 |
| A                  |    65 |
| B                  |    66 |
| C                  |    67 |
| D                  |    68 |
| E                  |    69 |
| F                  |    70 |
| G                  |    71 |
| H                  |    72 |
| I                  |    73 |
| J                  |    74 |
| K                  |    75 |
| L                  |    76 |
| M                  |    77 |
| N                  |    78 |
| O                  |    79 |
| P                  |    80 |
| Q                  |    81 |
| R                  |    82 |
| S                  |    83 |
| T                  |    84 |
| U                  |    85 |
| V                  |    86 |
| W                  |    87 |
| X                  |    88 |
| Y                  |    89 |
| Z                  |    90 |
| Numpad 0           |    96 |
| Numpad 1           |    97 |
| Numpad 2           |    98 |
| Numpad 3           |    99 |
| Numpad 4           |   100 |
| Numpad 5           |   101 |
| Numpad 6           |   102 |
| Numpad 7           |   103 |
| Numpad 8           |   104 |
| Numpad 9           |   105 |
| Mulitply           |   106 |
| Add                |   107 |
| Subtract           |   109 |
| Decimal            |   110 |
| Divide             |   111 |
| F1                 |   112 |
| F2                 |   113 |
| F3                 |   114 |
| F4                 |   115 |
| F5                 |   116 |
| F6                 |   117 |
| F7                 |   118 |
| F8                 |   119 |
| F9                 |   120 |
| F10                |   121 |
| F11                |   122 |
| F12                |   123 |
| F13                |   124 |
| Num Lock           |   144 |
| Scroll Lock        |   145 |
| Semicolon          |   186 |
| Equal              |   187 |
| Comma              |   188 |
| Minus              |   189 |
| Period             |   190 |
| Forward Slash      |   191 |
| Back Tick          |   192 |
| Left Hard Bracket  |   219 |
| Backward Slash     |   220 |
| Right Hard Bracket |   221 |
| Apostrophe         |   222 |

---

### Anchor IDs

| Anchor             | Value |
| ------------------ | ----: |
| Top Left           |     0 |
| Top Center         |     1 |
| Top Right          |     2 |
| Center Left        |     3 |
| Center             |     4 |
| Center Right       |     5 |
| Bottom Left        |     6 |
| Bottom Center      |     7 |
| Bottom Right       |     8 |