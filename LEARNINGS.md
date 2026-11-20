DAY - 01 , DATE :- 17/11/26
| Command | Area | What it does |
| --- | --- | --- |
| `mkdir -p ~/projects` | Ubuntu | Creates a `projects` folder in your Linux home (`~`); `-p` prevents errors if it already exists |
| `cd ~/projects` | Ubuntu | Changes your current terminal directory to `~/projects` |
| `code .` | WSL + VS Code | Opens Windows VS Code connected directly to the current Ubuntu folder (`.`) |
| `Ctrl + Shift + V` / Right-Click | WSL Terminal | Pastes into Linux terminals where `Ctrl + V` fails |
| `Ctrl + C` | Ubuntu | Cancels the current running command or stuck prompt |
| `git config --global user.name "..."` | Git | Sets your author name for all repos in WSL |
| `git config --global user.email "..."` | Git | Sets your author email (must match GitHub for your contribution graph) |
| `git config --global init.defaultBranch main` | Git | Sets `main` as the default branch name |
| `git config --global credential.helper store` | Git | Saves your GitHub PAT locally in WSL so you don't re-paste it on every push |
| `git clone <url>` | Git | Downloads a remote GitHub repo into a local folder |
| `git checkout -b <branch>` | Git | Creates a new branch and switches to it in one step |
| `git commit --allow-empty -m "..."` | Git | Creates a commit with zero file changes (used to initialize `main` in an empty repo) |
| `git push -u origin <branch>` | Git | Pushes your branch to GitHub and links it (`-u`) for future pushes |
| `git status` | Git | Checks your current branch and which files are staged or untracked |
| `git add .` | Git | Stages all changed/new files in the current directory for commit |
| `git commit -m "..."` | Git | Saves a permanent snapshot with a Conventional Commit message |


Day-02 , Date :- 20/11/26
- **Normalized tables & Foreign Keys:** Separated `order_items` from `orders` so one order can contain multiple products without duplicating data.
- **Data-safety rules in SQL:** Stored money in integer `price_cents` (no float rounding bugs), used `TIMESTAMPTZ` (prevents UTC vs IST timezone bugs), and added `CHECK (stock_qty >= 0)`.
- **Protect history (`ON DELETE RESTRICT` vs `CASCADE`):** Deleting an order cascades to `order_items`, while `RESTRICT` blocks deleting a product that has past sales.
- **Partial unique index:** Added `WHERE is_read = FALSE` on `notifications(product_id)` so PostgreSQL blocks duplicate unread low-stock alerts automatically.