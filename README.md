# YesBut AI

**A room of AI models that work on your question together, on your own computer.**

Most AI tools put one model behind one chat box. YesBut AI treats a model as a
*participant*: several of them, from different vendors, discuss the same
question in one room, challenge each other, and hand their conclusion to the
next step. You bring your own API keys; the program runs on your machine and
keeps your chats, files and keys there.

## What it does

- **Conference rooms.** Several models in one thread, over several rounds,
  answering as they type. You choose which agents take part.
- **Private chats** with a single agent, and **task chats** that run a
  workflow for each message.
- **Workflows.** An ordered list of rows; the agents inside a row work in
  parallel, and their combined answer becomes the next row's input. Run one
  by hand or on a schedule.
- **Specialists.** A domain expert assembled by having several models write
  its instructions together, choose its tools and seed its memory.
- **Agent memory.** Each agent can remember earlier answers and recall the
  relevant ones.
- **Image, speech and video generation**, with an optional "director" agent
  that turns what the room decided into the actual generation prompt.
- **Files.** Upload a document and refer to it in any prompt with `@file:12`
  or `@file:report.pdf`.
- **Connectors.** GitHub, GitLab, Wikipedia, arXiv, Google Search, Hugging
  Face, Discord and Slack, for agents that need live information.
- **Several accounts** on one installation, each with its own chats and files,
  and an administrator who manages them. The administrator sets the AI
  provider keys once for the installation; each account connects its own
  GitHub, GitLab, Slack, Discord or Hugging Face.
- **Seven interface languages:** English, Persian, Arabic, German, Spanish,
  Russian and Chinese.

## AI providers

You need a key from at least one provider, or a local model.

| Provider | What you need |
|---|---|
| OpenAI | an API key |
| Anthropic | an API key |
| Google (Gemini) | an API key |
| OpenRouter | one key for many models |
| AI/ML API | one key for many models, including image, speech and video |
| Any OpenAI-compatible endpoint | its address and key |
| Ollama | installed on your computer; no key |
| LM Studio | running on your computer; no key |

The header shows a green light for a provider only after the program has
checked that its key really works.

## Download and install

Download from the official release page only:
**<https://github.com/khosropk7-hash/yesbut-ai/releases/latest>**

| Your computer | File |
|---|---|
| Windows 10/11, 64-bit | `YesButAI-<version>-windows-x64.zip` |
| macOS, Apple silicon | `YesButAI-<version>-macos-arm64.tar.gz` |
| Linux, 64-bit | `YesButAI-<version>-linux-x64.tar.gz` |
| Docker (Linux, 64-bit) | `YesButAI-<version>-docker-image.tar.gz` |

**[INSTALL.md](INSTALL.md) has the full steps for each system**, including
what to click when Windows SmartScreen or macOS Gatekeeper warns about a
program that is not code-signed yet. In short:

- **Windows:** unblock the zip (Properties → Unblock), extract it, run
  `YesButAI.exe`.
- **macOS / Linux:** extract the archive and run `./YesButAI` in a terminal.
- **Docker:** `docker load -i YesButAI-<version>-docker-image.tar.gz`, then

  ```bash
  docker run -d --name yesbut -p 127.0.0.1:8642:8642 -v yesbut-data:/data yesbut-ai:<version>
  ```

Your browser opens on `http://127.0.0.1:8642/app` (with Docker, open that
address yourself).

## First run

1. **Create the administrator account.** The first account on an
   installation becomes its administrator. A user name and a password are
   enough; an email address is optional.
2. **Add a key.** Open **Connections**, choose a provider, paste your key.
   Keys are stored encrypted on your computer. A provider key is added by
   the administrator and is used by every account on the installation.
3. **Start a chat**, or build a row of agents and a workflow.

Forgot the administrator's password? `YesButAI --reset-password <username>`
prints a new one (see INSTALL.md).

## Updating

Stop the program, back up your data folder, download the new version, extract
it into a new folder and start it. The database is upgraded automatically.
The program does not update itself; administrators see a note in the app
when a newer version has been announced.

## Where your data is, and backing it up

Everything is in one folder:

| System | Folder |
|---|---|
| Windows | `%LOCALAPPDATA%\YesButAI` |
| macOS | `~/Library/Application Support/YesButAI` |
| Linux | `~/.local/share/yesbut-ai` |
| Docker | the volume mounted on `/data` |

It holds the database (`yesbut.db`), your uploaded files (`files/`) and
`settings.env` with this installation's two secret keys. **Stop the program,
then copy the whole folder.** The stored API keys can only be read together
with `settings.env`, so keep them together and treat the backup as sensitive.

## Privacy and network

