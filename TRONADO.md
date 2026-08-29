# درگاه ترونادو برای میرزا بات

<div dir="rtl">

این نسخه از میرزا بات، درگاه **ترونادو** را دارد: خریدار داخل مینی‌اپ ترونادو
کارت‌به‌کارت پرداخت می‌کند و فروشگاه **معادل آن را به ترون (TRX)** در ولت خودش
دریافت می‌کند. کارمزد ترونادو روی خریدار است؛ فروشگاه دقیقاً همان مقدار ترونی را
که فاکتور کرده دریافت می‌کند و ربات همان مبلغ تومانی فاکتور را به حساب خریدار
اضافه می‌کند (مثل بقیه درگاه‌ها).

## پیش‌نیازها

1. یک حساب کسب‌وکار در ترونادو (@trndsupport) و از آن‌ها دو مقدار بگیرید:
   **کلید API** و **کلید امضای IPN**.
2. یک آدرس ولت ترون (TRC20، با `T` شروع می‌شود، ۳۴ کاراکتر). بهتر است ولتی
   باشد که قبلاً فعال شده (تراکنش داشته)؛ ولت فعال‌نشده باعث می‌شود اولین
   خریدار حدود ۱٫۲ ترون بابت فعال‌سازی ولت اضافه بپردازد.
3. (توصیه‌شده) دامنه ربات خود را به پشتیبانی ترونادو بدهید تا آدرس کال‌بک
   `https://دامنه-شما/payment/tronado.php` را در لیست مجاز ثبت کنند. برای یک
   حساب کاملاً جدید الزامی نیست (پیش‌فرض باز است)، ولی برای امنیت (جلوگیری از
   سوءاستفاده در صورت لو رفتن کلید API) توصیه می‌شود.

## نصب

روی یک سرور تمیز Ubuntu 22.04 / 24.04 با کاربر root:

```
curl -o install.sh -L https://raw.githubusercontent.com/tronadorobot/mirzabot/main/install.sh && bash install.sh
```

همان نصاب میرزا است؛ فقط از این مخزن نصب و آپدیت می‌کند. اگر میرزا را از
مخزن اصلی نصب کرده‌اید، همین دستور را بزنید و گزینه **Update** را انتخاب کنید
(گزینه «Automatic» آخرین نسخه‌ی این مخزن را می‌گیرد، مثلاً `0.4.1-tronado.1`).

## تنظیم در ربات ادمین

1. ربات ادمین → **تنظیمات پرداخت** → ردیف **📌 ترونادو** → دکمه **⚙️ تنظیمات**.
2. دکمه‌ها را یکی‌یکی بزنید و مقدار بفرستید:
   - **🔑 کلید API ترونادو**
   - **🔏 کلید امضای IPN ترونادو**
   - **👛 ولت ترون (TRX) ترونادو** — ترونِ هر پرداخت به این ولت واریز می‌شود
   - **⬇️ حداقل مبلغ** / **⬆️ حداکثر مبلغ** (تومان؛ پیش‌فرض ۵۰٬۰۰۰ تا ۵۰۰٬۰۰۰)
   - **🎁 کش‌بک** (درصد؛ ۰ یعنی خاموش)
   - **📚 آموزش** (اختیاری؛ عکس/ویدئو/متنی که قبل از پرداخت به خریدار نشان داده می‌شود)
3. به ردیف ترونادو برگردید و آن را **روشن** کنید.
   دکمه‌ی پرداخت برای خریدار فقط وقتی ظاهر می‌شود که هر سه مقدار (کلید API،
   کلید IPN و ولت) ثبت شده باشد.

## خریدار چه می‌بیند

در «افزایش موجودی» / خرید، دکمه‌ی **💳 پرداخت کارت به کارت با ترونادو** را می‌زند،
به مینی‌اپ ترونادو می‌رود، کارت‌به‌کارت می‌کند و رسید می‌فرستد. بعد از تأیید
ترونادو، ربات خودکار موجودی/سرویس را تحویل می‌دهد و در کانال گزارش شما
`💵 پرداخت جدید` ثبت می‌شود.

## اگر پرداختی شارژ نشد

ربات به کانال گزارش پیام `⭕️ پرداخت ترونادو … شارژ نشد: <دلیل>` می‌فرستد و هیچ
چیزی خودکار واریز نمی‌کند تا یک نفر بررسی کند. رایج‌ترین دلایل:

