# The Zero-Cost Guide to Google Cloud Setup

Google Cloud Platform (GCP) can look intimidating with its enterprise-level menus and services. Follow this straightforward guide to claim your $300 (or ₹28,000+) free trial and set up your initial project securely—**without spending any money.**

---

## Phase 1: Activating the Free Trial

### Step 1: Sign In & Apply
1. Open your browser and navigate to [console.cloud.google.com](https://console.cloud.google.com).
2. Ensure you are signed in with a **Personal Google Account** (e.g., `@gmail.com`). 
   *Note: Do not use your student email, as university accounts usually restrict access to GCP.*
3. Click the prominent **"Try Google Cloud for free"** or **"Get Started for Free"** button.
4. Select your **Country** (e.g., India), agree to the Terms of Service, and click **Agree & continue**.

### Step 2: Payment Verification
Google requires a payment method to verify your identity and prevent spam accounts. **You will not be automatically charged** when the trial ends unless you manually upgrade.
1. Select a payment method (UPI or Debit/Credit Card). 
2. Complete the verification. A small, temporary refundable charge (like ₹2) may occur.
3. Click **Start free**.

> [!TIP]
> **Confirming your credits:** Once setup is complete, go to `Billing -> Overview` in the left sidebar. You will see a widget displaying your **Free trial credit** (e.g., ₹28,321) and the remaining days (90 Days).

### Crucial Payment Warnings

> [!NOTE]
> **For UPI Users (₹15,000 Autopay Limit):** 
> The ₹15,000 amount shown during setup is just an authorization mandate (autopay limit) and **will NOT be deducted**. You can safely cancel this autopay mandate in your UPI app immediately after your free trial is successfully activated and verified.

> [!CAUTION]
> **Getting a ₹1,000 Prepayment Error or "Closed" Billing Account?**
> If you immediately get a screen asking for a ₹1,000 manual prepayment, or it says your billing account is closed, **do not pay the ₹1,000**. This happens in India because some banks block Google's background recurring mandate check.
> * **The Fix:** Go to `Billing -> Payment Methods` and add a different method (a Credit Card is highly recommended as they handle international recurring mandates flawlessly). If it still insists on ₹1,000, start over with a brand new Gmail account and a Credit Card.
> * *Note: If you do choose to pay the ₹1,000, it acts as wallet credit and is fully refundable if you close your billing account later, but it locks up your money temporarily.*

> [!WARNING]
> **Do NOT click "Activate" in the top banner**
> Once your trial starts, you might see a button at the top of the console saying "Activate". **Do not click this.** Clicking it will cancel your free trial protections and prematurely upgrade you to a paid account.

---

## Phase 2: Creating Your First Project

Google Cloud organizes everything into "Projects". You must create one before you can use any cloud services (like Cloud Storage for your assignments).

1. Look at the top blue navigation bar, next to the "Google Cloud" logo. Click on the **Project Dropdown** (it might say "Select a project").
2. Click **New Project** in the top right corner of the modal window.
3. Name your project something simple (e.g., `tds-assignment-project`).
4. Click **Create**.
5. *Wait a few seconds for it to create, then select it from the dropdown at the top of the screen.*

---

## Phase 3: Peace of Mind (Cleanup)

When your assignment is fully graded, you can ensure you will absolutely never be billed by deleting your project.

1. Go to the GCP Console and click the **Project Dropdown** at the top.
2. Click the gear icon (`Manage resources`).
3. Select your project and click **Delete** (or Shut Down) at the top. 
4. Type the project ID to confirm.

This instantly destroys the project and any services attached to it, stopping any potential future billing. You get to keep any remaining free trial credits!
