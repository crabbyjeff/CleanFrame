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

## 2026-11-08 (later) — Phase 7 setup, code only

- Owner authorised setting up the Home-button remap in code, testing internally, and validating recovery, with no TV write yet.
- Adapted scripts/native-home/run.py: model guard -> QN75QN85DAFXZA, SDK path read from .local/signing.json (~/tizen-studio), and the compiled helper is staged only for query/apply/restore, not the read-only inspect.
- Blocked before any TV read: SDB will not connect (TV answers on 8001 and port 26101 accepts a raw TCP connect, but the developer daemon handshake fails). Local sdb kill/start-server did not clear it. Needs a TV power-cycle, per upstream's sleep/standby note. No baseline captured yet; Control.cs firmware guard not yet set; helper not built.
- SDB would not reconnect despite: TV power-cycle, Developer Mode off/on with restarts, and a full cold boot (unplug 60s). TV shows developerMode 1, developer IP set to this Mac, PowerState on, port 26101 open; curl/ping/raw-socket to the TV all succeed. Mac side verified clean (direct en0 route, no Tailscale interference, fresh sdb 4.2.36 server). sdb connect returns "failed to connect to remote target" with no device ever appearing. Phase 7 paused here; it needs a live SDB session for the read-only inspect, baseline and recovery proof. run.py adaptation is done but uncommitted.

## Later check

- TV network ports answered and Developer Mode still pointed at this Mac, but SDB connect failed, including after restarting the local SDB server. Launcher reopened through the TV's local REST API on port 8001.
- A raw SDB handshake to the TV succeeded, so the TV's developer daemon was healthy and the fault was on the Mac. Certificate Manager had been left running and was attached to the local SDB server. After quitting it and restarting the server, sdb connect works again and repeat reconnects hold. Exact cause not proven, but it matches upstream's report of Certificate Manager interfering with SDB. Close it after use.

## Replacement-remote feasibility tests

No TV settings changed by the agent, except the owner toggling Autorun Smart Hub off for one test (still off). Test scripts were temporary and are not in this repo.

- Launch request from the Mac over the TV's local REST API brought the launcher back over Paramount+ (0.5 s), Netflix (2.4 s), Prime Video (3.4 s) and Live TV (3.4 s), by the TV's own visible flag. Owner believes these looked right but was not specifically watching.
- REST will not open or report Netflix or Prime Video (404). The launcher-back request is the only one a remote would need.
- Samsung Home and TV Settings: the visible flag stays true underneath them, so it cannot detect them. With the owner watching, Samsung Home lasted about 20 s in total before the launcher showed, about 10 s after the request. Left alone with no request, it stayed up for at least a minute, so the request did close it, slowly. One run each. TV Settings stayed up after the request.
- Power-on with a watcher on the Mac: the launcher took over both times. Autorun Smart Hub on: about 25-40 s. Off: about 30-45 s. Samsung TV Plus plays first, at least with it off. One run each.
- SDB dropped again during testing with Certificate Manager not running; restarting the local server fixed it.

## To test

- Home button sending Back first, then the launch request, to close TV Settings. Needs the replacement remote or a key-sending test. Also check what Back does inside apps.


## Leaving apps with the current remote

- Moving the launcher to first on Samsung's row was not good enough for the owner: Home highlights the Home icon, so returning takes two extra clicks.
- REST reports running and visible for all five apps on these store IDs: Netflix 3201907018807, Prime Video 3201910019365, Disney+ 3201901017640, Apple TV 3201807016597, Paramount+ 3201710014981. The org.tizen IDs return 404. This corrects the earlier finding that Netflix and Prime Video could not be seen.
- Logged read-only while the owner used the remote. Netflix returned to the launcher on exit, opened from the launcher or from its button, and kept running. Prime Video, Disney+, Paramount+ and Apple TV went to Samsung TV Plus: visible emptied and the app stopped running, in all six logged exits (Prime Video and Disney+ twice each).
- Apple TV to Live TV through Samsung's row: visible emptied but the app kept running. One run. Going from the launcher to Live TV also changed nothing else in the log.
- Home inside an app changes nothing in REST, so it cannot be detected. Paramount+ has no Back exit; it needs its own exit button.
- Rule: when an app leaves the screen and stops running, open the launcher.

## Home Assistant helper

- Added at the owner's request through Chrome, using File editor: new cleanframe.yaml beside configuration.yaml, plus a homeassistant packages include at the end of configuration.yaml. The private copy is .local/home-assistant/cleanframe.yaml. Config check passed, Home Assistant restarted.
- Sensors from the Pi matched what the TV reported to this Mac. With Paramount+ on screen, a Home Assistant launch brought the launcher back: request about 0.8 s, launcher reported on screen about 1.8 s. So the TV accepts launches from the Pi, not only from the developer IP. One run.
- Asking rest_command to return the response fails with a JSON decoding error even though the TV accepted the request. The automations do not ask for the response.
- Owner tested with the remote, one run each. Prime Video exit returned to the launcher after about 5 s of Samsung TV Plus. Power-on with the launcher last used showed only the launcher, nearly instantly; Autorun Last App probably did that, not the helper. Going to Live TV from Prime Video and from Disney+ stayed on Live TV. Turning off inside Disney+ and back on resumed Disney+, then the launcher opened after about 15 s.
- Owner tested the rest: exits from the other apps returned to the launcher in 0-3 s. Power-on after Live TV was the slowest case, about 45 s before the launcher began loading and about 10 s to load. Owner does not wake the TV from the Xbox, so that case does not matter. Home inside an app is unchanged.
- Phase 7 dropped by the owner; the uncommitted edit to scripts/native-home/run.py was reverted. docs/MY-TV.md and RECOVERY.md are still not written.

## Prime Video button alias test

- Tizen's metadata reference lists `http://tizen.org/metadata/aliasappid`, written for native manifests. Added it to the launcher's config.xml with the value `org.tizen.primevideo` and installed normally; the TV accepted it.
- Owner pressed the Prime Video button with the entry active: Prime Video opened, not the launcher. The alias does not redirect the button. Tile behaviour was not checked, so it is unknown whether the TV ignored the entry or the button bypasses it. One run.
- Entry removed and the launcher reinstalled.
