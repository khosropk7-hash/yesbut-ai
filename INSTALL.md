# Installing YesBut AI

YesBut AI runs on your own computer. You start the program, it opens your
browser, and you use it there. Nothing is installed system-wide. Your data
stays in one folder on your machine.

**Download only from the official release page:**
<https://github.com/khosropk7-hash/yesbut-ai/releases/latest>

| Your computer | Download |
|---|---|
| Windows 10/11, 64-bit | `YesButAI-<version>-windows-x64.zip` |
| macOS on Apple silicon (M1 or later); the minimum macOS version is in the release notes | `YesButAI-<version>-macos-arm64.tar.gz` |
| Linux, 64-bit (glibc 2.35 or newer, e.g. Ubuntu 22.04+) | `YesButAI-<version>-linux-x64.tar.gz` |
| Docker on 64-bit Linux | `YesButAI-<version>-docker-image.tar.gz` |

Each download has a `.sha256` file next to it. To check your download is intact:

- Windows (PowerShell): `Get-FileHash .\YesButAI-<version>-windows-x64.zip -Algorithm SHA256`
- macOS / Linux: `shasum -a 256 YesButAI-<version>-*.tar.gz`

The hash it prints must equal the first word in the `.sha256` file; upper or
lower case does not matter. In PowerShell this compares the two for you and
prints `True`:

```powershell
(Get-FileHash .\YesButAI-<version>-windows-x64.zip).Hash -eq (Get-Content .\YesButAI-<version>-windows-x64.sha256).Split()[0]
```

> **The program is not code-signed yet.** Windows and macOS warn you the first
> time; the steps below say exactly what to click.

The program works without an internet connection, apart from reaching the AI
providers you use. Its pages, scripts and fonts are part of the download.

---

## Windows

1. **Unblock the zip before extracting.** Right-click the downloaded `.zip` →
   **Properties** → on the **General** tab tick **Unblock** → **OK**. If you
   skip this, Windows marks every extracted file as "from the internet" and
   may warn about each of them.
2. Extract the zip (right-click → **Extract All…**) to a folder you keep, for
   example `C:\Users\<you>\YesButAI`. Don't run it from inside the zip.
3. Open the extracted folder and double-click **`YesButAI.exe`**.
4. If a blue **"Windows protected your PC"** window appears, click
   **More info**, then **Run anyway**. This is SmartScreen. It shows up for
   programs that are not signed yet.
5. A console window opens, and then your browser opens on
   `http://127.0.0.1:8642/app`.

**Microsoft Defender**: new unsigned programs are sometimes flagged by mistake.
If Defender removes `YesButAI.exe` or another file from the folder, first check
your download against its `.sha256`. If it matches, open **Windows Security →
Virus & threat protection → Protection history**, select the item, and choose
**Restore** (or **Allow on device**).

**Smart App Control** (Windows 11 only): if it is turned on, it blocks unsigned
programs. Windows offers no exception for single apps. The only way to run
YesBut AI is to switch Smart App Control off in **Windows Security → App &
browser control**. Most PCs have it off.

## macOS (Apple silicon)

1. Double-click the downloaded `.tar.gz`. Finder extracts a folder named
   `YesButAI-<version>-macos-arm64`. Move that folder wherever you like,
   e.g. into your home folder. The program is a plain executable, not an
   `.app`, so it does not have to go in Applications.
2. Open **Terminal**, drag the program file **`YesButAI`** from that folder
   into the Terminal window, and press **Return**.
3. The first time, macOS says the program "cannot be opened" or "was
   blocked". Open **System Settings → Privacy & Security**, scroll to
   **Security**, and click **Open Anyway** next to the YesButAI message.
   This button is there for about an hour. Confirm with your password, then
   run the program again and choose **Open**.
   - On macOS 15 (Sequoia) and later, Control-click → Open no longer
     bypasses this. Use the steps above.
   - If macOS blocks the program's libraries one by one, clear the download
     mark for the whole folder instead, then start it again:

     ```bash
     xattr -dr com.apple.quarantine ~/YesButAI-<version>-macos-arm64
     ```

     Only do this for a download you checked against its `.sha256`.
4. Your browser opens on `http://127.0.0.1:8642/app`.

## Linux

```bash
tar -xzf YesButAI-<version>-linux-x64.tar.gz
cd YesButAI-<version>-linux-x64
./YesButAI
```

Your default browser opens on `http://127.0.0.1:8642/app`. Don't extract
into a folder mounted `noexec` (often `/tmp`).

The program only accepts connections from the same computer. On a machine
without a desktop, reach it from your own computer through an SSH tunnel:
`ssh -L 8642:127.0.0.1:8642 <user>@<host>`. Then open
`http://127.0.0.1:8642/app` locally.

## Docker

The image contains the same compiled program as the Linux download.

```bash
docker load -i YesButAI-<version>-docker-image.tar.gz
docker run -d --name yesbut -p 127.0.0.1:8642:8642 -v yesbut-data:/data yesbut-ai:<version>
```

Then open `http://127.0.0.1:8642/app`.

- **Your data is in the `yesbut-data` volume** (mounted on `/data`). Keep the
  volume when you replace the container. If you mount a folder of your own
  instead (`-v /srv/yesbut:/data`), it must belong to the account the program
  runs as: `chown 10001:10001 /srv/yesbut`.
