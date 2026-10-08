# Viewer9-AudioModels

Acoustic models for **JAVS Viewer 9's Audio Sync**, published as downloadable **audio model packs**.

This repo is **read by the application, not by people.** Viewer 9 fetches [`catalog.json`](catalog.json) from
this branch, shows what is available in Settings → Captions & Subtitles → Audio Models, and downloads the one
you choose. Nothing here needs to be cloned or installed by hand.

> **These are not UI translations.** Viewer 9's interface languages are a separate feature with its own import,
> under Settings → General → Edit Languages. A pack here contains no interface text — only the acoustic model
> that lets Audio Sync line caption words up with the recording. (This repo was briefly called
> *Viewer9-LanguagePacks*, which invited exactly that confusion.)

Viewer 9 itself is released from a third repo, **Viewer9-Releases**, and its auto-update feed reads only that
one. The separation is deliberate: the updater looks at a fixed window of the most recent releases, so
publishing packs alongside the application would eventually push real app releases out of view and stop
updates working.

## What a pack is

Audio Sync force-aligns caption words to recorded audio. It never transcribes and never decides what was said
— it is given the words and asked only *where* they are. That needs an acoustic model for the language
actually **spoken** on the recording.

A translated caption track does not need one: those are re-timed from the spoken language's measurement. So
only languages you can *hear* in a courtroom appear here, and a French transcript of English testimony needs
nothing from this repo.

Each pack is a zip containing:

| | |
|---|---|
| `model.onnx` | the quantized acoustic model |
| `vocab.json` | the character vocabulary it emits |
| `MODEL-CARD.md` | lineage, licence, measured performance and known limits |
| `pack.json` | the manifest |

A language can have more than one model, and they are not interchangeable — Spanish has a compact model and a
full one that measure differently. Viewer 9 lets you install both and choose which is active; exactly one is
used per language.

## Releases

One release per pack version, tagged `<pack-id>-<version>`:

```
ar-xlsr-1.0.0      ar-xlsr-1.0.0.zip + ar-xlsr-1.0.0.zip.sha256
es-xlsr-1.0.0      es-xlsr-1.0.0.zip + es-xlsr-1.0.0.zip.sha256
```

`catalog.json` carries the SHA-256 of every pack and Viewer 9 verifies the download against it before
installing, then opens the model and smoke-tests it. A pack that fails either check is discarded rather than
installed — a wrong model does not announce itself, it produces confident nonsense.

Model files are **never committed to git**; they exist only as release assets. See [`.gitignore`](.gitignore)
for why.

`catalog.json` is written by `scripts/publish-audio-model.ps1` in the Viewer 9 repo, which refuses to publish
a model the shipping aligner cannot open. A hand-edit will fail CI — see
[`.github/workflows/validate-catalog.yml`](.github/workflows/validate-catalog.yml).

## Licensing

**There is no repo-wide licence, and that is deliberate.** The packs are not uniform: some wrap third-party
models under their own terms (Apache-2.0, for instance), others are models JAVS trained and owns outright. A
single LICENSE file at the root would claim terms over work we do not own, and GitHub's "no licence" default
reads as all-rights-reserved, which is equally wrong for the permissively licensed weights we redistribute.

**Each pack's licence travels inside it, in `MODEL-CARD.md`**, together with its lineage and attributions.
That file is mandatory — a pack cannot be published without one. Check it before redistributing a pack.

## Contributing

Packs are built and published by Justice AV Solutions. Viewer 9 installs only packs listed in this catalog and
verified against it; there is no third-party pack format and no sideload path.
