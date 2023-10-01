# 	R1Q2 Changelog
![image](https://github.com/QuakeMM/test/assets/74773831/73c3542b-bc48-42eb-ac2c-ac2bda49793f)



## b7904 (Client only):
**October 25, 2008 - R1CH**

- Client: `cl_quietstartup` cvar, if set will suppress all initialization text in the console from client subsystems. Default 1.

- Client: `vid_quietload` cvar, if set will suppress all renderer initialization text in the console. Default 1.

- Client: Fixed a crash caused by setting `cl_particlecount` too high or low.

- Client: Changed +button / -button behaviour so -events are only generated if the corresponding +event was triggered.

- Client: Fixed a crash that could occur when switching between HTTP-enabled servers with HTTP downloads enabled.

- Client: Fixed a crash that could occur when issuing `vid_restart` or changing `vid_ref` while connected to a server.

- Client: Fixed a crash that could occur when using the start server menu and switching between mods.

- Win32 Client: Quadrupled the size of the DirectInput mouse buffer and process overflow conditions properly. Should result in better DirectInput mouse performance when running on low FPS systems that previously would overflow the buffer, causing R1Q2 to briefly ignore all mouse movement.

- Win32 Client: Improved crash report auto diagnosis. No longer upload reports caused by known buggy DLLs.



## b7864:
**September 30, 2008 - R1CH**

* Common: Remove "enhanced" server support (not used).

* Server: Fix `sv_interpolated_pmoves` sometimes moving clients to old origins when processing stringcmds.

* Server: Support `sv_features` / `g_features` for negotiating extended feature support for the Game DLL. A reference for mod authors will possibly be posted on the forum.

* Client: New "followip" command, will connect to the last IP address detected in a chat message.

* Client: APRQ2-style triggers, new commands "trigger" and "untrigger".

* Client: Fixed scr_chathud skipping last letter of name in (say_team) messages without scr_chathud_colored set.

* Client: `scr_chathud_highlight_char`, if set then highlighting will continue up until the specified character in the message. Defaults to " " so setting `scr_chathud_highlight 1` works mostly as expected now.

* Client: `scr_chathud_highlight` mask of 2 is now default - highlight will begin from the start of the line unless `scr_chathud_highlight` is masked with 2. Done so that defaults work more intuitively.

* Client: LOG_CHAT loglevel now includes only PRINT_CHAT messages.

* Client: Improved origin calculations for login/respawn/logout particle effects.

* Client: Fixed improper float comparisons on cheat protected cvars that could allow clients to manipulate them. Old R1Q2 versions will no longer be considered anticheat valid as a result of this change.

* Win32 Client: URL catcher, the last HTTP URL detected in a chat message will appear on the window menu for easy access.



## b7759:
**June 03, 2008 - R1CH**

* Common: Support 32 bits of precision for bounding boxes in the R1Q2 protocol. This allows the server to transmit integer bounding boxes of any size and have the client accurately predict. In the original Q2 protocol, bounding boxes must be multiples of 8. The game itself violates this with crouched players which have +z of 4 and therefore cannot be accurately quantized, resulting in mis-prediction when standing on a crouched player.

* Server: A bounding box of all zeros is special-cased and transmitted properly as 0, previously this would have been treated as {-8, -8, -24}-{8, 8, 8} on the client due to the limits of the quantization process. If you are a mod author, this allows removal of the "SVF_DEADMONSTER hack" that causes unreliable entity collisions.

* Server: `sv_gamedebug` will show warning messages if a bounding box does not lie on quantization boundary. Use `sv_gamedebug 5` to debug break when this occurs.

* Win32 Client / Server: Digitally signed executables for security.



## b7736:
**May 23, 2008 - R1CH**

* Client: Fixed protocol 35 -> 34 demo translation not properly writing kickangles, gunangles and blend playerstate to demos.

* Client: Increased usercommand cap to 400 to allow moving against water currents the same as how 3.20 works.

* Client: Don't write zero length messages to demos on an svc_frame overflow on a protocol 35 server. This causes premature demo end when played back.

* Server: Fix quantization check of entity origins incorrectly rounding to int first. This produced jittery misprediction effects on some entities.

* Server: New timing checks to help detect speed / lag exploits as the current technique is affected greatly by packet loss / lag. These new timescale checks work solely on the accumulated client and server time, so Internet lag should have less of an effect - however CPU lag (both client/server) is possibly an issue.


A new cvar, `sv_timescale_check`, is the maximum amount of time offset between the client / server in milliseconds before a warning is printed. An overloaded server may cause server time to fall behind the client, in which case the server will think everyone is cheating. Default is 0 which disables all checks. If you wish to enable this, start with about 1000 ms or higher to account for various discrepancies with the Internet. `sv_timescale_kick` will kick a client if they exceed this value of time offset. Default 0 means no kicks will happen. Eg `sv_timescale_check 1000` will print a server console warning if 1 second of time offset is exceeded, and `sv_timescale_kick 5000` will kick if over 5 seconds of time is exceeded.

Another set of cvars are `sv_timescale_skew_check` and `sv_timescale_skew_kick`. These work similar to above, but ignore accumulated time and act solely on the difference between time values. If the difference between two consecutive time values is sv_timescale_skew_check or higher, a warning is printed. If it's sv_timescale_skew_kick or higher, the client is kicked. Again, both of these default off since it requires careful tuning to work with your specific environment.

Note that neither of these checks are performed on listen servers due to UI / FS operations throwing off the time.

* Server: Do not treat zero length messages in demos as the end of the demo. This is a change from 3.20 demo playback, but allows playback of bugged demos recorded prior to this release. These demos can be fixed to play on 3.20 and other clients by removing the 4 0x00 bytes located at the offset shown when `developer 1` is set.

* Common: Cap water pmove usercmd values at 400 to prevent excessively high values from completely negating the effect of water currents.

* Common: Transmit new userinfo cvars immediately rather than on the next existing userinfo variable update.

* Win32: Updated libcurl to 7.18.0.

* Win32: More FPU stuff, somehow despite all the testing done for the previous build, I forgot to actually set the FPU to produce the desired results.

* Win32: Removed delay-load of dinput8.dll, R1Q2 now requires DirectX 8 or higher.



## b7587:
**January 13, 2008 - R1CH**

* Common: Initial support for variable server FPS. This reduces latency between client updates, giving the impression of a lower ping. Requires Game DLL support, so will NOT work with any existing mods, only new ones or open source ones that are updated to support this.

* Client: Fixed a number of crashes in the menu if certain cvars were set to invalid values. Thanks to everyone who submitted crash reports!

* Client: Improved cvar cheat capping code to catch serverinfo and other flagged variables.

* Server: Fixed a buggy printf from CheaterNet.

* Server: Fixed buffer reading issues in parsing of anticheat query replies. This may have led to some people being marked as not using anticheat over a game reset or other event where the client reconnected faster than the server.

* Server: Changed memory tag for CheaterNet so it no longer requires ANTICHEAT to be defined.

* Server: Fixed alignment in aclist output.

* Common (Win32): More FPU control word changes to improve certain physics mispredictions in the previous build that led to at least one map exploit.



## b7431:
**October 09, 2007 - R1CH**

* Server: Report by default to q2servers.com master server, use `sv_global_master 0` if you don't want this. See http://q2servers.com/ for the results. If anyone is interested in querying the data, eg for a mod server browser or similar, a protocol spec will be coming very soon. Note that this master server will NOT be queryable with the standard Q2 master query protocol due to the insecure design of the protocol. As a result of the new master, public defaults to 1 again.

* Server: "CheaterNet" support. CheaterNet is essentially a global banlist. Players caught cheating with a known hack (no heuristic detections, no file checks) on an anticheat-enabled server will have their IP address added to a list that your Q2 server can query, regardless of whether your server uses anticheat or not. The `sv_cheaternet` cvar controls how many minutes your server will consider the data valid for, the default being 60. This means if someone was caught cheating on an anticheat server in the last hour, they will be flagged by CheaterNet on your server. The default value of 60 is chosen to minimize the risk of false positives due to dynamic IPs. The `sv_cheaternet_action` cvar controls what action to take. 0 = only log to the server console, 1 (default) = show a message in the game, 2 = kick. Note that CheaterNet defaults on in order to help protect your server. Use `sv_cheaternet 0` to disable. Currently the cheaternet is in development so this may not detect anything for a few days, and may require another server update.

* Server: Fixed a buffer overwrite causing "ANTICHEAT ERROR: Unknown command byte" if the anticheat server had a lot of data to send to the Q2 server at once.

* Server: Reworked how `sv_recycle` works, it now does the same as a "map" map change.

* Server: Hopefully fixed `sv_interpolated_pmove` causing players to get stuck to one another.

* Server: Disallow anticheat on listen servers, such a configuration is not supported.

* Server: aclist shows anticheat-exempt players as intended, previously only the server console version (svaclist) worked.

* Server: Expose the protocol 34 sprite download hack as `sv_disallow_download_sprites_hack`. If set to 1 (default, and existed as hardcoded in previous R1Q2 versions), protocol 34 clients won't be able to download .sp2 files. This was added to prevent crashes as 3.20 and other old clients do not download linked skins on sprites which results in a renderer crash if they are missing, however, someone wanted this as an option so here it is.

* Client: Fixed behaviour of +left / +right (keyboard turning) under `cl_async 1`.



## b7391 (Client only):
**September 15, 2007 - R1CH**

Win32 * Client: Revert the r1q2.exe build to using "fast" floating point compiler option, even though "accurate" better matches server, it causes physics to feel wrong.

* Client: Cap usercommand upmove / sidemove / forwardmove at 300 since the server limits to 300 velocity in PMove and sending any higher is pointless, all it does is make delta compression less effective.



## b7392 (Client only):
**September 15, 2007 - R1CH**

* Client: Really cap usercommand to 300 😊. Previous build only worked for `cl_async 0`.



## b7387:
**September 14, 2007 - R1CH**

* Common: Usercommand scaling, a new protocol 35 feature. This makes use of unused bits in the usercommand 'buttons' field to transfer 5 bits of extra information. These bits are used to indicate if the usercommand movement or angles are multiples of certain integers and if so, only a byte needs to be sent over the net instead of a short for each value. Since the 'buttons' field is sent when the client is pressing some input and the values only change when the client is moving, this works out quite nicely as the two events usually coincide. While a few bytes may not seem worth saving, keep in mind that usercommands are sent at a much faster rate than server frames, so it adds up quickly.

* Common (Windows only): No longer set any FPU rounding modes. It seems doing so breaks things, despite 3.20 doing this every single frame. This bug was causing prediction misses and physics bugs that could be exploited if it happened on a server. This would occur if the gamex86.dll for the Game DLL is loaded as part of the command line, eg with +map, or if the server was running non-dedicated and the client issues a vid_restart with R1GL. Clients would get this bug if they re-loaded R1GL (which has since been fixed) after R1Q2 started, eg as part of anticheat loading. Code has been added to check the FPU control word for servers and it will cause a fatal error if the word is not set properly to prevent client/server physics desynchronization. Clients that have invalid control words will receive a warning in the console. If this happens, you are most likely using an out of date version of R1GL.

* Client: Support loading of the anticheat module on 64 bit Windows. NOTE: This is only supported with a 32 bit client running under WOW. Based on my own testing, the 32bit R1Q2 and anticheat work fine running under 64 bit Windows (tested on Vista, should work on all versions provided you have 64 bit drivers for your graphics card and such), but your results may vary. Please note this is an unsupported configuration; for best results, use a 32 bit Windows version. If you encounter problems, ensure R1Q2 has administrator privileges and you have 64 bit video / audio drivers installed.

* Client: Fixed an issue on map change where pending commands such as `reconnect` in the command buffer would be run before `CL_Changing_f` completes. This resulted in a full disconnect / reconnect occurring on map change, depending on the whether the commands came in a single packet or not.

* Client: Improve how protocol 35 versioning is handled - all new features from now on are versioned, in the event of a client/server mismatch only features that are advertised by the server are used. Old clients will still fall back to protocol 34 until they are updated to this version.

* Client: qport is randomized on each client connect to help work around even more broken NAT routers. Set qport to 0 if you have a non-broken router or are directly connected to save a small amount of bandwidth. This should fix 'delta from invalid frame' messages if you reconnect to a server you recently disconnected from.

* Server: `sv_anticheat_client_restrictions` cvar, can restrict which anticheat enabled clients are allowed on a forced anticheat server. Has no effect on an optional anticheat server. Add em up: 1 = R1Q2, 2 = EGL, 4 = AprGL, 8 = AprSW. Don't ask me why you'd want to do this, but it was requested, so here it is.

* Server: `sv_anticheat_force_protocol35` cvar, if set on a forced anticheat server, clients must be using protocol 35 to connect. This helps to prevent any undetected proxies from being used that were designed for protocol 34, as well as providing bandwidth savings and all the other good stuff that protocol 35 supports 😊. Has no effect on an optional anticheat server. Anyone connecting at protocol 34 will be forced to reconnect at protocol 35 - if you get players complaining that they take twice as long to load after enabling this cvar, chances are they were manually setting protocol 34 in their config for some reason. NOTE: I do NOT recommend enabling this cvar yet as other anticheat clients have to be updated for the new protocol 35 changes.

* Server: Fix behaviour of qport 0 clients. Previously, any packets from the same IP were considered to be from the same client which was not the intention. Instead, the packets must now match the IP:port combination from the initial connection. Not that anyone aside from myself uses qport 0 😊.

* Server: Change how overflows are handled, clients that overflow are held until the end of the game frame before being dropped. This prevents possible bugs where the Game DLL suddenly finds itself working with a disconnected client due to an overflow. Not that anyone overflows any more these days 😊.

* Server: Added a minimum allowed `net_maxmsglen` value from clients, it's set at 512 bytes currently - this is simply to prevent someone connecting with `net_maxmsglen 1` (thinking it's a toggle) and wondering what the hell is going on when they constantly overflow.

