# AGENTS

Repo purpose: build minimal cron wrapper container.

Build: `docker build -t crontab .`
Run dev: see README compose example.
Shell into image: `docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock:ro crontab sh`
Single cron test: create minimal config file then `docker run -v "$PWD":/opt/crontab crontab crond -f -d 6 -c /etc/crontabs`.
Logging test script: `bash ./test_logging` (captures stdout/stderr to jobs.log).
No formal test suite yet; add bash scripts under root, name `test_*`.
Lint: use `shellcheck` on bash files; keep POSIX where feasible, but bash features allowed (arrays, here-docs).
Formatting: 4-space indent inside here-doc only; otherwise tabs/spaces as existing. Avoid trailing spaces.
Imports/tools: prefer busybox/alpine base packages; only add runtime deps via `apk add --no-cache` in Dockerfile.
Variable naming: UPPER_CASE for constants/env (`HOME_DIR`,`LOG_FILE`); lower_snake for locals; avoid single letters beyond loop indexes.
Functions: `snake_case`, start with verb (`make_image_cmd`). Keep side effects explicit (echo commands). Return via echo only.
Error handling: always `set -e`; guard nullable jq fields (`if [ "${VAR}" == "null" ]; then VAR=; fi`). Use `|| exit 1` for healthcheck commands.
jq/dasel usage: always use `jq -r` for raw strings; use `dasel -r yaml|toml -w json` to normalize configs; check for `null` before use.
Cron generation: keep slugify consistent; never mutate schedule strings without parse_schedule.
Security: never chmod docker.sock; instead adjust group as in entrypoint. Do not run as root; use `docker` user.
Add new config keys: ensure backward compatibility (ignore unknown keys gracefully).
Commit style: ultra concise, imperative, describe why.
Multi-arch build: enable buildx then `docker buildx build --platform linux/amd64,linux/arm64 -t crontab:multi .` (test locally with `--platform`).