The program runs on your computer and listens on `127.0.0.1` only, so other
computers cannot reach it. It sends no telemetry or analytics to the
publisher or to anyone else. (It counts a few anonymous steps, such as
sign-ups and first messages, in its own database; the installation's
administrator sees them as totals.) Its pages, fonts and scripts are part of
the program; nothing is loaded from a CDN.

It connects to the internet for exactly these things:

- **The AI providers and connectors you set up**, with your keys, when you use
  them. Your prompts and attached files go to the provider you chose. To
  show whether a key works, the program also asks each configured provider
  for its model list from time to time; that request carries no content.
- **Agent memory**, for agents you turn it on for. To store and find
  memories, the program sends the text being stored or looked up to Google
  if a Google key is set, otherwise to OpenAI, whichever provider the agent
  itself uses.
- **Announcements.** While you use the app, it fetches one small public file
  from GitHub, at most about every 15 minutes, to show announcements from the
  publisher and to tell administrators about a newer version. The request
  carries no account data, cookies or identifiers. Like any internet request
  it reveals your IP address to GitHub. If an announcement has an image or a
  video thumbnail, your browser loads it from the address it names (for
  example YouTube).

Your accounts, chats, files and API keys are never sent to the publisher.

## Questions

**The page does not open.** Look at the program's window: it prints the
address. If another copy is already running, starting it again just opens the
browser on that copy.

**Windows or macOS says the program is unsafe.** The builds are not
code-signed yet. Check your download against its `.sha256` file, then follow
INSTALL.md.

**A provider's light is amber, not green.** The key is saved but the provider
did not accept it, or could not be reached. Hover over the light for the
reason.

**Can I use it without any paid key?** Yes, with a local model through Ollama
or LM Studio. The app also lists providers that offer a free key.

**Can other people on my network use my installation?** Not by default. It
only answers on your own computer. The Docker image can be published on a
network on purpose; read INSTALL.md first.

## Known limitations

- The builds are not code-signed yet, so Windows and macOS warn on first run.
- No automatic updates.
- Announcements cannot be switched off.
- One running copy per data folder; it is a single-machine program, not a
  server cluster.
- Video generation works through AI/ML API only. A job that takes longer
  than the time limit is marked failed.
- Agent memory is a similarity search over what that agent stored. It is
  quick for hundreds of memories per agent, not for millions. It needs a
  Google or OpenAI key; without one it only finds exact matches.
- The program's tests use simulated provider responses. When a provider
  changes its API, a feature can break until an update fixes it.
- In Docker, a local Ollama or LM Studio needs two extra settings (see
  INSTALL.md).
- You pay your providers for what you use. A room of several large models
  over several rounds is expensive by design; the app does not cap your
  spending for you (it limits video jobs per hour only).
- Linux builds need glibc 2.35 or newer (for example Ubuntu 22.04). There is
  no build for Intel Macs yet.

## Licence

Proprietary software. All rights reserved. See [LICENSE](LICENSE) for the
end-user licence, and `THIRD_PARTY_NOTICES.txt` in the program folder for the
libraries it includes.

© 2026 Khosro

---

<div dir="rtl" lang="fa">

# YesBut AI — راهنمای فارسی

**اتاقی از مدل‌های هوش مصنوعی که با هم روی پرسش شما کار می‌کنند؛ روی کامپیوتر خودتان.**

بیشتر ابزارهای هوش مصنوعی یک مدل را پشت یک کادر گفتگو می‌گذارند. در YesBut AI
چند مدل از شرکت‌های مختلف در یک اتاق درباره‌ی یک پرسش بحث می‌کنند، حرف هم را
نقد می‌کنند و نتیجه را به مرحله‌ی بعد می‌دهند. کلید API را خودتان می‌آورید.
برنامه روی دستگاه شما اجرا می‌شود و گفتگوها، فایل‌ها و کلیدها همان‌جا می‌مانند.

## چه کارهایی می‌کند

- **اتاق کنفرانس:** چند مدل در یک گفتگو و در چند دور.
- **گردش کار (Workflow):** ردیف‌هایی از ایجنت‌ها که پشت سر هم اجرا می‌شوند؛ دستی یا زمان‌بندی‌شده.
- **متخصص‌ها:** ایجنتی که چند مدل با هم دستورالعمل، ابزار و حافظه‌اش را می‌سازند.
- **ساخت تصویر، صدا و ویدیو.**
- **فایل‌ها:** سند را بارگذاری کنید و با `@file:12` در هر پیام به آن اشاره کنید.
- **چند حساب کاربری** روی یک نصب، با یک مدیر.
- **هفت زبان:** فارسی، انگلیسی، عربی، آلمانی، اسپانیایی، روسی و چینی.

## نصب

