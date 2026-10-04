# Marketing skills release

## User request
Finish and push Marketing Studio, MCP, skills, CLI and API updates.

## What changed
Synchronized the canonical 1.3.0 skills package, including conversational Marketing Studio, ad copywriting/direction and contextual brand/logo/social skills. Added source/license notices, updated installation lists and retained existing generation/identity/NSFW skill families.

## Why
The public installation repository still contained the older five-skill 1.1.0 package; shipping only the application repository would leave installs without the new production guidance.

## Gotchas
Runtime approval, ownership, spend and retry gates remain authoritative. Skills are guidance, not proof a media operation or review succeeded. The CLI source is released with the application; npm authentication currently returns 401, so a fresh npm publication is not claimed. Existing stored credentials were not printed or changed.

## Validation
Canonical skills parity and link checks pass. All eleven skill entrypoints match VERSION. New families preserve upstream source and license notices. Application tests cover bounded contextual loading and MCP resource access.
