# Versioning Policy — Iron District

All three repos in the Iron District project use the same tag format so releases are easy to correlate across projects.

## Tag Format

```
vMAJOR.MINOR-phaseN-gate
```

| Component | Meaning |
|-----------|---------|
| `MAJOR` | Breaking change to `.tdproj` schema or sensor CSV format |
| `MINOR` | New feature or phase gate completion |
| `phaseN` | Phase number (1–5) |
| `gate` | Indicates this tag is a formal phase gate release |

## Example Tags

| Tag | Meaning |
|-----|---------|
| `v0.1-phase1-gate` | Phase 1 gate passed; initial foundation complete |
| `v0.2-phase2-gate` | Phase 2 gate passed; first physical test complete |
| `v0.9-phase4-gate` | Phase 4 gate passed; full pipeline validated |
| `v1.0-final` | Final event complete; public release |

## Which Repos Get Tagged

All three repos should be tagged at each phase gate, even if only one repo had significant changes that phase. This keeps the release history aligned.

> **Helper note:** After tagging, go to GitHub → Releases → Draft a new release. Select the tag, write a 3-sentence summary of what was completed, and attach any artifacts (CSV, `.tdproj`, photos, video). This is the permanent record of the phase gate.
