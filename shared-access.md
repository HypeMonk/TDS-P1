# AI Agent Setup Guide (Shared Service Account Version)

Since Google Cloud's free trial signup has been rejecting many payment methods (specifically UPI mandates), we have set up a workaround. Instead of creating your own Google Cloud account, you will use a **Shared Service Account Key** to authenticate your AI Agent.

---

## 1. Preparation & Security

Your group leader has provided a Google Drive link containing 2-3 different `.json` access keys. 

> [!CAUTION]
> **STRICT USAGE RULES**
> 1. **Pick Randomly:** Do not just grab the first key in the folder! Pick any random key to help balance the API traffic.
> 2. **No Personal Storage:** Do NOT upload personal files or create random personal buckets. If the admin detects unauthorized usage, the storage for the entire project will be deleted instantly, ruining the assignment for everyone.
> 3. **No GitHub Leaks:** DO NOT upload the JSON file to GitHub or paste its contents online. If Google detects it, the key will be permanently disabled and your group will lose access.

1. Download one random `.json` file from the Drive link to your computer. (eg. key-1.json)
2. Save it in a very simple location, for example: `C:\temp\shared-key.json` (on Windows) or `~/Downloads/shared-key.json` (on Mac/Linux).
3. **Copy the exact file path** — you will need it for the AI Agent prompts below.
4. **Get your Project ID:** Open the downloaded `.json` file in Notepad (or any text editor), look for the line that says `"project_id"`, and copy the value next to it (e.g., `tds-project-123456`). You will need to paste this wherever the prompt says `YOUR_PROJECT_ID`.
---

## 2. Installing Google Cloud SDK (gcloud CLI)

If you haven't installed the Google Cloud CLI yet, you must install it before running your AI agent.

**Windows (PowerShell):**
```powershell
(New-Object Net.WebClient).DownloadFile("https://dl.google.com/dl/cloudsdk/channels/rapid/GoogleCloudSDKInstaller.exe", "$env:Temp\GoogleCloudSDKInstaller.exe")
& $env:Temp\GoogleCloudSDKInstaller.exe
```
*Note: Keep the default "Add gcloud to PATH" checked. Close and reopen your terminal/VS Code after installing.*

**macOS / Linux:**
```bash
curl https://sdk.cloud.google.com | bash
exec -l $SHELL
```

**Verify install (run in a fresh terminal):**
```text
gcloud --version
```
If it prints a version number, you are ready to proceed.

---

## 3. Updated Q3 Prompt (Bucket Setup)

Follow complete Q3.md.. 
Just use this prompt in Cline instead of the original one from the Q3 guide. Make sure you replace `YOUR_PROJECT_ID`, `YOUR_BUCKET_NAME`, and the path to your JSON file before running it!

```text
You are a cloud engineer agent working in a terminal. The Google Cloud CLI (gcloud) is already installed on this machine — do not attempt to install it.

First, detect which shell/terminal you are running in (e.g. PowerShell, cmd, bash, zsh) and use the correct command syntax for that shell throughout this task (e.g. use `;` not `&&` in PowerShell, correct path separators, correct quoting). Do not assume a shell — check first (e.g. via $PSVersionTable in PowerShell, or echo $SHELL in bash) and adapt.

Complete this entire task autonomously without asking me questions — make reasonable decisions yourself and keep going until done. After each major step, print a clear status line (e.g. "STEP X DONE: ...") before moving to the next step. If a command fails, print the exact error and try one reasonable fix before moving on — don't silently skip a failed step.

Details (use these exact values everywhere a project ID or bucket name is needed below):
- Project ID: YOUR_PROJECT_ID
- Bucket name: YOUR_BUCKET_NAME
- Location: asia-south1
- Service Account Key Path: C:\path\to\shared-key.json

Steps:
1. Verify gcloud is available: `gcloud --version`.
2. Check current auth status with `gcloud auth list`. If not authenticated, authenticate using the service account key located at the path above using the command: `gcloud auth activate-service-account --key-file="C:\path\to\shared-key.json"`. Only proceed once you detect a successful login.
3. Set the active project using the Project ID above. Confirm with `gcloud config get-value project`.
4. Enable the Cloud Storage API: `gcloud services enable storage.googleapis.com`. Confirm it's enabled.
5. Create the bucket using the bucket name and location above: `gcloud storage buckets create gs://BUCKET_NAME --location=LOCATION`.
6. Make the bucket publicly readable and listable by granting `roles/storage.objectViewer` and `roles/storage.legacyBucketReader` to `allUsers`.
7. Confirm everything by running `gcloud storage buckets describe` and `gcloud storage buckets list` on the bucket.
8. Print a final summary confirming: authentication status, project set, API enabled, bucket created, public access granted, and full describe/list output.
```

---

## 4. Updated Q4 Prompt (Dataset Upload)

Similarly, use this updated prompt for Q4. Remember to start a **New Task (New Chat)** in Cline before running this, so your JSON logs don't get mixed up!

```text
You are a cloud engineer agent working in a terminal. The Google Cloud CLI (gcloud) is already installed on this machine — do not attempt to install it.

