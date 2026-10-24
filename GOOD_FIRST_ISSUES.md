# Good first issues

Small, well-scoped tasks for new contributors. Each one can be done in a
single pull request. Comment on the issue before starting so two people do
not pick up the same one.

## 1. Add a `--user` mode that targets user timers

**What:** Add a `--user` flag. When set, generated timers should install to
`timers.target` under `~/.config/systemd/user/` semantics: the README's
install instructions should show the `--user` variant
(`systemctl --user enable --now ...`), and `--out-dir` should default to a
sensible user path hint in `--help`.

**Why:** Many cron jobs run as non-root users, and user timers are the
systemd-native way to schedule them.

**Files:** `cron2timer.py` (`main`, argument parsing, README example).

## 2. Handle `%` newline semantics in commands

**What:** cron currently treats an unescaped `%` in the command as a
newline (stdin for the command). `--check` already warns about this.
Implement the real mapping: split the command at unescaped `%` and emit
the remainder in a way systemd understands, or document precisely why it
cannot be mapped and upgrade the warning to suggest a workaround.

**Why:** Silent behavior differences during migration are the worst kind
of bug. Either fix it or document it loudly.

**Files:** `cron2timer.py` (`parse_crontab`, `render_service`,
`check_text`), plus tests.

## 3. Map `MAILTO=` to `OnFailure=` notification guidance

**What:** `MAILTO=` is currently passed through as a plain `Environment=`
variable, but systemd will not mail anything by itself. Add an option
(such as `--mailto-unit`) that, when `MAILTO` is set, adds an
`OnFailure=` hook to the generated service pointing at a small companion
mailer unit, or generates that companion unit automatically. Update the
README's "Known limitations" section accordingly.

**Why:** People migrating from cron expect their failure emails to keep
working.

**Files:** `cron2timer.py` (`render_service`), `README.md`, plus tests.

## 4. Add JSON output mode

**What:** Add a `--json` flag that prints the parsed jobs as JSON instead
of unit files: line number, schedule fields, generated `OnCalendar=`
expressions, unit name, environment, and command. This makes cron2timer
usable as a library-style building block in larger migration scripts.

**Why:** Machine-readable output unlocks integrations (inventory tools,
auditing, dry-run diffing) without parsing unit files.

**Files:** `cron2timer.py` (`main`), plus tests.
