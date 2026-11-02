# קורא EPUB ל-Google Drive

אפליקציית "פתיחה באמצעות" ל-Google Drive: קליק ימני על קובץ EPUB ב-Drive ← **פתיחה באמצעות** ← הספר נפתח בקורא.

**יכולות:** שמירת מיקום קריאה שמסתנכרנת בין מכשירים (בתיקייה מוסתרת של האפליקציה ב-Drive), זיהוי אוטומטי של RTL לספרים בעברית/ערבית (עם מעבר ידני), מצב בהיר/ספיה/לילה, שינוי גודל גופן, תוכן עניינים, ניווט בחיצים/החלקה/לחיצה בצדדים, רשימת "המשך קריאה", ופתיחת קובץ מקומי.

**הרשאות:** האפליקציה מבקשת רק `drive.file` (גישה לקבצים שפתחת דרכה בלבד, לא לכל ה-Drive), `drive.appdata` (לשמירת המיקום) ו-`drive.install` (כדי להופיע בתפריט "פתיחה באמצעות").

## מבנה

```
index.html      האפליקציה כולה (קובץ אחד)
icons/          אייקונים בגדלים 16–256 (ל-Drive UI integration ולפאביקון)
```

## הקמה

### 1. אחסון באתר (GitHub Pages)
1. יוצרים repo חדש (למשל `epub-reader`) ומעלים אליו את `index.html` ואת תיקיית `icons/`.
2. Settings ← Pages ← Deploy from branch ← `main` / root.
3. הכתובת תהיה `https://<username>.github.io/epub-reader/`.

### 2. פרויקט ב-Google Cloud
1. ב-[Google Cloud Console](https://console.cloud.google.com/) יוצרים פרויקט חדש.
2. APIs & Services ← Library: מפעילים את **Google Drive API** ואת **Google Picker API**.

### 3. מסך הסכמה (OAuth consent screen)
1. סוג משתמשים: **External**.
2. ממלאים שם אפליקציה ומייל.
3. Scopes: מוסיפים את `drive.file`, `drive.appdata`, `drive.install`.
4. Test users: מוסיפים את המייל שלך. (במצב Testing זה מספיק לשימוש אישי, ואין צורך באימות של גוגל.)

### 4. Credentials
1. **OAuth client ID** ← Web application ← Authorized JavaScript origins: `https://<username>.github.io` (בלי נתיב ובלי `/` בסוף).
2. **API key** ← מומלץ להגביל: API restrictions ← Google Picker API, ו-Website restrictions ← `https://<username>.github.io/*`.
3. את **Project number** לוקחים מדף ה-Dashboard של הפרויקט.

### 5. מילוי ההגדרות בקוד
בראש ה-`<script>` ב-`index.html`:

```js
const CONFIG = {
  CLIENT_ID: '1234-abc.apps.googleusercontent.com',
  API_KEY:   'AIza...',
  APP_ID:    '123456789012',
};
```

ה-Client ID וה-API key לא סודיים (הם ממילא נחשפים בדפדפן), וההגבלות מסעיף 4 הן מה שמגן עליהם.

### 6. Drive UI integration (מה שמוסיף את "פתיחה באמצעות")
APIs & Services ← Enabled APIs ← **Google Drive API** ← לשונית **Drive UI integration**:

| שדה | ערך |
|---|---|
| Application name | קורא EPUB |
| Application icons | להעלות את הקבצים מ-`icons/` (PNG שקוף) |
| Open URL | `https://<username>.github.io/epub-reader/` |
| Default MIME types | `application/epub+zip` |
| Default file extensions | `epub` |
| Creating files | לא לסמן |

**לא** לסמן את האפשרות להציג אוטומטית את מסך ההסכמה (היא deprecated). שומרים עם Submit.

### 7. הפעלה ראשונה
1. נכנסים לכתובת האתר ולוחצים "בחירת ספר מ-Google Drive". מתחברים ומאשרים את ההרשאות, כולל ההתקנה ב-Drive.
2. מעכשיו, ב-Drive בדפדפן: קליק ימני על EPUB ← פתיחה באמצעות ← קורא EPUB. (האייקון יכול לקחת עד 24 שעות להופיע, אבל הפריט עצמו מופיע מיד.)

## הערות
- **מובייל:** אפליקציות ה-Drive ל-iPhone/Android לא מציגות "פתיחה באמצעות" לאפליקציות ווב. בטלפון פותחים את האתר ישירות (אפשר "הוספה למסך הבית") ובוחרים ספר או ממשיכים מרשימת "המשך קריאה". המיקום מסונכרן.
- **מקשים:** ← / → לפי כיוון הספר, PageUp/PageDown, רווח.
- **ספר מקומי:** נפתח בלי חיבור ל-Google, והמיקום נשמר רק בדפדפן הנוכחי.
- **Drive שיתופי:** נתמך (`supportsAllDrives`). אם רוצים שיופיע ב-"פתיחה באמצעות" גם שם, מסמנים Shared drives support בהגדרות ה-UI integration.
