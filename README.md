# Test 2 — labs.ly subdomain test

This folder holds everything you need to add a **second test subdomain**
(`test2.labs.ly`) to your cloned platform repo **https://github.com/donking12/LABS**
using only drag-and-drop, and to verify that the system works.

## What is in this folder

```
test-two-for-lab/
├── index.html          ← the welcome page (the "target" website)
├── cnames/
│   └── test2.json      ← the entry file that goes INTO the cloned repo
└── README.md           ← this guide
```

There are **two separate things**, because the platform repo only stores the
*subdomain entries* — it does not host the websites itself:

1. **A live page to point at** → `index.html` (upload it to its own small repo).
2. **A subdomain entry** → `cnames\test2.json` (upload it to the cloned `donking12/LABS` repo).

The file name is the subdomain. `cnames\test2.json` ⇒ **`test2.labs.ly`**.

---

## PART A — Put the welcome page online (this is the step BEFORE the entry)

1. On GitHub (account **donking12**) create a **new public repository** named
   `test-two-for-lab`.
2. On the new repo: **Add file → Upload files** → drag in **`index.html`** from
   `C:\test-two-for-lab\` → **Commit changes**.
3. Go to **Settings → Pages**. Under *Build and deployment*, set
   **Source = Deploy from a branch**, **Branch = `main` / `(root)`**, then **Save**.
4. Wait ~1 minute. Open **https://donking12.github.io/test-two-for-lab/** and
   confirm you see the "Welcome / أهلاً وسهلاً" page.

> If you choose a different repository name, you must update the target in
> `cnames\test2.json` to match (`https://donking12.github.io/<repo-name>/`).
> The target **must** start with `https://`.

---

## PART B — Add the subdomain entry to the cloned repo (this is the NEXT step)

Answer to your question: **yes, `test2.json` is added to the cloned repository**
(`donking12/LABS`). The welcome page is NOT added there — it lives in its own repo.

1. Open **https://github.com/donking12/LABS**.
2. **Recommended:** create a branch first (e.g. `add-test2`) so the validation
   check runs as a Pull Request — button **Branch: main → type `add-test2`**.
   (If you would rather test the deploy path, stay on `main`.)
3. Open the **`cnames`** folder → **Add file → Upload files** → drag in
   **`cnames\test2.json`** from `C:\test-two-for-lab\cnames\` → **Commit changes**.
4. Important rules for the file:
   - The name must stay exactly **`test2.json`** (do not rename it).
   - Put it **directly inside `cnames/`**, not in a sub-folder.
   - Its content must be a JSON object with a non-empty `target`, e.g.
     ```json
     { "target": "https://donking12.github.io/test-two-for-lab/" }
     ```
5. If you used a branch, click **Compare & pull request** and open it.

---

## PART C — Verify everything works

**1. Watch the automation**
   - Repo → **Actions** tab.
   - On a Pull Request: the **`validate`** workflow runs. Green = the entry is valid.
     It checks: file name/label rules, the reserved-word blocklist, the JSON format
     (a non-empty `"target"`), and that the target is reachable over HTTPS and is
     not a parked domain.
   - After merging to `main`: the **`deploy`** workflow runs. It rebuilds
     `data/active.json` (the public list) and syncs the CNAME in Cloudflare.

**2. Check the public index**
   - `data/active.json` in the repo should now list your entry (count increases).

**3. Check the live subdomain**
   - Visit **https://test2.labs.ly**.
   - This only resolves if the **Cloudflare** secrets are configured in the repo
     (see below) and the merge reached `main`. On a plain fork with no secrets,
     the DNS step cannot run, so the subdomain will not resolve yet — but the
     `validate` step still proves the entry itself is correct.

---

## Troubleshooting

- **Validation fails with "target not reachable"** → your GitHub Pages site
  isn't live yet (usually a 404 right after creating it). Wait a minute, then
  **Re-run failed jobs** in the Actions tab.
- **"target must use https"** → use `https://…`, never `http://…`.
- **"reserved label"** → the label is blocked (see the `domains/` folder). `test2`
  is fine.
- **File ignored / no validation** → it must be named `*.json` and sit directly
  in `cnames/`.

## Configure the platform (only needed for the live `test2.labs.ly` DNS step)

In `donking12/LABS` → **Settings → Secrets and variables → Actions**, add the
secrets the Cloudflare sync expects (see the repo's `README.md` / workflow files),
for example:

- `CLOUDFLARE_API_TOKEN` — token with DNS edit rights for the zone.
- `CLOUDFLARE_ZONE_ID` — the zone id of `labs.ly`.

Without these, validation passes but no DNS record is created.