* Server: `sv_max_traces_per_frame` now works as advertised (broken in the previous build due to leftover testing code)

* Server: `sv_disconnect_hack` now disconnects the client after the Game DLL code has finished running instead of immediately.

* Server: Fix anticheat ping timeout when manually trying to reload anticheat after a clean disconnect by the server.

* Server: Catch more Game DLL errors in the form of NULL entities passed to link/unlink.

* Server: All "GAME ERROR" messages are now logged under the ERROR log level, previously it was a mix of WARNING and ERROR levels.

* Common: 64 bit Windows code cleanups.



## b7322:
**April 10, 2007 - R1CH**

* Common: Initialize `Sys_Milliseconds` on startup, this should hopefully fix the "anticheat ping timeout" on startup that some server admins were experiencing as well as any other one-time timer related bugs.

* Common: Check chunk sizes do not point outside the allocated buffers when loading maps. This should fix crash bugs that occur as a result of trying to use partially downloaded .bsp files.

* Server: Experimental `sv_interpolated_pmove` cvar, if set greater than 0, player movements of this duration or higher will be interpolated server-side to compensate for clients with a low frame rate sending a small number of long moves. I'll try and explain how this works since some people may consider it a gameplay change (for this reason it defaults off too).

Normally, a client moves so many milliseconds in a frame, a common value is 10-30. The client sends a packet to the server saying "Hey, I'm at xyz, I'm moving in this direction, calculate where I will be in 30 ms.". The server then obliges and updates the player's position immediately. Now consider that to the server, the player has just warped from the old origin to the new origin. Normally this is OK, since player movement packets come in fast enough so that the 'warping' is limited to what seems like smooth, continuous movement.

A player with a low frame rate (say, below 30) will send less packets, causing the server to calculate up to 250ms worth of movement in one go. This obviously leaves a big gap in between movements. You will never see this happening on your client, since the server hasn't informed you about the player's new origin (and even when it does, your client will still pretend everything is OK and interpolate from the old to the new origin). When you fire your railgun at Mr. Low FPS however, the server uses his new origin to calculate whether you hit him or not. You'll most likely miss unless you were leading, as he's now up to 250ms ahead of where you thought he should be.

`sv_interpolated_pmove` attempts to compensate for this by changing what the server does when it receives a movement packet. Instead of processing it all at once, it runs the movement gradually over the course of many milliseconds, updating the origin in small increments. If you fire your railgun at the right time, you may now hit the player since instead of him being 250ms ahead, the server may have only processed 30 or 40ms worth of movement.

This is hugely experimental and disgustingly hacky (it alters shared client state information), but seems to work from my brief testing. The entire process *should* be completely invisible to both clients and the Game DLL, but no guarantees, especially as this could conflict with how some mods might alter the entity origin. Note that enabling this cvar will likely cause a CPU hit since the server will be re-calculating every players origin every time it receives a packet. Try a value of 10 or so to start with, as the benefits of interpolating 10ms worth of movement are probably negligible.

* Server: `sv_minpps` cvar, minimum number of packets per second a player has to send before being kicked. Not recommended for use on Internet servers unless you understand the implications lag and client FPS will have on this.

* Server: `sv_disconnect_hack`, if set, will turn an svc_disconnect writebyte or a svc_stufftext "disconnect" into a proper disconnect. Useful for old / buggy mods that don't remove clients properly on a "kick". Note, this defaults 1 (on) since it has no negative consequences (hopefully!).

* Server: Don't allow `svacinvalidate` and `svacupdate` to be used if there is no server running.

* Server: Don't show anticheat disconnect messages for connecting clients.

* Server: Update "anticheat" server info variable on ac server connection / disconnection so it should never be stale.

* Server: Don't instruct clients to load anticheat if the server itself isn't connected to the ac server.

* Server: Fix crash on exit if maximum number of command line arguments is exceeded.

* Server: `sv_strafejump_hack` now defaults to 1.

* Server: Show anticheat exempt players in the output of aclist.

* Client: Allow `pause` command during demo playback.

* Client: Removed FOV override during demo playback.

* Client: Fixed player teleport particles drawing at incorrect origins.



## b7185:
**February 09, 2007 - R1CH**

* Common: Added a "fast planes" array of map planes that use integer indexes rather than bytes. An oprofile run of r1q2ded showed that a lot of CPU time was spent tracing boxes through the map (clipping, etc) and that the byte to integer conversion was one hot spot in the code, so this may provide a small speedup for both clients and servers.

* Common: `processtimes` command for showing kernel/user CPU usage.

* Server: Support "0 ms" frames. Previously, under high packet rates, the Q2 server could return from select() too early and enter a tight loop waiting for the next millisecond. This update allows the server to re-enter select() if not enough time has passed for a game frame. Profiling revealed this as a fairly hot area, so dedicated servers may see some CPU usage reduction with this change, especially Linux servers. Note that on Win32, there was a 1 ms built-in delay for dedicated servers which R1Q2 had previously removed, so this is more of a bugfix on Windows 😊. If for some reason this new code does not work properly for you, set `sys_loopstyle 0` to use the original event loops.

* Server: Support includes in the anticheat-*.txt files via a \include directive, eg \include ../common-hashes.txt. Note, no recursion checks are performed, so don't be stupid 😊.

* Server: Support flags for anticheat-hashes.txt. Line format is now filepath hash flags. Currently only two flags are supported, required and negative, use a comma to specify multiple flags. If required is set, any hash failures for the filepath will result in a client kick, regardless of any other server settings. If negative is set, wildcard matches (see below) will also be considered for negative matches. Useful for enforcing sensitive files such as player models while being less strict with others.

