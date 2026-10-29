# Troubleshooting notes

## 1. Agent would not connect (24 May 2026)

**Symptom:** dashboard showed `Legacy-Win7-New` as *Disconnected*, agents coverage 0.00 % (screenshot 07).

**Agent log (`ossec.log`, screenshot 06):**
```
16:49:16 ERROR (1216): Unable to connect to '[192.168.1.10]:1514/tcp': 'A connection attempt failed because the connected party did not ...'
16:49:47 INFO: Requesting a key from server: 192.168.1.10
16:50:08 ERROR (1208): Unable to connect to enrollment service at '[192.168.1.10]:1515'
16:50:18 WARNING (4101): Waiting for server reply (not started). Tried: '192.168.1.10'. Ensure that the manager version is 'v4.7.5' or hig[her]
16:50:18 WARNING: Unable to connect to any server.
...
16:51:31 INFO (4102): Connected to the server ([192.168.1.10]:1514/tcp).
```
(The log lines are cut off at the window edge in the screenshot; `...` marks that.)

**Steps taken (screenshot 05):**
1. `ping 192.168.1.10` → replies, TTL 64, 0 % loss (screenshot 12; that screenshot has no date). Network OK, so the problem is the Wazuh service/ports.
2. Manual key request with `agent-auth` against 192.168.1.10 using agent name `Legacy-Win7-New` → "Valid key received" (14:45:11).
3. `net start Wazuh` (already running) → `net stop Wazuh` → `net start Wazuh` to load the key.
4. Watched `ossec.log` until `Connected to the server` (16:51:31), then verified *Active (1)*, 100 % coverage in the dashboard (screenshot 08).

**Port reference:** 1514/tcp = agent ↔ manager event channel, 1515/tcp = enrolment (authd).

**Root cause:** not proven by my screenshots. Error 1216 ("connection refused"-type) with a working ping points to the manager side not accepting connections on 1514/1515 at that time (service not up yet or firewall). Things I would check next time: `systemctl status wazuh-manager`, `ss -tlnp | grep -E '1514|1515'`, the Ubuntu firewall, and that the manager version is ≥ the agent version (4.7.5).

## 2. Ping-sweep syntax error (26 May 2026)
`FIND: Parameter format not correct` – the `find` search string must be in double quotes: `find "Reply"`. Fixed by correcting the quoting (screenshot 11).

## 3. vmwgfx warning on Ubuntu boot
`vmwgfx seems to be running on an unsupported hypervisor` (screenshot 03). Graphics-controller warning in VirtualBox; the VM booted and ran Wazuh. Possible fix: change the VM graphics controller in VirtualBox settings (not tested).
