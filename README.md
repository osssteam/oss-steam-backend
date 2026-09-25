# OSS Steam — Backend

Bu backend saytdagi buyurtma formasidan kelgan ma'lumotlarni qabul qiladi, bazaga
saqlaydi va **har bir yangi buyurtmani avtomatik ravishda Telegram botga** yuboradi.
Admin panel orqali buyurtmalar holatini kuzatib borasiz (yangi → jarayonda → bajarildi).

## Nima uchun Steam akkauntni o'zi avtomatik yaratmaydi

Steam'da yangi akkaunt ochish bosqichida captcha va tasdiqlash choralari bor.
Buni dasturiy ravishda aylanib o'tadigan bot yasash Steam qoidalarini buzadi va
firibgarlik uchun ishlatilishi mumkin, shuning uchun bu qism qo'shilmagan. Akkaunt
yaratish jarayonini operator (siz) qo'lda yoki o'zingiz tanlagan usulda bajarasiz,
so'ng admin paneldan buyurtmani "bajarildi" deb belgilaysiz.

## O'rnatish

1. Node.js 18+ o'rnatilgan bo'lishi kerak.
2. Paketlarni o'rnating:
   ```
   npm install
   ```
3. `.env.example` faylini nusxalab `.env` yarating va qiymatlarni to'ldiring:
   ```
   cp .env.example .env
   ```
   - `TELEGRAM_BOT_TOKEN` — @BotFather orqali yaratilgan bot tokeni.
   - `TELEGRAM_CHAT_ID` — xabarlar keladigan chat/kanal ID (@userinfobot orqali oling).
   - `ADMIN_TOKEN` — o'zingiz o'ylab topgan uzun, tasodifiy maxfiy kalit.
   - `ALLOWED_ORIGIN` — saytingiz manzili (masalan `https://osssteam.uz`).
4. Serverni ishga tushiring:
   ```
   npm start
   ```
5. Sayt tomonida `oss-steam.html` faylidagi `API_ENDPOINT` o'zgaruvchisiga
   serveringiz manzilini yozing, masalan:
   ```js
   var API_ENDPOINT = "https://sizning-domeningiz.com/api/order";
   ```

## Qayerga joylashtirish mumkin

Bu oddiy Node.js server — Railway, Render, VPS (Ubuntu + PM2/Nginx) yoki shunga
o'xshash har qanday joyda ishga tushirish mumkin. Production'da albatta HTTPS
(SSL sertifikat) yoqilgan bo'lishi shart, aks holda ma'lumotlar ochiq uzatiladi.

## Xavfsizlik choralari (nima qilingan)

- **helmet** — standart HTTP xavfsizlik header'lari
- **CORS** — faqat `ALLOWED_ORIGIN`da ko'rsatilgan domendan so'rovlarga ruxsat
- **Rate limiting** — umumiy va buyurtma endpointiga alohida spam/bot cheklovi
- **Input validatsiya** — telefon, ism, nik, parol uzunligi va formati tekshiriladi
- **Boshqaruvchi belgilar filtri** — oddiy in'ektsiya urinishlariga qarshi
- **Parol hash** — mijoz bergan parol bazada ochiq emas, SHA-256 hash holida saqlanadi
- **Admin panel himoyasi** — `x-admin-token` maxsus kalitisiz hech kim buyurtmalarni ko'ra olmaydi
- **JSON hajmi cheklangan** (20kb) — katta so'rovlar bilan yuklashning oldini oladi

## Muhim eslatma — kafolat haqida

Hech qanday sayt yoki server 100% "buzilmas" bo'lmaydi. Yuqoridagilar standart
va keng qo'llaniladigan himoya choralari, lekin siz production'ga chiqarishdan
oldin quyidagilarni ham qiling:
- Serverni doimiy yangilab turing (`npm audit`, `npm update`)
- `.env` faylini hech qachon GitHub'ga yuklamang
- Muntazam zaxira nusxa (`orders.db` faylini backup qiling)
- Agar yuqori trafik kutilsa, professional xavfsizlik auditidan o'tkazing

## API

| Endpoint | Usul | Tavsif |
|---|---|---|
| `/api/order` | POST | Yangi buyurtma qabul qiladi, Telegramga yuboradi |
| `/api/admin/orders` | GET | Buyurtmalar ro'yxati (admin token kerak) |
| `/api/admin/orders/:id/status` | POST | Buyurtma holatini yangilash (admin token kerak) |
| `/api/health` | GET | Server ishlayotganini tekshirish |
| `/admin.html` | GET | Admin panel sahifasi |
