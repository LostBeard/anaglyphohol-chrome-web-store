# Anaglyphohol and the Chrome Web Store

A documented record of a Chrome Web Store review dispute over a free, open source WebAssembly browser extension. **Resolved October 14, 2026: Google reinstated Anaglyphohol 4.0.0 and it is live on the store.**

[Anaglyphohol](https://github.com/LostBeard/AnaglyphoholV2) turns the videos and images on web pages into 3D as you browse. It is free on every site, has no account, no ads, no tracking, and sends nothing anywhere: the depth AI runs on your own graphics card. The full source is public.

Version 4.0.0 was submitted to the Chrome Web Store in October 2026. It was rejected twice and two appeals were denied. On October 14, after a third look, Chrome Web Store Developer Support wrote: "We took a closer look at your item again and found it to be compliant with our policies. Your item has been reinstated." The email did not say what changed, so this record does not claim to know.

**The short version:** Anaglyphohol 1, 2 and 4 are .NET Blazor WebAssembly apps. Version 3 is plain JavaScript and HTML. Google approved versions 1 and 2, which requested `storage` and used it from compiled WebAssembly, where a text search of the package's JavaScript cannot find it. Google approved version 3, which requests `storage` and `offscreen` and names them in its JavaScript. Then it rejected version 4, which requests the same `storage` and `offscreen` as version 3 and uses them from WebAssembly exactly as versions 1 and 2 did, on the grounds that it could not see them being used. A week later it reversed that decision.

Every claim below links to the source code, the commit history, or the verbatim email it comes from.

- [Install Anaglyphohol 4.0.0 from GitHub, Edge or Firefox](INSTALL.md)
- [The emails, verbatim](correspondence/)
- [What it takes to publish a free extension on the Chrome Web Store](THE-WORK.md)
- Article: [Google reversed it: the Chrome Web Store and a WebAssembly extension](https://dev.to/lostbeard/the-chrome-web-store-is-hostile-to-webassembly-google-rejected-my-free-extension-for-not-using-177m) (dev.to)

---

## Summary

| Date (Eastern) | What happened |
|---|---|
| Oct 7, 2026 06:07 | **Rejected** for "keyword spam": the description listed the sites the extension is built for, including Google. |
| Oct 7, 2026 | Site list removed. Description changed to the exact text Google already approved for 3.0.14. Resubmitted. |
| Oct 9, 2026 04:43 | **Rejected** for "requesting but not using" the `storage` and `offscreen` permissions. Both are used. |
| Oct 9, 2026 | Appeal filed from the developer dashboard. |
| Oct 9, 2026 08:39 | **Appeal denied.** Google asks for "evidence (code snippet) of usage". |
| Oct 9, 2026 08:48 | Developer replies with the code snippets, file names, line numbers and GitHub permalinks to the exact commits in the build. |
| Oct 10, 2026 02:18 | **Appeal denied again.** "We are not able to see the provided code in the extension package." Google asks the developer to prove the extension is broken without the permissions. |
| Oct 14, 2026 08:30 | **Published.** Google's automated notice: version 4.0.0, visibility Public. |
| Oct 14, 2026 08:40 | **Reinstated.** "We took a closer look at your item again and found it to be compliant with our policies." Google apologizes for the inconvenience. |

## Rejection 1: "Keyword Spam" (Yellow Argon)

Google's [rejection](correspondence/2026-10-07-rejection-keyword-spam.md) quoted this part of the manifest description as "excessive and / or irrelevant keywords":

> "YouTube, Twitch, Pluto TV, Tubi, Bing, Google, Yahoo and more."

These are not keywords. They are the sites Anaglyphohol is built for: it switches 3D images and videos on by default on exactly those sites ([README, "Where it works"](https://github.com/LostBeard/AnaglyphoholV2#where-it-works)). Telling a user which sites an extension works on is the most relevant thing a description can say.

A list like it had already passed review. Version 2.0.0, published on the Chrome Web Store in 2025, had the manifest description ["View internet videos and images in anaglyph 3D. YouTube, Twitch, South Park Studios, Bing, Google, and more."](https://github.com/LostBeard/AnaglyphoholV2/blob/e37e0dc/Anaglyphohol/wwwroot/manifest.json) The policy did not change. The reviewer did.

The list was removed anyway ([e0724cd](https://github.com/LostBeard/AnaglyphoholV2/commit/e0724cd), then [11b6484](https://github.com/LostBeard/AnaglyphoholV2/commit/11b6484e1383bbd9195a2029983a2794f80a755d)), and the description became the exact sentence that was live on the Chrome Web Store for version 3.0.14.

## Rejection 2: "Requesting but not using" storage and offscreen (Purple Potassium)

Google's [rejection](correspondence/2026-10-09-rejection-unused-permissions.md) said Anaglyphohol requests `storage` and `offscreen` and does not use them, and told the developer to remove both.

Both are used.

- **`storage`** saves every setting the user changes: 3D on or off, per-site image and video switches, 3D mode, 3D level, focus and toolbar state ([DefaultOverlay.razor.cs](https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/ExtensionContent/DefaultOverlay.razor.cs#L66-L175), [ContentOverlay.razor.cs](https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Layout/ContentOverlay.razor.cs#L121), [Options.razor](https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Pages/Options.razor#L39-L47)). Without it, no setting survives a page load.
- **`offscreen`** runs the "Shared 3D converter", an option on the Options page that runs one depth and 3D converter for every tab ([ConverterRelayService.cs](https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Background/ConverterRelayService.cs#L55-L100)). Without it, that option cannot work.

Google did not say Anaglyphohol is malware, a scam, or a privacy risk. It said the extension asks for two permissions and does not use them. Neither permission shows users a warning at install time ([Chrome's permission list](https://developer.chrome.com/docs/extensions/reference/permissions-list) gives `history`, `tabs` and others a "Warning displayed" line; `storage` and `offscreen` have none). And the claim was wrong: both are used.

### Why a text search does not find them

Anaglyphohol is written in C# and compiled to WebAssembly. Chrome supports WebAssembly in Manifest V3 extensions (the `wasm-unsafe-eval` content security policy exists for exactly this). The compiled code reaches the extension APIs through an open source interop library, [SpawnDev.SpawnJS.BrowserExtension](https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension), which reads the `storage` and `offscreen` properties of the global `chrome` object by name:

```csharp
// Browser.cs
public Storage? Storage => JSRef!.Get<Storage?>("storage");
public Offscreen? Offscreen => JSRef!.Get<Offscreen?>("offscreen");

// Offscreen/Offscreen.cs
public Task CreateDocument(OffscreenCreateParameters parameters) => JSRef!.CallVoidAsync("createDocument", parameters);
public Task<bool> HasDocument() => JSRef!.CallAsync<bool>("hasDocument");
```

So the literal text `chrome.storage` and `chrome.offscreen` does not appear in the package's `.js` files. The calls are made from the compiled WebAssembly. The names are still in the package: the strings `storage`, `offscreen`, `createDocument` and `hasDocument` are stored in the compiled code (`app/_framework/SpawnDev.SpawnJS.BrowserExtension.*.wasm`, as UTF-16 text, the way .NET stores strings).

Versions 1 and 2 of Anaglyphohol were also .NET Blazor WebAssembly apps. They also requested `storage`. They also called it from compiled WebAssembly through an interop library ([SpawnDev.BlazorJS.BrowserExtension](https://github.com/LostBeard/SpawnDev.BlazorJS.BrowserExtension)), so their JavaScript never named `chrome.storage` either. Google approved both.

### The appeal

Google [asked](correspondence/2026-10-09-appeal-denied.md) for "evidence(code snippet) of usage of above permission". The developer [sent it](correspondence/2026-10-09-developer-reply.md): every call site, with file names, line numbers, the interop library code that calls `chrome.storage.sync.get/set` and `chrome.offscreen.createDocument/hasDocument/closeDocument`, GitHub permalinks to the exact commits used for the build, and the steps to watch the offscreen document run.

Google's [answer](correspondence/2026-10-10-appeal-denied-again.md), in full on the point:

> We are not able to see the provided code in the extension package. So, we request you to update by removing "storage" and "offscreen" permissions from the manifest file.
>
> [...] Please reply to this email with evidence that the functionality is not working after removing the permissions.

Google asked for code, received code, and replied that it could not see the code. It then asked the developer to remove both permissions and send evidence that the extension no longer worked. Checking whether a permission is used is the reviewer's job, and it takes about a minute: install the extension, change the 3D level, reload the page, see that the setting was kept.

Four days later, on October 14, [Google reversed the decision](correspondence/2026-10-14-appeal-reinstated.md).

## Same permissions, already approved

Every Chrome version of Anaglyphohol has requested `storage`. Every one before 4.0.0 passed review.

| Version | Written in | Permissions | How `storage` is called | Chrome Web Store |
|---|---|---|---|---|
| 1.0.x (2025) | .NET 8 Blazor WebAssembly | `storage` | from WebAssembly, by name ([DefaultOverlay.razor.cs](https://github.com/LostBeard/Anaglyphohol/blob/288a739/Anaglyphohol/ExtensionContent/DefaultOverlay.razor.cs#L53)) | **Approved** |
| 2.0.0 (2025) | .NET 8 Blazor WebAssembly | `storage` | from WebAssembly, by name ([DefaultOverlay.razor.cs](https://github.com/LostBeard/AnaglyphoholV2/blob/e37e0dc/Anaglyphohol/ExtensionContent/DefaultOverlay.razor.cs#L68)) | **Approved** |
| 3.0.14 (Aug 2025) | JavaScript and HTML | `storage`, `offscreen` | literal `chrome.storage` | **Approved** |
| 4.0.0 (Oct 2026) | .NET 10 Blazor WebAssembly | `storage`, `offscreen` | from WebAssembly, by name | Rejected twice as "not used", appeals denied twice, **reinstated Oct 14** |

## What the record shows

- Chrome supports WebAssembly in Manifest V3 extensions, and its content security policy has a setting (`wasm-unsafe-eval`) made for it.
- For a week, the review treated a permission as unused unless the package's JavaScript named it. A permission used from WebAssembly was rejected as "not used".
- When the developer sent the code, the first answer was "We are not able to see the provided code in the extension package", and the fix requested was to remove the permissions.
- Google's own AI described the review as a "naive static string-matching analyzer" that only clears the build if JavaScript names the API, and advised faking that JavaScript ([evidence](evidence/gemini-workaround.md)).
- On October 14, Google looked again and found the extension compliant, with no changes requested.

An extension written in C#, Rust, Go, C++ or any other language that compiles to WebAssembly can use exactly the APIs it declares and still be rejected by a scan that only reads JavaScript. Versions 1 and 2 of Anaglyphohol got through. Version 4 did not, until the second look. This record does not know whether the scan itself changed. A developer who hits the same rejection has this evidence that the appeal can succeed, and what to send.

## The cost

- **Users.** Chrome users stayed on 3.0.14 for a week, without 4.0.0's new engine, its video depth model that stops scenes from flickering, cleaner edges, or the removal of every time limit (4.0.0 is free on every site).
- **The developer.** A solo developer spent days answering form letters instead of building software. See [THE-WORK.md](THE-WORK.md).
- **Everyone else.** Any extension that calls extension APIs from WebAssembly through an interop layer can hit the same rejection.

## What was not done

The obvious workaround is to add a JavaScript file that names `chrome.storage` and `chrome.offscreen` literally, only so that a text search finds them. That would change nothing about what the extension does. It was not done, and the extension was reinstated without it.

That workaround came from Google's own AI. Asked about the rejection, Gemini described the review as a "naive static string-matching analyzer" and recommended what it called "The Canary String Trick (Easiest Bypass)": dead code inside `if (false)` that names `chrome.storage` and `chrome.offscreen`, so "the scanner will see them and clear the build" ([Gemini's reply](evidence/gemini-workaround.md)). Claude, the AI the developer builds with, pointed out that this would likely make things worse: code that exists only to pass a scan looks like an attempt to mislead the review if a reviewer ever notices it.

## Get Anaglyphohol

- [Chrome Web Store](https://chromewebstore.google.com/detail/anaglyphohol/fjbffnhfchidmfcbecccnmdedjahankc)
- [Microsoft Edge Add-ons](https://microsoftedge.microsoft.com/addons/detail/anaglyphohol/njohkaaolgfakmollkfjikipedmlapog)
- [GitHub release](https://github.com/LostBeard/AnaglyphoholV2/releases/tag/v4.0.0), with [install steps for Chrome, Edge and Firefox](INSTALL.md)

## Support the developer

Anaglyphohol is free, with no ads, no tracking and no subscription, and it will stay that way. It is built by one developer, along with the open source libraries it runs on. If it is worth something to you, sponsoring or a one-time donation directly funds more of this work.

[![GitHub Sponsor](https://img.shields.io/github/sponsors/LostBeard?label=Sponsor&logo=GitHub&color=%23fe8e86)](https://github.com/sponsors/LostBeard)
[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=2F6VANCK2EMEY)

## Sources

- Anaglyphohol 4.0.0 source: [LostBeard/AnaglyphoholV2](https://github.com/LostBeard/AnaglyphoholV2) (commit [11b6484](https://github.com/LostBeard/AnaglyphoholV2/tree/11b6484e1383bbd9195a2029983a2794f80a755d) is the submitted build)
- Interop library: [LostBeard/SpawnDev.SpawnJS.BrowserExtension](https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension) (commit [05a55d0](https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension/tree/05a55d04e820c69a2f2e66c9ede51d18431a3428), version 2.2.2)
- Version 1: [LostBeard/Anaglyphohol](https://github.com/LostBeard/Anaglyphohol)
- Chrome Web Store listing: [Anaglyphohol](https://chromewebstore.google.com/detail/anaglyphohol/fjbffnhfchidmfcbecccnmdedjahankc) (version 4.0.0, updated October 14, 2026)
- [All correspondence](correspondence/)

Google support staff are not named here. Their emails were signed "Chrome Web Store Developer Support" and are reproduced as received.

---

Todd Tanner ([@LostBeard](https://github.com/LostBeard)), October 14, 2026
