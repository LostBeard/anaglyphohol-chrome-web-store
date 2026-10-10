# What it takes to publish a free extension on the Chrome Web Store

Anaglyphohol costs nothing, shows no ads, collects no data and has no account. Its source is public. This is what Google required before a single person could install it from the Chrome Web Store, and what it took to try to ship one update.

## Before the first upload

- **Pay Google.** A one-time developer registration fee, to give away free software.
- **Hand over your identity.** Google required the developer's full name, home address and phone number. The public listing displays his full name, home street address and email address to anyone who opens it. There was no option to use a PO box. For a solo developer, there is no business address to put there instead: it is the family's home.
- **Fill in the listing.** A description written under keyword rules that are applied inconsistently (see below), screenshots, promotional images, a category, and a privacy section: a "single purpose" statement, a written justification for every permission, and data-use certifications.

## Every update after that

- **Rebuild.** Anaglyphohol is compiled ahead of time to WebAssembly for speed. A full release build takes over an hour. Any change to the manifest, even one sentence of description, means a new package.
- **Submit and wait.** Every submission goes back into review. Every rejection means fix, resubmit, and wait again.
- **Guess the rules.** The same site list Google approved in an earlier version's description was rejected as "keyword spam" in this one. Separately, an Overview text in the dashboard was refused for naming the sites the extension works on.
- **Argue with form letters.** Appeals come back as templates. They ask for evidence, receive it, and answer as though nothing was sent.

## This update, in numbers

| | |
|---|---|
| Days from first rejection to second appeal denial | 4 |
| Rejections | 2 |
| Appeals denied | 2 |
| Reasons that held up | 0 |
| Permissions requested that 3.0.14, live on the store, does not already request | 0 |
| Lines of code Google asked for, received, and said it could not see | all of them |
| Chrome users who received the update | 0 |

## Where the time should have gone

Every hour spent on this was an hour not spent on the extension, its users, or the open source libraries it is built on ([SpawnDev.ILGPU](https://github.com/LostBeard/SpawnDev.ILGPU), [SpawnDev.ILGPU.ML](https://github.com/LostBeard/SpawnDev.ILGPU.ML), [SpawnDev.SpawnJS](https://github.com/LostBeard/SpawnDev.SpawnJS), [SpawnDev.SpawnJS.BrowserExtension](https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension)), which other developers use for free.

Back to the [main page](README.md).
