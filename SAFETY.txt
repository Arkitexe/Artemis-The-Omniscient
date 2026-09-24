Artemis, The Omniscient: what it is, and is it safe?
====================================================

The short version
-----------------

- Artemis only listens. It gets a read-only copy of Halo's network traffic.
  It never changes, blocks or sends game traffic, and it doesn't touch the
  game itself.
- Everything it records stays on your PC. There's no account, no sign-in,
  no uploading, no analytics and no automatic updates.
- Windows warns you before it runs because the app isn't code-signed and it
  needs administrator rights. Both are explained below, along with how to
  check the download yourself.


What it is
----------

Artemis is a small Windows app for Halo Infinite on PC. While you play, it
works out which Microsoft Azure server is hosting your match, where that
server is, and what your real ping to it is. It logs every match, so "that
match felt off" can be checked against actual numbers later.

It was made by Arkitexe to answer one question: does how a match feels
depend on your ping to the server?


What it does while you play
---------------------------

1. Watches Halo's game traffic. It uses WinDivert, a widely used open-source
   packet capture driver, in "sniff" mode, which only hands the app a copy of
   the traffic. It looks at UDP traffic on the ports Halo uses (30000 to
   31000, and 3075) and keeps only packet sizes, timing and the server's
   address. It never reads what's inside the packets.

2. Pings the match server once a second. This is a normal ping, the same
   kind the "ping" command sends, and it's how Artemis measures your real
   latency. The ping carries the text "ArkitexeArtemis", so anyone looking at
   it can tell what sent it. It stops when the match ends.

3. Looks up where the server is, using Microsoft's public list of Azure IP
   ranges. The list is downloaded from microsoft.com the first time you run
   Artemis and again whenever your copy is more than two weeks old.

4. Reads a few things on your PC, without changing them:
   - Halo's own log file, to note the game mode.
   - The Halo entry in your Steam controller settings, to note whether Steam
     Input is on.
   - Which window is in front, to know when Halo is the active window.

5. Asks you one optional question at the end of each match: how sticky the
   aim assist felt, from 0 to 3. A Windows notification pops up, and you can
   answer in the app or skip it. Once per session it also asks, optionally,
   about your controller setup.

6. Saves all of this in a small database and a few log files on your PC.


What it never does
------------------

- It never reads or writes Halo's memory, injects anything into the game,
  sends keystrokes or mouse input, or changes any of Halo's files.
- It never changes, delays, blocks or re-sends network traffic. It only ever
  opens the capture driver in read-only mode.
- It never takes screenshots or records your screen, microphone, keyboard
  or voice chat.
- It never records gamertags, account IDs or anyone's personal details.
- It never uploads anything. No accounts, no analytics, no crash reporting,
  no automatic updates, nothing that downloads or replaces its own code.
- It doesn't install itself. No startup entry, no service of its own, no
  scheduled task, no firewall rules, no registry changes. It runs from the
  folder you unzipped it to.


Where your data goes
--------------------

Everything is saved under your own Windows profile:

    %USERPROFILE%\Arkitexe\Artemis\

That folder holds the match database (feel.db), per-session logs, a plain
text summary of each match, and the app's own logs (trace.log, and crash.log
if something went wrong). It records match times, the game server's address
and region, ping and packet numbers, and your answers to the optional
questions.

A few things worth knowing:

- Capture is based on ports, not on the Halo program. If some other program
  on your PC uses the same ports, the addresses of the servers it talks to
  are logged too. What's inside that traffic is still never read.
- The log files include file paths on your PC, and those contain your
  Windows username.
- None of this leaves your PC unless you send it to someone yourself, for
  example sending crash.log to get help with a problem.


Why it needs administrator rights
---------------------------------

Reading network traffic takes a driver (WinDivert), and Windows only lets
administrators load drivers. That's the only reason. Windows runs the whole
app as administrator once you say yes, so the window and the notifications
run that way too.

The driver is only loaded while Artemis uses it. In WinDivert's own words it
is "never permanently installed on your system", and restarting Windows
after you delete Artemis removes it completely.


Why Windows warns you about it
------------------------------

- "Windows protected your PC" (SmartScreen). Windows shows this for any app
  that isn't code-signed and hasn't been downloaded by many people yet. It is
  not a virus detection. Click "More info", then "Run anyway".

- "Unknown publisher" on the admin prompt. Same reason: the app isn't
  code-signed.

- Some antivirus programs flag WinDivert64.sys. Packet capture drivers get
  flagged by some security software because the same kind of tool can be
  misused. A few may also dislike how notifications are shown: the
  notification library uses a hidden PowerShell command to show them.
  Microsoft Defender scanned this release and found no threats (checked
  2026-09-24).


About the driver's signature
----------------------------

If you open the properties of _internal\pydivert\windivert_dll\WinDivert64.sys,
the digital signature is from a company in China:

    成都密思听科技有限公司

That's expected. It's the signature on the official WinDivert 2.2.2 release,
and the file in Artemis is byte-for-byte identical to the one in that
release:

    https://github.com/basil00/WinDivert/releases/tag/v2.2.2
    SHA-256: 8DA085332782708D8767BCACE5327A6EC7283C17CFB85E40B03CD2323A90DDC2

The steps below show how to check that yourself.


About Halo's anti-cheat
-----------------------

Artemis doesn't interact with Halo or Easy Anti-Cheat at all: no memory
access, no injection, no input, no file changes, and it never alters
traffic. It has been run alongside Halo for thousands of matches without
problems. Still, nobody outside the game's developers can promise how an
anti-cheat will behave in the future, so use it knowing that.


Check it yourself
-----------------

1. The download. In PowerShell, in the folder you downloaded to:

       Get-FileHash .\Artemis-*-win64.zip -Algorithm SHA256

   The result should match the SHA-256 listed on the release page.

2. A virus scan. Right-click the unzipped folder and choose "Scan with
   Microsoft Defender" (on Windows 11 it's under "Show more options").

3. The driver. In PowerShell, inside the unzipped folder:

       Get-FileHash .\_internal\pydivert\windivert_dll\WinDivert64.sys -Algorithm SHA256

   It should print the SHA-256 shown in the driver section above.

4. Watch what it does. Task Manager lists it as "Artemis, The Omniscient".
   The Network tab of Resource Monitor (search for "resmon") shows which
   addresses it exchanges traffic with.


Removing it
-----------

1. Close it with the QUIT bubble, the window's X, or Quit in the tray icon's
   menu. "Hide" in the tray menu only hides the window; the app keeps
   running and capturing.
2. Delete the folder you unzipped.
3. Delete %USERPROFILE%\Arkitexe\Artemis\ if you want your match history
   gone too.
4. Restart Windows. That unloads the WinDivert driver.


Good to know
------------

- It plays an intro sound and music when it opens. The SOUND bubble turns
  all of its audio off.
- Only one copy runs at a time.
- It needs Microsoft's Edge WebView2 runtime to draw its window. Windows 11
  already has it; if yours doesn't, run MicrosoftEdgeWebview2Setup.exe from
  the Artemis folder once.
