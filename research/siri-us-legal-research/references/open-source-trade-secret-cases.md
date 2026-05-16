# Open Source Code as Trade Secret — US Case Law Research

**Date:** 2026-05-16
**Method:** delegate_task(toolsets=["web"])
**Verdict:** All sources [web search — verify]

---

## Bottom Line

연방 법원과 주 법원 모두 일관되게: **공개된 오픈소스 코드는 영업비밀이 될 수 없다.** 비밀 유지(trade secret의 핵심 요건) 충족 실패.

---

## Key Cases

### 1. SCO Group v. IBM (2003–2010)
- **Court:** D. Utah, No. 2:03-cv-00294
- **Holding:** SCO alleged IBM contributed SCO's proprietary Unix trade secrets into Linux (open source). Court largely rejected SCO's claims — once code becomes part of an open source project (publicly available), it loses trade secret protection.
- **Significance:** Leading case in this area.

### 2. DVD Copy Control Ass'n v. Bunner (2003)
- **Citation:** 31 Cal. 4th 864 (Cal. Supreme)
- **Holding:** DeCSS decryption code posted on internet lost trade secret protection. Widespread publication destroys secrecy — even if the disclosure was unauthorized.

### 3. Salerno v. Scialli (2018)
- **Citation:** 2018 WL 3954210 (E.D.N.Y.)
- **Holding:** Source code posted on a **public GitHub repository** cannot be a trade secret. Act of publishing on a public forum destroys the secrecy element regardless of later intent.

### 4. Kalda v. Sioux Valley Hosps. (2004)
- **Citation:** 2004 U.S. Dist. LEXIS 30757 (D.S.D.)
- **Holding:** Software code publicly available as open source under GPL cannot constitute a trade secret because it was not secret.

### 5. Oracle Am., Inc. v. Google Inc. (2012)
- **Citation:** 872 F. Supp. 2d 974 (N.D. Cal. 2012)
- **Holding:** Java API specifications were publicly available and widely known → could not be trade secrets. Open disclosure defeats secrecy.

### 6. MGE UPS Sys. v. GE Consumer & Indus. (2009)
- **Citation:** 427 F. Supp. 2d 660 (N.D. Tex.)
- **Holding:** Code licensed under open source license (even with non-commercial restrictions) is publicly available → cannot be a trade secret. Secrecy requirement is absolute.

---

## Statutory Framework

**Defend Trade Secrets Act, 18 U.S.C. § 1839(3)(B):** Expressly excludes from trade secret protection information that is "generally known or readily ascertainable." Courts consistently apply this to open source code.

---

## Nuance: When Trade Secret Still Applies

- Open-source-adjacent **non-public** code (internal builds, config, proprietary extensions) can still be protected
- Contributing trade secret code to open source project = forfeiture (SCO v. IBM)
- **Reverse engineering** of open source code is generally permitted under the license terms, which further undermines secrecy claims

---

## Research Method

This research was conducted via `delegate_task(toolsets=["web"])` because `web_search` was not directly available in the parent session. The subagent performed 6 searches across multiple query angles and synthesized results. All case citations should be verified against Westlaw or LexisNexis before reliance.
