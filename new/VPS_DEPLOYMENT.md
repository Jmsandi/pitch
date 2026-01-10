# Deployment Guide: Hosting on VPS (Root)

Follow these steps to deploy and run the WhatsApp-Geneline Bridge on your Linux VPS (Ubuntu/Debian) as the root user.

## 1. Transfer Files
The best way to transfer files while excluding `node_modules` and hidden cache folders is using `rsync`.

### Option A: Using rsync (Recommended)
```bash
rsync -avz --exclude 'node_modules' --exclude '.wwebjs_auth' --exclude '.git' . root@143.198.17.145:/opt/whatsapp-geneline-bridge
```

### Option B: Using scp (Fallback)
If you don't have rsync, use this scp command (run this from inside the project folder):
```bash
scp -r ./* root@143.198.17.145:/opt/whatsapp-geneline-bridge
```
*Note: This might skip hidden files like `.env`. You may need to copy `.env` separately.*

## 2. SSH into your VPS
You must log in to the remote server to perform the next steps.

```bash
ssh root@143.198.17.145
aifor@6282Africa
```

## 3. Run Bootstrap Script
Once logged into the VPS, navigate to the project directory and run the bootstrap script.

```bash
cd /opt/whatsapp-geneline-bridge
chmod +x scripts/bootstrap-vps.sh
./scripts/bootstrap-vps.sh
```

## 3. Configure Environment Variables
Create a `.env` file based on your local one.

```bash
cp .env.example .env
nano .env # Fill in your Geneline, WhatsApp, and Supabase keys
```

## 4. Start the Application
Use PM2 to start and manage the process.

```bash
pm2 start ecosystem.config.js
```

## 5. Authenticate WhatsApp
To see the QR code and authenticate, view the PM2 logs:

```bash
pm2 logs
```

Scan the QR code with your WhatsApp mobile app. Once "WhatsApp client is READY" appears, you can exit the logs with `Ctrl+C`. The bot will continue running in the background.

## 6. Persistence (Reboot Survival)
To ensure the bot starts automatically if the VPS reboots:

```bash
pm2 save
pm2 startup
# Follow the instructions printed in the terminal to complete setup
```

## How to Update the Codebase
Whenever you make changes locally and want to push them to the VPS:

### 1. Transfer only changes from your Mac
Run the same `rsync` command from your local project folder:
```bash
rsync -avz --exclude 'node_modules' --exclude '.wwebjs_auth' --exclude '.git' . root@143.198.17.145:/opt/whatsapp-geneline-bridge
```

### 2. Rebuild on the VPS
SSH into your VPS and run:
```bash
cd /opt/whatsapp-geneline-bridge
npm install  # Only needed if you added new packages
npm run build
```

### 3. Restart the bot
```bash
pm2 restart whatsapp-geneline-bridge

```
*Note: You don't need to re-scan the QR code for updates unless you deleted the `.wwebjs_auth` folder.*


node dist/index.js

ash
which chromium-browser || which chromium
Depending on the output:

If it returns a path (like /usr/bin/chromium or /snap/bin/chromium):
Open your .env file again: nano .env
Update PUPPETEER_EXECUTABLE_PATH with the path you just found.
Save and restart the app.
If it returns nothing:
Chromium might not have installed correctly. Try installing it manually:
bash
sudo apt-get update
sudo apt-get install -y chromium-browser
If that fails, try: sudo apt-get install -y chromium
Once you have the correct path in your .env, run node dist/index.js again to verify!

How to fix it:
Kill anything on port 3000:
bash
fuser -k 3000/tcp
(If fuser is not found, use pm2 delete all instead).
Start the app properly with PM2:
bash
cd /opt/whatsapp-geneline-bridge
pm2 start ecosystem.config.js
Scan the QR code: Run this to see the QR code in your terminal:
bash
pm2 logs
Note: Once you scan the QR code and see "WhatsApp client is READY", your bot is officially live and hosted!

Edit the .env file on your VPS:
bash
cd /opt/whatsapp-geneline-bridge
nano .env
Find the line for PUPPETEER_EXECUTABLE_PATH and change it to the Linux path (or just comment it out with a # so the bot can auto-detect it):
env
# Change this:
PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser
Save and Exit: (Press Ctrl+O, Enter, then Ctrl+X).
Restart the app with PM2: Now that the path is fixed, PM2 should be able to start it properly:
bash
pm2 delete all
pm2 start ecosystem.config.js
pm2 logs

It looks like the pm2 tool hasn't been installed on your VPS yet. This is handled by the bootstrap-vps.sh script.

To fix this, please run the bootstrap script on your VPS:

bash
cd /opt/whatsapp-geneline-bridge
chmod +x scripts/bootstrap-vps.sh
./scripts/bootstrap-vps.sh

## Troubleshooting Voice/Audio on VPS
If voice messages are not working, ensure you have the required audio dependencies installed:
```bash
sudo apt-get update
sudo apt-get install -y ffmpeg sox libsox-fmt-all
```

Also, run the diagnostic test to verify your Google API Key:
```bash
# From the project root
npx ts-node test-google-tts.ts
```
> [!NOTE]
> This project uses a **Google API Key** (configured as `GOOGLE_TTS_API_KEY` in `.env`), not a Service Account JSON. You do not need to set `GOOGLE_APPLICATION_CREDENTIALS`.