- `-p 127.0.0.1:8642:8642` publishes the port on the Docker host only, and
  the program answers to `127.0.0.1` and `localhost` only. If you
  publish it on a network (`-p 8642:8642`), anyone who can reach the host
  sees the sign-in page, and on a fresh install the first account created
  becomes the administrator: create yours first. Put it behind HTTPS and tell
  the program the name it is reached under (`ALLOWED_HOSTS`; without it the
  page answers "Invalid host header") and its public address:

  ```bash
  docker run -d --name yesbut -p 8642:8642 -v yesbut-data:/data \
    -e APP_PUBLIC_BASE_URL=https://ai.example.com \
    -e ALLOWED_HOSTS=ai.example.com \
    -e FORWARDED_ALLOW_IPS=172.17.0.1 yesbut-ai:<version>
  ```

  `FORWARDED_ALLOW_IPS` is the address your HTTPS proxy connects from (here
  Docker's default bridge; use your proxy's). Without it every visitor seems
  to come from the proxy, and the limit on sign-in attempts counts them all
  together. Sign-in with Google and "Connect" buttons send the browser back
  to `APP_PUBLIC_BASE_URL`; set `OAUTH_REDIRECT_BASE_URL` only if that has to
  be a different address.
- **Ollama or LM Studio on the same computer:** inside the container,
  `localhost` is the container itself. Point the program at the host:

  ```bash
  docker run -d --name yesbut -p 127.0.0.1:8642:8642 -v yesbut-data:/data \
    --add-host=host.docker.internal:host-gateway \
    -e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
    -e LMSTUDIO_BASE_URL=http://host.docker.internal:1234/v1 yesbut-ai:<version>
  ```

  Ollama must also accept connections from outside its own computer
  (`OLLAMA_HOST=0.0.0.0` on the host).

- Reset a password: `docker exec -it yesbut /opt/yesbut-ai/YesButAI --data-dir /data --reset-password <username>`
- Update: `docker load` the new image, then `docker stop yesbut && docker rm yesbut`
  and run the new image with the same volume.

---

## First start

The first time, YesBut AI asks you to **create the administrator account**.
The first account on an installation becomes its administrator. You can add
more accounts later under **Admin**.

Then add an AI provider key under **Connections** (OpenAI, Anthropic, Google,
OpenRouter, …). Or use a local model through Ollama or LM Studio. A provider
key is added by an administrator and is used by every account on the
installation.

**Forgot the administrator password?** With the program running or stopped,
open a terminal in the program folder and run:

- Windows: `YesButAI.exe --reset-password <username>`
- macOS / Linux: `./YesButAI --reset-password <username>`

It prints a new password once. Sign in with it, then choose your own with the
key button next to your name at the top right. Add `--data-dir <folder>` if
you use a custom data folder.

## Stopping

Close the console/terminal window, or press **Ctrl+C** in it. Starting the
program again while it is already running just opens the browser on the
running copy.

Signing out in the app signs that account out on every device and browser.

## Where your data lives

Everything is in one folder:

| System | Folder |
|---|---|
| Windows | `%LOCALAPPDATA%\YesButAI` (e.g. `C:\Users\<you>\AppData\Local\YesButAI`) |
| macOS | `~/Library/Application Support/YesButAI` |
| Linux | `~/.local/share/yesbut-ai` (or `$XDG_DATA_HOME/yesbut-ai`) |
| Docker | the volume mounted on `/data` |

Inside it:

- `yesbut.db` holds your accounts, chats, workflows and stored API keys
  (encrypted).
- `files/` holds the files you uploaded.
- `settings.env` holds this installation's settings and its two secret keys.

**Backing up:** stop the program first, then copy the whole folder.
Keep `settings.env` with the database: the API keys stored in the database
can only be read with the `CREDENTIAL_ENCRYPTION_KEY` in that file. Treat the
backup as sensitive, because it unlocks those keys.

**Portable mode:** to keep the data next to the program instead, for example
on a USB stick, create an empty folder named **`data`** in the program folder
(or an empty file named `portable`). The data then goes into that `data`
folder. You can also choose any folder with `YesButAI --data-dir <folder>`.

## Updating

1. Stop the running program (close its window).
2. Back up the data folder (see above).
3. Download the new version, extract it to a new folder, and start it.

Your data folder is separate from the program, so nothing is lost. The
database is upgraded automatically on first start. Delete the old program
folder afterwards. In portable mode, move the `data` folder into the new
program folder first.

## Settings

`settings.env` in the data folder accepts the app's settings, one
`KEY=value` per line. Save it as UTF-8 and restart the program afterwards.
For example `OPENAI_API_KEY=sk-...` sets a provider key, as an alternative to
entering it in the app.

## Options

```
YesButAI [--data-dir FOLDER] [--port PORT] [--host ADDRESS] [--no-browser]
         [--reset-password USERNAME] [--version]
```

By default the program listens on `127.0.0.1` only, so other computers cannot
reach it. `--host 0.0.0.0` makes it listen on every network interface; that is
what the Docker image does inside its container, and not something to do on a
desktop.

It uses port 8642, or another free port if 8642 is busy the very first time,
and keeps that port from then on: your browser keeps you signed in per
address, port included. If its usual port is taken by another program at
start-up, it uses a free one for that session only.

## Uninstalling

Delete the program folder. To remove your data as well, delete the data
folder listed above.
