# Audit and Design Notes

## Current assessment

The repository contains the raw eNSP topology, device-state files, firewall/router configurations, endpoint inventories, verification evidence, and presentation files.

## BackOffice IP decision

The following repeated address is intentional in the project documentation:

```text
R-BackOffice = 192.168.20.10
PC6          = 192.168.20.10
```

`R-BackOffice` was retained as the active BackOffice test device because it successfully allows the FTP test from the BackOffice segment. The PC implementation was tested but rejected the FTP test, so PC6 was disconnected/removed and is not included as an active device in the saved eNSP version.

Accordingly, the duplicate address is not meant to be used by two active devices at the same time. It documents the original PC inventory and the final practical decision used in the eNSP project.

If PC6 is restored in a future version, it must receive a unique address, for example:

```text
R-BackOffice = 192.168.20.10
PC6          = 192.168.20.20
```

The gateway, ARP table, firewall sessions, and FTP behavior should then be tested again.

## Policy count

The firewall file currently contains 16 active security-policy rules, including `default_deny`. Documentation should use this count unless an additional explicit rule is added to the CLI.

## Security hygiene

This is an isolated training lab. Replace all demonstration credentials before using any imported configuration outside eNSP, and never commit production passwords, private keys, tokens, or sensitive network information.
