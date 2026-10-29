# Lab setup notes

All facts here come from my own screenshots (see `../screenshots/`). Dates are from the VM clocks and VirtualBox snapshot list (May 2026).

## Hypervisor and network
- Oracle VirtualBox, three VMs: `SIEM-Wazuh`, `LegacyVictimWin7`, `ModernWin10`.
- `LegacyVictimWin7` adapter 1: **Attached to: Internal Network**, **Name: intnet**, **Promiscuous mode: Allow All**, cable connected (screenshot 01; MAC address blurred).
- The adapter settings of the other two VMs are not in my screenshots, but all three hosts answer each other on 192.168.1.0/24, so they are on the same internal network.
- Internal network = isolated from the home network and the internet, which is what you want for attack simulation.

## Snapshots (LegacyVictimWin7)
| Snapshot | Taken |
|---|---|
| Windows 7 installerad klar | 2026-05-22 14:02 |
| Snapshot 1 | 2026-05-22 21:03 |
| Agent Grön-Nätverk funkar | 2026-05-23 15:48 |
| ping funkar -skjut mig inte | 2026-05-23 20:40 |
| Snapshot 2 | 2026-05-24 17:42 |

## SIEM-Wazuh (Ubuntu 24.04)
- IP 192.168.1.10 (the address the agent reports to; replies to ping with TTL 64).
- Wazuh installed 22 May 2026; log shows Filebeat (started 13:36:18) and Wazuh dashboard (installed 13:41:40, web app initialized 13:43:23), installation finished 13:43:39. Dashboard reached at `https://localhost` in Firefox on the VM.
- The installer summary printed the `admin` user and its password in clear text → **redacted** in screenshot 04 and never stored in this repo. (Recommended: change it after install.)
- Boot warning on the VM: `vmwgfx ... *ERROR* vmwgfx seems to be running on an unsupported hypervisor ... Please switch to a supported graphics device` (screenshot 03). It is a VirtualBox virtual graphics controller message and did not stop the SIEM from working.

## LegacyVictimWin7 (Windows 7 Professional SP1, 6.1.7601)
- Wazuh agent **v4.7.5**, installed in `C:\Program Files (x86)\ossec-agent`, agent name `Legacy-Win7-New`.
- `ossec.conf` client section (screenshot 09): server 192.168.1.10, port 1514, protocol tcp, config-profile `windows, windows7`, crypto_method aes, notify_time 10, time-reconnect 60, auto_restart yes.
- FIM (screenshot 10): default Windows directories plus
  `<directories check_all="yes" realtime="yes">C:\Users\LegacyUser\Desktop\HEMLIGT</directories>`.
- After connecting, the agent log shows: event-log analysis of Application, Security and System; syscollector; rootcheck; FIM scan frequency 43200 seconds; SCA "No policies defined"; ciscat and osquery modules disabled.

## ModernWin10 (Windows 10, Swedish UI)
- Running in the lab (screenshot 02, 21 May 2026). No Wazuh agent yet – the dashboard lists one agent only. **(planned)**
