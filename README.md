# ExpireAlert ⏳🌱
### Intelligent Household Expiry Tracking, Barcode Scanner & AI Zero-Waste System

**ExpireAlert** is a full-featured Django web application designed to combat the silent expiry of everyday household products — from dairy, groceries, and packaged foods to medicines and cosmetics.

---

## 🌟 Key Features & Workflow

### 1. Barcode Scanning & Product Ingestion
- **Live Device Camera Scanner**: Scan barcodes directly from product packaging using your smartphone camera or webcam (powered by `html5-qrcode`).
- **Open Food Facts API Integration**: Real-time lookup fetching product name, brand, category, and suggested shelf-life from Open Food Facts with local offline catalog caching.
- **One-Click Demo Presets**: Includes instant demo barcode buttons for Amul Milk, Britannia Bread, Amul Paneer, Maggi Noodles, and Dolo 650.
- **Expiry Date Shortcuts**: Quick-set buttons for `+3 Days`, `+5 Days`, `+6 Days (7-Day Alert Demo)`, `+1 Month`, `+6 Months`, and `+1 Year`.

### 2. Proactive 7-Day Pre-Expiry Notification Engine
- **7-Day Threshold Detection**: When an item's expiry date is within **7 days** (`0 <= days_left <= 7`), the system triggers an alert.
- **Smart AI Recipe Generator**: Automatically analyzes near-expiry food items alongside other available pantry leftovers to generate zero-waste rescue recipes (e.g. French Toast, Creamy Pasta, Paneer Bhurji, Bread Upma, Vegetable Khichdi, etc.).
- **Interactive Reminders & Alerts Session**: The generated recipe and alert remain in the dedicated **Reminders** notification session with countdown urgency badges (`Critical <= 1d`, `Urgent <= 3d`, `Warning <= 7d`).
- **One-Click "Cooked / Consumed" Action**: Users can mark items as cooked, instantly archiving the alert and logging the saved financial value.

### 3. Automated Expiry Purge (Auto-Removal)
- **Automatic Expiry Cleanup**: As soon as an item passes its expiration date (`days_left < 0`):
  1. The item is automatically removed from active household inventory.
  2. Its active reminder notification is automatically deleted/cleared from the notification session.
  3. The item is safely logged into the `WasteRecord` database with the exact financial loss (`purchase_price * quantity`) and marked as `was_auto_removed = True`.
- Runs on page load, via Celery beat background task, or on-demand via the **"🧹 Run Expiry Cleanup"** button.

### 4. Waste Analytics & Sustainability Dashboard
- **Financial Loss Calculation**: Accurate tracking of money lost to expired products vs money saved by consuming items on time.
- **Sustainability Score**: Percentage efficiency rating of household pantry management.
- **Interactive Visual Charts (Chart.js)**:
  - Consumption Ratio Doughnut Chart (Active vs Consumed vs Expired).
  - Category Tracking & Waste Bar Chart.
- **Audit Log**: Detailed history of all expired, auto-removed, and discarded products.

### 5. Scheduled Background Alerts (Celery + Redis)
- Scheduled background tasks for **7-day, 3-day, and 1-day** email notifications.
- Daily automated inventory cleanup task.

---

## 🚀 Quick Start Guide (Windows / Mac / Linux)

### 1. Setup Virtual Environment & Dependencies
```bash
python -m venv venv
venv\Scripts\activate      # On Linux/macOS: source venv/bin/activate
pip install -r requirements.txt
```

### 2. Initialize Database & Seed Demo Data
```bash
python manage.py makemigrations
python manage.py migrate
python manage.py seed_demo_data
```

> **Demo Credentials created by `seed_demo_data`:**
> - User Account: `demo_user` / `demo1234`
> - Admin Panel: `admin` / `admin1234`

### 3. Run Development Server
```bash
python manage.py runserver
```

Open:
- **ExpireAlert Dashboard**: [http://127.0.0.1:8000/](http://127.0.0.1:8000/)
- **Django Admin Panel**: [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/)

---

## 🧪 Demonstration Walkthrough

1. **Log in** with `demo_user` / `demo1234`.
2. **Dashboard**: View the active pantry stats, the value at risk, and the 7-day pre-expiry banner.
3. **Reminders & AI Recipes** (`/reminders/`):
   - Notice the items expiring within 7 days (e.g. Amul Milk expiring in 4 days, Brown Bread expiring in 2 days).
   - Expand the attached **AI Rescue Recipe** to see cooking directions and required ingredients.
   - Click **"✓ Cooked / Consumed"** to mark it used — notice how the alert clears and the money saved increases!
4. **Add New Product with Barcode Scanner** (`/products/add/`):
   - Click one of the **Quick Demo Barcodes** (e.g., Amul Paneer) or click **"📷 Camera Scanner"** to scan a physical barcode.
   - Click the **"+6 Days"** shortcut button to set an expiry date within the 7-day window.
   - Save and observe how the system immediately generates an AI recipe and places it into the **Reminders** session!
5. **Verify Automated Expiry Cleanup**:
   - Click **"🧹 Run Expiry Cleanup"** — items whose expiration date has passed are automatically purged from active storage, their notifications removed, and financial loss logged.
6. **Waste Analytics** (`/analytics/`):
   - Review the Chart.js visual charts, financial loss, and sustainability score.

---

## ⚙️ Optional Celery & Redis Setup

Start Redis server, then run:

```bash
celery -A expirealert worker -l info
celery -A expirealert beat -l info
```

Reminders print directly to the console in development via Django's console email backend.
