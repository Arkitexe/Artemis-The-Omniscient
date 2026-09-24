Artemis, The Omniscient
=======================

A Halo Infinite capture-and-log agent. It watches Halo's game traffic,
works out which Azure server hosted each match, measures your real ping
to it, and logs every match so "this match felt off" can be checked
against numbers later.

It only reads traffic. It doesn't touch the game, change any packets or
talk to 343/Microsoft for you.


HOW TO RUN
----------
1. Unzip this folder anywhere (Desktop is fine). Keep the folder intact -
   Artemis.exe needs the _internal\ folder next to it.
2. Double-click Artemis.exe.
3. Accept the Windows admin (UAC) prompt. Packet capture uses the WinDivert
   kernel driver, which requires administrator rights. There is no way
   around this.
4. If Windows SmartScreen says "Windows protected your PC": click
   "More info" then "Run anyway". The exe is not code-signed.
5. Start Halo. Matches are detected and logged automatically. Hover the
   SESSION WATCHER bubble to see the live server, region and ping.

To quit: the QUIT bubble or the window's X. The tray icon's "Hide" only
minimizes it - the app keeps running and capturing.


REQUIREMENTS
------------
- Windows 10 or 11, 64-bit.
- Microsoft Edge WebView2 runtime. Windows 11 ships it. If Artemis
  says it is missing, run MicrosoftEdgeWebview2Setup.exe from this folder
  once and launch again.
- Internet on first launch: it downloads Microsoft's Azure IP-range list
  (a few MB) so servers can be mapped to regions. Cached afterwards.


WHERE YOUR DATA GOES
--------------------
Everything lands under your own profile, nothing in this folder:

    %USERPROFILE%\Arkitexe\Artemis\
        feel.db          every match + ping measurements (SQLite)
        sessions\        per-session .log and .jsonl
        matches\         one plain-text summary per match
        crash.log        only exists if something went wrong - send this
        trace.log        program trace, useful with crash.log

Delete that folder to reset.

If you ran an older build, its data is moved here automatically the
first time Artemis starts, so your match history carries over. Close the
older build first, or the move can't happen.


THINGS TO KNOW
--------------
- Don't run other programs that use the WinDivert driver at the same
  time. They can register their own copy of the driver, which breaks
  capture in Artemis until you reboot.
- Some antivirus products flag WinDivert64.sys because it is a packet
  filter driver. It is the standard, signed WinDivert 2.2 driver used by
  many networking tools.
- The "measured ping" shown is a 1 Hz ICMP probe to the elected game
  server. The path-health "est. loss" figure is an estimate from stream
  regularity, not measured packet loss.
- Halo's Easy Anti-Cheat has coexisted with this capture method across
  thousands of matches. Capture is read-only (SNIFF mode).
