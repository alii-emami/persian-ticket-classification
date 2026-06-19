# ✈️ Persian Ticket Classification API

## 📌 معرفی پروژه

این پروژه یک **API سبک، سریع و قابل توسعه** برای دسته‌بندی تیکت‌های پشتیبانی کاربران در حوزه **سفر هوایی** است.  
سیستم، متن فارسی را دریافت کرده و آن را در یکی از سه دسته طبقه‌بندی می‌کند:

| دسته | نام فارسی | توضیح |
|------|-----------|-------|
| `flight` | ✈️ پرواز | زمان، مسیر، موجودی پرواز، ساعت حرکت و رسیدن |
| `airfare` | 💰 هزینه بلیط | قیمت، ارزان‌ترین گزینه، کلاس اقتصادی/بیزینس |
| `ground_service` | 🚖 خدمات زمینی | تاکسی فرودگاه، اتوبوس، اجاره خودرو |

پروژه دو مدل را پشتیبانی می‌کند:  
- **Baseline:** مدل SVM با TF‑IDF (دقت $93.4\%$)  
- **مدل نهایی:** BERT فارسی فاین‌تیون شده (دقت $97.5\%$) – **تأکید اصلی روی این مدل است**

ساختار ماژولار، production‑ready، با قابلیت اجرا روی **CPU** و سازگار با اینترنت محدود می‌باشد.

---

## 🧠 سیر تکاملی مدل‌ها

### 1️⃣ مدل اولیه (SVM) – ۴ کلاس
- **الگوریتم:** LinearSVC + TF‑IDF  
- **دقت:** $93.39\%$  
- **گزارش:**

| کلاس | Precision | Recall | $F_1$-score |
|------|-----------|--------|-------------|
| airfare | $0.74$ | $0.96$ | $0.84$ |
| flight | $0.94$ | $0.98$ | $0.96$ |
| ground_service | $0.92$ | $1.00$ | $0.96$ |
| other | $0.97$ | $0.76$ | $0.85$ |
| **weighted avg** | **$0.94$** | **$0.93$** | **$0.93$** |

### 2️⃣ مدل نهایی (BERT) – ۳ کلاس (حذف `other` برای افزایش دقت)
- **معماری:** `HooshvareLab/bert-fa-base-uncased` فاین‌تیون شده روی Persian-ATIS  
- **دقت:** **$97.48\%$**  
- **گزارش کامل روی تست:**

| کلاس | Precision | Recall | $F_1$-score | Support |
|------|-----------|--------|-------------|---------|
| airfare | $0.88$ | $0.88$ | $0.88$ | $56$ |
| flight | $0.99$ | $0.99$ | $0.99$ | $469$ |
| ground_service | $1.00$ | $0.97$ | $0.98$ | $30$ |
| **accuracy** | | | **$0.97$** | $555$ |
| **weighted avg** | **$0.97$** | **$0.97$** | **$0.97$** | |

مدل BERT با درک عمیق‌تر متن، خطای بسیار کمتری دارد و برای محیط واقعی مناسب‌تر است.

---

## 🗂️ دیتاست

