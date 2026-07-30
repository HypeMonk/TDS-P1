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

1. Download one random `.json` file from the Drive link to your computer.
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

Use this prompt in Cline instead of the original one from the Q3 guide. Make sure you replace `YOUR_PROJECT_ID`, `YOUR_BUCKET_NAME`, and the path to your JSON file before running it!

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
6. First, disable Public Access Prevention on the bucket by running `gcloud storage buckets update gs://BUCKET_NAME --no-public-access-prevention`. Then, make the bucket publicly readable and listable by granting `roles/storage.objectViewer` and `roles/storage.legacyBucketReader` to `allUsers`.
7. Confirm everything by running `gcloud storage buckets describe` and `gcloud storage buckets list` on the bucket.
8. Print a final summary confirming: authentication status, project set, API enabled, bucket created, public access granted, and full describe/list output.
```

---

## 4. Updated Q4 Prompt (Dataset Upload)

Similarly, use this updated prompt for Q4. Remember to start a **New Task (New Chat)** in Cline before running this, so your JSON logs don't get mixed up!

```text
```text
You are a cloud engineer operating in a terminal. Google Cloud CLI is already installed. Do not install or update it.

Complete the task autonomously without asking questions. Use the exact values supplied below.

VALUES
- Project ID: "YOUR_PROJECT_ID"
- Bucket name: "YOUR_BUCKET_NAME"
- Location: "asia-south1"
- Local file: "C:\YOUR\EXACT\PATH\TO\eval.jsonl"
- Service account key: "C:\path\to\shared-key.json"
- Object name: "eval.jsonl"

EXECUTION RULES
1. Detect the current shell before running cloud commands, then use that shell's syntax consistently.
2. On Windows PowerShell, invoke `gcloud.cmd` instead of `gcloud` to avoid PowerShell treating normal gcloud stderr messages as `NativeCommandError`. Use `$LASTEXITCODE` to determine success. Use `;`, not `&&`.
3. Run one major operation at a time. Keep commands short and readable. Do not build large PowerShell wrapper functions or one-line scripts.
4. Do not merge stderr into stdout unless a command actually fails. On failure, print the command's exact error, try one reasonable fix once, and then stop if the retry also fails.
5. After every successful major step, print exactly one status line in this format:
   `STEP X DONE: <result>`
6. Treat an operation as successful based on its exit code and a direct verification command, not by parsing informational text.
7. Use exact-match checks. Do not use substring filters that could confuse `storage.googleapis.com` with another service.
8. Never print the service account key contents.
9. Do not install components, run `gcloud components update`, or run `gcloud survey`.
10. Preserve the local file unchanged and upload it with the exact object name `eval.jsonl`.

STEPS

1. Detect and print the shell name. Verify the CLI:
   - PowerShell: `gcloud.cmd --version`
   - Other shells: `gcloud --version`
   Print `STEP 1 DONE` only if the command exits successfully.

2. Validate that the service account key file and local upload file exist. If either is missing, print the exact missing path and stop.

3. Check authentication with `gcloud auth list` (use `gcloud.cmd` in PowerShell). Read only the `client_email` field from the service account JSON without displaying any other key data. Compare it with the active account. If they differ, activate the supplied key. Confirm the active account afterward.
   Print: `STEP 2 DONE: Authenticated as <account>`

4. Read the active project. If it is not `YOUR_PROJECT_ID`, set it. Read it again and require an exact match.
   Print: `STEP 3 DONE: Active project is YOUR_PROJECT_ID`

5. Compute the local SHA-256 hash before upload:
   - PowerShell: `(Get-FileHash -LiteralPath "C:\YOUR\EXACT\PATH\TO\eval.jsonl" -Algorithm SHA256).Hash.ToLowerInvariant()`
   - Bash/zsh: `sha256sum '/exact/path/eval.jsonl'`
   Print exactly:
   `LOCAL HASH BEFORE UPLOAD: <sha256>`
   Then print `STEP 4 DONE: Local SHA-256 calculated`

6. Enable Cloud Storage API:
   `gcloud services enable storage.googleapis.com --project=YOUR_PROJECT_ID`
   Confirm using an exact service-name check. In PowerShell, a clean confirmation command is:
   `gcloud.cmd services list --enabled --project=YOUR_PROJECT_ID --filter="config.name=storage.googleapis.com" --format="value(config.name)"`
   Require the result to equal `storage.googleapis.com` exactly.
   Print: `STEP 5 DONE: Cloud Storage API is enabled`

7. Check whether `gs://YOUR_BUCKET_NAME` exists by running a bucket describe command.
   - If it exists, verify that it belongs to `YOUR_PROJECT_ID` and is in `asia-south1`, then reuse it.
   - If the describe command returns Not Found, create it:
     `gcloud storage buckets create gs://YOUR_BUCKET_NAME --project=YOUR_PROJECT_ID --location=asia-south1`
   - Do not attempt creation first and use the resulting error as an existence check.
   Print either:
   `STEP 6 DONE: Bucket created in asia-south1`
   or
   `STEP 6 DONE: Existing bucket verified in asia-south1`

