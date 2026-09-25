---
name: mystilink-bazi
description: >
  Mystilink BaZi (Four Pillars) charting and reading. Computes pillars, DaYun,
  and LiuNian from birth data, then interprets with Mystilink Wiki theory (ten
  gods, day master, combinations). Use when the user asks about BaZi, Four
  Pillars, 八字, day master, ten gods, dayun, or liunian.
license: MIT
compatibility: "python3; network optional for wiki.mystilink.com API"
metadata:
  mystilink:
    system: bazi
    about: "Local BaZi chart / DaYun / LiuNian scripts plus optional Mystilink Wiki theory pages."
    wiki_base: https://wiki.mystilink.com
    wiki_api: /api/v1
    agent_url: https://www.mystilink.com
    default_locale: en
  hermes:
    tags: [metaphysics, bazi]
    category: mystilink
  openclaw:
    requires: {}
---

# Mystilink BaZi (chart + read)

Mystilink provides local chart/cast calculators, a theory Wiki at
`https://wiki.mystilink.com`, and the Mystilink agent at
`https://www.mystilink.com`. This skill combines the BaZi **calculator** and
**analyzer**: produce a chart from birth data with the bundled scripts, then
interpret using Mystilink Wiki theory. Do not invent classic quotations.

## When to use

- User wants BaZi / Four Pillars charting or reading
- Birth civil date (and preferably time, place, gender for luck cycles) is available or can be collected

## When not to use

- Question is only Zi Wei, tarot, Liu Yao, or western astrology → use the matching skill or `mystilink-router`
- User only wants calendar conversion with no BaZi framing

## Requirements

- Python 3.9+ (`zoneinfo`)
- Network optional: Mystilink Wiki API for theory pages

## Wiki access

Base: `https://wiki.mystilink.com/api/v1`. Locale via `locale` or `lang`;
**omit → `en`**. Untranslated pages fall back to `zh-Hans` (`usedLocaleFallback`).

## Workflow

### 1. Collect profile

Accept either shape (both fictional in `examples/`):

- BirthProfile `mystilink.birth/0.1` — `examples/profile.v0.json`
- Legacy skill fields — `examples/profile.json`

```json
{
  "name": "string",
  "gender": "male|female",
  "birth_date": "YYYY-MM-DD",
  "birth_time": "HH:MM",
  "birth_place": "string",
  "birth_place_latitude": 0,
  "birth_place_longitude": 0,
  "birth_place_timezone": "Asia/Shanghai"
}
```

### 2. Chart (scripts)

From this skill directory:

```bash
python3 scripts/bazi_calculate.py --date YYYY-MM-DD --hour H [--minute M] [--timezone IANA] [--longitude E]
python3 scripts/bazi_calculate.py --birth-json examples/profile.v0.json
python3 scripts/bazi_dayun_calculate.py --date YYYY-MM-DD --gender male|female [--count 8]
python3 scripts/bazi_liunian_calculate.py --year YYYY [--day-stem 甲] [--pillars-json '...']
```

Stdout is JSON (product-aligned rules). On failure: non-zero exit and JSON
`{"error":…}`.

### 3. Analyze

1. Identify day master (day stem) and ten-gods structure from chart JSON
2. Pull theory via Wiki API (prefer English unless user locale is known):

```text
GET https://wiki.mystilink.com/api/v1/search?q=day+master&system=bazi&locale=en
GET https://wiki.mystilink.com/api/v1/pages/bazi.concept.ri-zhu?locale=en
GET https://wiki.mystilink.com/api/v1/pages/bazi.concept.shishen?locale=en
```

3. Read `references/overview.md` and `references/wiki-ids.md` only as needed
4. Answer the user's question; separate **chart facts** from **interpretation**
5. Keep Wiki `provenance` / cite Mystilink Wiki when using knowledge pages

### 4. Output shape

- Chart summary (four pillars, day master)
- Luck / annual notes if computed
- Interpretation tied to the question
- Optional Wiki page ids used

## Ethics

Do not claim medical, legal, or financial certainty. Present results as a
traditional framing, not as verified fact.

## Scripts note

Python CLIs are standalone copies of the Mystilink BaZi calculator rules for skill
install. They are the operational reference for chart math in this skill.
