# reliastra-action-test

Cross-repo contract tests for [`ReliaAstra/reliastra-action`](https://github.com/ReliaAstra/reliastra-action).

This repository exists to answer one question that unit tests cannot: **does the published
Action actually work when a different repository consumes `ReliaAstra/reliastra-action@v1`?**

Everything here runs over the real network against the real API. Nothing is mocked. If this
breaks, it will break here before it breaks in somebody else's repository.

## What it asserts

| # | Case | Expected |
|---|---|---|
| 1 | Public feed, quiet vendor (`stripe`) | exit `0` |
| 2 | Invalid API token | exit `3` |
| 3 | Unreachable host | exit `6` |
| 4 | Missing `dependency` input | exit `1` |
| 5 | Unknown `fail-on` value | exit `1`, not silently ignored |
| 6 | `output-file` produces a well-formed record | all honesty invariants hold |
| 7 | Open incident trips the gate | exit `5`, step fails |
| 8 | `action.yml` and `dist/` resolve from the `v1` tag | present and non-trivial |

## Honesty invariants

Case 6 is the one worth reading. It asserts that the machine-readable record always contains:

- `observationPoints == 1` — Reliastra runs a single observation point, and no surface may
  ever present it as corroboration
- a valid `verdict` from the documented set
- an `attributionCaveat` that says attribution is a weighted score, not proof

If any of those change, this repository fails. That is the point.

## Running it

```bash
gh workflow run contract.yml --repo ReliaAstra/reliastra-action-test
gh run watch --repo ReliaAstra/reliastra-action-test
```

Or just push to `main`.

## Not a product

This repository is infrastructure. It is not part of Reliastra and is not published anywhere.
Its only job is to fail loudly when the distribution surface is broken.