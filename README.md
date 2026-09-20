# lifts

A barebones static workout log. Routines and sessions are plain JSON files in
`data/`, and `index.html` reads them and writes them back through the GitHub
API, so the log follows you across devices without a server.

## How it works

- `data/routines.json` — your routines: a name and a list of exercises.
- `data/sessions.json` — every logged session: a date, the routine used, and
  weight × reps for each set.
- `index.html` — the whole app. Reads the two files, and commits them back to
  this repository when you save.

Without a token the site is read-only: it loads the copies deployed alongside
the page, so you can look at your history on any device. Saving needs a token.

## Setup

1. **Turn on Pages.** Settings → Pages → Source: *GitHub Actions*. This has to
   be done by hand once: the workflow's token is not allowed to create the
   Pages site. After that, `.github/workflows/pages.yml` deploys on every push
   to `main`.
2. **Create a token.** Go to
   [Settings → Developer settings → Fine-grained tokens](https://github.com/settings/personal-access-tokens/new)
   and create one that is:
   - limited to **only this repository**,
   - granted **Repository permissions → Contents: Read and write**,
   - given nothing else.
3. **Paste it in.** Open the site, go to **Settings**, paste the token, save.

The token is stored in that browser's local storage on that device only, so
repeat step 3 on each device you log from. Anyone with access to the device can
read it, which is why the token is scoped to this repository alone. Revoke it
on GitHub if a device is lost.

## Using it

- **Routines** — add a routine with a name and one exercise per line. *Start*
  loads it into the log form.
- **Log a session** — pick a date and routine. Sets are prefilled with what you
  lifted last time for each exercise, and the last session's numbers are shown
  above them, so a repeat workout is a few taps. Add or remove sets, add an
  exercise that is not in the routine, then **Save session**. Exercises left
  blank are not recorded.
- **History** — every session, newest first.

Each save is one commit, which redeploys the site. An in-progress session is
kept in the browser, so closing the tab mid-workout loses nothing.

## Viewing locally

The page fetches local files, so serve it over HTTP rather than opening the
file directly:

```
python3 -m http.server
```

Then open http://localhost:8000/.

## Notes

- Weights are in pounds.
- The app sends the file's blob SHA with every write, so if the same file was
  changed elsewhere since the page loaded, the save is refused rather than
  silently overwriting. Reload and try again.
