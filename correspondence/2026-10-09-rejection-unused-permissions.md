# Rejection 2: "Requesting but not using" storage and offscreen (Purple Potassium)

- **From:** Chrome Web Store (chromewebstore-noreply@google.com)
- **To:** the developer
- **Date:** October 9, 2026, 04:43 Eastern (08:43 UTC)
- **Subject:** Chrome Web Store: Rejection notification for Anaglyphohol

Reproduced verbatim.

```text
Chrome Web Store

Hi there,

We regret to inform you that the most recent submission of your item was
rejected. Please find the details below.

Item name: Anaglyphohol

Item ID: fjbffnhfchidmfcbecccnmdedjahankc

Violation(s):

Use of Permissions:

Violation reference ID: Purple Potassium
Violation:
Requesting but not using the following permission(s):

storage
offscreen

How to rectify: Perform a full review of your extension's permissions:

Remove the unused permission(s) listed above from your manifest file.
Audit all other permissions requested by your item. Ensure every permission
is actively used and necessary for your item's functionality.

Relevant section of the program policy:

Request access to the narrowest permissions necessary to implement your
Product's features or services. Don't attempt to "future proof" your
Product by requesting a permission that might benefit services or features
that have not yet been implemented. (learn more)

This violation was detected as a result of an internal review. Learn more
about enforcement here.

Do you believe this was a mistake?

If you believe that this decision was made in error, please initiate an
appeal request by following these steps:

Log into your Chrome Web Store Developer Dashboard.
Select the relevant item from your list.
Navigate to the Build > Status page.
Locate the corresponding item revision (either the draft or the published
version).
Click the Appeal button to submit your request for review.

If you are located in the EU, you can also learn about additional redress
options that may be available to you here.

Routing ID: FZSL

We value developer contributions to the Chrome Web Store, and look forward
to helping you bring your item into compliance with our policies.

Thanks,

Chrome Web Store Developer Support

------------------------------------------------------
Developer Terms of Service
Program Policies
Branding Guidelines

Google
© 2026 Google LLC, 1600 Amphitheatre Parkway, Mountain View, CA 94043 , USA
Email Preferences: You have received this mandatory email service
announcement to update you about important changes to your Chrome Web Store
developer account.

The English language version of the Developer Program Policies will control
and translations, if any, are non-binding and for reference only.
```

## The facts

- `storage` saves every user setting. `offscreen` runs the Shared 3D converter option. See the [main README](../README.md#rejection-2-requesting-but-not-using-storage-and-offscreen-purple-potassium).
- Version 3.0.14, live on the Chrome Web Store when this was sent, requests the same two permissions.

Next: [Appeal denied](2026-10-09-appeal-denied.md)