فقط از صفحه‌ی رسمی دانلود کنید:
**<https://github.com/khosropk7-hash/yesbut-ai/releases/latest>**

- **ویندوز:** روی فایل zip راست‌کلیک کنید، Properties را باز کنید و تیک
  **Unblock** را بزنید. بعد آن را Extract کنید و `YesButAI.exe` را اجرا کنید.
  اگر پنجره‌ی آبی «Windows protected your PC» آمد، روی **More info** و سپس
  **Run anyway** بزنید.
- **مک و لینوکس:** فایل را از حالت فشرده خارج کنید و `./YesButAI` را در ترمینال اجرا کنید.
- **Docker:** راهنمای کامل در [INSTALL.md](INSTALL.md) است.

برنامه هنوز امضای دیجیتال ندارد؛ برای همین ویندوز و مک بار اول هشدار می‌دهند.
مراحل کامل هر سیستم‌عامل در [INSTALL.md](INSTALL.md) آمده است.

## اولین اجرا

1. **حساب مدیر را بسازید.** نخستین حساب، مدیر آن نصب می‌شود. نام کاربری و رمز
   کافی است و ایمیل اختیاری است.
2. **کلید اضافه کنید.** در بخش **Connections** سرویس را انتخاب کنید و کلید را
   وارد کنید. کلیدها رمزنگاری‌شده روی دستگاه شما ذخیره می‌شوند. کلید سرویس‌های
   هوش مصنوعی را مدیر وارد می‌کند و همه‌ی حساب‌های آن نصب از آن استفاده می‌کنند.
3. **گفتگو را شروع کنید.**

اگر رمز مدیر را فراموش کردید، دستور `YesButAI --reset-password <username>` رمز تازه می‌دهد.

## داده‌ها و پشتیبان‌گیری

همه‌چیز در یک پوشه است:

- ویندوز: `%LOCALAPPDATA%\YesButAI`
- مک: `~/Library/Application Support/YesButAI`
- لینوکس: `~/.local/share/yesbut-ai`

برای پشتیبان‌گیری، اول برنامه را ببندید و بعد کل پوشه را کپی کنید. فایل
`settings.env` را همراه پایگاه‌داده نگه دارید؛ کلیدهای ذخیره‌شده بدون آن خوانده نمی‌شوند.

## حریم خصوصی

برنامه روی کامپیوتر شما اجرا می‌شود و فقط از همان دستگاه در دسترس است. هیچ
آمار یا ردیابی‌ای برای ناشر یا کس دیگری نمی‌فرستد. (چند گام بی‌نام، مثل ثبت‌نام و
نخستین پیام، فقط در پایگاه‌داده‌ی خود برنامه شمرده می‌شود و مدیر آن نصب جمعشان
را می‌بیند.) صفحه‌ها، فونت‌ها و اسکریپت‌ها جزو خود برنامه‌اند و چیزی از CDN
بارگیری نمی‌شود. برنامه فقط در این موارد به اینترنت وصل می‌شود:

- **سرویس‌های هوش مصنوعی و اتصال‌هایی** (مثل GitHub) که خودتان تنظیم
  کرده‌اید، با کلید خودتان و وقتی از آن‌ها استفاده می‌کنید. پیام‌ها و فایل‌های
  پیوست به همان سرویسی می‌رود که انتخاب کرده‌اید. برنامه برای اینکه نشان دهد
  کلیدی کار می‌کند، هر از گاهی فهرست مدل‌های هر سرویس تنظیم‌شده را هم می‌پرسد؛
  این درخواست هیچ محتوایی از شما ندارد.
- **حافظه‌ی ایجنت‌ها**، برای ایجنت‌هایی که حافظه‌شان را روشن کرده‌اید: متنی که
  ذخیره یا جستجو می‌شود به Google (اگر کلید Google تنظیم شده باشد) و در غیر این
  صورت به OpenAI فرستاده می‌شود، فارغ از اینکه خود ایجنت از کدام سرویس استفاده کند.
- **اعلان‌ها:** وقتی از برنامه استفاده می‌کنید، دست‌بالا هر ۱۵ دقیقه یک بار یک
  فایل کوچک عمومی از GitHub خوانده می‌شود تا اعلان ناشر و خبر نسخه‌ی تازه نشان
  داده شود. این درخواست هیچ اطلاعی از حساب یا کار شما ندارد، ولی مثل هر درخواست
  اینترنتی نشانی IP شما را به GitHub نشان می‌دهد.

حساب‌ها، گفتگوها، فایل‌ها و کلیدهای شما هرگز برای ناشر فرستاده نمی‌شوند.

## مجوز

این نرم‌افزار انحصاری است و همه‌ی حقوق آن محفوظ است. متن مجوز در فایل
[LICENSE](LICENSE) آمده است.

</div>