* Server: Support wildcards in anticheat-hashes.txt for filenames. You can use this to allow one hash for multiple files, for example, some players do not like the female player model and replace the female files (md2, wav, etc) with the ones from the male model. This would cause an anticheat file failure if you checked that the players/female/tris.md2 etc matched the female model. Using the wildcards, you can for example do players/*/tris.md2 where the hash would then be allowed for any player model, allowing players to mix and match. Remember to be conservative, you wouldn't want to specify *.md2 as then players could use player models for grenade or other things they probably shouldn't be allowed to.

IMPORTANT NOTE: If you use a wildcard match for a path, it will NOT be considered a negative (bad) match for all files matching the path, only positive (good) matches unless the negative flag is set (see above). Example, if you have 3 hashes each beginning players/*/tris.md2 but no corresponding non-wildcard match, the files matching the pattern will NOT be checked. For this example to work, you would need to specify the individual models, eg players/male/tris.md2, players/female/tris.md2, players/cyborg/tris.md2 in addition to the wildcard matches. If negative is set, specifying a hash for players/*/tris.md2 means that every single player model loaded must match the specified hash.

* Server: Receive more than one anticheat message per frame. This helps if a client has a lot of modified files as they will (hopefully) no longer continue to spam the server (or themselves) with modified file warnings after connecting due to the delay in reading messages.

* Server: Verify the client matches the one a request was sent for when dealing with anticheat. There was a small race condition possible where a client could disconnect and reconnect and a stale response would arrive for the old client. This was exasperated by the above problem too since the reply could be delayed by seconds or more. This unfortunately requires a protocol change, so this version is mandatory for anticheat enabled servers.

* Server: `sv_anticheat_disable_play` cvar for anticheat enabled servers, if set, anticheat enabled clients will not be allowed to use the "play" command to play arbitrary sounds such as timer wavs. This cvar may be toggled mid-game, however only clients (re)connecting after the cvar is changed will have the new setting applied.

* Server: Show client version on `aclist` for anticheat enabled clients.

* Server: `svacupdate` command, reload and send config files (anticheat-*.txt). Can be used mid-game, however the new checks will only apply to clients (re)connecting after the config is reloaded.

* Server: `svacinvalidate` command, invalidate all clients causing them to be marked as AC-invalid. Useful after a config change and you want clients to reconnect.

* Server: Only send anticheat load message after the reconnect if using `sv_force_reconnect`, should provide faster client connection times.

* Server: Fixed the CIDR-style masks for the addacrequirement, etc. No, really this time!

* Server: Long values for `sv_force_reconnect` (eg a hostname instead of an IP) are no longer truncated.

* Client: Fixed a crash involving $$loc_here / $$loc_there in configs causing `cl_default_location` to be read before it was initialized.

* Win32: Switched to rich edit control in dedicated server window for performance improvement when rendering console. Much less flickering / scrolling as it redraws and it now supports a 1000 line buffer. Keep in mind Q2 is single threaded, so if you are scrolling the console using the scroll bar, your server will be frozen.

* Win32: Remove highbits and control characters from text which is sent to the old style dedicated console.

* Win32: Improve client behaviour to try and prevent crashes if no renderer was able to load due to renderer DLLs crashing on startup and leaving a dangling window pointer.

* Win32: Run GetMessage loop while waiting for the next ms in the main event loop, this may or may not help with delayed input or other weird problems. Either way, it makes more sense like this 😊.



## b7187 (Win32 Only):
**February 09, 2007 - R1CH**

And a super-quick 
* Fixed `-oldconsole` behaviour.



## b7011:
**January 28, 2007 - R1CH**

* Client: `cl_default_location` cvar. The contents of this cvar will be returned in $$loc_here / $$loc_there on a map without a .loc file. This can be used in conjunction with server-side locations, eg "%L" for CTF, so if there is no client .loc file for a map, a suitable message is still sent.

* Server: Use network byte ordering for the network mask on blackholes / ac requirements / etc. This fixes a bug with matches on partial bytes not working correctly (ie, /8, /16, /24 and /32 were the only working masks). I guess endianness is important after all 😊.

* Server: An ICMP unreachable from a client in zombie state will immediately free the client.

* Server: Fixed a crash with newline expansion on strings with \n at the end. Thanks to Zouns for report.

* Server: Apply change effects to cvars that require a new map to load. Previously they would have no change notification, eg changing `sv_anticheat_required` would not properly update the "anticheat" serverinfo var.

* Server: Anticheat token (password) support for bypassing anticheat requirements. List tokens one per line in anticheat-tokens.txt. Only one client may use a single token at once. Instruct players to set actoken "token" in their config to make use this feature.

* Server: Anticheat hash list naming. Begin a hash list (anticheat-hashes.txt) with ! to set the name, eg the first line in the file should be !My hash list v1.0. This will be shown to players using the aclist command so they know what kind of file checks the server is using. Newlines are supported, but not recommended. Max 256 characters.

* Server: Anticheat file violations include the failing file hash in the console message.

* Server: Whitehole support, for bypassing blackholes. `addwhitehole` / `listwhiteholes` / `delwhitehole`.

* Server: `delacrequirement`, `delacexception`, `listacrequirements`, `listacexceptions` commands added.



## b6990:
**January 13, 2007 - R1CH**

* Server: Added `sv_redirect_address` cvar, if set, clients will be redirect to this address when the server is full.

* Server: Fixed `sv_lag_stats` sometimes generating multiple packets for a single sample.

* Server: Fixed 'reserved slots are full' showing up for clients who aren't using reserved slots.



## b6974:
**January 11, 2007 - R1CH**

* Server: Added `sv_max_packetdup` cvar which specifies the maximum number of duplicate packets a client can request using the new `packetdup` command. If a client is experiencing consistent packetloss, they may enter `packetdup 1`, regardless of what client they use. If allowed by the server, this will cause 1 duplicate packet to be sent for every normal packet, significantly reducing the likelihood of data being lost. Note that each duplicate packet results in twice as much bandwidth usage for the client, so keep this in mind before setting it, although keep in mind the likelihood of clients using this feature too 😊. If you're not paranoid about bandwidth usage, I recommend setting this to 1.

* Server: Randomize the times at which the packet is sent for `sv_lag_stats` to minimize the effect time-based packetloss has on the results.

* Client: `cl_cmdcomplete` defaulted to 0 for "compatibility". Set to 2 if you preferred the old default.

* Common: Allow use of all whitespace (not just newline) in console log.

* Linux: Fixed 100% CPU usage problem when certain types of ICMP messages were received and not handled in a timely fashion.

* Linux: Added `net_no_recverr` to disable the extended error info functions that are used for ICMP / 'connection reset by peer'.

* Linux: Updated `net_ignore_icmp` cvar to ignore ICMP unreachables (ie, don't kick players) for Linux systems.



## b6964, aka "the release that my ISP didn't want to happen":
**December 17, 2006 - R1CH**

* Server: Added `lag` client command, will show latency, packetloss and general connection quality information. Connection quality is determined by whether packets from the client arrive at regular time intervals as the server expects. For client to server packetloss stats, the server must use `sv_lag_stats 1`. This will incur a tiny bit of extra bandwidth usage. Still somewhat of an experiment as to how useful/accurate this is.

* Server: Added `sv_func_entities_hack` cvar. If set to 1, sounds from func_plat (lifts) and func_door (doors) will be attenuated server-side so they are not broadcast to every client, saving both bandwidth and providing some level of exploit reduction. Additionally, the origin for sounds on a func_plat will be at the top of the platform as opposed to the middle of the entity. This provides more realistic sound positioning. Note that this setting may BREAK sounds for some mods since this kind of adjustment should be done by the Game DLL, not the engine. Please test that everything works OK on your server before keeping this enabled.

* Client: `cl_original_dlights` cvar, if set will use standard Q2 3.20 dlights (aka yellow and more yellow). Note, this default on for compatibility purposes.

* Server: Fixed rare (1/16 chance) occurance of a player being unable to move for 1 second after joining a server.

* Server: `sv_anticheat_nag_defer`, defer showing the `sv_anticheat_nag_message` for this many seconds (to avoid conflicting with mod MOTDs etc)

* Server: Support quick disconnects of crashed clients via 'connection reset by peer' on Linux.

* Server: Strip high bits and controls chars from text before writing it to the log file.

* Client: Disable deferred models and deferred rendering when using `timedemo 1`.

* Client: Restart input on a new window creation, fixes mouse issues in software mode rendering.

* Client: Verify byte count for temporary entities and sounds.



## b6908 (Client Only):
**November 16, 2006 - R1CH**

* Fixed `scr_chathud_lines 0` in a config file causing a crash.

* Fixed a security issue with the automatic protocol fallback code on the R1Q2 client that could allow a remote attacker to cause you to disconnect from a protocol 35 server.

* Removed the echo connectionless packet as it poses numerous security risks.

* Ignore connectionless `print` command when not connecting.



## b6869 / b6870:
**October 10, 2006 - R1CH**

* Fixed anticheat requirement / exception lists causing crashes.

* Fixed exception clients being forced to reconnect on mapchange.



## b6868:
**October 10, 2006 - R1CH**

* Improved server behaviour on an anticheat server problems, including a ping sytem

* New anticheat cvars, `sv_anticheat_client_disconnect_action` and `sv_anticheat_show_violation_reason` to control various things.

* `sv_anticheat_nag_message` is now less annoying.

* Improved error messages on loading a broken .pak file.

* Allow use of `map` for replaying demos with a Game DLL. Some mods require a custom Game DLL for demo playback. If replaying a standard dm or other such demo, you should continue to use `demomap`.



## b6824:
**September 16, 2006 - R1CH**

* Use unsigned timers for most things. This (should) fix issues with long uptimes (24 days+) causing odd behaviour, notably clients sometimes becoming stuck on connection and other timer related problems. There may be a few odd cases where the new code causes problems, so please post reports if something seems broken.

* `sv_anticheat_nag_time` and `sv_anticheat_nag_message` cvars, the message will be shown to non-anticheat clients for time seconds after connecting. Useful to promote anticheat 😊. Remember, http://antiche.at/ for the client files!

* Reverted the `sv_allow_map` "fix" since it turned out I was just imagining it was broken 😊.

* Disabled new speed cheat detection code until more testing is done.

* Fixed `svacinfo` help showing incorrect command name.



## b6808:
**September 04, 2006 - R1CH**

* r1ch.net anticheat client support. When connecting to an anticheat enabled server, R1Q2 is able to load the anticheat module if you have it installed. Please see http://antiche.at/ for more information about the r1ch.net anticheat module. When on a supported server, you can use `aclist` to see the anticheat status of other players.

* Experimental anticheat server support. Still undergoing testing, so don't ask about this yet 😊.

* Drop stale clients on a reconnect from the same ip/port combination. This will remove the "Player xxx is already connected from your address" message on a client crash / timeout that the server didn't notice.

* New technique to detect speed cheats thanks to squeaky. From my limited local LAN testing, this appears to be very sensitive and accurate. Currently will only print a message in the server console, not used for kicks yet. Note that this may be incompatible with `sv_predict_on_lag`.

* Copy `vid_ref` commands to engine memory, this should fix numerous problems resulting from failed renderer shutdowns subsequently causing a crash due to invalid command pointers. One of the most frequently submitted crash reports.

* Fixed `sv_allow_map 0` not working as intended.

* Client FPS counter / map timer can be moved with `cl_drawfps_x` / `cl_drawfps_y` and `cl_drawmaptime_x` / `cl_drawmaptime_y`.

* Win32: command-line argument -nopathcheck to skip the path length check if you're unable to move your Quake II installation.

* Win32: re-enabled the 24 bit FPU precision code to hopefully match Linux server binaries with regards to float rounding and thus client prediction. From my testing, as long as everything now uses this version or higher, there should be no further precision issues between Windows / Linux clients and servers that cause prediction errors. Thanks to Jitspoe for helping to test this.



## b6557:
**April 26, 2006 - R1CH**

* Workaround for `sv_force_reconnect` not functioning on old clients due to a parser bug in the 3.20 code.

* Fixed client not being able to connect to non-R1Q2 servers.



## b6555:
**April 24, 2006 - R1CH**

* Fixed `sv_force_reconnect` breaking horribly on long server IPs (I rewrote it last build and forgot to mention).

* Removed 'pmove msec' debug message.



## b6552:
**April 24, 2006 - R1CH**

* Added a check for stale data left in the message buffer by Game DLLs and automatically clear if present. This should fix any broken mods causing '0xXX: not playerstate' and other weird errors on the client.

* Really fixed `scr_chathud_ignore_duplicates` and also allowed it to match configurable number of lines, eg `scr_chathud_ignore_duplicates 2` will ignore the message if it matches the previous two lines of chathud text.

* Fixed crash when using stuffall if not running a server.

* Added built in help for cvars. Use `cvarhelp cvarname` to get usage information about a cvar. So far only server cvars are documented (sv_*).

* Added `sv_format_string_hack`, if set will remove all % from user input for use with mods that have buggy code when handling format strings.

* Removed client/server support for advanced deltas since it is never used (and rightly so, plain netcode outperforms it in both bandwidth and CPU).

* Fixed configstring overflow debug dump missing CS_ITEMS array.

* Win32: Crash report text is now actually readable in Windows Notepad (seems I grew a bit too attached to Metapad 😅)

* Win32: Crash reports about gamex86.dll, ref_soft.dll or ref_gl.dll are now no longer automatically selected for upload. If you want to see gamex86.dll crashes fixed, send the crash log (.txt) and minidump (.dmp.gz) to the author(s) of the mod you were running as there's nothing I can do with them. The vanilla renderers (ref_soft, ref_gl) don't have any debug symbols and contain known crash bugs as-is so only crash reports from the latest versions of R1Q2 and R1GL will be investigated.

* Win32: When uploading crash report, server can inform if you are using an obsolete build of R1Q2.



## b6377 (Win32 Only):
**January 04, 2006 - R1CH**

* Really fixed HTTP downloading and game directory selection.

* Fixed `scr_chathud_ignore_duplicates` not working correctly.

* Updated Win32 exception handler to include more debug info in the minidump. If not already present, please install the debug symbols for R1Q2 to help generate better crash reports. These are included in the client download on the r1q2 site.



## b6358:
**December 24, 2005 - R1CH**

* Fixed HTTP downloading requesting map filelists with a missing gamedir.

* Fixed how HTTP downloading determines the game directory (would break if basedir was set to non-default)

* Changed how floating point operations are rounded on Win32. Should fix the speed/sticking/strafe/etc issues reported with the latest builds.

* `scr_chathud_highlight` cvar to highlight nicks on the chathud. Bit 2 will highlight from the beginning of the message to the end of the player nick if desired; bit 4 will highlight an extra character at the end.

* Updated how malformed sounds are handled (hopefully no longer crashes or gives an error)

* Client now always connects at server-specified protocol, no more "guessing". This will only work properly on up to date R1Q2 servers (most have updated to compatible version already).

* Fixed a few more floating point conversions that resulted in quadword loading.

* Fixed a remote crash vs servers that could occur when auto downloading was enabled on certain platforms that don't distinguish between files/directores in fopen(). Thanks Orange for report.

* Updated Makefiles for better non-linux support.



## b6274 (Win32 Client Only):
**November 13, 2005 - R1CH**

* Fixed an idiotic crash in my own error handling code. Note that this version will drop when it resamples a .wav down to zero-length as this will cause a rare division by zero exception in other parts of the code. If you get a message stating a 0 length resampled sound was encountered, you may wish to play with your s_khz or delete/replace the offending .wav file. I would also like to know what .wav file and other settings you use to cause the problem, as I have been unable to reproduce it with any of the baseq2 .wav files, and from the crash dumps, it appears some of you are crashing on baseq2 sounds...



## b6272 (Win32 Client only):
**November 12, 2005 - R1CH**

* chathud (use `scr_chathud*` to configure)



## b6264 (Win32 only):
**November 12, 2005 - R1CH**

* Built using Visual C++ 2005.

* Demos are no longer allowed to execute commands.

* Updated crash handler a bit more.

* Hopefully fixed rare crash when playing sounds that arrived before configstring after a level change that had previously been freed.

* dbghelp.dll included in downloads of R1Q2 client and dedicated.

* Updated curl and zlib.

* Few other various tweaks here and there.

 

November 02, 2005 - R1CH, 11:36:57 PM »
## b6203 (Win32 only):

* Fixed a crash that occured if the sound hardware was in use. Thanks to whoever uploaded the crash dump 😊.

* Fixed Win32 dedicated server in NT service mode crashing.

* More cleanups for AMD64 client build. Client now compiles, however it crashes for unknown reasons when loading pak files. Without an AMD64 system this is extremely difficult to track down.



## b6198 (Win32 only):
**October 28, 2005 - R1CH**

* Removed int3 if no debugger present to stop people creating useless crash reports because they can't read.

* Fixed rare crash in SV_Trace entering a clipbox with no leafside.

* Updated exception handler with more useful information depending on what module crashed (READ the crash report before sending please!)

* AMD64 windows client cleanups (not usable yet though)

* Exception handler on loading vid_ref as not to kill entire Q2 if loading bad DLL.



## b6187 (Win32 only, absolutely nothing was changed that would affect the Linux builds, so b5930 is still the latest for Linux):
**October 26, 2005 - R1CH**

* Top-level exception handler to catch and report unhandled exceptions (crashes). This will allow you to generate a useful crash dump should R1Q2 crash and optionally automatically upload it to my server for analysis. Requires a recent version of Dbghelp.dll for full functionality, you may find a download of Dbghelp.dll here. Place it next to r1q2.exe. Newer versions of Dbghelp will be distributed via the auto updates system should the need arise. If you want feedback on as to why a crash occured, you must post your crash report on the forums so that I can identify who it belongs to.

* Added return address logging to memory allocator. Game DLL memory corruption should now be pinpointable to the exact line where the allocation occured in the disassembly, allowing for binary patching for the most common problems if desired.

* Fixed annoying problem when minimizing window that could cause R1Q2 to vanish off-screen when restored.



## b5930:
**October 19, 2005 - R1CH**

* Fixed precision differences on Linux binaries causing prediction errors for the majority of clients. An example of this is in The Edge (q2dm1) when going from the SSG to the MH room and hugging the wall - near the top of the steps, you will experience large prediction errors. Note, if building your own Q2 client for Linux, be sure to specify -ffloat-store in the gcc command line or your client will mispredict.

* Fixed server max allowed packet in `sinfo` being off by 10 bytes.

* Fixed `sv_enhanced_setplayer` so it actually works.

* Tweaked `sv_predict_on_lag` slightly for better performance.



## b5920:
**September 24, 2005 - R1CH**
 
**HTTP auto-downloading!**

Yes, it's here at last! Servers may specify via the `sv_downloadserver` cvar a full URL (including trailing /) to a web server where clients can get any files they need. Since the files are transferred over HTTP instead of Quake II's UDP-based netchan, downloads are much faster and multiple files can be transferred in parallel. Other enhancements include filelist support, allowing a client who has never played on your server before to be given a complete list of required files and begin downloading them via HTTP.

It is strongly recommended that you use gzip on any files you host as the HTTP client supports transparent decompression of gzip-compressed files. See the Apache 'AddEncoding' or 'MultiViews' documentation for instructions on how to serve gzip-encoded content. I'll also likely be posting an example .htaccess for use on an Apache content repository. By default, two persistent connections are made to the target webserver as recommended by the HTTP specification, this helps reduce the effects of latency when requesting small files such as textures. You may wish to bump the Apache MaxKeepAliveRequests setting if you have a lot of files that need to be downloaded. The address of the Q2 server that the client is connecting to is sent as the HTTP referer header. If you wish to only allow downloading from clients who are on your game servers, simply send a 403 Forbidden or other error header (NOT 404!) to unauthorized clients and they will abort all downloads and not try to connect for the remainder of the session. If you do not intend to host all content on your HTTP server, it is recommended you use 'ErrorDocument 404 "404' to minimize the amount of bytes wasted on 404 replies. The client will disable HTTP downloading if it receives a 404 reply with a body of more than 512 bytes.

The HTTP client will also by default fetch a "filelist" for each mod and map you download. The first download you start will request the mod filelist and downloading a .bsp file will request the appropriate map filelist. The filelist should contain plain text path references to extra files that the Q2 client would not normally auto download - eg for a map you may wish to include the paths of PNG textures, for a mod you would reference every file your mod needs in order to run. This allows a client who has never played on your server before to download the entire mod via HTTP downloading and not miss any files as often happens with plain auto-downloading. The map filelists are read from http://server/gamename/maps/mapname.filelist and the mod filelist from http://server/gamename/gamename.filelist and consist of newline-separated relative paths from the gamedir of files you wish the client to download - eg for maps/hells_corner.bsp you would create a hells_corner.filelist with any references to files the map needs, eg:
```
textures/mymap/texture1.wal
textures/mymap/texture1.png
sound/mymap/door01.wav
```
and host it at http://server/gamename/maps/hells_corner.filelist. Filelists may only reference "safe" Quake II game content, other files such as .zip and other unknown extensions will be ignored. You may prefix a line with @ to make the check game-local - ie say your mod uses custom conchars.pcx and you specify pics/conchars.pcx in your filelist, it won't be downloaded as Q2 will see it already exists from the baseq2 pak files. By specifying @pics/conchars.pcx, it will be checked only from pak files and disk files in the mod dir. If you have a lot of content that replaces the same names as used in baseq2 pak files, it is recommended you use pak file as detailed below.

Filelists may reference .pak files provided they will only be downloaded to the mod directory - ie the list entry should read "pak0.pak". The file subsystem will reload when a .pak is downloaded to ensure correct ordering of files; any files in the HTTP queue which are also in a newly downloaded pak will be removed from the queue. Additionally, .paks will be downloaded as soon as they are queued and will block everything else until they are done. The @ prefix is redundant with pak files and will cause an error if used. Note that any files that were queued with the @ prefix with the same names as baseq2 files will be removed once a pak has finished downloading when the queue is revalidated. Thus, it isn't a good idea to mix both pak files and @-prefixed files. You may wish to use **FileListFinder** to generate a filelist-compatible list of specified files under a root directory (useful for map/mod authors).

Expect a few bugs in amongst all this - there's a lot of new code and changes throughout the client. Where HTTP downloading fails, the client will try to revert to standard UDP downloading. Any errors, please report on the forum as usual. The 'files remaining' count is somewhat inaccurate, but gives a good idea when the current batch will finish. This is by design; the client can't know how many extra textures/sounds/etc are needed until for example the map has downloaded. Client cvars: `cl_http_downloads 0/1` controls the whole HTTP subsystem, `cl_http_filelists 0/1` controls the use of filelists and `cl_http_proxy` can be set to use a proxy server (host:port) if needed. All the `allow_download_*` cvars are also still valid. I've written the HTTP downloading client code as modular as possible so other Q2 clients can hopefully implement it with minimal trouble since it isn't tied to R1Q2's protocol 35 in any way.

* Client-side demo protocol translation! Packetentity and playerstate delta writing has been implemented client-side so instead of the client simply dumping the network message, it writes its own deltas based on current state. The result is that regardless of protocol or anything else, demos recorded in R1Q2 are now written as standard protocol 34 demos which should be compatible with every other client and demo editing software. This translation does have some limits though - mainly, there is no way to translate large frames into a protocol 34 compatible format, so any frames that exceed the max. protocol 34 frame length will be discarded. Note that this feature cannot be disabled - protocol 35 demos will no longer be written at all. R1Q2 will still contain support for replaying demos recorded in **b5510** and possibly earler revisions of protocol 35, but protocol 35 demos are now considered deprecated. Demos recorded on protocol 34 servers or with forced `cl_protocol 34` will still be written as per 3.20 (full net message dump).

* Even more server-side delta quantization optimizations for entities (at the expense of a tad more CPU). This reduces outgoing bandwidth usage for servers regardless of the Q2 client being used.

* Win32 client quake2:// protocol support. You will need to setup R1Q2 as a quake2:// handler using Quake2HandlerSetup. You can then use links such as tastyspleen.net vanilla to jump straight into a server. You will also be able to make shortcuts using quake2:// URLs too.

* Removed `cl_strafejump_hack`, the `sv_strafejump_hack` value of the server is now transmitted over the net to protocol 35 clients. `sv_strafejump_hack 1` will allow strafejump hack for all protocol 35 clients, `sv_strafejump_hack 2` will force it on for all clients. A reminder, the strafejump hack fixes the "feature" where you will sometimes become stuck to the ground when strafe jumping at higher packet rates. Almost all players consider this "feature" a bug, so you should definitely consider running `sv_strafejump_hack 1` to provide a "fix" for protocol 35 clients. Note that `sv_strafejump_hack 2` forces it on for all clients - this will allow any clients to be able to strafejump freely, however non-protocol 35 clients will have prediction misses each time the fix takes effect resulting in a screen "shake".

* messagemodex support, allows a custom command with chat-style input prompt. Eg, `bind x messagemodex say_admins` will bring up a "say_admins: " chat prompt. Can be used with other commands, eg `bind x messagemodex "rcon kick"` to bring up a kick prompt 😊.

* `messagemode*` commands can no longer be used when not connected.

* Win32 client, win_disablewinkey cvar. If set to 1, will hook the keyboard and prevent the Windows key from interrupting gameplay and allows you to bind either key as LWINKEY and RWINKEY respectively. Requires Windows 2000 or higher for API support.

* Win32 client, the 'context menu key' (the one next to the right windows key) is now bindable as APP.

* Reduced default value of `sv_max_download_size` to 8MB (max allowed filesize over UDP downloads).

* Experimental (ie, only use for testing) server cvar, `sv_predict_on_lag`. Will continue running client thinking code for lagged out clients, preventing freezing in mid-air and other such things associated with lagged out clients. Since the client can become unlagged at any time, this could cause some small warping if/when the clients backed-up packets catch up. Currently "lagged out" is defined as more than 200ms since the last move packet and will continue predicting the clients movement at 10fps based on their last intended move packet. This is usually bad for the client in question (they will warp a lot on their screen when they unlag) but good for everyone else since they don't have a frozen/warping player to deal with. Although this cvar sounds nice, be sure to test it thoroughly before using it on a production server as I've not had a chance to do so and there may be some unintended side effects.

* Optimized redblack binary tree creation for pak files to minimize small memory allocations (1 alloc per pak instead of 1 alloc per file).

* Changed prediction 'teleport detection' when replaying demos to double the distance of regular gameplay since demo replaying only has 10fps snapshots to go on. Previous builds of R1Q2 disabled teleport detection entirely which resulted in view 'snapping' when respawning or teleporting. This new value is a good compromise at avoiding both the '10 fps jitters' and view snapping.

* Quake III's bit-based entity delta compression has been backported to R1Q2's protocol 35. Note that this method of delta compression is significantly more CPU intensive and thus is only enabled via `sv_advanced_deltas 1`. Doing so right now is not worth it unless you do your own testing and find it to be better. R1Q2 protocol 35 deltas are both more CPU and bandwidth friendly, but this may change as I explore the Q3 system some more.

* Win32 `net_ignore_icmp` cvar to ignore ICMP unreachables for the server. Use this if you get people randomly disconnecting with "Connection reset by peer". I also implemented a safety check so that the ICMP kicks won't kick any client who has sent a packet in the past 1500 ms, so this should hopefully fix the spurious kicks.

* Added list of supported protocols to the challenge packet, in the format p=xx,xx,xx where xx are the protocol numbers. R1Q2 protocol clients should inspect the protocol numbers and pick the highest they support. The R1Q2 client will still default to the try/fallback method for a few releases until all R1Q2 servers have updated.

* Server kick/stuff/dumpuser and any other commands that take a player id/name can now take a substring match of the name. Preference is still given to an exact match first. Requires `sv_enhanced_setplayer 1` to work as some buggy mods or admin DLLs may issue kicks for disconnected clients which could inadvertently catch innocent players with this new matching. Additionally, an empty string is no longer interpreted as client 0.

* Fixed protocol 35 rate-dropped packets count not being sent to client (no yellow on netgraph if you were rate limited)

* Multiple userinfoban checks are now executed on a userinfo string until a KICK or BLACKHOLE target is reached. This prevents a MESSAGE action matching an earlier userinfo var and then all later vars go unchecked.

* `Userinfoban` checks on connect have moved to on new. This allows use of the STUFF action and is generally a bit tidier. This check is performed both before and after any `sv_force_reconnect`, allowing KICK/BLACKHOLE action to be taken before the Game DLL even knows about the client.

* "new" during spawning causes a stuffed disconnect/reconnect, this typically occurs if the client types `reconnect` in the console whilst connecting. Since "reconnect" is shared by the server levelchange stuff, this is probably the best way to implement a "hard" reconnect where it should be one in the first place 😒.

* 127.0.0.1 is explicitly pinged when doing broadcast server query.

* In-game server browser should no longer close on network errors such as unreachable host.

* `cl_gun` value transmitted to server on protocol 35 so it can avoid sending gun frames, modelindex, etc if `cl_gun 0` is set.

* Gun model is now drawn at any FOV. Yes, it looks weird. Don't blame me, you guys are the ones who wanted it 🤔.

* `cl_stfu_ilkhan` renamed to `cl_drawmaptime`.

* Permission for autodownloading sprites is now under `allow_download_models` and not `allow_download_others`.

* Updated `sinfo` client command to show more useful protocol-related info about the client and server, eg max msg len, strafejump hack, etc.

* DirectInput is delay-loaded on Win32 client.

* DirectSound on Win32 client is now imported rather than dynamically loaded since even OpenAL will likely need dsound.dll. This means you need DirectX to run R1Q2 now (previous versions also needed it for imported DirectInput).

* Fix for single player savegames not loading properly.

* Attempting to record while replaying a demo is no longer allowed (demos don't contain enough entity state to permit re-recordings).

* Linux client builds again (thanks to turol for OpenAL patches)




## b5510:
**May 15, 2005 - R1CH**

* Added a new config, postinit.cfg that gets executed on the client in place of where the old autoexec.cfg used to run.

* Fixed `sv_allow_map` behaviour not working correctly, added `sv_allow_map 2` which will convert map commands to gamemap.

* Fixed server capping of client's `net_maxmsglen` not working if the client set `net_maxmsglen` to 0.

* Really fixed reserved slots behaviour (I hope...)

* Removed cl_maxfps warning if running `cl_async 0`.

* New cvar on Win32, `net_ignore_icmp` will ignore ICMP unreachables and not use them to drop uncleanly disconnected clients.


 
## b5494:
**May 07, 2005 - R1CH**

> [!WARNING] 
>*This release changes a few aspects of default behaviour and breaks compatibility with a few things. Please read the Changelog carefully and be sure to update any relevant files, configs and such that may be affected by some of these changes.*

* **COMPATIBILITY AFFECTED:**
autoexec.cfg is now run BEFORE the sound, input and video subsystems intialise on the client. This allows you to have things in your autoexec.cfg that control these subsystems such as s_initsound 2 to be able to use OpenAL sound before the sound system loads up. No major errors appeared in my testing, but this is a fairly significant change to how configs run so please let me know if this causes any problems.
* **COMPATIBILITY AFFECTED:**
beginmapcmd / endmapcmd cvars changed so they are able to work on servers. The variables have had to change names to accomodate this. Client-side commands should go into cl_beginmapcmd and cl_endmapcmd, server commands in sv_beginmapcmd and sv_endmapcmd. The $$mapname metavar is also now usable on the server, so you can for example have "set sv_beginmapcmd exec configs/${$mapname}.cfg" to load map-specific configs.
* **COMPATIBILITY AFFECTED:**
Changed pak file behaviour so that they remain open for the duration of a game to facilitate faster access (perhaps). As a side effect of the required changes, the new R1GL enhanced renderer API compatibility is broken. This shouldn't be a problem for most users as by updating R1Q2 you should also get the new R1GL.

* More server-side delta state optimizations through `sv_optimize_deltas` cvar. View angles are no longer sent to a client when they won't be used, resulting in 6 bytes per frame of savings. This has the small side effect of causing a view "flip" when you die on non-R1Q2 clients as the client expects to be able to use the old view angles when they are not there. R1Q2 has been altered to use the client's aiming direction and not what the server sent which seems more technically correct and fixes this issue, although it may look very slightly different from normal. For this reason, `sv_optimize_deltas` can be set to 0 (disable optimizations). `sv_optimize_deltas 1` (default) will optimize for protocol 35 clients (which are assumed to have this fix), `sv_optimize_deltas 2` will optimize for all clients. Optimizing for all clients will cause demo recording of freeflying spectator movements to be lost unless the client is using R1Q2, as well as additional view angle data depending on how the mod works.

* New server-side settings support, provides a framework for the client to inform the server of certain settings, ie if sound is disabled then the server can avoid sending sound packets. Not used at present besides to inform server if client is recording so viewangles can be sent without optimizations.

* New muzzleflash/dynamic lighting behaviour on the client. When a muzzleflash or dynamic light is drawn, it now 'follows' the entity (including lerping) it was drawn on until it ceases to exist. This will fix the issue of people with high latency not seeing their own muzzleflashes as well as fixing the "10 fps" effect that occurs since muzzleflashes and tempent effects used to be locked onto entity origins and didn't do any lerping. This makes things such as the TE_FLASHLIGHT entity actually usable too.

* Fixed a possible issue with `sv_force_reconnect` getting stuck on servers with over 24 days uptime on 32 bit architectures. This caused an apparent stall when trying to connect.

* Fixed an auto downloading bug where the wrong packet size was reported to the R1Q2 client if a zlib compressed packet managed to fit over 64KB of data. This resulted in decompression errors on the client, causing it to exit.

* Fixed an auto downloading bug where the maximum message length was used instead of the client's own buffer size when downloading to R1Q2 clients. This most notably caused stalled downloads to R1Q2 clients.

* Fixed view blending in the playerstate optimization being quantized with the wrong multiplier, resulting in incorrect screen blending on ## b5xxx servers.

* Fixed a bug with macro expansion and $$loc variables when the replacement text was shorter than the variable name.

* Updated string tokenizer to treat high-ASCII text (colortext) as valid characters. This allows binds that contain a solid block of "colortext" to work correctly.

* `sv_rcon_buffsize` cvar to control the maximum packet length sent over rcon. Some rcon tools and non-R1Q2 clients are unable to handle large rcon packets, so this defaults to the 3.20 default of 1384 bytes.

* `sv_rcon_showoutput` cvar, will also display the results of rcon commands in the server console. Note that this will appear before the rcon command itself shows, this is intentional and a side effect of the Q2 packet redirection system.

* Prevent changing of the "game" cvar on the client once connected to a server as this will cause Bad Things(tm).

* Fixed timescale and other cvars being capped on demo playback start.

* No writes to the netchan are permitted before the `sv_force_reconnect` setting takes effect. This should prevent clients stalling on the reconnect if the mod or something causes a global message that gets stuck in the netchan.

* `sv_show_name_changes` cvar, will inform all players when someone changes their name.

* Join server menu now provides shortcut keys to each slot.

* Bumped R1Q2 protocol sub-version to ensure no old R1Q2 clients end up recording a broken demo with the new delta optimizations.

* Increased `adrX` cvars up to adr31. You can set from adr0 to adr31 using the console / configs, the servers which respond the fastest will make it into the server browser list. The address book function from within Q2 however is still limited to 8 due to screen space reasons.

* Changing from non-Dinput to Dinput mid-game with `m_fixaccel` set will now properly reset acceleration on exit.

* Fixed more demo recording issues with the new maximum message length.

* Moving laser beams (RF_BEAM) are now lerped.

* Mouse sensitivity is now a field in the options menu.

* Fix a possible memory corruption issue in Cbuf_Execute when an oversized string was constructed. Thanks to Maniac.

* `allow_download_pics` cvar for server, controls downloading from "pics/" dir.

* Client prediction always runs during demo playback, this should fix the problem of players moving at high speeds having jerky first person movement.

* Force screen update on "changing" to hopefully avoid stale frame buffers being used in end of map screenshots that `cl_endmapcmd` may be making.

* Fixed animated textures on brush models not working correctly.

* Properly apply `win_noalttab` setting on startup.

* Win32 `win_priority` command is now a cvar.

* Server no longer sends reliable sounds to connecting clients. I'm not sure why this was the default behaviour, but it doesn't seem to make any sense.

* `sv_allow_map 2` will convert all `map` commands to `gamemap`. This may break some buggy mods if they rely on `map` to clear their internal state.

* Serveraliases, nullcmds and commandbans now support blocking of things with spaces in them, eg `addcommandban "give all"`.

* Dedicated servers will no longer execute an autoexec.cfg on game change.

* `sv_restartmap` will now be used on ERR_DROP errors. These error types are used by Q2 to signify a condition that requires the server to drop, but not a complete exit of Q2. Be sure `sv_restartmap` is set to something safe such as q2dm1 otherwise if an error occurs whilst `sv_restartmap` is trying to start up the server then it will likely get into an 100% CPU loop.

* Added API version number for the enhanced renderer API to prevent things such as the pak file API change having to break compatibility in a way that crashes.

* Fixed server console output being lost if the server crashed as a result of an rcon command.

* Zlib decompression errors now drop the client instead of exiting Q2.

* Unset `password` userinfo after processing reserved slots to prevent buggy mods from comparing empty password with reserved slot password and refusing access.

* Fixed wrong error message that was sent back to client if all reserved slots were full.

* Filesystem cache will not cache any files until Q2 is done initializing. This will allow you to actually have `set fs_cache 3` in a config file for example without having the config file get cached before the cvar is set.

* Updated VC6 project files with correct path for the OpenAL source files.

* Cleaned up Linux client build thanks to turol, now supports OpenAL and extended renderer API.

* Linux r1q2ded binary is now built with GCC 4 using -fvisibility=hidden. Symbol versioning is disabled for both r1q2ded and r1q2ded-old and binaries are stripped.

* Additional byte optimization information is shown in `status 1` output.

* New preprocessor definition NPROFILE will build a binary with no netcode or message profiling support.

* Small readme.txt included in R1Q2 client distribution.

* Note to server admins: Don't forget to set `net_maxmsglen 0` on your server if your ISP/router supports fragmented UDP packets! This will give much faster autodownloading support and allow for larger frames in heavy games to R1Q2 clients.



## b5240:
**April 07, 2005 - R1CH**

* Fix `userinfobans` always using LOG target.

* Fix `userinfo`/`cvarbans` ignoring the ! modifier.

* Fix `listcvarbans`/`listuserinfobans` showing BLACKHOLE instead of LOG.

* Add MESSAGE and STUFF targets to `userinfo`/`cvarbans`.

* Updated console help for `addcvar`/`userinfoban`.

* Fixed dodgy $$mapname / $$game behaviour.

* Few internal code cleanups (static functions, const variables)



## b5218:
**April 06, 2005 - R1CH**

* Fix numerous demo recording/playback issues. Keep in mind that demos recorded at protocol 35 will not be able to be replayed on non-R1Q2 clients.

* Really fixed the maximum message size bug.

* Fixed auto downloading / `allow_download_others` cvar disabling all auto downloading.

* Re-added `cl_noskins` cvar to client.

* New cvar, `z_allowcorruption` will ignore any memory corruption errors. If you use this option and experience crashes, it is pointless reporting anything.



## b5186:
**April 04, 2005 - R1CH**

* Fix old R1Q2 clients connecting to new server not falling back properly due to large packets being sent by default.

* Fix incorrect naming of `sv_badcvarcheck` cvar.



## b5184: (Win32 Client Only):
**April 04, 2005 - R1CH**

* Fixed 1600x1200 res typo as 1600x120, added 1440x960.



## b5183:
**April 04, 2005 - R1CH**

* Linux x86-64 dedicated server support.

* New crash backtrace output on Linux.

* (Win32 Client) OpenAL support. Requires OpenAL runtimes to function, use `s_initsound 2` to activate. Code based on Q2E.

* New protocol 35 netcode. Offers more bandwidth savings by making best use of otherwise-unused bits in the data stream. Allows large (fragemented) UDP packets, "disabled" (uses traditional packet size) by default since some consumer routers suck and can't reassemble properly. Use `net_maxmsglen` to control the maximum packet size - clients can choose what they want to use by setting this, a server can allow/disallow it by also changing this to the maximum they want to accept. Defaults to 1390 - the default protocol 34 message size. Set to 0 to allow the maximum new default packet size (4086 bytes). If you have problems connecting as a client, set this back to 1390. See http://r-1.ch/r1q2-protocol.txt for details of the current implementation of protocol 35. **bug** don't set `net_maxmsglen` to 0 on a server in this build. Use 4086 if you wish to enable the new larger packets for now.

* Fix a crash when handling some 0 byte files (particularly .wav files).

* New LOG target for `userinfobans`/`cvarbans` to only print a message to the console when hit.

* `sv_badcvarcheck` cvar, choose which action to perform on a bad cvar check result. 0 = log, 1 = drop, 2 = blackhole. Default 1.

* Fixed client console history editing sometimes showing remainder of previous line.

* Win32 dedicated server console word wraps correctly now.

* Win32 client binary can no longer be installed as a system service, only dedicated.exe supports this now.

* `status 3` server command shows maximum buffer sizes (from new netcode) and bad movement counter for all clients.

* Client will no longer display models/skins containing an invalid path.

* Windows Visual Studio 6 project file now included in source release.

* `allow_download_textures` / `allow_download_others` cvars for server. textures includes "textures" and "env" dir, others includes anything not specified by other download variables (ie not maps sound models players textures env). Using `allow_download_others 0` you can prevent downloading from within any arbitrary directories from within the mod dir.

* A whole bunch of other stuff I've forgotten since it's been so long. I really should keep a Changelog.txt...

I also updated the forum permissions to enable reply notification. If you want to be informed via email when a new R1Q2 release is out, simlpy press the notify button in the bar at the bottom of this page.



## b4812:
**February 25, 2005 - R1CH**

* Fixed a client crash if vid_restarting immediately on map load.

* Altered fs_cache behaviour to a bitmask - bit 1 is cache positive hits in pak files, bit 2 is cache negative hits and bit 4 is cache positive hits of external (non-pak) files. If you don't like not being able to edit a file after R1Q2 has loaded it, `set fs_cache 3` and you should be fine.

* Removed a whole ton of 80 bit float loads being truncated, this should hopefully improve floating point performance as well as code size.

* New server command, `addserveralias`/`delserveralias`/`listserveraliases`. Can be used to create server-side aliases that get stuffed to clients. Note that these don't "consume" the command, so if you don't want them to be passed to the Game DLL, be sure to add a nullcmd for them. Example: `addserveralias railz "connect my.railz.server"` followed by `addnullcmd railz`. Any macro expansion will occur on the client side unless you add it without quotes in which case it will be expanded at the time of entry.

* Increased the non-malloc message length to 69 bytes following profiling data gained from busy servers. This will mean each client consumes a bit more RAM but increases performance.

* Fixed some rogue/xatrix temporary entities not showing.

* Reset player score to zero on disconnect. This is a workaround for mods that don't do this themselves so that when a new player is connecting, the old score is not visible in the server status response.

* `allow_download_players` now tries to download all player files, including pain sounds etc. It will abort the current player on the first file that doesn't exist on the server.

* `z_buggygame` can be set to higher values than 1 and will multiply allocation requests by this value. Eg if the mod is still crashing with `z_buggygame 1`, try `z_buggygame 2` or 3. Note that this will greatly increase memory usage.

* Several other things I've since forgotten.



## b4644:
**February 14, 2005 - R1CH**

* Replace client locations system with NoCheat-style '$$loc_here' and '$$loc_there' metavariables. These are now expanded everywhere instead of just a say/say_team command. To do forced expansion of metavars you should enclode the $name portion in the curly brackets as you would with cvars. Examples: `bind x say "I am at the $$loc_here"` will work fine. `bind x say "I am [$$loc_here]"` won't work as expected as the cvar parser will try to expand '$$loc_here]'. Use { } to force a match: `bind x say "I am [${$loc_here}]"` will work as expected. Apologies for the slightly convulted syntax but I didn't see any other way to implement it in a sensible manner consistent with the Q2 cvar design without resorting to some even nastier hacks than what I'm using now 😅.

* Server-side entity replacement and map overriding support. Using this you can create "virtual" maps that exist only as a .override file on the server, but load up an existing map with a modified entity string. Example: q2dm1.bsp is your normal q2dm1. To add some excitement, you want to put a quad in the map, but don't want clients to have to download a whole new .bsp. So you edit the entity string for q2dm1 to include a quad, then generate a .override file specifying "maps/q2dm1.bsp" as the map to load with the new entity string. Then you save the .override file as 'maps/q2dm1-quad.bsp.override' and you can use 'q2dm1-quad' as if it were a valid map, eg in map rotations, `gamemap q2dm1-quad`, etc. Upon loading it, the server will change the real map back to q2dm1 and use the modified entity string specified in the .override file.

The .override file format is a proprietary R1Q2 binary format including a bitfield specifying what is present. This allows it to be extended at a later date without breaking compatibility. I've created a PHP script to generate a .override file since explaining the format here would take too long. Generate the .override file at http://r-1.ch/r1q2gen.php. If you don't know how to get an entity string from a .bsp, try entdump: http://r-1.ch/entdump.c (Win32 binary: http://r-1.ch/entdump.exe, runs from cmd prompt, eg entdump q2dm1.bsp > q2dm1.txt). Note that the server will very likely crash if you use a bad .override file so be careful! Please don't ask me about this feature if you aren't sure how to use it, it's pretty complicated and amazingly hacky but it does work. I think.

* Add more configstring checking on the server - all configstrings (except CS_STATUSBAR) are now length checked and the mapname is allowed to partially overflow depending on the mod use of the CS_CDTRACK index.

* Fixed configstring length checking on the client that didn't actually work as intended.

* Fixed the server-side userinfo validation considering keys/values that were 1 character less than the maximum allowed as invalid and caused it to kick rather as well as blackhole on bad userinfo updates.

* `dellrconcmd` now actually works and takes a substring against all lrcon commands to make it easier to use via rcon where spaces aren't permitted. Eg `addlrconcmd "status "` can be deleted by `dellrconcmd sta`. Only the first match will be deleted.

* Speed up connection process when using `cl_async 0`.

* Restore output of 'status' player ID to original 3.20 for compatibility with some rcon parsing tools.

* `in_initjoy` can be changed in-game.

* Fixed an array bounds read error when loading some (corrupted?) .wav files.

* Fixed rare "x is greater than full buffer size" non-fatal error being treated as a fatal error.

* Made some Game DLL errors non-fatal.

* Netgraph drawing fixed and is now drawn behind the HUD.

* Properly clear console history buffers so cycling through commands and editing them doesn't break.



## b4450 (Client Only)
**February 06, 2005 - R1CH**

* Fixed `vid_restart` crash caused by backporting some 3.20 code in a vain attempt to fix this damn "sticky" bug.



## b4449:
**February 06, 2005 - R1CH**

* Fixed the `begin` fix disconnecting clients who were in the middle of connecting during a map change.

* Command expansion exploits no longer kick and are only logged due to the number of people who blatantly type what some random spammer tells them to.

* Joystick support is compiled in by default to the Win32 client binary, use `in_initjoy 1` to enable it.

* New Linux build process contributed by turol.

* Removed the 1 ms delay after rendering as it caused the Linux client to time out during demo playback (!!!)



## b4437:
**February 04, 2005 - R1CH**

* Fixed server crashing download exploit.

* Fixed abuse of connection process `begin` command to cause instant respawning and text spam and other issues depending on the mod.

* Fixed `sv_max_traces_per_frame` likely crashing the Game DLL if the limit was hit.

* Removed some debugging code from message queue.

* `stuffall` server command.

* `listnullcmds` and `listlrconcmds` commands.

* Path supplied for auto downloading is passed through StripHighBits regardless of the `sv_filter_stringcmds` setting.

* Linux client compilation fixes.

* Treat - as a valid character for `sv_validate_playerskins`.

* Adjust how the `set` command works so that it takes all arguments as a value. This allows you to use set from within quoted binds / aliases to set variables containing spaces.

* Reduced memory allocation for some small commands, using alloca instead.

* "Extended API" for rendering DLLs. This is experimental and specific to R1Q2/R1GL at the moment. It essentially allows Q2 to export more functions through a different linker-level function call so that the rendering DLL has access to them. At present, FS_FOpenFile, FS_Read and FS_FCloseFile are exported, allowing R1GL to only read needed bytes from files instead of loading the entire file to memory. This is still fully compatible with other renderers since the change is at the linker-level, not the rendering API structure itself.

* Fixed linux auto downloading failing to read the user supplied path before trying the lower case version.



## b4359 (Client Only):
**February 01, 2005 - R1CH**

* Fixed random parts of the map drawing randomly and randomly crashing at random.


 
## b4358:
**February 01, 2005 - R1CH**

* Fixed dedicated server crash when using cvarbans.



## b4357:
**February 01, 2005 - R1CH**

Fixed possible memory corruption on the client when loading .loc files.

* Fixed `sv_ratelimit_status` not doing what it was supposed to do.

* Blackholes now take an optional bit mask against the IP address, eg 192.168.0.0/16 would block 192.168.*.*.

* `sv_blackhole_mask`, default mask for automatic blackholes.

* Defaulted `in_dinputkeyboard` to 0 due to numerous complaints that Windows hotkeys no longer work (side effect of using DirectInput unfortunately)

* Removed unset command, was not really usable.

* Fixed pics/models getting unloaded and then reloaded with `cl_defermodels 1` (end registration is now done after all models load)

* Game DLL memory leaks are displayed on shutdown.

* Fixed some varargs functions with incorrect arguments (thanks to turol)

* Fixed unrequested cvar check results not kicking clients.

* Fixed zlib UDP downloads favoring uncompressed over compressed content if the compressed content was < 1300 bytes.

* ignore / unignore client commands for ignoring blocks of chat text.

* `serverstatus` client command for displaying quick status of a server. Use by itself for the current server, `serverstatus address` for status of a different server. Accepts `serverstatus /s` for showing full serverinfo and `serverstatus /i` for quick info. This replaces the undocumented `sinfo` command of previous builds.

* rcon results no longer have x.x.x.x: print displayed.

* Increased debug graph width to 2048 pixels.

* Fixed `gl_mode` and video menu disagreeing about which resolutions to use for which selections.

* Other miscellaneous fixes here and there.



## b4257: (Client Only)
**January 24, 2005 - R1CH**

* Hopefully improve `cl_async 0` behaviour a bit more.

* Fixed KP_MINUS / KP_PLUS acting reversed and allow Alt+Enter in DirectInput keyboard mode.

* Delay for 1ms when rendering speed is below vsync to avoid 100% CPU usage.



## b4228: (Client Only)
**January 23, 2005 - R1CH**

* Backported all (hopefully) the frame and input code from 3.20 into R1Q2. Using `cl_async 0` now should use code identical to the 3.20 and other clients such as NoCheat for movement.

* `k_repeatrate` and `k_repeatdelay` (both in milliseconds) for the new DirectInput keyboard repeat settings. Default of 0 tries to lookup your current global Windows repeat rate/delay and use a similar value.



## b4215: (Client Only)
**January 23, 2005 - R1CH**

* Fixed more mouse issues and keyboard repeating itself during input subsystem restarts.

**NOTE: `cl_async 0` is broken in these previous two builds. Working on it now.**



## b4214: (Client Only)
**January 23, 2005 - R1CH**

* Fixed crash on startup if not using DirectInput mouse.

* Fixed some key mappings (particularly numpad) with DirectInput keyboard.

* Caps Lock, Num Lock and Scroll Lock are bindable.

* Add 1280x1024 and 1680x1050 to resolutions menu.



## b4186:
**January 23, 2005 - R1CH**

* Fixed an infinite loop on Linux if low-ASCII characters were attempted to be output.

* DirectInput Keyboard support on Win32 Client. Should fix the long-standing "sticky keys" that happens if your FPS drops. Default enabled, use `in_dinputkeyboard 0` to disable it if you have problems.

* Client chat buffer stores the previous messages, use UP/DOWN arrow keys to retrieve previous messages.

* All memory allocated by the Game DLL is now tagged for buffer overflow checking.

* New cvar, `z_buggygame 1` will attempt to work around buggy Game DLLs that manage memory incorrectly by ignoring free() of invalid pointer and allocating twice the requested size to hopefully account for any poor code that writes past the end of memory. Horrible, I know... but so are some Game DLLs with the memory management it seems 😊.

* Attempted command expansion exploit no longer adds a blackhole.

* All exploit type of attacks are logged with a line beginning "EXPLOIT:"



## b4107:
**January 21, 2005 - R1CH**

* Fixed a crash when using `developer 1` and `sv_validate_playerskins 1`.

* Client .loc files are now loaded from "maps/mapname.loc" and if not found, "locs/mapname.loc". Thus global loc files should go into C:\Quake2\baseq2\locs\q2dm1.loc for example.

* .loc files loaded from a pak no longer overrun the file lengh.

* Fixed R1Q2 menu reading invalid strings if you had a cvar set outside the expected range.

* Improved a few utility functions such as the `userinfo`/`stringcmd` filter.

* Don't allow low-ASCII characters to be printed to the server console, fixed stripping of high ASCII chars on Linux.

* 64 bit portability cleanups.



## b4066: (Client Only)
**January 18, 2005 - R1CH**

* Fixed a crash that occured if using `cl_defermodels` and trying to get the sound origin of a brush model that hadn't fully loaded.

* Fixed a number of demo playback issues.

* .loc files are now loaded from both the /mod/maps/ directory and if not found, the global /locs/ directory.

* Fixed console starting dropped after first connecting.



## b4038:
**January 18, 2005 - R1CH**

* Fixed `sv_connectmessage` sending if it's empty.

* Fixed possible memory corruption issue with newline-expanded cvars being set to empty strings.

* Improved `z_debug 1` to verify memory size before the buffer so it hopefully doesn't cause an access violation (`z_debug 1` already found the source of one bug I'd been trying to track down in the Gloom mod for ages)

Fixed cvarbans to work correctly if more than 6 or 7 were set.

* Reduced `cl_maxfps` cap to 5 for allowing some physics exploits to continue to work.

* Playerstate weaponindex is now delta-compressed, not sure why it wasn't before. Let me know if this causes any view weapon model issues.



## b4030: (Client Only)
**January 16, 2005 - R1CH**

* Home/End behaviour in the console when at the beginning/end of the line or at an empty console buffer now applies to the console buffer due to popular demand.



## b4027: (Client Only)
**January 15, 2005 - R1CH**

* Fix console buffer automatically filling up with old commands after cycling through the number of saved commands.



## b4025:
**January 15, 2005 - R1CH**

This is perhaps one of the largest amount changes ever made to R1Q2 as you can probably guess by the huge build number bump. Both the new client and server have been tested by a number of people so I hope there aren't too many bugs 😊. If you run a server and update to this version, please be sure to check for updates regularly as there might still be the odd few issues that made it past testing. If you notice anything odd with this version then post a bug report in the forums! Enjoy!

* All-new server-side netcode! It is now impossible to be disconnected from an "overflow" since all messages are packetized, queued and sorted. This gives the server much more power to inspect each individual type of message and choose what to do with it. Mods are now free to dump as much junk as they want at the client (within reason 😉) and the server will handle the delivery. The new code guarantees enough room for one reliable message per netchan ack and fills the remaining packet space with unreliables - first the svc_frame and then room permitting, all remaining datagrams starting with the most "noticable" - tempents and sounds. If they all fit and the netchan has just been acked, the packet is filled with all remaining reliable messages. Any unreliables that didn't make it get dropped as unreliable is assumed to be time-sensitive and thus not worth preserving for the next frame. The concept of overflowing both the unreliable and reliable buffers is gone, and the "Netchan_Transmit: dumped unreliable" dropping of the unreliable messages can also never happen. Clients probably won't notice any changes at all, but internally this makes for a MUCH cleaner and reliable implementation. Since this is all server-side it's fully compatible with any existing client. This will increase CPU usage a little bit, but I barely went over 50% with 40 players on a 700MHz Pentium III so it shouldn't be too noticable at all. This change also benefits the connection process - the server can begin sending data as soon as the client issues "new" without having to wait for the individual configstrings/baselines prompts, resulting in faster connection times.

* Reduced bandwidth usage / packet size when sending delta updates from the baseline. Previously Q2 would always send the old origin even if it were identical to the baseline, causing a minimum of 10 bytes per entity. With this fix, unmodified deltas are down to just 2 bytes which should give quite a significant improvement for maps that use a lot of brush models or other static entities.

* Server-side limited rcon support. Use `lrcon_password` to set the password, clients should use `rcon_password` as normal. You can then use `addlrconcmd` substring to allow certain commands, for example `addlrconcmd "set dmflags "` will allow a client to use `rcon set dmflags 0`, `rcon set dmflags 16404` but not `rcon set dmflags` (notice the space). Multiple commands in a single rcon are discarded. Use `delrconcmd` substring to remove an `lrcon` command.

* `sv_force_reconnect` cvar to force a client reconnection to the server. Set this to the ip:port of the server to force clients to be reconnected as an anti-proxy check. Note that using this will be much faster than similar features in Q2Admin or whatever as R1Q2 is able to send the reconnect as soon as the client begins connecting instead of waiting for them to finish loading. The reconnect feature will ignore any IP addresses beginning 127. 10. 192.168. or 172.16. for compatibility with a LAN/WAN server and is completely transparent to any mods.

* `sv_download_refuselimit` cvar, will refuse auto-downloading when this many players are in game. Default 0 means no limit.

* `sv_gamedebug 1` will turn on extra debug info for mod developers. Messages are printed to the console whenever something "bad" happens such as attempting to link unused entities, duplicating configstrings, unused entities having state, etc. Setting `sv_gamedebug` to higher values will cause breakpoints to occur depending on the severity of the problem, up to a maximum of `sv_gamedebug 4` to break on all messages.

* Game DLL errors that would otherwise cause a fatal error condition are now always displayed regardless of `sv_gamedebug` setting.

* Additional Game DLL checks for correct usage of `cprintf`, `centerprintf`, `unicast` and `WriteByte`/`WriteChar`.

* `sv_max_downloadsize` defaulted to 16MB.

* `sv_strict_userinfo_check` defaulted to 1.

* Disallow blank and all-whitespace names.

* Memory allocated by Q2 is no longer initialized to zero. Often the memory is immediately overwritten again with other data so initializing it to zero was only slowing things down. I believe I've caught all instances where the code expected it to be initialized to zero, but if you experience any crashes please let me know how to reproduce it, or better yet get me a core file or crash dump.

* Reworked the speedbot detection code into a generic "bad movement" detector. Moving too fast, too slow or having very irregular movement (maybe caused by lag) will trigger a detection -- see also `sv_enforcetime` change.

* `sv_enforcetime` set to values higher than 1 will kick clients that exceed this amount of "bad movement". Every bad movement detection will increase an internal counter by 1 plus however far off the movement was. When this counter exceeds `sv_enforcetime`, the client will be kicked. Good movements will decrease the counter over time to hopefully account for the odd lag spike causing it to be hit. Good values would be between 5-25.

* Disallow use of the `link` command to link outside of the Quake 2 directory.

* Fixed name change notification spamming a lot if the client tries to use a name over 15 characters.

* Duplicate configstrings updates by Game DLLs are discarded.

* No longer add automatic blackholes when a client attempts to download a name ending in /.

* Re-implemented qport checking into the server as it appears some consumer routers still don't know how to do NAT properly.

* For protocol 35 (R1Q2, EGL), qport is now a single byte. If you have multiple clients behind a single IP address with a broken NAT router, be sure you each have a unique qport value between 0 and 255. You can also set qport to 0 to avoid sending it completely if your router is not broken, doing so will save a tiny bit of outgoing bandwidth.

* Force the correct value of `CS_MAXCLIENTS` regardless of what the Game DLL sets it to.

* Fixed .loc file loading on the client not properly reading the full location name.

* Fixed possible configstring corruption on client when dealing with malformed `CS_PLAYERSKINS` strings.

* Fixed a buffer overflow (doesn't look exploitable) in client address book handling.

* Fixed four buffer overflows in the "start server" menu that look possibly exploitable...

* ..and another in the player config menu...

* ..and yet another that looks remotely exploitable... Noticing a pattern here? The Q2 menu code is pretty awful 😅.

* Added some assembly-optimized string functions to the Win32 Client (also shared by R1GL) that are about 200-300% faster than the libc implementations for some reason.

* Used some float->int unicast checks against zero for faster processing in some functions such as tracing on Win32. Disabled under linux though, presumably because gcc -ffast-math also tries to do something similar.

* Disallow path traversal characters in logfile_name to prevent anyone with rcon from being able to overwrite arbitrary files.

* Fixed really stupid bug on client that causes Sleep to be called every frame when not running a server. This was accountable for around 10% performance drop in the client. Oops.

* Reduced frequency at which commands are executed, previously the function was being called for every single tick regardless if anything actually happened.

* `logfiler_filterlevel` and `con_filterlevel` cvars to control the level of messages written to the logfile and console respectively. Set the value to the ones you DON'T want from these:

```C++
#define   LOG_CLIENT      0x1
#define   LOG_SERVER      0x2
#define   LOG_DEBUG       0x4
#define   LOG_WARNING     0x8
#define   LOG_ERROR       0x10
#define   LOG_GAME        0x20
#define   LOG_CONNECT     0x40
#define   LOG_NAME        0x80
#define   LOG_DROP        0x100
#define   LOG_KICK        0x200
#define   LOG_EXPLOIT     0x400
#define   LOG_DOWNLOAD    0x800
#define   LOG_NOTICE      0x1000
#define   LOG_CHAT        0x2000
#define   LOG_NET         0x4000
#define   LOG_GAMEDEBUG   0x8000
````

I hope they are mostly self explanatory. For example if you don't want to see heartbeat notices and other trivial information in your log file, set `log_filterlevel` to 4096 to filter out LOG_NOTICE messages.

* Defaulted client `msg` cvar to 0.

* `messagemode` / `messagemode2` accept default text as a parameter.

* Added binary tree for sound name lookups.

* Slightly optimized the hud drawing functions in the client.

* Increased server console command line length to 1024 from 256.

* Handle log file write failures gracefully by closing the log file.

* Doubled client console buffer size.

* Auto-downloading to a client on Linux will try both the original and a lower-case version of the requested file. It is highly recommended where possible to keep all filenames and filename references (eg in maps) lower case.

* Normalize auto-download requests beginning with ./ on both client and server.

* Filesystem cache is now case-sensitive on Linux.

* Fixed `sv_fpsflood` kicking clients that auto download too fast.

* Fixed client mispredicting against brush models if `cl_defermodels` was set to 0.

* Fixed client switching from non-baseq2 to baseq2 during connection resulting in ./ being used as the mod dir instead of ./baseq2

* Client will render menu on a black background and won't draw text characters that would be obscured by the console. This is to help support a change in R1GL that groups all text drawing into a single glBegin/glEnd call for speed.

* Fixed server-side baseline creation possibly causing entity state corruption for one frame when running.

* `cl_autorecord` client cvar, will automatically begin recording a demo at the start of a map and stop on a map change.

* Two special client cvars, `beginmapcmd` and `endmapcmd` will be expanded and treated as a command upon the beginning and end of a map. Eg set `endmapcmd "screenshot"` will take a screenshot just before the map changes.

* Fixed server browser not showing some servers if they have the info string as another.

* Automatically empty file system cache after failing to load a map, this allows you to install it while R1Q2 is still running without having to use `fsflushcache` for R1Q2 to notice it's presence.

* `cl_smoothsteps 2` on the client will enable more agressive step smoothing, but may cause the odd attempted smoothing when not on stairs such as when going up lifts or ramps. cl_smoothsteps 3 will use a similar method but will only consider 'steps' to be differences of 64, 96 and 128 units. At low cl_maxfps values and on most maps, cl_smoothsteps 3 should give the best response. If you still can't get step smoothing to work how you want it to, you may wish to consider using cl_async 0 to disable the net/fps separation and adjusting r_maxfps to 100 or so. Since a low cl_maxfps is the default in R1Q2, I have also made cl_smoothsteps 3 the default.

* Server `status` command will show version strings of all connected clients if you use `status 1`, and will show active downloads with `status 2`.

* Automatically fall back to protocol 34 if the server disconnects the client or if the client receives unknown command bytes from the server within the first 30 seconds of connecting.

* `sv_download_drop_file` / `sv_download_drop_message` cvars. If `sv_download_drop_file` is set to a path and a client attempts to auto download that path, `sv_download_drop_message` will be printed to their console and they will be disconnected. This can be used to stop people trying to auto download large amounts of client-side files for big mods and direct them to the mod homepage instead. Supports \n newline expansion. For example use, the Q2 Gloom mod requires a sprite called sprites/s_firea.sp2 that is precached by the mod on every map. Thus I can set `sv_download_drop_file "sprites/s_firea.sp2"` and anyone connecting who hasn't installed the Q2 Gloom mod will be disconnected after downloading the map. This will vary from mod to mod but you should try to set the dropfile to a model/sprite if possible as they are the first things a client tries to auto download. Try using the `modellist` command to find a common model that is always loaded on each map.

* Fixed `cl_async 0` causing huge amounts of packets when used at low `r_maxfps` values. Note that if you don't have `r_maxfps` set higher than your video card refresh rate (assuming vsync is enabled) then you will experience high CPU usage as the client will be doing tight loops with no delay. This shouldn't have any effect on the game though, just something to be aware of.

* Client will properly auto-download sprites and skins instead of ignoring the linked skins which subsequently crashes when trying to render.

* Prevent `cl_maxfps` / `r_maxfps` from being set too low to cause problems.

* Default `m_fixaccel` to 1 if Windows 2k/XP/etc is detected.

* `demomap` will load demos under the baseq2 Game DLL to avoid unnecessary interference from any mod progs. It will also set the maxclients configstring to 1 allowing you to use timedemo/timescale during demo playback.

* `sv_nc_clientsonly 0` will no longer check visibility of brush models.

* Client locations support @t in chat messages for the location "there" where you are currently aiming. Note that this doesn't use %t due to some mods already using %t for other purposes.

* Client locations now use %l for compatibility with server-side expansion if you don't have any local .loc files.

* Cvar expansion supports bash-like ${varname}something for expanding from part of a string.

* Against my better judgement, added "Xania Railgun Trail" support with `cl_railtrail` 1 / 2 / 3 etc for different colours. Values higher than 5 will result in the original railtrail with a modified palette.

* Win32 client is built single-threaded for slightly faster calls to libc functions. To support this change, the dedicated server console is no longer thread-based, meaning if you drag the server console window the game will freeze and possibly cause timer-related problems.

* Default auto-downloading on for client.

* Fixed R1Q2 cvars `cl_lents` and `s_focusfree` being saved to config.cfg

* Not a change in R1Q2 but I was looking over some of the default cvars, and s_mixahead is one I see many people never touch. If you have a fast CPU and a decent sound card, try lowering s_mixahead to 0.1 for decreased sound latency. If you experience stuttering or other problems, increase it by 0.01 until it goes away. For reference, I can use 0.05 on a Athlon 64 with a Santa Cruz sound card, but this seems to be a minority case. 0.08 - 0.1 seem the best for most modern systems.

* Reworded "maximum performance / maximum compatibility" options in sound config menu. This actually controls whether Q2 writes directly to the DirectX primary buffer. According to the SDK the primary buffer is supposed to be used for DirectX to mix in and secondary buffers for individual audio sources. However since Q2 does its own internal mixing the use of secondary buffers is somewhat pointless. Technically Q2 should write to the secondary buffer for greatest compatibility, however `s_primary 1` ("maximum performance") will write directly to the primary buffer. I'm not convinced this will have a noticable performance increase though, so I would recommend `s_primary 0` for most purposes.

* Client `s_ambient` cvar to control whether to play world sounds.

* Client mouse cvars (`in_mouse`, `m_fixaccel`, `m_directinput`) are applied immediately when changed.

* New cvarban syntax for greater flexibility. Supports inverted matching on every test and will not consider empty string to be a valid result unless used with *. New syntax: `addcvarban <cvarname> <[!]((=|<|>)numvalue|[~|#]string|*)> <KICK|BLACKHOLE> message`. Message is now optional. # is used as an explict string match, eg `addcvarban foo #!` will match on on foo containing "!".

* Userinfobans, similar to cvarbans but operate on userinfo. `adduserinfoban` / `deluserinfoban` / `listuserinfobans`.

* Validate userinfo key/value pair lengths that come from the client. A normal client should never send them over 64 characters, so R1Q2 will drop and blackhole any clients that send excessively long userinfo key/value pairs which could be used for malicious purposes.

* Using the console kick function for players who have a number at the beginning of their name will no longer treat the number as a player ID.

* Using `cl_async 0` will cause `cl_maxfps` and `r_maxfps` to become linked to allow servers that cap `cl_maxfps` to continue to cap your packet rate.

* Use OS-specific functions (stat/GetFileAttributesEx) instead of fopen/fseek/ftell/fclose to determine file lengths.

* New cvar, `z_debug 1` will switch all memory allocation into debug mode which is able to detect a lot of extra memory corruption problems at the expense of much slower allocation/free code. Mod developers or servers that seem to crash often should use `z_debug 1` to see any problems related to memory management.

* Game DLL memory allocation locations are tracked to catch any bad free attempts.

* Fatal server crashes now write the error message to the logfile.

* `cl_hidewindowtitle` cvar for disabling the Q2 Window titlebar in windowed mode for hopefully better compatibility with some window managers.

* `delhole` command now takes either an index or IP address as the argument.

* Fix for brush model loopsound bug at http://www.quakesrc.org/forums/viewtopic.php?t=4262

* Fix for brush sides -1 array index bug at http://www.quakesrc.org/forums/viewtopic.php?t=4351

* `sv_packetentities_hack 2` will try both full frame data with zlib for protocol 35 clients and then go back and cut off the packet if it still doesn't fit. Gives the best of both worlds at the price of doing some potentially useless compression.

* Editable console and chat text buffers.

Let the bug hunting begin...




## b2803:
**December 21, 2004 - R1CH**

* Fix `sv_validate_playerskins` not replacing some invalid skins.

* New cvar, `sv_entity_inuse_hack` to control whether server sends entities that aren't marked as in use. Some mods seemingly do not clean up entities that are not in use, resulting in them possibly going over the network and wasting bandwidth. Set this to 1 if possible to save bandwidth and CPU time, but depending on the mod you are using this behaviour may be intended by the mod author not realising of the problem. Default to 0.

* Display client ping as 0 instead of 9999 when not reciving any frame latencies in `sv_calcpings_method 2`.

* Add R1GL and NCGL to video menu as options.

* Automatically update latched cvars on map change by reloading Game DLL.




## b2789:
**December 20, 2004 - R1CH**

* Fix bad bytes being sent to client on a message overflow (this will fix the "Illegible server message" bug)

* Made `Com_sprintf` always terminate output.

* Changed PMove to ignore any bbox set if the server isn't running in enhanced mode.

* Make client .loc reading support more robust.

* `addloc` and `saveloc` commands on client.

* Added new byte to R1Q2 serverdata for the server to convey whether it is running in enhanced mode, removed the downloadport. PROTOCOL 35 COMPATIBILITY IS BROKEN WITH THESE CHANGES.

* Client can now detect when packet overflow on the server is occuring and prints suitable message.

* Moved "mod didn't clean up unused entity..." message to only show when `sv_gamedebug` is set.



## b2750:
**December 19, 2004 - R1CH**

(Client only)
* Fix crash when reading malformed .loc file.



## b2746:
**December 19, 2004 - R1CH**

Quick update to remedy some small issues in 2742:

* Fix `sv_filter_stringcmds 2` causing clients to get dropped as the version check got filtered.

* Fix `snd_restart` / `vid_restart` being printed on dedicated server console.

* New cvar, `logfile_name` to determine where to log qconsole output.



## b2742:
**December 03, 2004 - R1CH**

This is a **lot** if changes 😊. Please post any bug reports as it's quite likely there may be some.

COMMON:
* Optimize some map access functions.

* Added intvalue to cvars for less cache stalling due to FPU usage.

* Optimized some 0-byte SZ_Writes

* Include binary name in server startup for easier debugging when people copy/paste.

* Merge more Win32/Linux common networking code.

* `unset` command to remove cvars - dangerous, removing a cvar that is in use or will be accessed at a later date will crash.

* Support escape sequence for sending $ byte (warning, using this in the client will probably get you kicked)

* Changed Win32 file searching API (maybe 0.001% faster? 😊).

* Load *.pak files. pak0-1024 are loaded first, then other .pak files in alphabetical order. Thus maps-v2.pak will override any files which also exist in maps-v1.pak.

* Timestamp support on qconsole.log. Use `logfile_timestamp 1` to enable (on by default) and `logfile_timestamp_format` to control the timestamp format as a strftime string. Warning, a malformed `logfile_timestamp_format` may crash the server. Defaults to ISO 8601 format.

* Load pak files into binary tree for faster lookup of files.

* cvar and cmd lookup also made into binary tree.

CLIENT:
* +button events no longer fire when typing in the client console.

* Fix the first sound loaded by mods not playing when using deferred model loading.

* Implement mini R1Q2 options menu in the client for common settings.

* `cl_cmdcomplete` cvar to determine how to do command completion, 0 = regular q2, 1 = tab/cycle through matches, 2 = tab list all matches.

* Support buffered DirectInput mouse, won't "skip" very very fast button presses any more. Use `m_directinput 1` to enable, can be toggled mid-game. `m_directinput 2` uses the old-style immediate sampling.

* Windows window-specific keys in windowed mode such as Alt and F10 won't lock up the game when pressed.

SERVER:
* Change how setmaster behaves when executed before the server is fully started so that the heartbeat actually gets sent.

* Support old-style Win32 console for GameHost, should be automatically used if GameHost is detected. Run dedicated.exe -oldconsole +whatever... to use it manually.

* Add `listbannedcommands` and `listcvarbans`.

* Fix memory leak in client version string lookup.

* `sv_mapdownload_denied_message` and `sv_mapdownload_ok_message` cvars to send message to client when map download fails / starts. Can be used for example to direct clients to a website map repository where they can download the maps. Supports \n for newline expansion.

* Don't allow the server to start as root on linux.

* Fix `sv_randomframe`, now safe to use.

* Change how multiple userinfos in same packet are handled, I forgot that userinfo belong in reliable portion and thus multiple userinfos are perfectly legit.

* Support zlib compression on UDP downloads to make best use of the limited packet size (R1Q2 specific).

* Flush netchan during connection process. This speeds up autodownloading and connection times.

* Sanity check length of configstrings coming from the Game DLL.

* `sv_recycle 2` can be use to reload the Game DLL every map change where this may be desirable. Note that recycling the Game DLL does not cause a full server init like the `map` command.

* `sv_blackholes 0` no longer disables manual additions of blackholes with the addhole command.

* `sv_max_traces_per_frame` cvar to control how many traces are allowed in a single game frame. This is to overcome buggy mods and/or maps that repeatedly call gi.trace in a loop which keeps hitting the same object over and over. Right now it's set to 10000 which should be way more than any mod needs legitimately. If you experience SV_Trace ALERT messages more often than you experience server freezes though, you may wish to increase this value, but even at 10000 my Athlon 64 noticably stalls so it's unlikely you'll need to touch this.

* `sv_ratelimit_status`, number of status requests/sec to accept. This is used to mitigate any potential "reflection" Denial of Service attacks as a 10 byte status packet can generate up to almost 1400 bytes of response.

* rcon commands that use the correct password first time are no longer subject to rate limiting. A bad rcon attempt will enable rate limiting (currently hard coded to 0.5 seconds) to mitigate against rcon brute force floods.

* `addstuffcmd` command, allow you to stuff commands to clients on connect or entering the game. Also `delstuffcmd` and `liststuffcmds`.

* `addnullcmd` command for making a client command be ignored. Eg `addnullcmd invdrop` if the mod doesn't support invdrop and people keep hitting it anyway and saying it. Or something. Also `delnullcmd`, `listnullcmds`.

* `bancommands` allow option of showing message, kicking or silently dropping when matched.

* `cvarbans` support substring matching on the return value.

* `stuff` server console command.

* Server-side skin validation, `sv_validate_playerskins`. If a malformed skin is used it is replaced with male/grunt before being sent to other clients. Skins that do not consist of alphanumeric or _ characters will be considered bad also. This is recommended to leave enabled unless you have specific problems with it as a malicious user using skin "$$" will cause connected clients to try to auto download "download players/$$/skin.pcx" which of course is an illegal variable expansion resulting in them getting dropped. The R1Q2 client and EGL are not affected as they both quote the download path before sending it. Other client authors are encouraged to also quote the download paths to mitigate this problem.

* `sv_idlekick`, seconds before kicking idle clients. 0 to disable, "idle" is defined as not moving/attacking or talking for the time period.

* `sv_packetentities_hack` to control the "cutting off" of packet entities if they will not fit into a packet. If entities do not fit, the entire packet is discarded and the client will experience a "freezing" effect, however if it is cut off, clients may experience delta problems which manifest themselves as flying objects, incorrect models/frames, bad effects, etc. Default 0, was previously hard-coded enabled on older builds.

* Send entities to clients that have an associated effect but not a modelindex. This allows effects to show up in correct place. Previously ents with no model were incorrectly assumed to always be world sounds and thus not sent if over 400 units away.

* `bancommand` and `unbancommand` renamed to fit into more intuitive naming scheme, now `addcommandban` and `delcommandban`.



## b1918: Wow, this has been a busy week for R1Q2 updates...
**November 25, 2004 - R1CH**

Fix use of `map` command causing server to goto sleep.



## b1917: Or not. God damnit...
**November 25, 2004 - R1CH**

* Fix FREAKING HUGE security hole caused by pyroadmin (pyroadmin support is no longer compiled in by default any more)

* Fix starting of single player games

This is an **essential** upgrade.


## b1915: (why they all seem to end in 5 I don't know 🤔):
**November 24, 2004 - R1CH**

* Fix memory leak and/or Z_Free: bad magic crash.

* Don't duplicate send 'version' check.

* `z_stats` now includes TAG_GAME/TAG_LEVEL allocs.

* Few other misc things.

Hopefully I can slow down on the releases now, I know how annoying it is to constantly upgrade every week.



## b1895:
**November 23, 2004 - R1CH**

* Fix vwep issues on client.

* Split configstrings into MAX_QPATH chunks on server for CS_STATUSBAR etc, prevents bandwidth waste by sending same strings multiple times.

* Fix client handling of configstrings over MAX_QPATH length, only CS_STATUSBAR is allowed to "overflow". Thanks to [SkulleR] for noticing these problems.



## b1875:
**November 22, 2004 - R1CH**

* Fix 100% CPU loop when using `sv_strict_userinfo_check`



## b1873:
**November 22, 2004 - R1CH**

* Fix high CPU usage issue on linux dedicated servers if stdin was closed during execution.

* Fix clients getting kicked for "attempted command exploit" when using 0x7F (after further testing, the client side fix also affects how the server processes these packets, thus only 0xFF needs to be blocked. 0x7F is used as the "arrow" character in some binds)

* Rewrote cvarban implementation, now multiple checks on the same cvar can be added and will not cause multiple check commands to be sent to the client.

* Ignore duplicate userinfo if present in a command packet, should never happen but could be used to cause high CPU usage on the server by a malicious user.

* No longer filter > 256 byte stringcommands from the server, some legitimate ones were getting cut off and after looking over the code on the client, > 256 bytes are perfectly normal.

* Optimized delta encoding/decoding on client/server by removing redundant "null" state and memsets on each call.

* Client connections are refused if the userinfo is too full to allow the full /ip/ key to be inserted.

* `sv_strict_userinfo_check` cvar, set to 1 to disallow any non-standard bytes in userinfo, recommended.

* `sv_calcpings_method` cvar to control how to calculate pings. 0 to disable pings entirely, 1 for normal method, 2 for new best case minimum method. Option 2 seems to give much more accurate pings comparable to what server browsers or external ping programs report.

* Fix client command history crash on filling the command buffer with clipboard data.

* Failing to open a network socket is now a fatal error (note, r1q2 client will not try and open the dedicated server socket unless a server is specifically started)

* Server no longer consumes 100% when no game is running, ie before a `map` command or after a gi.error.

* Option to disable stdin on linux, set `nostdin 1` to prevent polling of input from the console, useful for servers running in the background.

* Removed loopback packet functions for DEDICATED_ONLY builds.

* Optimized order of statements in network functions to allow for most common matches first.

* Rewrote how commands are tokenized, no longer uses dynamic memory.

* Possibly some more stuff I forgot already.



## b1788:
**November 16, 2004 - R1CH**

* Client now defers loading of models when connecting. This will give faster connection times to servers that require an anti-bot reconnect at the expense of a little bit of stuttering immediately after joining.

* Fix for a network bandwidth consumption attack on the server when dealing with "info" packet that could have the spoofed source address of another Q2 server, resulting in them constantly querying each other.

* In addition to the above, ignore all packets from localhost that have the same source port as the server as a catch-all against spoofed 127.0.0.1 loops.

* Removed `cmd` connectionless command from client, I've yet to see anything that actually uses it plus it seemingly doesn't work from outside of Q2 anyway...

* 0xFF now filtered from userinfo/stringcmds.

* Laser beam transparency now matches Q2 3.2x default.

* Teleporter particles returned to Q2 3.2x default.

* Reverted stair detection / smoothing code to original.

* Merged Win32/Linux common network functions to a single file.

* Network functions now pass the address as a pointer instead of struct copying. This has been tested and seems to work OK, but since it's quite a major  change please look out for any network anomalies.

* Added code to skip over cinematic sequences when not compiled in.



## b1621
**November 11, 2004 - R1CH**

* Fix the 0x7F-in-string bug.

* Allow server admins to block/unblock certain commands.

* Further optimized filesystem cache.

* Support reserved slots (scarcely tested)

* Optional allow use of 'map' command from server console.

* Optional allow commands from unconnected clients.

* Finally fix all Q2Admin kick issues.

* Ugly "fix" for the strafe jump bug at > 125 FPS values.

See the appropriate cvar and command threads for full details of how to use the new features.

## b1390
**October 15, 2004 - R1CH**

* Two separate linux binaries to fix segfault issues.

* Filesystem cache now implemented as a redblack binary search tree.

* Calls to Pmove are faster (redundant code removed).

* Fix rare `SZ_GetSpace` crash when R1Q2 clients built a zpacket that was exactly 5 bytes smaller than the buffer.

* More of the imfamous misc changes.

* New r1q2updater binary for linux.



## b1258
**October 10, 2004 - R1CH**

* Implement linux download fix (how I overlooked this one I'll never know)...

* Attempts to download illegal paths now get the user blocked from the server

* Fix linux binary to support almost all gamei386.so files from various mods (no more segfaults with some luck!)

* Misc things here and there as usual

* Slipsteam fix (because I forgot to bump build number): Fix for some bots crashing the server if they joined before players.



## b1241
**October 03, 2004 - R1CH**

* More DInput mouse handling improvements.

* Fix rare `SZ_GetSpace` server crash due to incorrect netchan buffer size for non-R1Q2 clients.

* Reworked how the Q2Admin workaround works so it doesn't interfere with use of the `wait` command.

* Other misc. stuff along the way



## b1190
**September 23, 2004 - R1CH**

* DirectInput mouse support - set `m_directinput 1` followed by `in_restart` to activate.

* `cl_async`, master cvar to control FPS separation. If set to 0, no FPS separation occurs and every rendered frame results in a packet to the server.

* `cl_instantpacket` cvar, if set to 1 will immediately send a packet on important event such as weapon firing where waiting for the next packet frame could waste vital milliseconds. Use of this option will of course lead to unpredictable packets/sec. Only has effect is using FPS separation.

* `cl_smoothsteps` cvar, allows you to turn off the smoothing out of movements up steps. Useful at low FPS when using FPS separation as it incorrectly marks some crates/ramps as steps (this will be fixed ASAP).

* Default `sv_fpsflood` off (0).

Thanks to [j]`FragBait for suggestions that this build is focused upon.



## b1155: Starting now I think I'll document any noteworthy changes in R1Q2. As always, to get the latest version run the R1Q2 Updater.
**September 22, 2004 - R1CH**

* Fix Q2Admin erroneously thinking R1Q2 is a "client side proxy". This was caused by the FPS separation, depending on the FPS, multiple userinfo updates were merged into 1 so the server never saw that they individually went through. This caused the Q2Admin ratbot detector to trigger when it never saw the ratbot name detect test.

* Made `scr_conheight` between 0 to 1 instead of 0 to 100. This fixes compatibility with configs from NoCheat as NC uses the latter format.

* Don't spam speed cheat notices to listen servers.

* Add warning message if you attempt to use a high `cl_maxfps` value. `cl_maxfps` higher than 30 is rarely needed so please try and keep it low to save bandwidth and CPU time for the server.

&nbsp;
&nbsp;
![image](https://github.com/QuakeMM/test/assets/74773831/1f1bd486-e261-40ff-93fb-47652a4f6a05)
&nbsp;
&nbsp;
 
> [!NOTE]
> Changelog text converted to markdown by SGC-Puffy @QuakeMM - 2023
>
> Original forum changelog can be found archived here:  https://web.archive.org/web/20100616232538/http://www.r1ch.net/forum/index.php?topic=106.0

