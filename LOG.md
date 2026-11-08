# Setup log

No DUIDs, serials, local IPs, certificates or tokens in this file.

## 2026-11-08

- Phase 0 done. Browser preview runs on port 4795.
- Phase 1 done. Seven Home items set, Dock kept as the default, three icons added.
  `npm run check` and `npm run check:browser` pass (423 checks). Nothing committed.
  Unverified until the TV is reachable: the Paramount+ app ID and whether Live TV lands on the tuner.
- Phase 2 in progress.
  - Mac is Apple Silicon. Tizen Studio 6.1 is published for macos-64 (Intel) only, so it runs under
    Rosetta, which is present. Samsung's requirements page does not confirm Apple Silicon support.
  - Downloaded web-cli_Tizen_Studio_6.1_macos-64.bin from download.tizen.org.
    SHA-256 1630194c222776093785b274554461f896243dfa6b59581928047816fab75a80
  - Running the installer from the agent was blocked by the permission check, so the owner runs it.
  - Installer finished: SDB 4.2.25, Tizen CLI 2.5.25, Package Manager 0.5.61. sdb is x86_64, runs under Rosetta.
  - PATH edit to shell profiles was blocked by the permission check. Owner agreed to skip it; brief now says full paths.
  - Installed TV-SAMSUNG-Public-WebAppDevelopment and TV-SAMSUNG-Extension-Tools (no licence terms listed).
    These also pulled in WebIDE and Certificate-Manager. Emulator skipped.
    The TV tools replaced SDB 4.2.25 with 4.2.36. Tizen CLI still 2.5.25. Upstream has one report of
    4.2.36 failing on a 2019 Frame where 4.2.25 worked; watch for this in Phase 3.
  - Owner accepted the Samsung Certificate Extension licence; cert-add-on 2.0.75 installed.
  - Owner enabled Developer Mode. Phase 2 done.
- Phase 3 done (read-only).
  - SDB 4.2.36 connected on the first attempt, so the upstream 4.2.36 report does not apply here.
  - Tizen 6.0, armv7, model code 21_NIKEM2_QTV. Developer IP on the TV matches this Mac. TokenAuthSupport true.
  - Device info, capability, DUID and app list saved under .local/ (git-ignored, mode 600).
  - Confirmed on this TV: Apple TV, Disney+, Paramount+ (rJyOSqC6Up.CBSAllAccess), Netflix, Prime Video, org.tizen.tv-viewer.
  - Still unknown: whether launching tv-viewer lands on the tuner, and which HDMI port the Xbox uses.
- Phase 4 done.
  - Certificate profile cleanframe-tv created with Certificate Manager under Rosetta; owner did sign-in and passwords.
  - Author and distributor certificates issued by Samsung, Public privilege, TV ID checked against the TV. Both expire 2027-11-08.
  - Stored in ~/SamsungCertificate/cleanframe-tv; author.p12 backup kept outside the repo, checksum matches.
  - .local/signing.json written with a generated package ID, not the maintainer's. npm run package signs with the SDK's saved password.
  - Not installed on the TV yet.
- Phase 5 in progress.
  - Installed on the TV with npm run tv:install; the TV accepted the signature. Startup defaults unchanged.
  - Launched with npm run tv:launch. Waiting on the owner to confirm each of the seven items.
  - Owner confirmed the launcher displays on the TV. Item testing still to do.
  - Owner tested on the TV: all five streaming apps open, Live TV showed the tuner, Inputs lists ports, Xbox is on HDMI 4.
    Response and navigation smooth, icons and labels correct.
  - Still to confirm: selecting HDMI 4 switches the picture to the Xbox, and Live TV reaches the tuner when the TV is on HDMI 4.
  - Owner's test: from HDMI 4, Live TV showed the Xbox, so launching tv-viewer only resumes the last source.
  - Fix: Live TV now selects the source of type TV, then opens the viewer. Errors if no tuner is reported, never substitutes another input.
    425 browser checks and 14 device tests pass. Rebuilt and reinstalled; waiting on the owner to retest.
  - Owner retested: Inputs to HDMI 4, then Live TV shows the tuner. All seven items work. Phase 5 done.
  - Owner confirmed Samsung Home opens from the launcher (as Samsung's overlay).
  - TV Settings did nothing: com.samsung.tv.coba.setting starts without showing anything here. org.tizen.menu opened Settings when launched over SDB, so the button now uses it.
  - Owner confirmed the TV Settings button opens Settings.
  - Art Mode button did nothing: the feature is Frame-only and this TV reports FrameTVSupport false. Button removed from the launcher.
  - Owner asked for Ambient Mode in the Art Mode slot. com.samsung.tv.coba.ambient showed nothing; Ambient Mode opened
    over SDB after org.tizen.ambientapp was launched, so the button now uses it. Owner confirmed the button opens Ambient Mode.
  - Owner confirmed Home exits Ambient Mode.
- Phase 6 in progress. Owner changes the TV settings; no agent writes to startup settings.
  - Test 1: Autorun Last App on, Autorun Smart Hub off, launcher on screen at power-off. TV came back on Live TV, not the launcher.
  - Test 2: Autorun Last App on, Autorun Smart Hub on. Launcher resumes at power-on when it was last used, with Samsung's home bar over it for about 10 seconds. Paramount+ resumed the same way when it was last used. Phase 6 done with that limit. Brief updated.
- Stopped here by owner's choice. Phase 7 not started; docs/MY-TV.md and RECOVERY.md not written yet.
