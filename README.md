![CI](https://github.com/AntonioBernalNavarro/Coursera-MLOPs-Foundations-Lab-2-poker-simulator/actions/workflows/cicd.yml/badge.svg)
![Docker Image CI](https://github.com/AntonioBernalNavarro/Coursera-MLOPs-Foundations-Lab-2-poker-simulator/actions/workflows/docker-image.yml/badge.svg)
![Python 3.10](https://img.shields.io/badge/python-3.10-blue)
![Coverage 95%](https://img.shields.io/badge/coverage-95%25-brightgreen)
![Pylint 10.00/10](https://img.shields.io/badge/pylint-10.00%2F10-brightgreen)

# Poker Simulator — MLOps Foundations Lab 2

An improved version of the [Coursera MLOps Foundations Lab 2](https://github.com/nogibjj/Coursera-MLOPs-Foundations-Lab-2-poker-simulator)
poker simulator. The focus is on **software quality**: a professional
project layout, exhaustive test coverage, and correction of the evaluation
bugs that shipped with the original implementation.

## What's different from the original

| Area | Original | This fork |
|---|---|---|
| Layout | Flat (`poker.py`, `test_main.py`, `mylib/`) | `src/poker/` + `tests/` + `archive/` |
| Evaluation logic | Mixed with CLI and I/O | Pure function in `src/poker/hand_evaluator.py` |
| Tests | 1 trivial test (`assert add(1,2) == 3`) | 33 tests, 95% coverage |
| Royal Flush detection | Never matched | Correct (`ranks_sorted == [10, 11, 12, 13, 14]`) |
| Straight detection | Any 5 distinct ranks | Consecutive ranks, includes the wheel (A-2-3-4-5) |
| Two Pair / Full House | Misclassified | Correct |
| Devcontainer | `universal:2-focal` (Python 3.12) | `python:1-3.10-bullseye` (matches CI) |
| Pylint | — | 10.00/10 |

## Project Structure

```
.
├── src/poker/
│   ├── __init__.py
│   ├── game.py             # CLI (click) + helpers
│   └── hand_evaluator.py   # pure evaluation logic (100% coverage)
├── tests/
│   ├── test_cli.py              # 13 CLI tests via click.testing.CliRunner
│   ├── test_hand_evaluator.py   # 18 evaluator tests (10 ranks + 8 regressions)
│   └── test_smoke.py            # 2 smoke tests
├── archive/                # original template demo (not used by the project)
├── .devcontainer/          # Python 3.10 devcontainer (aligned with CI)
├── .github/workflows/      # cicd.yml + docker-image.yml
├── conftest.py             # pytest path setup for src/
├── .pylintrc               # pylint init-hook for src/
├── Makefile                # install, test, lint, format, deploy
└── requirements.txt
```

## Usage

```bash
make install    # pip install -r requirements.txt
make test       # 33 tests, 95% coverage
make lint       # pylint 10.00/10
make format     # black
make deploy     # (placeholder)
```

### CLI

```bash
python -m src.poker.game info                   # list all hands with examples
python -m src.poker.game info --probability     # with probability table
python -m src.poker.game deal --hands 3         # deal 3 random hands
python -m src.poker.game play --name Alice --bet 5
python -m src.poker.game interactive --rounds 5 --money 100
```

## Bugs fixed

The original `evaluate_poker_hand` had five classification bugs that
this fork corrects, each covered by a regression test in
`tests/test_hand_evaluator.py`:

1. **Royal Flush was never detected** — `sorted()` is lexicographic, so
   `["10H","JH","QH","KH","AH"]` was ordered as `["10H","AH","JH","KH","QH"]`
   and the endpoint check compared against the wrong card.
2. **Any flush with 5 distinct ranks was "Straight Flush"** — no
   consecutiveness check.
3. **Any hand with 5 distinct ranks was "Straight"** — same root cause.
4. **Two Pair → Three of a Kind** — the original used `hand[0]` as the
   reference card instead of counting rank frequencies.
5. **Full House → Four of a Kind** — same root cause.

Additionally, the `build_poker_hand()["High Card"]` example contained a
pair of Jacks and evaluated to "One Pair"; it has been corrected.

## Devcontainer

The devcontainer uses `mcr.microsoft.com/devcontainers/python:1-3.10-bullseye`
so that the local environment, the CI pipeline, and the pinned
`requirements.txt` (2022-era dependencies: pandas 1.5, tensorflow 2.10,
torch 1.12) all share the same Python version. The `yarn.list` APT
source shipped in the base image is removed before `apt-get update`
because its GPG key expired and caused `apt-get update` to fail with
exit code 100.

## Acknowledgments

Original lab and instructions: [Coursera-MLOPs-Foundations-Lab-2-poker-simulator](https://github.com/nogibjj/Coursera-MLOPs-Foundations-Lab-2-poker-simulator).

## License

See `LICENSE`.