First, detect which shell/terminal you are running in (e.g. PowerShell, cmd, bash, zsh) and use the correct command syntax for that shell throughout this task. Do not assume a shell — check first and adapt.

Complete this entire task autonomously without asking me questions — make reasonable decisions yourself and keep going until done. After each major step, print a clear status line (e.g. "STEP X DONE: ...") before moving to the next step. If a command fails, print the exact error and try one reasonable fix before moving on — don't silently skip a failed step.

Details:
- Project ID: YOUR_PROJECT_ID
- Bucket name: YOUR_BUCKET_NAME
- Location: asia-south1
- Local file to upload: C:\YOUR\EXACT\PATH\TO\eval.jsonl
- Service Account Key Path: C:\path\to\shared-key.json

Steps:
1. Verify gcloud is available and check auth status with `gcloud auth list`. If not authenticated, authenticate using the service account key located at the path above using the command: `gcloud auth activate-service-account --key-file="C:\path\to\shared-key.json"`. Confirm the active project is YOUR_PROJECT_ID (set it if not already).
2. Compute the SHA-256 hash of the local file BEFORE upload, using the correct command for this shell (e.g. `Get-FileHash` in PowerShell, `sha256sum` in bash). Print this hash clearly labeled as "LOCAL HASH BEFORE UPLOAD".
3. Ensure the Cloud Storage API is enabled: `gcloud services enable storage.googleapis.com`.
4. Create the bucket: `gcloud storage buckets create gs://YOUR_BUCKET_NAME --location=asia-south1`. If it already exists, skip this step and note that.
5. Upload the file unchanged, preserving the exact filename `eval.jsonl`:
   `gcloud storage cp "C:\YOUR\EXACT\PATH\TO\eval.jsonl" gs://YOUR_BUCKET_NAME/eval.jsonl`
6. Make the bucket publicly readable and listable by granting `roles/storage.objectViewer` and `roles/storage.legacyBucketReader` to `allUsers`.
7. Download the uploaded object back to a temp location and compute its SHA-256 hash, or use `gcloud storage objects describe` to check the object's stored hash/metadata. Print this clearly labeled as "UPLOADED FILE VERIFICATION".
8. Confirm by running `gcloud storage buckets describe gs://YOUR_BUCKET_NAME` and `gcloud storage ls gs://YOUR_BUCKET_NAME`.
9. Print a final summary: project confirmed, API enabled, bucket created/existing, file uploaded, LOCAL HASH BEFORE UPLOAD, UPLOADED FILE VERIFICATION, public access granted, and full describe/list output. Explicitly state whether the local hash and the uploaded file appear to match.
```

---

## 5. Extracting the Logs
Once the agent finishes, extract the `api_conversation_history.json` file exactly as described in the original assignment guides and submit it!
