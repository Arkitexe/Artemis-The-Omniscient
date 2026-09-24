# Artemis, The Omniscient - prebuilt Windows package

This repo is the ready-to-run build of Artemis, The Omniscient, a Halo
Infinite capture-and-log agent. It figures out which Azure server is hosting
each match, measures your real ping to it and logs every match. No Python
needed.

## Get it

Download `Artemis-<version>-win64.zip` from the [Releases](../../releases)
page, unzip it anywhere and double-click `Artemis.exe`.

You can also clone the repo. The checkout is the same thing as the unzipped
folder:

```
git clone https://github.com/Arkitexe/Artemis-The-Omniscient.git
Artemis-The-Omniscient\Artemis.exe
```

Keep `Artemis.exe` and `_internal\` together.

## First launch

1. Accept the Windows admin (UAC) prompt. Packet capture uses the WinDivert
   kernel driver, which needs it.
2. If SmartScreen says "Windows protected your PC", click More info, then
   Run anyway. The exe is not code-signed.
3. If it says the Edge WebView2 runtime is missing (Windows 11 already has
   it), run `MicrosoftEdgeWebview2Setup.exe` from this folder once.
4. Start Halo. Matches log automatically. Your data goes to
   `%USERPROFILE%\Arkitexe\Artemis\`, and nothing gets written to this
   folder. If you ran an older build that kept its data somewhere else, it
   gets moved over automatically the first time you launch this one.

`README.txt` has the full rundown. The main thing to know: don't run other
programs that use the WinDivert driver at the same time, or capture stops
working.

## Source

This repo only has the PyInstaller output from the private source repo's
`build_exe.bat`, and each release here matches a tagged version there.