| دلیل | معنی |
|---|---|
| `amount short (x of y TRX)` | بعد از ساخت سفارش، مبلغ در ترونادو کمتر تأیید شده (مثلاً کارت‌به‌کارت کمتر از فاکتور). خودتان تصمیم بگیرید چقدر شارژ شود. |
| `order record unsealed or tampered` | کلید امضای IPN را وسط سفارش‌های باز عوض کرده‌اید، یا رکورد سفارش در دیتابیس دستکاری شده. این سفارش را دستی شارژ کنید. |
| `status check unavailable` (در error_log) | سرور شما در آن لحظه به ترونادو نرسیده؛ ترونادو تا چند ساعت دوباره تلاش می‌کند و کرون هم هر ۳ دقیقه سفارش‌های باز را چک می‌کند. |

**کلید امضای IPN را بی‌دلیل عوض نکنید**؛ سفارش‌های در جریان با کلید قدیمی مهر
شده‌اند و بعد از تعویض، به‌صورت «tampered» گزارش می‌شوند.

## کرون

اولین باری که بعد از آپدیت وارد پنل ادمین شوید، ربات خودش خط کرون
`*/3 * * * * curl https://دامنه-شما/cronbot/tronado.php` را اضافه می‌کند.
لازم نیست کاری بکنید.

</div>

---

# Tronado gateway for Mirza Bot

This build of Mirza Bot ships the **Tronado** gateway: the buyer pays
card-to-card inside the Tronado mini app and the shop receives the
**equivalent in TRON (TRX)** in its own wallet. Tronado's fee is on the buyer;
the shop receives exactly the TRX it invoiced and credits the buyer its own
Toman price, like every other gateway.

## Requirements

1. A Tronado business account (@trndsupport) and, from them, an **API key** and
   an **IPN signing key**.
2. A TRON wallet address (TRC20, starts with `T`, 34 characters). Prefer one
   that is already activated (has had a transaction); an unactivated wallet
   makes the first buyer pay ~1.2 TRX extra for on-chain activation.
3. (Recommended) Give Tronado support your bot's domain so they whitelist the
   callback URL `https://your-domain/payment/tronado.php`. Not strictly
   required for a brand-new account (it is open by default), but recommended
   for security — it stops a leaked API key from redirecting your IPNs.

## Install

On a clean Ubuntu 22.04 / 24.04 server, as root:

```
curl -o install.sh -L https://raw.githubusercontent.com/tronadorobot/mirzabot/main/install.sh && bash install.sh
```

It is the stock Mirza installer, pointed at this repository for install and
update. An existing upstream install: run the same command, pick **Update**;
"Automatic" takes this repository's latest release (e.g. `0.4.1-tronado.1`).

## Shop setup (admin bot)

1. Admin bot → **Payment settings** → the **📌 Tronado** row → **⚙️ Settings**.
2. Set, one button at a time: **API key**, **IPN signing key**, **TRX wallet**,
   **min** / **max** amount (Toman; defaults 50,000–500,000), **cashback** (%,
   0 = off), optional **tutorial** media shown to the buyer.
3. Back on the Tronado row, switch it **on**. The buyer button appears only
   once API key, IPN key and wallet are all set.

## What the buyer sees

On top-up / purchase they tap **💳 Card-to-card via Tronado**, land in the
Tronado mini app, transfer card-to-card and upload the receipt. Once Tronado
accepts it, the bot delivers the balance/service automatically and posts
`💵 New payment` to your report channel.

## When a payment is not credited

The bot posts `⭕️ … not credited: <reason>` to the report channel and credits
nothing until a human looks. Common reasons:

| reason | meaning |
|---|---|
| `amount short (x of y TRX)` | the order was repriced on Tronado's side after creation (transfer smaller than the invoice). Decide the credit yourself. |
| `order record unsealed or tampered` | the IPN signing key was rotated while orders were open, or the order row was edited in the DB. Credit that order by hand. |
| `status check unavailable` (in error_log) | your server could not reach Tronado at that moment; Tronado retries for hours and the cron re-checks open orders every 3 minutes. |

Do not rotate the IPN signing key casually: in-flight orders are sealed with
the old key and will be reported as tampered afterwards.

## Cron

The first time an admin opens the admin panel after updating, the bot adds
`*/3 * * * * curl https://your-domain/cronbot/tronado.php` itself. Nothing
to do.

## Source

Gateway code: `payment/tronado.php` (signed IPN receiver),
`cronbot/tronado.php` (poll fallback), the `tronado*` helpers in
`function.php`, buyer flow in `index.php`, admin settings in `admin.php`,
migration `db/migrations/009_tronado_gateway.php`. Licensed with the rest of
Mirza Bot (AGPL-3.0-or-later).
