# Changelog

All notable changes to this project will be documented in this file.

## [1.0.0] - 2026-10-23

Initial release.

### Added

* Parse standard 5-field cron expressions plus `@reboot`, `@yearly`,
  `@annually`, `@monthly`, `@weekly`, `@daily`, `@midnight`, and `@hourly`.
* Convert schedules to systemd `OnCalendar=` expressions, validated against
  the real systemd parser: step normalization (`*/15` to `0/15`), `..`
  ranges, month names to numbers, weekday names, and weekday step expansion.
* Two `OnCalendar=` lines when both day-of-month and day-of-week are
  restricted, preserving cron OR semantics.
* `VAR=value` assignments mapped to `Environment=` directives, applying to
  jobs below them as cron does.
* One `.service` (`Type=oneshot`) plus one `.timer`
  (`WantedBy=timers.target`) per job; `@reboot` emits a service targeting
  `multi-user.target` with no timer.
* Unit names derived from the job command, sanitized and de-duplicated.
* `--check` mode with line-numbered errors (exit 1) and warnings that do
  not fail the check, `--stdout` mode, and `--out-dir` selection.
* 49-test suite, including `systemd-analyze calendar` validation of every
  generated expression where available.
