# Module Override Priority

1. `/vpodrepo/20XX-labs/XXXX/Startup/{module}.py` (highest)
2. `/vpodrepo/20XX-labs/XXXX/{module}.py`
3. `/home/holuser/{labtype}/Startup/{module}.py` (external team override repo)
4. `/home/holuser/hol/{labtype}/Startup/{module}.py` (in-repo labtype override)
5. `/home/holuser/hol/Startup/{module}.py` (lowest)

## Supported Lab Types

Use ONLY one of the following lab types:

| Type | Description |
| ------ | ------------- |
| HOL | Hands-on Labs |
| Discovery | Discovery Labs |
| VXP | VCF Experience Program |
| ATE | Advanced Technical Enablement (Livefire) |
| EDU | Education/Training |

### Startup Sequences

All lab types use the same comprehensive startup sequence (skipping modules that don't apply):

**HOL / Discovery / VXP / ATE / EDU**:

```bash
prelim → ESXi → VCF → VVF → vSphere → pings → services → Kubernetes → urls → VCFfinal → final → odyssey
```

> **Note:** Individual modules detect if their components are configured. If not present in `config.ini`, the module runs but skips unconfigured checks.

---

## Setting Up Overrides

### prelim.py Override

> [!NOTE]
The use of prelim.py here is just an example, but can be any of the .py files found in the Startup folder. If you consider adding any custom code, please do review the CUSTOM section of the core team prelim.py as it contains several examples in commented code

Copy `Startup/prelim.py` to your repository in the `Startup` folder:

Locate the CUSTOM section of the script and insert your code there:

Example:

```bash
    ##=========================================================================
    ## CUSTOM - Insert your code here using the file in your vPod_repo
    ##=========================================================================
    
    # Example: Add custom final checks here
    
    ##=========================================================================
    ## End CUSTOM section
    ##=========================================================================
```

This can be applied to any of the files found in the Startup folder: https://github.com/Broadcom/HOLFY27-MGR-HOLUSER/tree/main/Startup

### Testing Overrides

Run standalone before committing:

```bash
python3 /vpodrepo/2027-labs/2705/Startup/prelim.py --standalone --dry-run
```

---

## Adding Custom DNS Entries

### Define DNS Records in config.ini

Add records under the `[VPOD]` section using this format:

```ini
[VPOD]
new-dns-records = site-a.vcf.lab,gitlab,A,10.1.10.211
    site-a.vcf.lab,harbor,A,10.1.10.212
    site-a.vcf.lab,registry,CNAME,gitlab.site-a.vcf.lab
```

Multiple records can be separated by newlines (with indentation)

### Some Example Record Types

| Type | Example Value | Result |
| ------ | --------------- | -------- |
| A | 10.1.10.211 | Forward lookup |
| AAAA | 2001:db8::1 | IPv6 forward lookup |
| CNAME | target.domain | Alias |
| TXT | "v=spf1 ..." | Text record |

### Verify Records

After lab starts:

```bash
nslookup gitlab.site-a.vcf.lab
dig git.site-a.vcf.lab
```

---
