# EPUB Reader for Google Drive

An "Open with" app for Google Drive: right-click an EPUB file in Drive → **Open with** → the book opens in the reader.

**Features:** reading position synced across devices (stored in the app's hidden folder in Drive), automatic RTL detection for Hebrew/Arabic books (with a manual override), light/sepia/dark themes, adjustable font size, table of contents, navigation by arrow keys / swipe / side taps, a "Continue reading" list, and opening local files.

**Permissions:** the app only requests `drive.file` (access only to files opened through the app, not your whole Drive), `drive.appdata` (to store reading positions) and `drive.install` (to appear in the "Open with" menu).

## Structure

```
index.html      The whole app (single file)
icons/          Icons in sizes 16–256 (for the Drive UI integration and favicon)
```

## Setup

### 1. Hosting (GitHub Pages)
1. Settings → Pages → Deploy from branch → `main` / root.
2. The site will be available at `https://lraviv.github.io/epub-reader/`.

### 2. Google Cloud project
1. In the [Google Cloud Console](https://console.cloud.google.com/), create a new project.
2. APIs & Services → Library: enable **Google Drive API** and **Google Picker API**.

### 3. OAuth consent screen
1. User type: **External**.
2. Fill in the app name and email.
3. Scopes: add `drive.file`, `drive.appdata`, `drive.install`.
4. Test users: add your own email. (Testing mode is enough for personal use, and no Google verification is needed.)

### 4. Credentials
1. **OAuth client ID** → Web application → Authorized JavaScript origins: `https://lraviv.github.io` (no path, no trailing `/`).
2. **API key** → recommended restrictions: API restrictions → Google Picker API, and Website restrictions → `https://lraviv.github.io/*`.
3. Get the **Project number** from the project Dashboard.

### 5. Fill in the config
At the top of the `<script>` in `index.html`:

```js
const CONFIG = {
  CLIENT_ID: '1234-abc.apps.googleusercontent.com',
  API_KEY:   'AIza...',
  APP_ID:    '123456789012',
};
```

The Client ID and API key are not secrets (they're exposed in the browser anyway); the restrictions from step 4 are what protect them.

### 6. Drive UI integration (adds "Open with")
APIs & Services → Enabled APIs → **Google Drive API** → **Drive UI integration** tab:

| Field | Value |
|---|---|
| Application name | EPUB Reader |
| Application icons | Upload the files from `icons/` (transparent PNG) |
| Open URL | `https://lraviv.github.io/epub-reader/` |
| Default MIME types | `application/epub+zip` |
| Default file extensions | `epub` |
| Creating files | Leave unchecked |

Do **not** check the option to automatically show the OAuth consent screen (it's deprecated). Click Submit.

### 7. First run
1. Open the site and click "Choose a book from Google Drive". Sign in and approve the permissions, including the Drive install.
2. From then on, in Drive on the web: right-click an EPUB → Open with → EPUB Reader. (The icon can take up to 24 hours to appear, but the menu item shows up right away.)

## Notes
- **Mobile:** the Drive apps for iPhone/Android don't show web apps under "Open with". On a phone, open the site directly (you can "Add to Home Screen") and pick a book or continue from the "Continue reading" list. Positions stay in sync.
- **Keys:** ← / → follow the book's direction; PageUp/PageDown and Space also work.
- **Local books:** open without signing in to Google; the position is saved only in the current browser.
- **Shared drives:** supported (`supportsAllDrives`). To have the app show up in "Open with" there too, check Shared drives support in the UI integration settings.
