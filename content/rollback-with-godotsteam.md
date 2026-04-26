+++
draft = false
date = "2026-04-25"
title = "Godot Rollback With Steam (Relayed and True P2P)"
tags = ["godot", "gamedev", "networking", "tutorial"]
unlisted = false
+++

This post is for those who want to use Snopek's Godot Rollback addon with the Steam's peer-to-peer multiplayer APIs.
I will cover both Relayed and True P2P. This tutorial was made on Godot 4.6.2, if it breaks on a later version
or you encounter any problems in general, please reach out to me at brogolem35@protonmail.com.

I will be using a Godot 4 port of Snopek's example project from his [tutorials](https://www.youtube.com/playlist?list=PLCBLMvLIundBXwTa6gwlOUNc29_9btoir).
I would highly recommend for you to watch his videos first before proceeding. The port in question can be found in this
[repo](https://codeberg.org/Brogolem35/godot4-rollback-tutorial).

# Relayed vs True P2P

## Relayed P2P
- Packets are relayed over the Valve's servers. (Adds delay)
- Doesn't require hole punching.
- Can be used with GodotSteam's SteamMultiplayerPeer. Supports RPC and stuff.
- Much easier to work with.

## True P2P
- Also referred to as mesh.
- No relay/middleman. (Minimum delay)
- Requires hole punching and leaks IP. (It can be disabled at Settings>In Game>More>Steam Networking, will use relayed when disabled)
- Steam falls back to relayed P2P if the hole punching fails or if it is disabled.
- Steam's legacy multiplayer API is needed to be used.
- Can't use RPC. Rawdog the packets, like the God intended.
- The one we are using on [Flame Up](https://store.steampowered.com/app/4536730/Flame_Up/).

I personally recommend you to try the relayed method first and see if the delay is tolerable or not.
Switch to true P2P if the relayed method is not tolerable. Even if you intend to directly jump to true
P2P, you should still follow the relayed part as I will do some setting up there.

# Relayed P2P

Add the [GodotSteam](https://godotengine.org/asset-library/asset/2445) addon from the AssetLib and reload the editor.

Create a SteamManager.gd file, fill it with the contents given [here](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/relayed/SteamManager.gd)
and add it as an Autoload.

## Main.gd
Add two methods that connect to `Steam.lobby_created` and `Steam.lobby_joined` on `_ready`.
Fill these methods as given [here](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/relayed/Main.gd).

Change the contents of `_on_ServerButton_pressed` and `_on_ClientButton_pressed` as given
[here](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/relayed/Main.gd).

And you are set. Pressing the Server button will now host a lobby and your clipboard will be set to
the lobby ID. 

## Result
The resulting repo should look like [this](https://codeberg.org/Brogolem35/godot-steam-rollback).

# True P2P
Now, the horrors.

Shoutouts to [hislittlecuzin](https://github.com/hislittlecuzin)'s [example](https://github.com/hislittlecuzin/Snopek-Rollback-Steamworks-FP-Template).
I wouldn't be able to pull this off without it.

## SteamManager.gd
Change the whole contests of it with the contents given [here](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/true_p2p/SteamManager.gd).

The biggest changes are the connection of `_on_p2p_session_request` and `_on_p2p_session_connect_fail`,
and the addition of `read_all_p2p_packets` and `send_p2p_packet`. These methods make use of the legacy
multiplayer API and packets sent with these methods are not relayed if the hole punching succeeds,
otherwise they fall back to relaying. I highly encourage you to read the code yourself, it is quite
documented. You will see some errors in the code now, they should disappear after the next section.

## CustomMessageSerializer.gd
Change the whole contests of it with the contents given [here](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/true_p2p/CustomMessageSerializer.gd).

class_name is given to make working with the static functions and enums easier.

`MessageType` enum is created to tag the different types of messages we will be serializing.

`serialize_message` and `unserialize_message` methods are overridden to make the input messages distinguishable
from the other types of messages we will send.

Serialize and unserialize methods for messages such as `ping`, `start`, and `stop` exist to allow us
to have these basic functionalities where we took them as granted while we were using MultiplayerPeer.

Feel free to add your own message types here when you are building your own game. I have some other
types of messages in my game as well.

## SteamAdapter.gd
Create this file and fill it with the contents [here](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/true_p2p/steam_adapter.gd).
Set `network/rollback/classes/network_adaptor` setting to it.

SteamAdapter overrides many of the functionalities of the default network adapter and forces the SyncManager
to use the Steam networking APIs we have set up instead of RPC.

If you look closely to the `is_network_master_for_node` method, you will see that we get a field named
`steam_mp_id`, which doesn't actually exists. At least not now. We will resolve this in the future sections
and I will explain what it is and why it is needed.

## Main.gd
Remove signal connection relayed to `multiplayer` API at the `_ready`. We won't use them.

Connect `Steam.lobby_chat_update` signal and add the method from [here](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/true_p2p/Main.gd).

Add the `start_game` method and rewrite the `_on_lobby_created` and `_on_lobby_joined` like
[here](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/true_p2p/Main.gd).

Remove anything relating to the multiplayer API and replace `multiplayer_authority` instances with `steam_mp_id`,
if there are any.

## Project Wide
`steam_mp_id` is our variant of the `multiplayer_authority` and it is what dictates which player controls
what node. The reason why we use our own value instead of the existing `multiplayer_authority` is that
the `multiplayer_authority` internally keeps the IDs as 32-bit integers while the Steam user IDs are
64-bits. This causes an implicit downcast at the API boundary and can cause data loss. To avoid this,
we must add our own property to the nodes.

Replace all, and I mean ALL, instances of `multiplayer_authority` with `steam_mp_id`, including the
ones in the addons.

Add an `int` field named `steam_mp_id` to all nodes in the `network_sync` group. Make the values
of those that are not player controlled `0` and the player controlled ones `1` by default to avoid breaking
the single player. This includes addon provided ones such as `NetworkAnimationPlayer`. The ones you
have missed will reveal themselves during the tests. 

## Result
The resulting repo should look like [this](https://codeberg.org/Brogolem35/godot-steam-rollback/src/branch/true_p2p).

# Shameless Self-promotion
Checkout my game, [Flame Up](https://store.steampowered.com/app/4536730/Flame_Up/). It was where I developed
and tested the methods I showed above. Feel free to try out the demo and wishlist it.
