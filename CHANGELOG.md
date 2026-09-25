# Changelog

Version is tracked in `SKILL.md` under `metadata.mystilink.version` and in this file.

## 0.1.0

- Agent Skill for BaZi (Four Pillars) charting and reading
- `scripts/bazi_calculate.py`, `scripts/bazi_dayun_calculate.py`, and `scripts/bazi_liunian_calculate.py` emit JSON for chart, DaYun, and LiuNian
- `references/overview.md` and `references/wiki-ids.md` map chart fields to Mystilink Wiki theory pages (`wiki.mystilink.com`)
- `examples/profile.json` and `examples/profile.v0.json` give runnable chart input
- Runtime: `python3`; the Wiki lookup is optional and only used when network access is available
- Install by copying this directory into a host skills path that reads `SKILL.md`