داده‌های آموزش و ارزیابی از **Persian-ATIS** گرفته شده است:  
🔗 [https://github.com/DSInCenter/Persian-Atis](https://github.com/DSInCenter/Persian-Atis)

این دیتاست شامل پرسش‌های فارسی درباره پرواز، قیمت، خدمات فرودگاهی و موارد جانبی است.

---

## 🏗️ ساختار پروژه (ماژولار)
```text
new-pr/
├── app/
│   ├── main.py                    # نقطه ورود FastAPI
│   ├── api/routes.py              # تعریف endpointها
│   ├── core/
│   │   ├── config.py              # تنظیمات (مسیر مدل، لاگ، محدودیت توکن)
│   │   ├── preprocessing.py       # اعتبارسنجی ورودی (حروف فارسی، تعداد کلمات)
│   │   └── classifier.py          # بارگذاری مدل BERT و تابع predict
│   ├── schemas/request_response.py # مدل‌های Pydantic
│   └── templates/index.html       # رابط کاربری گرافیکی
├── models/                        # فایل‌های مدل BERT (config.json, model.safetensors, tokenizer, ...)
├── requirements.txt
└── README.md
```
---

## 🚀 نصب و راه‌اندازی

### پیش‌نیازها
- Python $3.9+$

### ۱. کلون پروژه
bash
git clone https://github.com/your-username/persian-ticket-classification.git
cd persian-ticket-classification

### ۲. ایجاد محیط مجازی (توصیه می‌شود)
bash
python -m venv venv
source venv/bin/activate        # لینوکس / Mac
venv\Scripts\activate           # ویندوز

### ۳. نصب وابستگی‌ها
bash
pip install -r requirements.txt

محتوای `requirements.txt`:
text
fastapi==0.110.0
uvicorn==0.44.0
torch==2.12.0
transformers==4.53.1
hazm==0.7.0
numpy==1.26.4
Jinja2==3.1.6
python-multipart

### ۴. قرار دادن فایل‌های مدل
فایل‌های مدل BERT فاین‌تیون شده (شامل `config.json`، `model.safetensors`، `tokenizer.json`، `vocab.txt` و ...) را در پوشه `models/` قرار دهید.

### ۵. اجرای سرویس
bash
uvicorn app.main:app --reload

سپس لینک‌های زیر را در مرورگر باز کنید:
- **رابط کاربری (UI):** [http://localhost:8000](http://localhost:8000)
- **مستندات Swagger:** [http://localhost:8000/docs](http://localhost:8000/docs)
- **بررسی سلامت (Health Check):** [http://localhost:8000/health](http://localhost:8000/health)

---

## 🖥️ رابط کاربری (UI)

رابط کاربری این پروژه یک صفحه وب HTML ساده، سبک و واکنش‌گرا است. در این صفحه می‌توانید متن تیکت خود را وارد کرده و نتیجه دسته‌بندی را به همراه نوار درصد اطمینان (Confidence) به صورت در لحظه مشاهده کنید.

---

## 📡 API Reference

### `POST /predict`
دسته‌بندی متن فارسی.

**درخواست:**
json
{
  "text": "قیمت بلیط تهران به مشهد برای فردا چقدر است؟"
}

**پاسخ موفق (200):**
json
{
  "label": "airfare",
  "label_fa": "هزینه بلیط",
  "confidence": 0.9834,
  "cleaned_text": "قیمت بلیط تهران به مشهد برای فردا چقدر است؟"
}

**خطاها:**
- `400` – متن خالی، فاقد حروف فارسی، یا تعداد کلمات خارج از بازه ۲ تا ۱۰۰
- `422` – خطای اعتبارسنجی Pydantic
- `500` – خطای داخلی سرور

### `GET /health`
بررسی سلامت سرویس:

**پاسخ:**
json
{
  "status": "alive",
  "model_loaded": true
}

---

## 🧪 نمونه تست با cURL

bash
curl -X POST "http://localhost:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{"text":"آیا پرواز مستقیم از اصفهان به کیش دارید؟"}'

**خروجی:**
json
{
  "label": "flight",
  "label_fa": "پرواز",
  "confidence": 0.9912,
  "cleaned_text": "آیا پرواز مستقیم از اصفهان به کیش دارید؟"
}

---

## ✅ قوانین اعتبارسنجی ورودی
قبل از ارسال به مدل، متن ورودی بررسی می‌شود:
- نباید خالی باشد.
- حداقل یک حرف فارسی داشته باشد.
- تعداد توکن‌ها (کلمات جدا شده با فاصله و علائم) بین ۲ تا ۱۰۰ باشد.

*در صورت نقض هر یک از موارد فوق، خطای ۴۰۰ با پیام مناسب برگردانده می‌شود.*

---

## 🧰 تکنولوژی‌های استفاده شده
- **FastAPI** – چارچوب وب
- **Transformers + PyTorch** – اجرای مدل BERT
- **Hazm** – پردازش زبان فارسی (نرمال‌سازی، توکن‌سازی، لماتایز)
- **Jinja2** – قالب‌سازی HTML
- **Uvicorn** – سرور ASGI

---

## 📄 مجوز
این پروژه تحت مجوز **MIT** منتشر شده است.

---

## 👨‍💻 نویسنده
- **[نام شما]** – [لینک گیت‌هاب شما](https://github.com/your-username) – `your.email@example.com`

---

## ⭐ قدردانی
- از **Persian-ATIS** برای دیتاست با کیفیت.
- از **HooshvareLab** برای مدل پایه BERT فارسی.

