Vanilla Tweaks™ Lite documentation<br>
As-of version: `1.17.1-beta`<br>
Last updated: `June 17, 2026`<br>

---
---

### Introduction

Vanilla Tweaks™ Lite (VT) is a native, Dynamic Link Library (DLL) mod for Minecraft Bedrock Edition for Windows, developed and owned by ambient (`@ambiennt` on Discord). It aims to make subtle, but powerful improvements to gameplay (specifically PVP), as well as add several quality of life mods.

VT is developed for the keyboard and mouse control scheme. Other schemes, such as controller, are not officially supported.

The VT DLL is built for the 64-bit version of Minecraft for Windows and requires an x86-64 CPU with AVX2 (or newer) instruction set support.

The current versions that VT supports are: `1.21.114`.

---
---

### Installation and Setup

At any given time, VT will likely NOT support the latest version of Minecraft Bedrock Edition (MCBE). Therefore, it is recommended that the end user turn off Microsoft Store auto-updates (if applicable), and use a 3rd party version manager, such as [mc-w10-version-launcher
](https://github.com/MCMrARM/mc-w10-version-launcher) with [this](https://raw.githubusercontent.com/ddf8196/mc-w10-versiondb-auto-update/master/versions.json.min) version list endpoint. This approach also installs MCBE as an unpackaged installation (loose files in a normal, writable folder rather than in `C:\Program Files\WindowsApps\`), which avoids UWP sandboxing restrictions and provides the best compatibility.

The mod (in `.dll` format) can be 'injected' into a running instance of MCBE through any DLL injector of choice. [This injector](https://github.com/ambiennt/MCBE-DLL-Injector/releases/) is recommended due to the simple UI and minimal dependencies.

A successful injection will show a watermark, i.e. `Vanilla Tweaks Lite by Ambient`, followed by a version tag in the MCBE window titlebar. The mod can be unloaded, or 'ejected' by pressing `Control` + `L` at the same time on your keyboard, or typing `.eject` in the in-game chat. See the [Commands](#commands) section for a full list of commands.

For the sake of simplicity, and so that more focus can be diverted to core features, this mod has no UI. All configurable settings are deserialized from `%LOCALAPPDATA%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\RoamingState\VanillaTweaks\config.json`. This directory can be accessed via the Windows `Run` application. The `VanillaTweaks` directory, and the corresponding `config.json` file are created upon inject if they do not already exist. The configuration file is read upon inject and execution of the in-game `.config reload` command.

---
---

### Commands

Commands are only executable through the in-game chat interface. The default command prefix is `.`, but is configurable via the `command_prefix` setting (see below). The syntax for running a command follows similarly to vanilla commands, i.e. \<prefix\> \<command\> \<args...\>. Command arguments are delimited by one or more space. String arguments with spaces can be escaped with quotes, e.g. "my spaced argument" -> `my spaced argument`.

---

#### `config <reload/open/copy/paste>`
Provides config utility functions. Aliases: `cfg`.

The `reload` enum reloads the current `config.json` data at runtime. This differs from ejecting, then re-injecting VT, because this will not serialize its config options back to the `config.json` and overwrite any tentative changes.

The `open` enum opens the current `config.json` file with the system-defined program.

The `copy` enum copies the current `config.json` contents to the system clipboard.

The `paste` enum reads the contents of the system clipboard and applies it as the active config (same effect as `.config reload`). This action only takes effect if the clipboard contains valid JSON; otherwise the current config is left unchanged.

Example usage:
- `.config reload`
- `.config open`
- `.config copy`
- `.config reload`
- `.config paste`

---

#### `customchat <enable/disable/connect/disconnect>`
Controls the custom chat system for the `custom_chat` feature. Aliases: `cc`.

The `enable` enum activates custom chat mode. When enabled, messages will default to being routed through the configured external chat server. This does not establish a network connection by itself.

The `disable` enum deactivates custom chat mode. Messages will default to vanilla Minecraft chat instead. This also does not close an existing connection to the external chat server; it only changes message routing behavior.

The `connect` enum attempts to establish a connection to the configured `custom_chat` endpoint. If not currently connected, this must be called before messages can be sent through the external chat server. If already connected or currently connecting, the request is ignored.

The `disconnect` enum closes the current connection to the external chat server. If not currently connected, the request is ignored.

Note: `enable`/`disable` control how messages are routed, while `connect`/`disconnect` control the network connection state. You may remain connected to the external chat server even while custom chat is disabled; in that case, messages will simply not be routed to it unless explicitly prefixed.

Example usage:
- `.customchat enable`
- `.customchat disable`
- `.customchat connect`
- `.customchat disconnect`

---

#### `eject`
Unloads VT from MCBE. This process takes about 0.25 seconds. If successful (although this command should never fail), the watermark in the titlebar should disappear.

Example usage:
- `.eject`

---

#### `help [pageNumber: int = 1]`
Shows a paginated list of client commands with their aliases and usage. Displays up to 20 commands per page (1-indexed). If the requested page is out of range, the nearest valid page is shown.

Example usage:
- `.help 1`

---

#### `ip`
Prints numerical IP, DNS, and port information of the currently joined server.

Example usage:
- `.ip`

---

#### `netstat [verbose: bool = false]`
Describes various network statistics, such as ping and total throughput. Aliases: `.ping`.

Example usage:
- `.netstat true`

---

#### `stealskin <playerName: string> [fetchFromWorld: bool = false]`
Steals a player's skin, cape, and geometry data. Player name lookups are case insensitive. `fetchFromWorld` retrieves the skin data from the target player in the world as opposed to the pause menu's player list. Prefer this option if there is no skin data from the player list, or the skin data from the player list undesirably differs. Retrieved skin files are outputted in `%LOCALAPPDATA%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\RoamingState\VanillaTweaks\StolenSkins\`.

Example usage:
- `.stealskin username123`
- `.stealskin "spacedusername 456" true`

---
---

### Built-in Features

VT has certain features that are non-configurable due to the vast amount of internal changes required to implement. As such, supporting the ability to turn on/off said feature would be too difficult to maintain.

---

#### Java Inventory, Input, and Movement System (Lite)
Java Inventory, Input, and Movement System (JIIMS) overhauls the input, and input -> build action pipeline to be more continuous and precise. This new system borrows heavily from the mechanics of Minecraft Java Edition (MCJE). Here, 'build action' refers to an attack (left click) or interact (right click) input. Additionally, JIIMS also applies changes to packet handling to reduce some of the negative effects of latency.

- MCBE has a longstanding client-side desynchronization issue where changes to equipment slots (such as which hotbar slot is selected) are not reported to the server until the next game tick. Because item use and other inventory transactions can occur at a higher frequency than a tick, there is a short window (up to one tick) in which the client may attempt to use an item before the server is informed of the new changes to the relevant slot. In that case, the server can legitimately reject the action because it does not match the server's current view of the player's equipment items. JIIMS addresses this issue by sending the relevant updates immediately and in-order relative to subsequent item-use actions, ensuring the server receives it before the corresponding use occurs. In practice, many modern servers implement their own mitigation by tolerating or reconciling this bug, so the difference may be subtle depending on the server.

- In vanilla MCBE, certain 'simulation-tick' item-use interactions (automatically generated while interact is held, rather than from discrete input presses) are still forwarded to the server even when they are predicted to fail. This contributes to client/server block placement desynchronization and reduced item-use reliability on higher latency connections. JIIMS cancels these predicted-failure interactions to ensure only meaningful item-use actions are sent during continuous use (e.g., holding interact while placing blocks or using items).

---

#### Performance Improvements
VT is already optimized for minimal overhead. There are a few zero-cost optimizations implemented to offset any performance penalties one might incur when using this mod. In practice, overall performance is approximately unchanged, with variation depending on hardware and configuration.

---
---

### Syntax Legend

The `Allowed values` field uses set and interval notation:

- `{...}` = a finite set of explicitly allowed values, e.g. `{0, 1, 2}`
- `[a, b]` = a closed interval; both endpoints are included
- `(a, b)` = an open interval; both endpoints are excluded
- `[a, b)` / `(a, b]` = a half-open interval; only one endpoint is included

Server addresses use the `<address>:<port>` format. The `address` may be a DNS hostname or a numeric IP address, and `port` must be a value in the range `[0, 65535]`.

The `*` symbol may be used as a port wildcard, meaning "all ports for this address."

Examples:
- `127.0.0.1:3000`
- `play.example1.net:19132`
- `geo.server.com:*`

---
---

### Configurable Features

#### `always_show_paper_doll`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Forces the paper doll to remain visible, provided that the vanilla `Hide Paper Doll` setting is disabled. In vanilla, the paper doll may appear only under certain conditions, such as while sneaking or for 1 second after the player stops sneaking; this feature bypasses those temporary visibility rules.

---

#### `attack_while_using_item_anim`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Plays the right arm/right item swing animation when attacking while using an item. The first-person animation is modeled after MCJE 1.7. This is a strictly visual change, and does not actually relay an attack input to the server.

---

#### `background_app_execution`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Allows MCBE to continue running while minimized or tabbed out from a fullscreen window. This can prevent the client from reloading resources or automatically disconnecting while logged into a server. For this feature to work, Minecraft must also be enabled under Windows `Background apps` settings. Otherwise, this feature will silently fail.

---

#### `cache_xbox_authentication_token`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Caches the Xbox game server authentication token after a successful multiplayer join and reuses it for future server join attempts while the token remains valid. This can reduce server join latency by skipping the repeated Xbox authentication request normally performed before each remote server connection. Cached tokens are account-specific and are not reused after expiration or when signed into a different Xbox account.

---

#### `center_cursor`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Centers the cursor within the game window whenever it becomes visible, such as when opening a container screen. The cursor is centered relative to the game window itself, not the full desktop. This differs from vanilla in that the cursor reappears at its last visible absolute screen position if it still clips within the game window.

---

#### `command_prefix`
- Type: `string`
- Allowed values: *all non-empty strings whose first character is not `/`*
Sets the prefix by which client commands are executed. Messages beginning with this prefix are parsed as client commands; other messages are sent as normal chat.

---

#### `cubecraft_servers`
- Type: `array` of `string`
- Allowed values: *any server address*
- Specifies a list of server addresses that should be treated as CubeCraft for CubeCraft-specific features. This is useful if you connect through an alternate IP/hostname (e.g. a region address, proxy, or custom DNS) and still want CubeCraft-specific behavior to apply.

---

#### `cubecraft_tweaks`
- Type: `object`
- Various tweaks to improve the user experience on CubeCraft.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `disable_serverside_entity_culling`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- CubeCraft uses a server-side entity culling system to hide entities that it believes are out of a player's view. This system is only active when the server has acknowledged the player is in first-person perspective. In practice, it can cause visual artifacts such as entities popping in/out due to latency, appearing late, or remaining invisible.

---

#### `custom_chat`
- Type: `object`
- Routes chat messages to a user-hosted external server, rather than sending them to server the player is currently connected to. This enables private, uncensored conversations between VT users that persist across different Minecraft servers, and provides faster message delivery in situations where server-side chat throttling occurs. An example chat server implementation can be found [here](https://github.com/ambiennt/tcp-chat-server).
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `default_to_custom_chat`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Controls the default routing behavior when typing messages. A setting value of `true` means messages are sent to the external chat server by default, and vice versa.
	- `endpoint`
		- Type: `array` of `string`
		- Allowed values: *any server address with port*
		- The address of the external chat server to connect to.
	- `route_custom_to_vanilla_prefix`
		- Type: `string`
		- Allowed values: *all non-empty strings whose first character is not `/`*
		- When in custom chat mode, messages beginning with this prefix are routed to vanilla Minecraft chat instead of the external chat server. Example (prefix set to `;`): `;hello non-VT users!`.
	- `route_vanilla_to_custom_prefix`
		- Type: `string`
		- Allowed values: *all non-empty strings whose first character is not `/`*
		- When NOT in custom chat mode, messages beginning with this prefix are routed to the external chat server instead of the vanilla Minecraft chat. Example (prefix set to `,`): `,hello VT users!`.
	- `use_route_custom_to_vanilla_prefix`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the `route_custom_to_vanilla_prefix` behavior.
	- `use_route_vanilla_to_custom_prefix`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the `route_vanilla_to_custom_prefix` behavior.

---

#### `damage_tilt`
- Type: `object`
- Multiplies the speed of the damage tilt animation about the target forward/look axis (local Z). The vanilla MCBE multiplier is actually `4.0`, but this appears slower than that of MCJE. A value of `0.0` effectively disables the respective damage tilt animation. Here is a [visual representation](https://www.desmos.com/calculator/ruih0owh8a) of the animation over the domain `[0.0, 1.0]`, where the exponent is the configured multiplier.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `camera_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Controls the strength of the *camera* damage tilt animation. Higher values indicate a faster tilt, and vice versa.
	- `first_person_objects_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Controls the strength of the damage tilt animation applied to *first-person objects* (equipped mainhand/offhand items, hands, etc. rendered in the first-person perspective). Higher values indicate a stronger faster tilt, and vice versa.

---

#### `disable_breathing_bob`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Disables the 'breathing' animation in which the first-person item(s) bob up and down slightly when the vanilla `View Bobbing` setting is enabled. The ability to disable this animation exists to be in parity with MCJE.

---

#### `disable_chat_tts_hotkey`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Disables the hardcoded `Control` + `B` hotkey to toggle the vanilla `Text To Speech For Chat` setting.

---

#### `disable_cobweb_waterlogging`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Prevents water buckets from waterlogging cobwebs client-side. 'Waterlogging' refers to placing a water source block inside another block. This feature is useful when the intended action is to break a cobweb with water, since a waterlogged cobweb remains intact. It also avoids server/client desyncs on servers such as CubeCraft, where cobweb waterlogging transactions are rejected and cause incorrect bucket state or ghost water blocks.

---

#### `disable_projectile_throw_swing_anim`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Disables the first and third-person right arm/right item swing animation when throwing a projectile. The ability to disable this animation exists to be in parity with MCJE. This only disables the animation for the local player, as there is no way to filter on a swing animation triggered by other players throwing projectiles.

---

#### `enchant_glint_color`
- Type: `object`
- Configures the enchant glint color.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `base_ui_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the base enchant glint color for UI items. The alpha channel of this setting overrides the vanilla `Glint strength` setting.
	- `base_world_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the base enchant glint color for world items and equipment. The alpha channel of this setting overrides the vanilla `Glint strength` setting.
	- `base_ui_color_fills_ui_slot_area`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Determines whether the base enchant glint for UI items fills the full inventory slot area instead of only the non-transparent pixels of the item sprite. Currently, this option does not properly blend the enchant overlay's alpha channel when the vanilla `Anti-aliasing` setting is greater than 1.
	- `smooth_world_uv`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Corrects how the enchant glint overlay samples UVs on 2D texture items rendered in the world. Instead of sampling from a single corner of each item texture pixel, the glint uses the full pixel area, fixing the pixelated appearance on flat item textures and bringing the appearance closer to MCJE. Enabling this setting may cause visual artifacts when used alongside the ray tracing graphics mode.
	- `ui_uv_rotation`
		- Type: `Vec2`
		- Allowed values: [`-180.0`, `180.0`)
		- Sets the UV rotation, in degrees, for the enchant glint rendered on UI items. The X and Y components control the rotation of each glint layer.
	- `world_uv_rotation`
		- Type: `Vec2`
		- Allowed values: [`-180.0`, `180.0`)
		- Sets the UV rotation, in degrees, for the enchant glint rendered on world items and equipment. The X and Y components control the rotation of each glint layer.
	- `ui_inverse_uv_scale_multiplier`
		- Type: `int`
		- Allowed values: *unbounded*
		- Controls how densely the enchant glint texture is tiled on UI items. Higher values make the glint pattern appear smaller and repeat more frequently. Lower values make it appear larger and repeat less frequently.
	- `world_inverse_uv_scale_multiplier`
		- Type: `int`
		- Allowed values: *unbounded*
		- Controls how densely the enchant glint texture is tiled on world items and equipment. Higher 	values make the glint pattern appear smaller and repeat more frequently. Lower values make it appear larger and repeat less frequently.
	- `ui_uv_anim_speed_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Scales the UV animation speed for the enchant glint rendered on UI items. Higher values make the glint move faster. Lower values make it move more slowly. This setting overrides the vanilla `Glint speed` setting.
	- `world_uv_anim_speed_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Scales the UV animation speed for the enchant glint rendered on world items and equipment. Higher values make the glint move faster. Lower values make it move more slowly. This setting overrides the vanilla `Glint speed` setting.
---

#### `entity_hitboxes`
- Type: `object`
- Draws a wire frame along an entity's hitbox bounds.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `line_thickness`
		- Type: `float`
		- Allowed values: [`0.0`, `0.1`]
		- Sets the thickless (in world units) for each hitbox 'line'. This setting is only applicable if `use_line_thickness` is enabled.
	- `primary_hitbox_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the primary hitbox.
	- `primary_hitbox_selected_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the primary hitbox when the local player is looking at the entity within pick distance.
	- `render_for_players_only`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Skips rendering hitboxes on non-player entities.
	- `sub_hitbox_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the sub-hitbox color of multi-part entities (e.g., the Ender Dragon). This setting has no effect on entities without sub-hitboxes.
	- `use_line_thickness`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the application of the `line_thickness` on hitbox rendering. When `true`, hitbox edges are rendered as quads using the thickness set by `line_thickness` (in world units). When `false`, edges are drawn as simple line strips and `line_thickness` is ignored. Thick lines are more expensive to render; turn this setting off for the more performant path if you don't need a custom thickness.

---

#### `entity_overlay_color`
- Type: `object`
- Changes various context-specific overlay colors for entities.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `armor_hurt_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the overlay color on equipped armor when the wearing entity is taking damage.
	- `armor_on_fire_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the overlay color on equipped armor when the wearing entity is on fire, but NOT taking damage.
	- `oscillate_armor_on_fire_color`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Sets whether the `armor_on_fire_color` should oscillate its green and alpha color channels, (in partity with vanilla MCBE), while using `armor_on_fire_color` as a base. Keep this setting `false` if you want the `armor_on_fire_color` to appear solid.

---

#### `first_person_model_matrix`
- Type: `object`
- Applies a custom translation, rotation, and scale transform to the first-person held item/model matrix. The transform is applied in translation -> rotation -> scale order. Rotation values are specified in degrees and applied in x -> y -> z order.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `translation`
		- Type: `Vec3`
		- Allowed values: *unbounded*
		- Applies an additive translation to the first-person model matrix. Values are specified as `{x, y, z}` offsets.
	- `rotation`
		- Type: `Vec3`
		- Allowed values: *unbounded*
		- Applies an additive rotation to the first-person model matrix. Values are specified in degrees as `{x, y, z}` Euler angles.
	- `scale`
		- Type: `Vec3`
		- Allowed values: *unbounded*
		- Applies scale to the first-person model matrix. Values are specified as `{x, y, z}` scale factors, where `{1.0, 1.0, 1.0}` leaves the model at its original size. Negative scale values may mirror the mode, but can cause rendering artifacts such as inverted/backface-culled geometry.
	- `show_debug_editor`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Shows an in-game editor UI for adjusting the `translation`, `rotation`, and `scale` values at runtime. This is intended for debugging and tuning transform values.

---

#### `first_person_sword_block_anim`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Plays the MCJE 1.7 first-person sword blocking animation while interacting with a sword. This is strictly a visual animation: MCBE does not have a networked sword blocking mechanic, so enabling this setting does not change combat behavior.

---

#### `fog_options`
- Type: `object`
- Controls fog render distance by applying multipliers to the base fog distance. Higher values push fog farther from the camera, and vice versa. Setting very large multipliers effectively disables fog.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `base_overworld_distance_multiplier`
		- Type: `float`
		- Allowed values: [`0.0`, `+∞`)
		- Multiplies the base fog distance in the Overworld dimension when the camera is not submerged (not in water, lava, or powder snow).
	- `base_nether_distance_multiplier`
		- Type: `float`
		- Allowed values: [`0.0`, `+∞`)
		- Multiplies the base fog distance in the Nether dimension when the camera is not submerged (not in water, lava, or powder snow).
	- `base_end_distance_multiplier`
		- Type: `float`
		- Allowed values: [`0.0`, `+∞`)
		- Multiplies the base fog distance in the End dimension when the camera is not submerged (not in water, lava, or powder snow).
	- `water_distance_multiplier`
		- Type: `float`
		- Allowed values: [`0.0`, `+∞`)
		- Multiplies the fog distance when the camera is submerged in water.
	- `lava_distance_multiplier`
		- Type: `float`
		- Allowed values: [`0.0`, `+∞`)
		- Multiplies the fog distance when the camera is submerged in lava.
	- `powder_snow_distance_multiplier`
		- Type: `float`
		- Allowed values: [`0.0`, `+∞`)
		- Multiplies the fog distance when the camera is submerged in powder snow.

---

#### `force_crafting_tab`
- Type: `object`
- Forces the crafting tab to always default to a configured index. This is only applicable to the inventory and the crafting table UIs. This feature is also advantageous because the cached tab index is not differentiable between different containers, and always (frustratingly) resets to the expanded view upon rejoining a game.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `crafting_table_show_all_recipes`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Controls the recipe filter for the crafting table UI. When enabled, all recipes are shown; when disabled, only currently craftable recipes are shown.
	- `crafting_table_tab_index`
		- Type: `int`
		- Allowed values: [`1`, `3`]
		- The tab index for the crafting table UI. A tab index of `1` is the minimized view that doesn't show recipes, `2` is the inventory + recipe view, and `3` is the creative menu, which is only applicable in the creative gamemode.
	- `inventory_show_all_recipes`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Controls the recipe filter for the inventory UI. When enabled, all recipes are shown; when disabled, only currently craftable recipes are shown.
	- `inventory_tab_index`
		- Type: `int`
		- Allowed values: [`1`, `3`]
		- The tab index for the inventory UI.

---

#### `force_time_of_day`
- Type: `int`
- Allowed values: [`0`, `23999`]
- Overrides the world's time of day. A tick value of 0 represents sunrise, 6000 for noon, 12000 for sunset, and 18000 for midnight. A total day in Minecraft is 24000 ticks, or 20 real-life minutes.

---

#### `freelook`
- Type: `object`
- Detaches the camera orbit from the local player's look direction. When `enabled` is `true`, holding down the configured `key` activates this feature.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `key`
		- Type: `int`
		- Allowed values: see [Key Codes](#key-codes) for a list of enumerated values.
		- The key code used to activate the feature. This feature is active only while the configured key is held.
	- `change_perspective`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- When `true`, the camera perspective switches to `perspective` while active, and restores the original perspective when deactivated.
	- `perspective`
		- Type: `int`
		- Allowed values: see [Camera Perspectives](#camera-perspectives) for a list of enumerated values.
		- Selects which camera perspective will be used when `change_perspective` is `true`.

---

#### `gui_scale`
- Type: `object`
- Overrides the vanilla `GUI Scale Modifier` setting, with added flexibility.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `disable_adaptive_scaling`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Disables the automatic GUI scaling mechanism when the MCBE window resizes. When this setting is off, any active GUI scale will be overwritten by the result of the vanilla adaptive scaling algorithm.
	- `scale_factor`
		- Type: `float`
		- Allowed values: [`1.0`, `8.0`]
		- The factor to scale the GUI by. Vanilla scale values are {`1.0`, `2.0`, `3.0`, `4.0`, `5.0`, `6.0`, `7.0`, `8.0`}. Any scale that isn't precisely one of these values is subject to 'pixel stepping', where some source pixels are mapped to one screen pixel while others are mapped to two or more, producing an uneven, jagged appearance. This occurs because MCBE's UI scaling is nearest-neighbor (no anti-aliasing), so non-integer scale factors cause irregular pixel sizes.

---

#### `inventory_hotkeys`
- Type: `object`
- Allows moving, swapping, and adding items within/between/from containers with a single key press. The inspiration for this feature comes from MCJE, in which hovering over a container slot and pressing one of the hotbar slot keys will swap the item stack in the hovered slot with the item stack in the respective hotbar slot. This implementation differs from MCJE in that item stacks of the same item type are added instead of swapped, where applicable.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `drop_hotbar_selected_stack_key`
		- Type: `int`
		- Allowed values: see [Key Codes](#key-codes) for a list of enumerated values.
		- The key code used to drop the entire hovered item stack while in HUD. This provides a more accessible alternative to the vanilla control + `Drop Item` keybind by allowing the action to be performed with a single key press.
	- `drop_hovered_stack_key`
		- Type: `int`
		- Allowed values: see [Key Codes](#key-codes) for a list of enumerated values.
		- The key code used to drop the entire hovered item stack while in a container screen. This is also beneficial for the same reasons as `drop_hotbar_selected_stack_key`.
	- `flying_item_anim`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the 'flying item' animation when an item stack is being swapped. This is the same animation that plays in vanilla MCBE when an item stack is shift-clicked into another slot. Disabling this animation means that the swap appears 'instant'.

---

#### `inventory_player_rotation_fix`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Fixes an animation bug with the paper doll in the inventory screen in which the model's root z rotation towards the cursor is inverted.

---

#### `item_display`
- Type: `object`
- Displays the local player's equipped items in the HUD.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `armor`
		- Type: `object`
		- Controls the display for items equipped the local player's armor slots.
		- Fields:
			- `enabled`
				- Type: `bool`
				- Allowed values: {`true`, `false`}
				- Toggles the parent object.
			- `screen_anchor`
				- Type: `int`
				- Allowed values: see [Anchor Points](#anchor-points) for a list of enumerated values.
				- Selects which screen anchor point the armor display will be aligned to.
			- `screen_anchor_offset`
				- Type: `vec2`
				- Allowed values: *unbounded*
				- Adjusts the position of the armor display relative to the chosen `screen_anchor`, measured in raw screen pixels. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up.
			- `content_anchor`
				- Type: `int`
				- Allowed values: see [Anchor Points](#anchor-points) for a list of enumerated values.
				- Defines which anchor point on the armor display's bounding box is aligned to `screen_anchor`.
			- `render_outside_hud`
				- Type: `bool`
				- Allowed values: {`true`, `false`}
				- When `true`, the armor display renders even when the active UI screen is not the HUD screen. When `false`, the armor display only renders when the active screen is the HUD screen.
			- `always_render_topmost`
				- Type: `bool`
				- Allowed values: {`true`, `false`}
				- When `true`, the armor display is always rendered on top of the vanilla UI. When `false`, the armor display may render behind the vanilla UI in some game states.
			- `item_scale`
				- Type: `float`
				- Allowed values: [`0.0`, `+∞`)
				- Sets the scale of each rendered armor item. A scale of `1.0` corresponds to the vanilla 16 * 16 pixel item icon size.
			- `item_spacing`
				- Type: `float`
				- Allowed values: *unbounded*
				- Sets the spacing, in raw screen pixels, between each rendered armor item.
			- `align_vertically`
				- Type: `bool`
				- Allowed values: {`true`, `false`}
				- When `true`, armor items are arranged vertically. When `false`, armor items are arranged horizontally.
			- `durability`
				- Type: `object`
				- Controls armor durability indicators.
				- Fields:
					- `enabled`
						- Type: `bool`
						- Allowed values: {`true`, `false`}
						- Toggles the parent object.
					- `text`
						- Type: `object`
						- Controls the armor durability text.
						- Fields:
							- `enabled`
								- Type: `bool`
								- Allowed values: {`true`, `false`}
								- Toggles the parent object.
							- `item_anchor`
								- Type: `int`
								- Allowed values: see [Anchor Points](#anchor-points) for a list of enumerated values.
								- Selects which anchor point on the armor item the durability text will be positioned relative to.
							- `text_anchor`
								- Type: `int`
								- Allowed values: see [Anchor Points](#anchor-points) for a list of enumerated values.
								- Defines which anchor point on the durability text's bounding box is aligned to `item_anchor`.
							- `offset`
								- Type: `vec2`
								- Allowed values: *unbounded*
								- Adjusts the position of the durability text relative to the chosen `item_anchor`, measured in raw screen pixels. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up.
							- `size`
								- Type: `float`
								- Allowed values: [`0.0`, `+∞`)
								- Sets the size, in raw screen pixels, of the durability text.
							- `show_total`
								- Type: `bool`
								- Allowed values: {`true`, `false`}
								- When `true`, durability is displayed as `current / max`. When `false`, only the current durability value is displayed.
							- `low_color`
							  - Type: `RGBA HTML color`
							  - Allowed values: [`#00000000`, `#FFFFFFFF`]
							  - Sets the durability text color used at 0% durability. The final text color is interpolated between `low_color` and `high_color` by durability percentage, using HSV hue interpolation.
						  - `high_color`
							  - Type: `RGBA HTML color`
							  - Allowed values: [`#00000000`, `#FFFFFFFF`]
							  - Sets the durability text color used at 100% durability.
							- `shadow`
								- Type: `bool`
								- Allowed values: {`true`, `false`}
								- Enables a shadow effect for the durability text.
							- `shadow_offset`
								- Type: `vec2`
								- Allowed values: *unbounded*
								- Sets the offset of the durability text shadow relative to the text positioning. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up. This setting is only applicable if `shadow` is enabled.
							- `shadow_color`
								- Type: `RGBA HTML color`
								- Allowed values: [`#00000000`, `#FFFFFFFF`]
								- Sets the color for the durability text shadow. This setting is only applicable if `shadow` is enabled.
					- `bar`
						- Type: `object`
						- Controls the armor durability bar.
						- Fields:
							- `enabled`
								- Type: `bool`
								- Allowed values: {`true`, `false`}
								- Toggles the parent object.
							- `item_anchor`
								- Type: `int`
								- Allowed values: see [Anchor Points](#anchor-points) for a list of enumerated values.
								- Selects which anchor point on the armor item the durability bar will be positioned relative to.
							- `bar_anchor`
								- Type: `int`
								- Allowed values: see [Anchor Points](#anchor-points) for a list of enumerated values.
								- Defines which anchor point on the durability bar's bounding box is aligned to `item_anchor`.
							- `offset`
								- Type: `vec2`
								- Allowed values: *unbounded*
								- Adjusts the position of the durability bar relative to the chosen `item_anchor`, measured in raw screen pixels. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up.
							- `size`
								- Type: `vec2`
								- Allowed values: [`0.0`, `+∞`) for each component
								- Sets the width and height, in raw screen pixels, of the durability bar fill.
							- `background_extra_height`
								- Type: `float`
								- Allowed values: [`0.0`, `+∞`)
								- Adds extra height to the durability bar background below the bar fill.
							- `background_color`
								- Type: `RGBA HTML color`
								- Allowed values: [`#00000000`, `#FFFFFFFF`]
								- Sets the color for the durability bar background.
						  - `low_color`
						  	- Type: `RGBA HTML color`
						  	- Allowed values: [`#00000000`, `#FFFFFFFF`]
						  	- Sets the durability bar color used at 0% durability. The final bar color is interpolated between `low_color` and `high_color` by durability percentage, using HSV hue interpolation.
						  - `high_color`
						  	- Type: `RGBA HTML color`
						  	- Allowed values: [`#00000000`, `#FFFFFFFF`]
						  	- Sets the durability bar color used at 100% durability.

---

#### `item_matrix_fix`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Restores the default hardcoded matrix transformations (position, rotation, and scale) for certain vanilla items (such as snowballs and arrows) and all custom items, bypassing their data-driven settings.

---

#### `java_first_person_bow_anim`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Enables a custom reimplementation of the MCJE 1.7 first-person bow animation. This replaces the default JSON-based bow attachable (in both first and third-person). When enabled, it also allows `enchant_glint_color`'s `smooth_world_uv` feature to apply to bows.

---

#### `java_first_person_crossbow_anim`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Enables a custom reimplementation of the MCJE 1.14+ first-person crossbow animation. This replaces the default JSON-based crossbow attachable (in both first and third-person). When enabled, it also allows `enchant_glint_color`'s `smooth_world_uv` feature to apply to crossbows.

---

#### `java_view_bobbing`
- Type: `object`
- A faithful reimplementation of the MCJE view bobbing animation, with extra configurations. This feature only applies when the vanilla `View Bobbing` setting is enabled.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `camera_strength`
		- Type: `float`
		- Allowed values: *unbounded*
		- Controls the strength of the *camera* bobbing animation. Higher values indicate a stronger bob, and vice versa.
	- `first_person_objects_strength`
		- Type: `float`
		- Allowed values: *unbounded*
		- Controls the strength of the bobbing animation applied to *first-person objects* (equipped mainhand/offhand items, hands, etc. rendered in the first-person perspective). Higher values indicate a stronger bob, and vice versa.
	- `first_person_objects_velocity_damping_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Scales how quickly a first-person object's motion decays after the camera orbits. Lower values make it ease back more slowly and smoothly. Higher values make it settle faster.
	- `first_person_objects_velocity_stiffness_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Scales the spring strength pulling the first-person object back to its rest pose. Lower values appear more 'springy'. Higher values appear stiffer/tighter.

---

#### `legacy_ui`
- Type: `object`
- Overrides certain UI screens with their legacy, JSON-based equivalents. Legacy screens generally load faster and are configurable via resource packs.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `accessibility_settings_screen`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Overrides the web-based Ore UI accessibility settings screen with the legacy JSON-based screen.
	- `audio_settings_screen`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Overrides the web-based Ore UI audio settings screen with the legacy JSON-based screen.
	- `classic_skin_picker_screen`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Overrides the 1.13+ persona dressing room screen with the 1.12.1 (and prior) skin picker screen. An additional advantage of this screen is that the layout is arguably more user-friendly and does not require any workarounds to display user-made skin packs with custom geometry or capes. Note: this is a deprecated feature. `skin_sideloader` should be used for changing skins instead of via this screen.
	- `disconnect_screen`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Overrides the web-based Ore UI disconnect screen with the legacy JSON-based screen.
	- `play_and_create_world_screens`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Overrides the web-based Ore UI play and create world screens with the legacy JSON-based screens.
	- `video_settings_screen`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Overrides the web-based Ore UI video settings screen with the legacy JSON-based screen.

---

#### `lighting_options`
- Type: `object`
- Various options to change terrain lighting.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `brightness`
		- Type: `float`
		- Allowed values: [`0.0`, `+∞`)
		- Sets the brightness applied to the terrain texture. This setting binds diectly to the vanilla `Brightness` slider setting, but is uncapped. Setting arbitrarily high values, e.g. `100.0` can mimic a traditional 'full-bright' mode.
	- `night_vision`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles whether the `night_vision_scale` setting applies.
	- `night_vision_scale`
		- Type: `bool`
		- Allowed values: [`0.0`, `1.0`]
		- The normalized scale by which the terrain texture should apply the visibility boost used by the night vision effect. This differs from the `brightness` setting in that it more closely respects the shading of the current environment.
	- `overworld_sunrise_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color of the overworld sunrise on the terrain light texture. This color is less pronounced when higher brightness/night vision values are set.

---

#### `limb_swing_anim_fix`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Fixes a longstanding animation bug in which the arm and leg movement swing for the local player appears 'stiff', and has a weaker amplitude when the jump key is pressed.

---

#### `load_resource_pack_shader_materials`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Allows `*.material.bin` files to be loaded by resource packs. Overwritten files must be placed in the `./renderer/materials/` folder, relative to the current resource pack root.

---

#### `movement_options`
- Type: `object`
- Various settings to control player movement.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `toggle_sprint`
		- Type: `object`
		- Automatically enables the sprint state without the end user having to press the sprint key.
		- Fields:
			- `enabled`
				- Type: `bool`
				- Allowed values: {`true`, `false`}
				- Toggles the parent object.
			- `key`
				- Type: `int`
				- Allowed values: see [Key Codes](#key-codes) for a list of enumerated values.
				- The key code used to activate the feature. Pressing the configured key toggles this feature on or off.
			- `is_default_active`
				- Type: `bool`
				- Allowed values: {`true`, `false`}
				- When `true`, the sprint state is active by default (without requiring a key press). When `false`, it starts disabled by default.

---

#### `name_tag_options`
- Type: `object`
- A highly customizable from-scratch reimplementation of entity name tag rendering.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `background_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the nameplate.
	- `background_darken_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the 'darkened' nameplate. 'Darken' in the context of name tags refers to when the entity is sneaking, as the vanilla name tag is visually darkened in this scenario.
	- `billboard_rotation_fix`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Fixes billboarding calculations so name tags properly face the camera. In vanilla MCBE, name tags can appear skewed near the edges of the view frustum because their camera-facing rotation is affected by view-dependent distortion from effects such as FOV changes and view bobbing.
	- `render_for_local_player`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Enables name tag rendering for the local player.
	- `local_pos_offset`
		- Type: `vec2`
		- Allowed values: *unbounded*
		- Applies a local offset to the name tag in *screen-space* relative to the camera orientation. Positive X moves the tag right (perpendicular to the camera's view direction), and positive Y moves it upward in the camera's local space.
	- `scale`
		- Type: `float`
		- Allowed values: [`0.0`, `4.0`]
		- Applies a uniform scale factor to the name tag.
	- `text_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the name tag text. Only the alpha channel of this setting is applied when the text is color coded via `§`. Inversely, the RGB component of the text color is always superceded by whatever the active color code is, if applicable.
	- `text_darken_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the darkened name tag text. The same preconditions as `text_color` apply for this setting.
	- `text_shadow`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Enables a shadow effect for the name tag text.
	- `text_shadow_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the text shadow. This color is always applied, regardless of color format specifiers (e.g. `§a`). This setting is only applicable if `text_shadow` is enabled.
	- `text_shadow_darken_color`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Sets the color for the text shadow when the name tag is darkened. This color is always applied, regardless of color format specifiers (e.g. `§a`). This setting is only applicable if `text_shadow` is enabled.
	- `text_shadow_offset`
		- Type: `vec2`
		- Allowed values: *unbounded*
		- Offsets the text shadow relative to the text positioning. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up. This setting is only applicable if `text_shadow` is enabled.
	- `world_pos_offset_y`
		- Type: `float`
		- Allowed values: *unbounded*
		- Offsets the name tag world position along the Y axis. Positive Y moves up, negative Y moves down.

---

#### `override_game_sensitivity`
- Type: `object`
- Overrides MCBE's in-game sensitivity, allowing finer-grained control beyond the vanilla `Camera Sensitivity` slider setting.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `scale_to_window_size_x`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Scales the in-game sensitivity in linear proportion to the x dimension of the MCBE window size. The 'default' window size is fullscreen, i.e. the monitor size. For example, if you have both `scale_to_window_size_x` and `scale_to_window_size_y` enabled, and are playing on a 1920 x 1080 monitor, shrinking your window dimensions to 1920/2 x 1080/2 = 960 x 540 will halve your in-game sensitivity.
	- `scale_to_window_size_y`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Scales the in-game sensitivity in linear proportion to the y dimension of the MCBE window size. The same logic as `scale_to_window_size_x` applies, but for the y dimension.
	- `value`
		- Type: `float`
		- Allowed values: *unbounded*
		- The raw in-game sensitivity value to be overridden. This value has the same units as the vanilla sensitivity slider. This field and the vanilla slider write to the same value-last write wins. If you adjust the vanilla slider, it overwrites this field (the override stops applying) until you set value again, either by re-injecting, or executing `.config reload`.
	- `weighted_scale_multiplier`
		- Type: `Vec2`
		- Allowed values: *unbounded*
		- The relative weighting of the raw camera turn delta per axis. Increasing one component biases the turn toward that axis and can make the other axis feel proportionally slower.

---

#### `perspective_fov`
- Type: `object`
- Controls the camera field of view (FOV) independently for each perspective. This allows fine-tuning the perceived zoom or spatial distortion when switching between first-person and third-person views. The valid range matches the vanilla `FOV` slider setting, typically `30.0` - `110.0` degrees.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `first_person`
		- Type: `float`
		- Allowed values: *dynamic*; see object description.
		- Sets the FOV used while in first-person view.
	- `third_person_back`
		- Type: `float`
		- Allowed values: *dynamic*; see object description.
		- Sets the FOV used while in third-person back view.
	- `third_person_front`
		- Type: `float`
		- Allowed values: *dynamic*; see object description.
		- Sets the FOV used while in third-person front view.

---

#### `premium_skin_spoof`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Spoofs your skin to add the same 'premium' tag that marketplace/paid skins have. Some servers may use this tag to determine whether to trust a player's skin. Although, in practice, this likely does not have an affect on anything.

---

#### `protocol_proxy`
- Type: `object`
- Enables protocol translation between the client's native version and a different target protocol version. This allows the client to join servers enforcing a newer protocol version. Current target protocol version: `1.21.132`. This feature is not supported on all servers and is currently only tested on CubeCraft. Certain content introduced after the client's native game version, such as newer blocks and items, is not implemented and will not function correctly.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `servers`
		- Type: `array` of `string`
		- Allowed values: *any server address*
		- Specifies a whitelist of servers on which protocol translation is applied. The proxy will only activate when connecting to a listed server.

---

#### `render_options`
- Type: `object`
- Various settings to change rendering visuals.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `disable_particle_rendering`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Disables all in-game particles from rendering. Particle rendering in MCBE is very computationally expensive on the GPU, so enabling this setting will net a very large performance improvement.
	- `disable_sky_rendering`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Disables clouds, sun, and moon tessellation, and sky tinting. This is an override of disabling the vanilla `Render Clouds` and `Beautiful Skies` setting. Enabling this setting nets a minor performance improvement.
	- `disable_weather_rendering`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Disables rain, snow, and thunder rendering. Enabling this setting nets a minor performance improvement when such weather is present.
	- `force_disable_vsync`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Disables frame vertical synchronization (vsync) at the graphics backend level. This feature is useful if you are on a device in which the following do not work to disable vsync:
			- Setting the internal vanilla `gfx_vsync` option to `0`, i.e. `gfx_vsync:0` in `%LOCALAPPDATA%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\LocalState\games\com.mojang\minecraftpe\options.txt`
			- Disabling vsync for `Minecraft.Windows.exe` in your graphics card control panel
	- `render_primary_ui_only`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Renders only the primary UI layer, skipping auxiliary layers (such as toast notifications and the debug overlay). This may slightly improve performance by avoiding additional UI passes.

---

#### `saturation_display`
- Type: `object`
- Displays the local player's saturation attribute value in the HUD. The display text has precision up to 2 decimal places.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `render_outside_hud`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- When `true`, the display renders even when the active UI screen is not the HUD screen (for example, while a container screen is open). When `false`, the display only renders when the active screen is the HUD screen.
	- `always_render_topmost`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- When `true`, the display is always rendered on top of the vanilla UI. When `false`, the display may render behind the vanilla UI in some game states.
	- `screen_anchor`
		- Type: `int`
		- Allowed values: see [Anchor Points](#anchor-points) for a list of enumerated values.
		- Selects which screen anchor point the display will be aligned to.
	- `screen_anchor_offset`
		- Type: `vec2`
		- Allowed values: *unbounded*
		- Adjusts the position of the display relative to the chosen `screen_anchor`, measured in raw screen pixels. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up.
	- `content_anchor`
		- Type: `int`
		- Allowed values: see [Anchor Points](#anchor-points) for a list of enumerated values.
		- Defines which anchor point on the display's bounding box is aligned to `screen_anchor`. By default, the display is rendered from its top-left corner.
	- `text_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the display text.
	- `text_shadow`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Enables a shadow effect for the display text.
	- `text_shadow_color`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the color for the text shadow. This setting is only applicable if `text_shadow` is enabled.
	- `text_shadow_offset`
		- Type: `vec2`
		- Allowed values: *unbounded*
		- Sets offset of the text shadow relative to the text positioning. Positive X moves right, negative X moves left; positive Y moves down, negative Y moves up. This setting is only applicable if `text_shadow` is enabled.
	- `text_size`
		- Type: `float`
		- Allowed values: [`0.0`, `+∞`)
		- Sets the size, in raw screen pixels, of the display text.

---

#### `screen_anims_fix`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Changes the behavior of the main menu panorama and splash text to keep animating even when the vanilla `Screen Animations` setting is off.

---

#### `skin_sideloader`
- Type: `object`
- Loads a custom player skin and optional cape directly from disk upon joining a server, bypassing the in-game skin menu. This can help resolve issues where skins fail to load or sync through the vanilla skin system. Skin and cape files are expected to be located relative to the configuration directory: `%LOCALAPPDATA%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\RoamingState\VanillaTweaks\`.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `cape_filename`
		- Type: `string`
		- Allowed values: *any valid `.png` filename*
		- Specifies the filename of the cape texture to load. The path is relative to the configuration directory, and the file must be located in the configuration directory root. Supported texture dimensions: `64x32` pixels. Example: `my_cape.png`.
	- `is_slim_arm`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Determines whether the skin uses the slim (Alex) model with 3-pixel-wide arms (`true`) or the classic (Steve) model with 4-pixel-wide arms (`false`).
	- `load_cape`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Enables loading and applying the custom cape file on login.
	- `load_skin`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Enables loading and applying the custom skin file on login.
	- `show_cape_texture_on_elytra`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Controls whether the cape texture is displayed on the elytra when equipped. When disabled, the dedicated elytra texture is shown instead. This setting should be disabled if the cape texture does not include an elytra part.
	- `skin_filename`
		- Type: `string`
		- Allowed values: *any valid `.png` filename*
		- Specifies the filename of the skin texture to load. The path is relative to the configuration directory, and the image file must be located in the configuration directory root. Supported texture dimensions: `64x32`, `64x64`, or `128x128` pixels. Example: `my_skin.png`.

---

#### `sky_color`
- Type: `object`
- Overrides the sky color for different dimensions.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `nether`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the sky color for the Nether dimension.
	- `overworld`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the sky color for the Overworld dimension.
	- `overworld_is_brightness_dependent`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- When enabled, the `overworld` sky color is modulated by the time of day: darker at night, brighter during the day. This matches vanilla behavior.
	- `the_end`
		- Type: `RGBA HTML color`
		- Allowed values: [`#00000000`, `#FFFFFFFF`]
		- Sets the sky color for The End dimension.

---

#### `snaplook`
- Type: `object`
- Temporarily switches the camera to a configured perspective. If freelook is active, freelook takes precedence over snaplook.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `key`
		- Type: `int`
		- Allowed values: see [Key Codes](#key-codes) for a list of enumerated values.
		- The key code used to activate the feature. This feature is active only while the configured key is held.
	- `perspective`
		- Type: `int`
		- Allowed values: see [Camera Perspectives](#camera-perspectives) for a list of enumerated values.
		- Selects which camera perspective will be used.

---

#### `theme_accent_color`
- Type: `RGBA HTML color`
- Allowed values: [`#00000000`, `#FFFFFFFF`]
- Sets the accent color used by VT in chat messages. Currently, a translucent alpha channel is not supported. This color is also accessible via the formatting code `§w`.

---

#### `use_xbox_profile_pictures`
- Type: `bool`
- Allowed values: {`true`, `false`}
- Uses Xbox Live profile pictures for all players, overriding the default Marketplace/Persona-generated skin headshot system.

---

#### `zoom`
- Type: `object`
- Dynamically sets the camera FOV to zoom in farther-away objects.
- Fields:
	- `enabled`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles the parent object.
	- `key`
		- Type: `int`
		- Allowed values: see [Key Codes](#key-codes) for a list of enumerated values.
		- The key code used to activate the feature. This feature is active only while the configured key is held.
	- `hide_first_person_objects`
		- Type: `bool`
		- Allowed values: {`true`, `false`}
		- Toggles whether first-person objects are hidden while this feature is enabled.
	- `inverse_easing_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Controls how quickly the zoom transitions to a new level after scrolling. The transition uses an [ease-in-out cubic](https://easings.net/#easeInOutCubic) curve, meaning it starts slowly, speeds up near the middle, and slows down again as it reaches the target zoom level. A value of `1.0` uses the default transition speed. Lower values make the transition slower, higher values make it faster, and `0.0` disables the easing animation.
	- `inverse_fov_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Sets the initial FOV amount the moment you start zooming (before any scroll). Effective FOV = `<base fov>` / `inverse_fov_multiplier`. A multiplier of `1.0` is the baseline, indicating that the initial zoom amount is equal to: `1.0` * the current FOV (identical).
	- `inverse_sensitivity_multiplier`
		- Type: `float`
		- Allowed values: *unbounded*
		- Scales mouse sensitivity while zooming. Effective sensitivity = `<base sensitivity>` / `inverse_sensitivity_multiplier`. A multiplier of `1.0` is the baseline, indicating that the sensitivity is equal to: `1.0` * the current sensitivity (identical).
	- `scroll_delta`
		- Type: `float`
		- Allowed values: *unbounded*
		- The step size for scroll-based zoom changes. Smaller values equate to finer control (smaller jumps). Larger values equate to coarser control (bigger jumps). Scrolling up on the mouse adds the delta (zooms in if positive), scrolling down subtracts the delta (zooms out if positive).

---
---

### Enumerations

---

#### Key Codes

| Enumeration        | Value |
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

#### Anchor Points

| Enumeration        | Value |
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

---

#### Camera Perspectives

| Enumeration        | Value |
| ------------------ | ----: |
| First Person       |     0 |
| Third Person Back  |     1 |
| Third Person Front |     2 |