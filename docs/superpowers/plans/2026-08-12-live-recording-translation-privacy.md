# Live Recording Translation Privacy Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add complete Chinese and English privacy disclosures for Starfire AI's live recording translation feature.

**Architecture:** Update the single static `index.html` policy page in place. Extend existing information, permission, storage/deletion, and data-transfer sections without changing the page structure or the App's privacy-consent flow.

**Tech Stack:** Static HTML5, Python standard-library HTML parser, GitHub Pages.

## Global Constraints

- Camera and microphone are accessed only after the user starts live recording translation.
- Speech recognition and translation are processed on device and are not actively uploaded to the operator's server.
- Completing the workflow saves content under the existing local video-project rules; cancelling removes App-managed temporary files.
- Do not change Flutter/iOS behavior, permissions, or permission timing.
- Keep Chinese and English disclosures semantically consistent.

---

### Task 1: Add and verify the disclosure

**Files:**
- Modify: `index.html`
- Test: shell assertions and Python standard-library HTML parsing

**Interfaces:**
- Consumes: existing Chinese and English policy sections in `index.html`
- Produces: a valid static privacy-policy page containing equivalent live recording translation disclosures in both languages

- [ ] **Step 1: Run content assertions before the change**

Run:

```bash
rg -n "边录边译|live recording translation" index.html
```

Expected: no complete privacy disclosure for the feature.

- [ ] **Step 2: Update the Chinese policy**

Extend the video-data row, Camera and Microphone permission descriptions, Storage and Deletion list, and Data Transfer paragraph. State that the feature collects only the current session's camera/microphone input after user action, processes recognition and translation locally, saves completed projects locally, and deletes App-managed temporary files on cancellation.

- [ ] **Step 3: Update the English policy**

Add semantically equivalent language to the corresponding English information, permissions, storage/deletion, and data-transfer sections.

- [ ] **Step 4: Validate content and HTML structure**

Run:

```bash
rg -n "边录边译|live recording translation|临时文件|temporary files|设备本地|on device" index.html
python3 - <<'PY'
from html.parser import HTMLParser
from pathlib import Path

class StrictEnoughParser(HTMLParser):
    pass

source = Path("index.html").read_text(encoding="utf-8")
parser = StrictEnoughParser()
parser.feed(source)
assert source.count('id="zh"') == 1
assert source.count('id="en"') == 1
assert "边录边译" in source
assert "live recording translation" in source.lower()
assert "临时文件" in source
assert "temporary files" in source.lower()
print("privacy policy checks passed")
PY
```

Expected: all assertions pass and the parser reports no exception.

- [ ] **Step 5: Commit and publish**

```bash
git add index.html docs/superpowers/plans/2026-08-12-live-recording-translation-privacy.md
git commit -m "docs: disclose live recording translation privacy"
git push origin main
```
