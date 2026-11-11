# cleantv

Copia personale di [CleanYourTV](https://github.com/Gathin23/cleanyourtv) (licenza MIT, © 2026 Gathin),
mantenuta da Giulio Donnini. Non è il progetto ufficiale e non riceve aggiornamenti automatici.

## Da dove parte

- Upstream: `https://github.com/Gathin23/cleanyourtv`
- Commit di partenza: `deba47ca1eac7abe3fef0f73ddaf5ae6fb86c1af` (10 novembre 2026)

## Cosa è cambiato rispetto all'originale

- Tolto `.github/workflows/maintainer-bot.yml`: è il bot che recensisce le PR dell'upstream con i suoi
  segreti e un modello a pagamento; qui non serve.
- Tolto `.github/FUNDING.yml`: è il pulsante "Sponsor" dell'autore originale.

Il resto è identico all'upstream di proposito: la suite di test (`tests/`) controlla README, liste ed
esempi, e un albero uguale rende facile portare a mano le correzioni future.

## Liste per Haier

| Cartella | Modello |
|---|---|
| `examples/haier-hr9216eu` | MatrixTV GE, Google TV 14 (debloat + launcher testati) |
| `examples/haier-matrixtv-de` | MatrixTV DE / HR9676EUF, Google TV 14 (debloat + launcher testati) |
| `examples/haier-matrixtv-be` | MatrixTV BE, Google TV 14 (prova breve) |
| `examples/haier-h32s80efx` | H32S80EFX, Android TV (solo debloat) |
| `examples/haier-android-tv-dvb2k` | Android TV generica (solo debloat) |

## Cosa toccano gli script sulla TV

- **Debloat** (`tools/disable.sh`): solo `pm disable-user --user 0 <pacchetto>`. Niente viene
  disinstallato; `tools/restore.sh` riattiva tutto con `pm enable`.
- **Launcher** (`launcher/build-and-install.sh`): `pm install` dell'APK compilato in locale, poi
  `cmd package set-home-activity`. Il launcher originale viene disattivato solo dopo che il nuovo risponde
  come Home.
- **Rete**: gli strumenti parlano solo con la TV in LAN (ADB, porta 5555 o debug wireless). Le uniche
  chiamate esterne sono in add-on opzionali (es. `tools/race_calendar.py`, calendario F1).
- **Permessi del launcher**: nessun `INTERNET`. Ha `ACCESS_FINE_LOCATION` (solo per leggere il nome
  della rete Wi-Fi) e `WRITE_SECURE_SETTINGS` (per ripristinare i servizi accessibilità del telecomando),
  assegnati con `pm grant` durante l'installazione.

## Dopo un aggiornamento firmware Haier

Il firmware riattiva i pacchetti disattivati e rimette il launcher originale. Ricontrolla con:

```bash
TV_HOST=<ip tv> tools/venv/bin/python -I tools/check.py examples/<cartella>/debloat.txt
```

e riapplica con `--fix`. Vedi `docs/firmware-updates.md` (anche in italiano in `docs/it/`).

## Portare un aggiornamento dall'upstream (solo quando lo decidi tu)

```bash
git remote add upstream https://github.com/Gathin23/cleanyourtv.git   # una volta
git fetch upstream
git log --oneline deba47c..upstream/main -- tools launcher              # leggi cosa cambia
git diff deba47c upstream/main -- tools launcher                        # e come
git cherry-pick <commit>                                                # porta solo quello che vuoi
```
