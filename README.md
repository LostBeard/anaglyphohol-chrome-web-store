# Stupid at Google

A documented record of how the Chrome Web Store handles a free, open source browser extension.

[Anaglyphohol](https://github.com/LostBeard/AnaglyphoholV2) turns the videos and images on web pages into 3D as you browse. It is free on every site, has no account, no ads, no tracking, and sends nothing anywhere: the depth AI runs on your own graphics card. The full source is public.

Version 4.0.0 was submitted to the Chrome Web Store in October 2026. As of October 10, 2026 it has been rejected twice and its appeal has been denied twice. Chrome users are still being served version 3.0.14, from August 2025.

Every claim below links to the source code, the commit history, or the verbatim email it comes from. Nothing here is paraphrased from memory.

- [Install Anaglyphohol 4.0.0 without the Chrome Web Store](INSTALL.md)
- [The emails, verbatim](correspondence/)
- [What it takes to publish a free extension on the Chrome Web Store](THE-WORK.md)

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

## Rejection 1: "Keyword Spam" (Yellow Argon)

Google's [rejection](correspondence/2026-10-07-rejection-keyword-spam.md) quoted this part of the manifest description as "excessive and / or irrelevant keywords":

> "YouTube, Twitch, Pluto TV, Tubi, Bing, Google, Yahoo and more."

These are not keywords. They are the sites Anaglyphohol is built for: it switches 3D images and videos on by default on exactly those sites ([README, "Where it works"](https://github.com/LostBeard/AnaglyphoholV2#where-it-works)). Telling a user which sites an extension works on is the most relevant thing a description can say.

A list like it had already passed review. Version 2.0.0, published on the Chrome Web Store in 2025, had the manifest description ["View internet videos and images in anaglyph 3D. YouTube, Twitch, South Park Studios, Bing, Google, and more."](https://github.com/LostBeard/AnaglyphoholV2/blob/e37e0dc/Anaglyphohol/wwwroot/manifest.json) The policy did not change. The reviewer did.

The list was removed anyway ([e0724cd](https://github.com/LostBeard/AnaglyphoholV2/commit/e0724cd), then [11b6484](https://github.com/LostBeard/AnaglyphoholV2/commit/11b6484e1383bbd9195a2029983a2794f80a755d)), and the description became the exact sentence that is live on the Chrome Web Store today for version 3.0.14.

## Rejection 2: "Requesting but not using" storage and offscreen (Purple Potassium)

Google's [rejection](correspondence/2026-10-09-rejection-unused-permissions.md) says Anaglyphohol requests `storage` and `offscreen` and does not use them, and tells the developer to remove both.

Both are used.

- **`storage`** saves every setting the user changes: 3D on or off, per-site image and video switches, 3D mode, 3D level, focus and toolbar state ([DefaultOverlay.razor.cs](https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/ExtensionContent/DefaultOverlay.razor.cs#L66-L175), [ContentOverlay.razor.cs](https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Layout/ContentOverlay.razor.cs#L121), [Options.razor](https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Pages/Options.razor#L39-L47)). Without it, no setting survives a page load.
- **`offscreen`** runs the "Shared 3D converter", an option on the Options page that runs one depth and 3D converter for every tab ([ConverterRelayService.cs](https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Background/ConverterRelayService.cs#L55-L100)). Without it, that option cannot work.

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

### The appeal

Google [asked](correspondence/2026-10-09-appeal-denied.md) for "evidence(code snippet) of usage of above permission". The developer [sent it](correspondence/2026-10-09-developer-reply.md): every call site, with file names, line numbers, the interop library code that calls `chrome.storage.sync.get/set` and `chrome.offscreen.createDocument/hasDocument/closeDocument`, GitHub permalinks to the exact commits used for the build, and the steps to watch the offscreen document run.

Google's [answer](correspondence/2026-10-10-appeal-denied-again.md), in full on the point:

> We are not able to see the provided code in the extension package. So, we request you to update by removing “storage” and “offscreen” permissions from the manifest file.
>
> [...] Please reply to this email with evidence that the functionality is not working after removing the permissions.

Read that again. Google asked for code, received code, and replied that it cannot see the code. Then it asked the developer to break his own extension and prove that it is broken.

Both requests are backwards.

1. **"Not able to see the provided code in the extension package."** The code is in the package. It is compiled, which is what WebAssembly is. The source of every line was linked. If a reviewer cannot evaluate a compiled WebAssembly extension, the honest answer is "we cannot review this", not "this does not exist".
2. **"Evidence that the functionality is not working after removing the permissions."** Checking whether a permission is used is the reviewer's job. It takes about a minute: install the extension, change the 3D level, reload the page, see that the setting was kept. Instead the developer is asked to produce a broken build, demonstrate that it is broken, and send that in, for a free extension, to get permission to ship the working one.

## Same permissions, already approved

Every Chrome version of Anaglyphohol has requested `storage`. Every one before 4.0.0 passed review.

| Version | Written in | Permissions | How `storage` is called | Chrome Web Store |
|---|---|---|---|---|
| 1.0.x (2025) | C# / Blazor WebAssembly | `storage` | from WebAssembly, by name ([DefaultOverlay.razor.cs](https://github.com/LostBeard/Anaglyphohol/blob/288a739/Anaglyphohol/ExtensionContent/DefaultOverlay.razor.cs#L53)) | Approved |
| 2.0.0 (2025) | C# / Blazor WebAssembly | `storage` | from WebAssembly, by name ([DefaultOverlay.razor.cs](https://github.com/LostBeard/AnaglyphoholV2/blob/e37e0dc/Anaglyphohol/ExtensionContent/DefaultOverlay.razor.cs#L68)) | Approved |
| 3.0.14 (Aug 2025) | JavaScript | `storage`, `offscreen` | literal `chrome.storage` | **Live today** |
| 4.0.0 (Oct 2026) | C# / Blazor WebAssembly | `storage`, `offscreen` | from WebAssembly, by name | **Rejected: "not used"** |

Versions 1 and 2 used `storage` the same way 4.0.0 does, from WebAssembly, and were approved. Version 3.0.14, live on the store right now, requests the exact same two permissions as 4.0.0 and carries the exact same description. Google approves the extension it is serving and rejects the update that asks for nothing more.

## The cost

- **Users.** About 145 Chrome users are still on 3.0.14. They do not get 4.0.0's new engine, its video depth model that stops scenes from flickering, cleaner edges, or the removal of every time limit (4.0.0 is free on every site).
- **The developer.** A solo developer spends days answering form letters instead of building software. See [THE-WORK.md](THE-WORK.md).
- **Everyone else.** Any extension written in C#, Rust, Go or any other language that compiles to WebAssembly and calls extension APIs through an interop layer can hit the same wall. Chrome supports WebAssembly in extensions. Chrome's own review process does not appear to.

## What was not done

The obvious workaround is to add a JavaScript file that names `chrome.storage` and `chrome.offscreen` literally, only so that a text search finds them. That would change nothing about what the extension does. It would only teach the review process that a keyword search is a review. It was not done.

## Install it anyway

Anaglyphohol 4.0.0 is published on GitHub. See **[INSTALL.md](INSTALL.md)** for Chrome, Edge and Firefox.

## Sources

- Anaglyphohol 4.0.0 source: [LostBeard/AnaglyphoholV2](https://github.com/LostBeard/AnaglyphoholV2) (commit [11b6484](https://github.com/LostBeard/AnaglyphoholV2/tree/11b6484e1383bbd9195a2029983a2794f80a755d) is the submitted build)
- Interop library: [LostBeard/SpawnDev.SpawnJS.BrowserExtension](https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension) (commit [05a55d0](https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension/tree/05a55d04e820c69a2f2e66c9ede51d18431a3428), version 2.2.2)
- Version 1: [LostBeard/Anaglyphohol](https://github.com/LostBeard/Anaglyphohol)
- Chrome Web Store listing: [Anaglyphohol](https://chromewebstore.google.com/detail/anaglyphohol/fjbffnhfchidmfcbecccnmdedjahankc) (serving 3.0.14 as of October 10, 2026)
- [All correspondence](correspondence/)

Google support staff are not named here. Their emails were signed "Chrome Web Store Developer Support" and are reproduced as received.

---

## From the developer: I am done with the Chrome Web Store

I am pulling Anaglyphohol from the Chrome Web Store.

I did everything Google asked. I removed the site list they called spam. I answered their permission claim with the exact code, file names, line numbers and links they asked for. Their answer was that they could not see it, and that I should break my own extension and send proof that it is broken. That is not a review. It is a form letter that nobody read, from people who either do not understand WebAssembly, which their own browser supports, or did not look.

**If you want a browser run by people who can review an extension, use [Microsoft Edge](https://www.microsoft.com/edge).** It runs every Chrome extension, Microsoft Edge Add-ons [lists Anaglyphohol](https://microsoftedge.microsoft.com/addons/detail/anaglyphohol/njohkaaolgfakmollkfjikipedmlapog), and it loads Anaglyphohol 4.0.0 directly ([INSTALL.md](INSTALL.md)).

To put a free extension on Google's store, I had to give Google my full name, home address and phone number. Google puts my name and home street address on the public listing page, where anyone can read them. I gave my family's home address to the public so that people could download something for free, and in return Google will not ship it.

This is not limited to Chrome. Google now requires Android developers to register and verify their identity with Google before their apps can be installed on certified Android devices, even apps that never touch the Play Store. Enforcement began on September 30, 2026 in Brazil, Indonesia, Singapore and Thailand, and Google plans to apply it worldwide in 2027. Apps from unregistered developers are pushed into an "advanced" install flow with extra steps and a waiting period ([Android Authority](https://www.androidauthority.com/android-sideloading-changes-timeline-3679204/), [9to5Google](https://9to5google.com/2026/08/18/google-gradually-rolling-out-androids-advanced-sideloading-ahead-of-developer-verification/)). Independent developers who publish anything on Google's platforms are treated as the problem.

I will not spend any more of my time on Google's stores. Anaglyphohol stays free and open source, on GitHub, on Edge, and in Firefox.

**Using Google is just not worth it.**

Todd Tanner ([@LostBeard](https://github.com/LostBeard)), October 2026
