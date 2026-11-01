# World Economic Data from Wikipedia

**Author:** Arun Kumar Prabhakaran
**Date:** 2026-11-01

A data project that scrapes economic and social indicators for the world's 15 largest economies (by nominal GDP) from Wikipedia, cleans them, compares the countries and visualizes the results. Everything is in one Jupyter notebook:

1. **Scrape** Wikipedia "List of countries by …" pages (HTML is cached locally).
2. **Clean** footnotes, units, symbols and country-name variants, keeping the source page and data year of each value.
3. **Analyze** rankings, derived trade metrics, correlations, income-group comparisons and a simple two-index scorecard.
4. **Visualize** the comparison with bar, dot, scatter, heatmap and radar charts.
5. **Conclude**, with caveats.

## Project layout

```
wiki_economy_analysis.ipynb      the notebook (start here)
requirements.txt                 Python dependencies
data/raw/                        cached Wikipedia HTML (not in git; created by the first run)
data/clean/economic_indicators.csv   final table, one row per country
data/clean/indicator_provenance.csv  source page and year for every value
figures/                         the charts as PNG files (created on run)
```

## Requirements

- Python 3.9 or newer
- An internet connection on the first run (or a copy of `data/raw/`, see below)

## Setup

Run these from the project folder.

**macOS / Linux**
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

**Windows (PowerShell)**
```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## Running the notebook

Start from the project folder: the notebook reads and writes `data/` and `figures/` relative to the folder it is opened from. If you activated the virtual environment, you can drop the `.venv/bin/` prefix from the commands below.

### Option 1: Jupyter Notebook
```bash
.venv/bin/jupyter notebook wiki_economy_analysis.ipynb
```
A browser tab opens on your own machine. Choose **Kernel → Restart & Run All**.

### Option 2: JupyterLab
```bash
.venv/bin/jupyter lab wiki_economy_analysis.ipynb
```
Then **Run → Run All Cells**.

### Option 3: VS Code
1. Open the project folder in VS Code (with the Python and Jupyter extensions).
2. Open `wiki_economy_analysis.ipynb`.
3. Click **Select Kernel** (top right) and choose the interpreter in `.venv`.
4. Click **Run All**.

### Option 4: Run without opening anything
```bash
.venv/bin/jupyter nbconvert --to notebook --execute --inplace wiki_economy_analysis.ipynb
```
This runs every cell, saves the outputs into the notebook and writes the CSVs and figures.

### Option 5: Hosted or in-browser notebooks
- **Server-side hosts (Google Colab, Binder, etc.):** upload the notebook (and optionally `data/raw/`), run `pip install -r requirements.txt` in a cell if a package is missing, then run all cells. These behave like a normal Python environment.
- **In-browser runtimes (JupyterLite / Pyodide):** the browser blocks direct requests to Wikipedia's article pages (CORS), so the notebook detects this and uses Wikipedia's API instead. This path is less tested and the first run can be slow because of rate limiting. The most reliable approach is to upload the `data/raw/` folder (cached pages) next to the notebook so nothing needs downloading. Make sure `pandas`, `matplotlib`, `lxml` and `beautifulsoup4` are installed there.

## Data and caching

- Each Wikipedia page is downloaded once into `data/raw/` and read from there afterwards, so re-runs are fast, repeatable and work offline. The cached copies never expire on their own, so re-running months later still uses the old snapshot.
- **To pull a fresh snapshot:** set `REFRESH = True` in the notebook's first code cell and run all cells. Every page is re-downloaded once and replaces its cached copy (a failed download keeps the old copy and stops with an error). Set it back to `False` afterwards. Deleting the files in `data/raw/` has the same effect.
- A refresh overwrites `data/clean/` and `figures/`. Copy them somewhere first if you want to keep the old snapshot, and note that the written conclusions in the notebook are static text from the original snapshot, so review them after refreshing.
- Wikipedia may answer "429 Too Many Requests" on a fresh download; the notebook waits and retries automatically.
- The values are a **snapshot**: each indicator is the latest value Wikipedia shows, and the data year differs by indicator (see `data/clean/indicator_provenance.csv`). The notebook's final section lists the caveats.

## Data sources and attribution

All values come from English Wikipedia's "List of countries by …" pages (plus a few "Economy of …" infoboxes and index pages such as the Corruption Perceptions Index). Wikipedia text and tables are available under CC BY-SA 4.0, and those pages in turn compile figures from the IMF, World Bank, UN, CIA World Factbook and others. Check the original sources' terms before reusing the data beyond personal or educational use. The cached HTML in `data/raw/` is not committed to this repository; run the notebook once to download it.

## License

The code and notebook are released under the [MIT License](LICENSE). The MIT license covers the code only: the underlying data comes from Wikipedia and its cited sources and keeps its own terms, as described above.

## Troubleshooting

**`NotOpenSSLWarning: urllib3 v2 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'LibreSSL …'`**
This is a harmless warning, common with the Python that ships with macOS. The notebook's first cell already silences it, so you should not see it there; it can still appear if another tool imports urllib3 (for example `pip` or a terminal script). To remove it everywhere, use either fix:
- Install a newer Python (for example from python.org or Homebrew), recreate `.venv` with it and reinstall the requirements; or
- Pin an older urllib3 in the environment: `pip install "urllib3<2"`.

**The kernel can't find pandas / "No module named …"**
The notebook is running with a different Python than the one you installed the requirements into. In VS Code, re-select the `.venv` kernel. With Jupyter, start it using `.venv/bin/jupyter` (or activate the environment first).

**`Failed to fetch` or connection errors when scraping**
You are probably running in an in-browser notebook, or offline without the `data/raw/` cache. See Option 5, or run locally.

**Charts don't show when running `nbconvert`**
They are saved into the notebook and into `figures/` instead of being displayed.