8. Upload the file unchanged:
   `gcloud storage cp "C:\YOUR\EXACT\PATH\TO\eval.jsonl" gs://YOUR_BUCKET_NAME/eval.jsonl`
   Confirm the object exists with:
   `gcloud storage objects describe gs://YOUR_BUCKET_NAME/eval.jsonl`
   Print: `STEP 7 DONE: eval.jsonl uploaded`

9. Configure public access in this order:
   `gcloud storage buckets update gs://YOUR_BUCKET_NAME --no-public-access-prevention`
   `gcloud storage buckets add-iam-policy-binding gs://YOUR_BUCKET_NAME --member=allUsers --role=roles/storage.objectViewer`
   `gcloud storage buckets add-iam-policy-binding gs://YOUR_BUCKET_NAME --member=allUsers --role=roles/storage.legacyBucketReader`
   Retrieve the IAM policy as JSON and verify structurally that `allUsers` appears in both requested role bindings. Confirm bucket Public Access Prevention is not `enforced`.
   Print: `STEP 8 DONE: Public read and list access granted`

10. Verify uploaded content by downloading it to a unique temporary file, without overwriting the source file:
    - PowerShell: use `$tempFile = Join-Path $env:TEMP ("eval-" + [guid]::NewGuid().ToString() + ".jsonl")`
    - Download with `gcloud storage cp gs://YOUR_BUCKET_NAME/eval.jsonl <temp-file>`
    - Compute the downloaded file's SHA-256 using the shell-native hash command.
    - Delete only the temporary downloaded file after hashing.
    Print exactly:
    `UPLOADED FILE VERIFICATION: <sha256>`
    Compare normalized lowercase hashes and print exactly one of:
    `HASH MATCH: YES`
    or
    `HASH MATCH: NO`
    If hashes do not match, stop and report verification failure.
    Print: `STEP 9 DONE: Uploaded file matches local file`

11. Run and print the complete output of both final commands:
    `gcloud storage buckets describe gs://YOUR_BUCKET_NAME`
    `gcloud storage ls gs://YOUR_BUCKET_NAME`
    Require the listing to include `gs://YOUR_BUCKET_NAME/eval.jsonl`.
    Print: `STEP 10 DONE: Final bucket and object checks succeeded`

12. Print a concise final summary containing:
    - Active authenticated account
    - Confirmed project
    - Cloud Storage API status
    - Bucket name, location, and whether it was created or reused
    - Uploaded object URI
    - `LOCAL HASH BEFORE UPLOAD`
    - `UPLOADED FILE VERIFICATION`
    - `HASH MATCH: YES` or `NO`
    - Public Access Prevention status
    - Confirmation of both public IAM roles
    - The full bucket describe output
    - The full object list output

Do not claim completion unless every verification succeeds. Do not expose secret key contents in any output.
```
```

---

## 5. Extracting the Logs
Once the agent finishes, extract the `api_conversation_history.json` file exactly as described in the original assignment guides and submit it!
