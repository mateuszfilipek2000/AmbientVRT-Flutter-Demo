# AmbientVRT Flutter demo

A tiny Flutter app whose `@Preview` widgets are guarded by **AmbientVRT**, a
visual-regression testing tool. Every pull request re-renders the previews in a
pinned container, diffs them against committed baselines, and **fails the check
on any unaccepted visual change** — attaching an HTML report with a
baseline / candidate / diff triptych for each changed snapshot.

> The AmbientVRT package itself is not open source yet. This repo is just a
> consumer demo: it depends on the package as a private git dependency, and CI
> reaches it with a read-only token.

## See it in action

Open the **Pull requests** and **Actions** tabs:

- On `main`, the **Visual gate** check is green — the re-captured previews match
  the committed baselines under [`.ambient/baselines/`](.ambient/baselines/).
- The demo PR tweaks a widget. The gate goes **red**, and the run's
  **`ambient-report`** artifact contains `report.html` plus the
  `baseline` / `candidate` / `diff` PNGs showing exactly what changed.

Download the artifact from the failing run and open `report.html` to eyeball the
diff.

## How it works

| Piece | Role |
| --- | --- |
| `lib/src/previews.dart` | The `@Preview`-annotated widgets that get captured. |
| `.ambient/baselines/` | The blessed PNGs each capture is compared against. |
| `ambient.config.yaml` | Declares the Flutter adapter, local baseline storage, and the compare threshold. |
| `.github/workflows/visual-gate.yml` | Runs the gate on every push/PR in the canonical container. |

The gate captures via `dart run ambient_flutter:capture` (the adapter pulled in
through `pubspec.yaml`), then the standalone `ambient` engine compares and
reports.

## Running locally

Requires the Flutter SDK and read access to the AmbientVRT package repo.

```sh
flutter pub get
# build the `ambient` engine from the package repo, then:
ambient test --config ambient.config.yaml --report-dir .ambient/report
open .ambient/report/report.html
```
