# ClickFix Guard

A browser-based defensive tool that checks a command **before** you paste it into Run, PowerShell or Terminal. It is built against the **ClickFix** malware technique, one of the most widely used malware delivery methods of 2026.

Everything runs locally in your browser (plain HTML, CSS and JavaScript). No server, no data upload, nothing is executed.

**Live demo:** `https://<your-username>.github.io/clickfix-guard/`

## 1. Case study: ClickFix (2026)

ClickFix is a social-engineering attack where a fake CAPTCHA or "fix this error" box tricks the victim into running a malicious command themselves. Because the user starts the command, many security tools do not block it.

Recent real-world examples:

| When | What happened |
|---|---|
| Early 2026 (May reports) | Attackers exploited a Ghost CMS flaw (CVE-2026-26980) to hijack 700+ legitimate websites, including education sites, and show ClickFix lures on them. |
| July 2026 | A ClickFix lure was injected on an Artlist subdomain after infostealer-stolen WordPress credentials were used, leading to a remote access trojan. |
| Sept 2026 | Fake ads on Reddit sent users to a page imitating HBO Max with a ClickFix lure. |
| 2026 | Nation-state groups such as Russia-linked Sandworm and others adopted the technique. |

Sources to read and cite in your report:
- Malwarebytes: 700+ education and tech websites hijacked in a ClickFix campaign (May 2026)
- TechCrunch: ClickFix attacks tricking Mac and Windows users (14 Sept 2026)
- BleepingComputer: ClickFix has never been bigger
- Wikipedia: ClickFix (overview and references)

## 2. How the attack works

1. **Lure:** user lands on a hacked site, malicious ad or SEO-poisoned result.
2. **Fake challenge:** page shows a fake CAPTCHA, fake browser error or fake update.
3. **Clipboard hijack:** JavaScript silently copies a malicious command to the clipboard.
4. **Self-execution:** the user is told to press `Win+R` (or open Terminal), paste, and press Enter.
5. **Payload:** the command downloads an infostealer or remote access trojan (RAT), often through PowerShell, `mshta`, or `curl | bash`.

## 3. Vulnerabilities and loopholes

| Loophole | Why it exists |
|---|---|
| Human trust | People are used to CAPTCHAs and "verify" steps; the attack needs no software bug. |
| Unrestricted clipboard writes | Browsers let a page copy text to the clipboard after a click, with no clear warning. |
| Powerful built-in tools | PowerShell, `mshta`, `curl`, `certutil` are legitimate and allowed by default. |
| User-initiated execution | Antivirus and email filters often treat a command the user typed as trusted. |
| Compromised legitimate sites | Real domains (with good reputation) host the lure, so URL blocklists fail. |
| Obfuscation | Base64 and hidden windows hide the real action from the victim. |
| Cheap crime kits | Ready-made ClickFix kits are sold, so low-skill attackers can run campaigns. |

## 4. Types of attack / variants

- Fake CAPTCHA (Windows): `Win+R` + PowerShell paste
- Fake macOS Terminal prompts: `curl | bash`, `osascript`
- Fake browser/driver error pages ("CrashFix" style)
- Fake update pop-ups
- Fake booking / delivery / support lures (hospitality, e-commerce)

## 5. How ClickFix Guard defends

| Attack behaviour | Tool response |
|---|---|
| Encoded PowerShell | Detects `-enc`, **decodes the base64 and shows the hidden command** |
| `iex`, `irm`, `iwr`, `DownloadString` | Flags "downloads and runs remote code" |
| `mshta`, `finger.exe`, `msiexec`/`rundll32` + URL | Flags known abused binaries |
| `curl ... \| bash`, `xattr -c`, `osascript` | Flags macOS/Linux variants |
| Fake "I am not a robot" comments, long blank padding | Flags disguise tricks |
| Short links / raw IPs | Flags hidden destinations |
| Any URL | Extracts and **defangs** it (`hxxp://evil[.]com`) so it can't be clicked by mistake |

It then gives a 0–100 risk score and a plain-language verdict: *No red flags*, *Suspicious*, or *Do not run this*.

### Limits (be honest in your viva)
It is a rule-based detector. Attackers can change wording, so a low score is **not** proof of safety. Future work: a browser extension that warns when a page writes to the clipboard, and a machine-learning classifier.

## 6. Run locally

Just open `index.html` in any browser.

## 7. Publish on GitHub

1. Create a new public repo named `clickfix-guard` on github.com.
2. Upload `index.html` and `README.md` (Add file → Upload files → Commit).
3. Repo **Settings → Pages → Branch: main / root → Save**.
4. After a minute your site is live at `https://<your-username>.github.io/clickfix-guard/`.
5. Paste the repo link (and the Pages link) into ERP Moodle.

## 8. Disclaimer

Educational project. Test samples in the tool are harmless and non-functional (`.example` domains).
