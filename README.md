# Polymarket NVIDIA CEO Forecaster

Entry for make.fun `polymarket-nvidia-ceo-forecast-01`: forecast the Yes price
of every market of Polymarket's "Next CEO of NVIDIA?" event, minute by minute,
240 minutes ahead, as five quantiles (5%, 25%, 50%, 75%, 95%) per minute.

## Program

`main.py` (Python 3.13, standard library only, no network, deterministic) reads
every `<id>.in` case in `$MAKEFUN_INPUT_DIR` (else `/input`) and writes
`<id>.out` to `$MAKEFUN_OUTPUT_DIR` (else `/output`).

## Method

Per market, take the empirical distribution of 15-minute price changes over
the last 720 minutes of the case's history, read off its 5/25/50/75/95%
quantiles, and scale them to horizon `h` by `(h/15)**0.35` — a sub-square-root
exponent, because these prices are sticky and mean-reverting, so long-run
moves grow slower than a random walk's `sqrt(h)`. Blend 10% toward the house
baseline (normal, sigma 0.0025 per sqrt-minute) so the tails are not too
narrow at long horizons. No drift, no trend chasing: both hurt on the
practice backtest.

Backtest over the kit's practice cases (each case's 240 minutes scored
against the next case's prices): pinball loss **0.3183** vs the house
baseline's **0.3925** — about 19% lower. Practice is no guide to the final,
which is scored on 48 hours nobody had seen when entries closed.

## Run locally

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```
