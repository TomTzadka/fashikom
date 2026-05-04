# Fashikom — אתר ייעוץ אופנה

אתר סטטי דו-לשוני (עברית / אנגלית) ללא build step, ללא npm, ללא framework.

---

## הפעלה מקומית

האתר משתמש ב-`fetch('./content.json')` ולכן **חייב** לרוץ מתוך שרת HTTP — פתיחה ישירה מהקובץ (`file://`) לא תעבוד.

```bash
# מתוך תיקיית הפרויקט:
python3 -m http.server 8080
```

פתח בדפדפן: [http://localhost:8080](http://localhost:8080)

### פורט תפוס?

```bash
lsof -ti :8080 | xargs kill -9
python3 -m http.server 8080
```

---

## פריסה לרשת (Vercel)

### דרישות מוקדמות

- חשבון [Vercel](https://vercel.com) (חינמי)
- Vercel CLI מותקן:

```bash
npm i -g vercel
```

### כניסה ראשונה

```bash
vercel login
```

### קישור הפרויקט (פעם אחת בלבד)

```bash
vercel link
```

### פריסה לסביבת Production

```bash
git add <files> && git commit -m "תיאור השינוי"
git push
vercel --prod --yes
```

האתר זמין בכתובת: **https://fashikom.vercel.app**

### פריסת Preview (לבדיקה לפני production)

```bash
vercel
```

מחזיר URL זמני כגון `https://fashikom-abc123.vercel.app`.

---

## מבנה הפרויקט

```
fashikom/
├── index.html       # כל ה-HTML, CSS, ו-JS (קובץ יחיד)
├── content.json     # כל הטקסטים בעברית ובאנגלית
├── src/
│   └── shirili_main_photo.png
└── vercel.json      # הגדרות headers לאבטחה
```

---

## עריכת תוכן

כל הטקסטים נמצאים ב-`content.json` — אין צורך לגעת ב-`index.html` לשם שינוי תוכן בלבד.

מבנה הקובץ:

```json
{
  "he": { ... },        // כל הטקסטים בעברית
  "en": { ... },        // כל הטקסטים באנגלית
  "articles": [ ... ]   // מאמרים (שדות טקסט לפי שפה)
}
```

אחרי עריכת `content.json`, פרוס מחדש עם `vercel --prod --yes`.
