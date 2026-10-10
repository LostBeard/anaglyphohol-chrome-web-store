# The developer's reply: the code, with permalinks

- **From:** Todd Tanner (developer)
- **To:** Chrome Web Store Developer Support (chromewebstore-dev-support@google.com)
- **Date:** October 9, 2026, 08:48 Eastern (12:48 UTC)
- **Subject:** Re: [6-2323000042053] Appeal for fjbffnhfchidmfcbecccnmdedjahankc

Exactly what Google asked for: "evidence(code snippet) of usage". Reproduced verbatim (the quoted copy of Google's previous email is left out; it is [here](2026-10-09-appeal-denied.md)).

```text
Hello,

Extension ID: fjbffnhfchidmfcbecccnmdedjahankc (Anaglyphohol 4.0.0)

Both "storage" and "offscreen" are used. Anaglyphohol is written in C# and
compiled to WebAssembly (app/_framework/ in the package). It calls the
extension APIs through a JS interop library, so the names chrome.storage
and chrome.offscreen appear in the C# source, not as literal text in the
package's .js files. The full source is public on GitHub. The links below
point to the exact commits used for this build.

1. How the WebAssembly code reaches chrome.storage and chrome.offscreen

The interop library (SpawnDev.SpawnJS.BrowserExtension 2.2.2, the version
in this build) gets the global "chrome" object and reads its "storage" and
"offscreen" properties:

    // Services/BrowserExtensionService.cs line 12
    return _JS.Get<Browser?>("chrome");

    // Browser.cs lines 23 and 40
    public Storage? Storage => JSRef!.Get<Storage?>("storage");
    public Offscreen? Offscreen => JSRef!.Get<Offscreen?>("offscreen");

    // Storage/Storage.cs lines 20 and 24
    public StorageArea Sync => JSRef!.Get<StorageArea>("sync");
    public StorageArea Local => JSRef!.Get<StorageArea>("local");

    // Storage/StorageArea.cs lines 54 and 70 (chrome.storage.<area>.get /
.set)
    using var tmp = await JSRef!.CallAsync<string, SpawnJSObject?>("get",
key);
    public Task Set(string key, object value) =>
JSRef!.CallVoidAsync("set", new Dictionary<string, object> { { key, value }
});

    // Offscreen/Offscreen.cs lines 18, 22 and 26 (chrome.offscreen.*)
    public Task CreateDocument(OffscreenCreateParameters parameters) =>
JSRef!.CallVoidAsync("createDocument", parameters);
    public Task CloseDocument() => JSRef!.CallVoidAsync("closeDocument");
    public Task<bool> HasDocument() =>
JSRef!.CallAsync<bool>("hasDocument");

https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension/blob/05a55d04e820c69a2f2e66c9ede51d18431a3428/SpawnDev.SpawnJS.BrowserExtension/Services/BrowserExtensionService.cs#L12
https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension/blob/05a55d04e820c69a2f2e66c9ede51d18431a3428/SpawnDev.SpawnJS.BrowserExtension/Browser.cs#L23-L40
https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension/blob/05a55d04e820c69a2f2e66c9ede51d18431a3428/SpawnDev.SpawnJS.BrowserExtension/Storage/StorageArea.cs#L54-L70
https://github.com/LostBeard/SpawnDev.SpawnJS.BrowserExtension/blob/05a55d04e820c69a2f2e66c9ede51d18431a3428/SpawnDev.SpawnJS.BrowserExtension/Offscreen/Offscreen.cs#L18-L26

2. "storage": used on every page that has images or videos

chrome.storage.sync stores the user's settings: 3D on/off (global and per
site), image and video toggles, 3D level, focus, color profile and toolbar
state. chrome.storage.local stores the Options page settings.

    // Anaglyphohol/ExtensionContent/DefaultOverlay.razor.cs
    SyncStorage = BrowserExtensionService.Browser!.Storage!.Sync;
                // line 66
    AnaglyphEnabledGlobal = await
SyncStorage.Get<int>(AnaglyphEnabledGlobalKey, 1); // line 74
    if (SyncStorage != null) await
SyncStorage.Set(AnaglyphEnabledGlobalKey, AnaglyphEnabledGlobal); // line
125

    // Anaglyphohol/Pages/Options.razor
    var local = BrowserExtensionService.Browser?.Storage?.Local;
                   // lines 39 and 47

https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/ExtensionContent/DefaultOverlay.razor.cs#L66-L175
https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Layout/ContentOverlay.razor.cs#L121
https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Pages/Options.razor#L39-L47

3. "offscreen": used by the Shared 3D converter

"Shared 3D converter" is a checkbox on the Options page. When it is on, the
service worker creates an offscreen document (reason WORKERS) that runs one
depth and 3D converter for all tabs, so each tab does not need its own. The
service worker also checks for the document with hasDocument and closes a
broken one with closeDocument.

    // Anaglyphohol/Background/ConverterRelayService.cs
    var offscreen = BES.Browser?.Offscreen;
    if (!await offscreen.HasDocument())
                  // line 89
    {
        _creating ??= offscreen.CreateDocument(new
OffscreenCreateParameters // line 92
        {
            Url = SharedConverter.DocumentPath,
            Reasons = new[] { OffscreenReason.Workers },
            Justification = "Runs Anaglyphohol's shared depth + 3D
converter (WebGPU) for every tab.",
        });
    }
    ...
    if (offscreen != null && await offscreen.HasDocument()) await
offscreen.CloseDocument(); // line 61

https://github.com/LostBeard/AnaglyphoholV2/blob/11b6484e1383bbd9195a2029983a2794f80a755d/Anaglyphohol/Background/ConverterRelayService.cs#L55-L100

To see it run: open the Options page, check "Shared 3D converter", then
open a page with a video. chrome://extensions > Anaglyphohol > "Inspect
views" lists the offscreen document.

Removing either permission would break the extension: without "storage" no
settings are saved, and without "offscreen" the Shared 3D converter cannot
work. Please re-review the submission.

Thank you,
Todd Tanner
```

Next: [Appeal denied again](2026-10-10-appeal-denied-again.md)
