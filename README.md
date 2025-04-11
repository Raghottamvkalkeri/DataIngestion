# 📰 News Data Ingestion Script - README

This project automates the process of fetching news articles from the [NewsAPI](https://newsapi.org/) and saving them to a CSV file and a SQLite database using a Python script. It runs hourly using macOS's `cron`.

---

## 📁 Folder Structure

```
news/
├── batch_ingest.py
├── api_keys.txt
├── batch_output/
│   └── tech_news_YYYY-MM-DD_HH-MM-SS.csv
└── logfile.log
```

---

## ⚙️ Prerequisites

### 1. Install Python Packages

Make sure you're using Python 3 (macOS default is fine). Then install dependencies:

```bash
python3 -m pip install pandas requests --break-system-packages
```

You may also need:
```bash
python3 -m pip install sqlite3
```

> If you see `externally-managed-environment`, add `--break-system-packages`.

---

### 2. Set Up API Key

Create a file `api_keys.txt` in the same directory:

```
your_newsapi_key_here
```

---

## 🧐 Script Overview (`batch_ingest.py`)

1. Loads API key from `api_keys.txt`
2. Calls NewsAPI with selected `category` and `country`
3. Parses and flattens data (especially `source.name`)
4. Saves a timestamped `.csv` file in `batch_output/`
5. Saves data to a local SQLite DB table called `batch_news`
6. Appends logs/errors to `logfile.log`

---

## 🚀 Running the Script Manually

```bash
/usr/bin/python3 /Applications/XAMPP/xamppfiles/htdocs/news/batch_ingest.py
```

If `pandas` gives a `ModuleNotFoundError`, confirm it's installed under the correct Python version:
```bash
/usr/bin/python3 -m pip install pandas --break-system-packages
```

---

## 🗃️ SQLite Setup (Handled Automatically)

If `batch_news` table does not exist, create it using:

```sql
CREATE TABLE batch_news (
    source TEXT,
    author TEXT,
    title TEXT,
    description TEXT,
    url TEXT,
    publishedAt TEXT,
    content TEXT,
    category TEXT
);
```

This can be executed manually or included in the script.

---

## 🧪 Output Example

**CSV File:**
```
batch_output/tech_news_2025-04-11_10-00-00.csv
```

**Database:**
`news_articles.db` inside the same folder.

---

## 🔄 Setting up Cron Job (Hourly)

### 1. Edit your crontab

```bash
crontab -e
```

### 2. Add the following line to run every hour

```bash
0 * * * * /usr/bin/python3 /Applications/XAMPP/xamppfiles/htdocs/news/batch_ingest.py >> /Applications/XAMPP/xamppfiles/htdocs/news/logfile.log 2>&1
```

> Make sure the path to Python and your script is correct.

### 3. Save & Exit

---

## 🔍 Debugging Cron

- Check the log:
```bash
cat /Applications/XAMPP/xamppfiles/htdocs/news/logfile.log
```

- If the job isn’t running, make sure:
  - The script is executable
  - Paths are correct
  - Dependencies (e.g., pandas) are available under cron's environment

---

## 🚫 Pausing Cron

Option 1: Comment out the line in `crontab -e`

```bash
# 0 * * * * /usr/bin/python3 /.../batch_ingest.py >> logfile.log 2>&1
```

Option 2: Backup & remove

```bash
crontab -l > backup_cron.txt
crontab -r  # remove all jobs
crontab backup_cron.txt  # to restore later
```

---

## 🌐 Troubleshooting

- **LibreSSL Warning**  
  Safe to ignore for basic usage:
  ```
  urllib3 v2 only supports OpenSSL 1.1.1+, currently compiled with LibreSSL 2.8.3
  ```

- **FileNotFoundError: api_keys.txt**  
  → Ensure `api_keys.txt` exists in the same folder as `batch_ingest.py`.

- **pandas not found**  
  → Use the same Python version for installing packages and running the script.

---

Let me know if you want a downloadable version or Markdown file!

