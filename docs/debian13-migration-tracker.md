# Debian 13 (Trixie) Migration Tracker

> **Migration Date:** 2026-02-06
> **Target:** Debian 13 only (dropping Debian 11 support)
> **Principle:** Same features, same architecture, updated packages and configs

## Status Legend
- BLOCKED = Cannot install/run at all on Debian 13
- BROKEN = Installs but config/behavior changed
- WARNING = Works but deprecated, will break eventually
- FIXED = Migration complete
- OK = No changes needed

## Component Tracker

| # | Component | Old Status | Fix Applied | New Status | Notes |
|---|-----------|-----------|-------------|------------|-------|
| 1 | Python 2 (`ws-stunnel` proxy) | BLOCKED - removed from repos | Port to Python 3 syntax | FIXED | `print` statements, `thread` module, bytes/string handling |
| 2 | Python 2 (`apt install python2`) | BLOCKED - removed from repos | Remove from package list, use `python3` | FIXED | |
| 3 | Dropbear config | BROKEN - `DROPBEAR_PORT`/`DROPBEAR_EXTRA_ARGS` no longer used | Rewrite to `DROPBEAR_OPTIONS` format | FIXED | Ports 109, 143 preserved |
| 4 | Stunnel5 (compiled from source) | BROKEN - build fails on Debian 13 | Switch to `stunnel4` repo package | FIXED | Config content identical, paths changed |
| 5 | vnstat (compiled from source 2.6) | BROKEN - build deps changed | Switch to `vnstat` repo package | FIXED | |
| 6 | `gnupg1` package | BLOCKED - removed from repos | Remove, `gnupg` (v2) is default | FIXED | |
| 7 | `dirmngr` package | BLOCKED - merged into `gnupg` | Remove from install list | FIXED | |
| 8 | `ntpdate` package | BLOCKED - removed from repos | Remove, use `chrony` only | FIXED | Already partially using chrony |
| 9 | Nginx `http2` directive | WARNING - deprecated in Nginx 1.25+ | Change to `http2 on;` directive | FIXED | |
| 10 | `ws-stunnel` systemd service | BROKEN - references `/usr/bin/python2` | Update to `/usr/bin/python3` | FIXED | |
| 11 | `iptables-persistent` | WARNING - nftables is default | Keep using iptables-nft compat layer | OK | Debian 13 still provides compat |
| 12 | `badvpn-udpgw` binary | RISK - pre-compiled binary | Test on Debian 13, recompile if needed | OK | Static binary, likely works |
| 13 | `lolcat` (ruby gem) | RISK - gem install may fail | Switch to `apt install lolcat` | FIXED | |
| 14 | `ruby` package | WARNING - only needed for lolcat gem | Remove (lolcat from apt now) | FIXED | |
| 15 | Build toolchain (`build-essential`, `gcc`, etc.) | N/A - no longer needed | Remove from install list | FIXED | Was only for stunnel5/vnstat compile |
| 16 | `libreadline-dev`, `zlib1g-dev`, `libssl-dev`, `libsqlite3-dev` | N/A - no longer needed | Remove from install list | FIXED | Build deps for stunnel5/vnstat |
| 17 | `screenfetch` | WARNING - may not be in repos | Already have `neofetch`, remove `screenfetch` | FIXED | |
| 18 | `netcat` | WARNING - ambiguous package name | Use `netcat-openbsd` explicitly | FIXED | |
| 19 | README.md | N/A | Update supported OS list | FIXED | |

## Files Modified

| File | Changes |
|------|---------|
| `setup.sh` | Package list cleanup, remove compile blocks |
| `ssh-vpn.sh` | Dropbear config, Stunnel4 migration, ws-stunnel service, remove stunnel5 compile |
| `ins-xray.sh` | Nginx http2 directive fix |
| `jembot.sh` | Remove vnstat compile-from-source |
| `ws-stunnel` | Full Python 2 to Python 3 port |
| `README.md` | Update supported OS badges |

## Testing Checklist

After deploying on Debian 13, verify each service:

- [ ] SSH on port 22, 2253, 40000
- [ ] Dropbear on ports 109, 143
- [ ] Stunnel4 on ports 447, 777, 442
- [ ] SSH Websocket (ws-stunnel) on port 700
- [ ] Nginx on port 80, 81, 443
- [ ] XRAY service running (all inbounds)
- [ ] Badvpn-udpgw on 7100, 7200, 7300
- [ ] Fail2Ban running
- [ ] vnstat monitoring
- [ ] Cron jobs (xp, clearlog, reboot, backup)
- [ ] SSL certificate generation (acme.sh)
- [ ] iptables rules loaded
- [ ] BBR congestion control enabled
