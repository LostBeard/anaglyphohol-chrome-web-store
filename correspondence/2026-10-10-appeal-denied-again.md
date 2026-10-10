# Appeal denied again: "We are not able to see the provided code"

- **From:** Chrome Web Store Developer Support (chromewebstore-dev-support@google.com)
- **To:** the developer
- **Date:** October 10, 2026, 02:18 Eastern (06:18 UTC)
- **Subject:** Re: [6-2323000042053] Appeal for fjbffnhfchidmfcbecccnmdedjahankc

The answer to [the code Google asked for](2026-10-09-developer-reply.md). Reproduced verbatim.

```text
Hello Developer,

Thank you for reaching out to us again. We reviewed your request and found
that the item with id : fjbffnhfchidmfcbecccnmdedjahankc and name:
Anaglyphohol is still not compliant with our use of permission, as we could
see that the extension’s requesting “storage” and “offscreen” permissions
which are not used in the code package.

We are not able to see the provided code in the extension package. So, we
request you to update by removing “storage” and “offscreen” permissions
from the manifest file. Hence, we request you to re-submit your item for a
review with corrective actions. Your submission will be approved if we find
it to be compliant with all our policies.

Do you believe this was a mistake?

Please reply to this email with evidence that the functionality is not
working after removing the permissions.

We value your contributions to the Chrome Web Store.

Thanks,

Chrome Web Store Developer Support

-----------------------------------------

Developer Terms of Service <https://developer.chrome.com/webstore/terms>

Program Policies <https://developer.chrome.com/webstore/program_policies>

Branding Guidelines <https://developer.chrome.com/webstore/branding>
```

## In plain terms

1. Google asked for code snippets. It got code snippets, file names, line numbers and permalinks.
2. Google replied that it cannot see that code in the package. The code is in the package, compiled to WebAssembly in `app/_framework/`. The names `storage`, `offscreen`, `createDocument` and `hasDocument` are stored as text inside `app/_framework/SpawnDev.SpawnJS.BrowserExtension.*.wasm`.
3. Google then asked the developer to remove both permissions and send "evidence that the functionality is not working". In other words: break the extension, then prove it is broken. Verifying that a permission is used is the review. It was handed back to the developer.

Back to the [index](README.md).
