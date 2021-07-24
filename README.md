This repo contains all files needed to build `mono.dll` & `mono-2.0-bdwgc.dll` with debugging support for Unity.

The `master` branch contains the original files. You have to check out the `dnSpy` branch to build everything. Use VS2017.

# Supporting a new Unity version for Dummies

- Pull in the latest Unity mono.dll source code (either `git pull` if you have it or `git clone https://github.com/Unity-Technologies/mono.git`)
- Get this repo and make sure `master` and `dnSpy` branches are at the latest commit (`git pull` in both branches)
- Compile `umpatcher` in this repo (you need VS2019 or later and .NET Core SDK 3.0 or later installed)
- Download the correct Unity editor version
	- https://unity3d.com/get-unity/download/archive
	- https://unity3d.com/unity/qa/lts-releases
	- https://unity3d.com/unity/qa/patch-releases
- Either install the Unity editor or extract the necessary .dlls with extractmono.bat
- If using extractmono.bat
    - 7zip must be installed and 7z.exe must be in PATH
    - extractmono.bat also assumes there are no - (hyphens) besides the one in the UnitySetup file name
    - Pass the directory the UnitySetupxxx.exe files(s) are in as the first argument
    - Pass the directory you want the file(s) to be extracted to as the second argument
    - Pass "mbe" or "both" as third parameter. "mbe" will extract mono-2.0-bdwgc.dll, "both" will extract both dlls, and no third parameter will extract mono.dll
    - Example: .\extractmono.bat C:\Users\Unfou\Downloads C:\Users\Unfou\Desktop\mono both
- Otherwise, if installing Unity editor:
- Locate the compiled `mono.dll` & `mono-2.0-bdwgc.dll` files, eg.:
	- `C:\Program Files\Unity\Editor\Data\PlaybackEngines\windowsstandalonesupport\Variations\win32_nondevelopment_mono\Data\Mono\EmbedRuntime\mono.dll`
	- `C:\Program Files\Unity\Editor\Data\PlaybackEngines\windowsstandalonesupport\Variations\win64_nondevelopment_mono\Data\Mono\EmbedRuntime\mono.dll`
	- `C:\Program Files\Unity\Editor\Data\PlaybackEngines\windowsstandalonesupport\Variations\win64_development_mono\MonoBleedingEdge\EmbedRuntime\mono-2.0-bdwgc.dll`
	- `C:\Program Files\Unity\Editor\Data\PlaybackEngines\windowsstandalonesupport\Variations\win64_nondevelopment_mono\MonoBleedingEdge\EmbedRuntime\mono-2.0-bdwgc.dll`
- Get the timestamp stored in mono.dll's PE header
	- `umpatcher --timestamp "C:\path\to\the\correct\version\mono.dll"`
- Check out the correct version branch in the Unity mono repo, eg. if it's v5.4.3, the branch is called `unity-5.4`. Branches ending in `-mbe` use .NET 4.x assemblies.
	- Use `git branch -a` to see all remote branches
- `git checkout unity-5.4` (or whatever version you need)
- `git pull` (make sure it has the latest stuff)
- `gitk` to start a UI
	- Find the closest merge by comparing the merge date with the timestamp reported by `umpatcher` above
	- Remember the commit hash, you'll need it later
- Run umpatcher again to patch the code and commit it to the dnSpy-Unity-mono repo
	- `umpatcher 5.4.3 aa8a6e7afc2f4fe63921df4fe8a18cfd0a441d19 "C:\path\to\Unity-mono" "C:\path\to\dnSpy-Unity-mono"`

# Building `mono.dll` & `mono-2.0-bdwgc.dll`

- `dnSpy-Unity-mono-vZZZZ.x.sln` (Unity with .NET 2.0-3.5 assemblies), where `ZZZZ` is the major version number, eg. 4, 5, 2017, ...
	- Use configuration `Release_eglib`
	- Use platform `x86` or `x64`

- `dnSpy-Unity-mono-vZZZZ.x-V40.sln` (Unity with .NET 4.x assemblies), where `ZZZZ` is the major version number, eg. 2017, 2018, ...
	- Use configuration `Release`
	- Use platform `x86` or `x64`

# Commit hashes

A few versions have the same hash. `-mbe` = `MonoBleedingEdge` branch (.NET 4.x assemblies).

version | git hash
--------|---------
2019.2.0-mbe | f93c5973976e4bec5e769a31a73f00003f0a32dc
2019.2.1-mbe | ed4090cc21ffb91a45749be62f9a2fa08b69a0fc
