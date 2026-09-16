# Python Libraries — A Teaching Notebook Series

Four Jupyter notebooks that take you from basic Python to competent, practical use of the four
libraries most data work in Python is built on.

These are not cheat sheets. Each notebook is a complete course in its subject, written to be
worked through from top to bottom, with explanations, exercises and a project at the end.

| Notebook | Subject | Cells | Exercises | Colab |
| --- | --- | --- | --- | --- |
| [`01_NumPy.ipynb`](01_NumPy.ipynb) | Arrays, shapes, broadcasting, vectorisation | 368 | 9 | [open](https://colab.research.google.com/github/Aman-Pathak-0243/Unified_Engineering/blob/main/Python_libraries/01_NumPy.ipynb) |
| [`02_Pandas.ipynb`](02_Pandas.ipynb) | Series, DataFrames, GroupBy, merging, cleaning | 672 | 15 | [open](https://colab.research.google.com/github/Aman-Pathak-0243/Unified_Engineering/blob/main/Python_libraries/02_Pandas.ipynb) |
| [`03_Matplotlib.ipynb`](03_Matplotlib.ipynb) | Figures, Axes, chart types, honest visualisation | 262 | 9 | [open](https://colab.research.google.com/github/Aman-Pathak-0243/Unified_Engineering/blob/main/Python_libraries/03_Matplotlib.ipynb) |
| [`04_BeautifulSoup.ipynb`](04_BeautifulSoup.ipynb) | HTML, parsing, `requests`, scraping responsibly | 287 | 9 | [open](https://colab.research.google.com/github/Aman-Pathak-0243/Unified_Engineering/blob/main/Python_libraries/04_BeautifulSoup.ipynb) |

Every exercise has a worked solution at the end of its notebook. The Colab links work once these
files are committed and pushed to `main`.

## Prerequisites

You should already know:

- variables and the basic data types
- `if` / `else`, `for` and `while`
- writing and calling functions
- lists and dictionaries
- roughly what a class is (little object-oriented code appears, but the vocabulary is used)

You do **not** need any prior exposure to NumPy, Pandas, plotting, HTML or the web. Where a
notebook uses a Python feature beyond the list above — a list comprehension, `zip`, `enumerate`,
unpacking, a `lambda`, a context manager — it explains it at the point of use.

## Recommended order

Work through them in order. Each one assumes the previous.

```
01_NumPy  →  02_Pandas  →  03_Matplotlib  →  04_BeautifulSoup
```

- **NumPy** first, because Pandas is built on it. Boolean masking, the `axis` argument and
  broadcasting all reappear in Pandas wearing different clothes.
- **Pandas** second, and it is the longest notebook because it is the library you will use most.
- **Matplotlib** third, so the tables you built in Pandas have somewhere to go.
- **Beautiful Soup** last, because its final project uses all four libraries together.

If you only have time for one, make it Pandas. If you are already using Pandas and things
occasionally surprise you, read the NumPy notebook's sections on `axis`, broadcasting and
views-versus-copies — most Pandas confusion starts there.

## Running the notebooks

### On Google Colab

Nothing to install — Colab already has NumPy, Pandas, Matplotlib, Beautiful Soup, requests, lxml
and openpyxl. Use the **Colab** links in the table above, or `File → Open notebook → GitHub` and
paste the repository URL.

Two things to know:

- **Colab runs Pandas 2.x**, not 3.x. Every notebook runs correctly there — this was verified by
  executing all four against Pandas 2.2.2 / NumPy 1.26 / Matplotlib 3.9 as well as against the
  pinned stack. Some cells print `object` where a Pandas 3 machine prints `str`, and
  `datetime64[ns]` where it prints `[us]`. The two cells where the actual *behaviour* differs
  detect the version and report what your copy does, then explain both cases.
- **`04_BeautifulSoup.ipynb` starts a small web server** on `127.0.0.1:8731` to scrape. This works
  on Colab, but if the runtime disconnects the server thread dies with it — re-run from the
  "Starting the server" cell rather than from the middle of the scraping section.

If you want the exact pinned stack on Colab, run this in a first cell and then
**Runtime → Restart session** before continuing:

```python
%pip install -q -r https://raw.githubusercontent.com/Aman-Pathak-0243/Unified_Engineering/main/Python_libraries/requirements.txt
```

That is optional, and for a class it is usually more friction than it is worth.

### Locally

Python 3.11 or newer. The exact verified stack is pinned in
[`requirements.txt`](requirements.txt):

```bash
python -m venv .venv
.venv\Scripts\activate           # Windows
source .venv/bin/activate        # macOS / Linux
pip install -r requirements.txt
```

To teach from that environment in Jupyter, register it as a kernel and select it in the notebook:

```bash
python -m ipykernel install --user --name py-libs --display-name "Python Libraries"
```

If you would rather not pin anything, this is enough to run everything:

```bash
pip install numpy pandas matplotlib beautifulsoup4 requests jupyterlab
pip install openpyxl lxml        # optional: Excel files; a faster HTML parser
```

Each notebook also opens with a commented-out `%pip install` cell, so you can install from inside
Jupyter if you prefer.

**Pandas 2 versus Pandas 3.** The notebooks are written against Pandas 3 and verified on both.
The Pandas notebook opens with a table of the four differences, the two cells whose behaviour
genuinely changed detect your version and report what it does, and the rest of the difference is
cosmetic dtype labels. There is nothing to work around on an older stack.

### Starting Jupyter

```bash
cd python-libraries
jupyter lab          # or: jupyter notebook
```

Then open a notebook and run the cells in order with `Shift+Enter`. VS Code's notebook editor
works equally well.

### Notes on running them

- **Run cells in order.** Later cells use variables defined earlier, as in any notebook.
- **Everything runs offline.** Every dataset is generated inside the notebook with a fixed random
  seed, so your output matches what is shown. Nothing is downloaded.
- **The scraping notebook starts its own web server.** It generates a small demo website, serves
  it from `127.0.0.1:8731`, and scrapes that. The HTTP requests are real; no external site is
  contacted, and the last cell shuts the server down and deletes the files.
- **A few cells write files** (a CSV, a demo site) and clean up after themselves in the same
  notebook. If you interrupt a notebook partway, a `demo_site/`, `pandas_io_demo/`,
  `matplotlib_output/` or `scrape_cache/` folder may be left behind; they are safe to delete.
- **All four notebooks are saved with their outputs**, so you can read them without running
  anything. To start clean, use *Kernel → Restart and Clear Output*.

Written and verified against NumPy 2.x, Pandas 3.x and Matplotlib 3.x. Where those versions
changed something that older tutorials get wrong — Pandas' copy-on-write, the removal of
`applymap`, Matplotlib's renamed arguments — the notebooks teach the current behaviour and say
what changed.

## How the Beginner → Pro method works

Most topics are taught twice.

**The beginner version** solves the problem with the Python you already have: an explicit loop, an
intermediate variable, one step per line. It is longer, and every step is visible.

```python
monthly = []
for i in range(len(salaries)):
    monthly.append(salaries[i] / 12)
```

**The idiomatic version** is how someone who uses the library daily would write it.

```python
monthly = salaries / 12
```

**Then the notebook explains the difference** — what changed, why the second version exists, and
when it is genuinely better. That last part matters, because it is not always:

- Sometimes the idiomatic version is unambiguously better, and the notebook says so plainly.
  Vectorised arithmetic over a row loop is never a trade-off.
- Sometimes it is faster but harder to read, and the right choice depends on how much data you
  have. `np.select` versus `apply` with an `if/elif` chain is measured, not asserted.
- Sometimes the "clever" version is worse, and the notebook says that too. There is an example in
  the NumPy project where `enumerate` beats a fancier one-liner, and the notebook keeps the
  simpler one.

The goal is not to collect one-liners. It is that you can read idiomatic code, write it when it
helps, and recognise when it does not.

Alongside this, each notebook includes:

- **Common mistakes** for every major topic, with the real error messages
- **Debugging sections** — a failure, the traceback, why it happened, the fix, and often a better
  approach that avoids the problem entirely
- **Exercises at three levels** — practice, application, and a challenge that requires a judgement
  call — with worked solutions at the end of each notebook that explain the reasoning, not just
  the code

## The projects

Each notebook ends with a project that requires decisions, not just typing.

- **NumPy — Student performance analysis.** 30 students across 5 subjects. Averages by student and
  by subject, pass/fail counts, rankings, z-scores so subjects of different difficulty can be
  compared fairly, and subject correlations.
- **Pandas — Employee analytics.** A deliberately messy HR export: inconsistent categories, text
  where numbers belong, mixed date formats, duplicates, gaps. Clean it, then answer real
  compensation questions — including one where the obvious answer turns out to be an artefact of
  group composition.
- **Matplotlib — Sales dashboard.** A year of daily orders across regions and categories, turned
  into a multi-panel figure where each panel answers a stated question and the chart type is
  justified.
- **Beautiful Soup — Product scraper.** The integrating project, and the reason to do the
  notebooks in order:

```
   a live local web server
            │  requests
            ▼
      HTML pages
            │  Beautiful Soup
            ▼
   list of dictionaries
            │  Pandas
            ▼
      DataFrame  ──►  cleaning  ──►  NumPy calculations
            │
            ▼  Matplotlib
      a dashboard and conclusions
```

Pages are fetched over HTTP, parsed, audited, analysed and plotted — every stage of a real data
workflow, in one notebook.

## A note on the scraping notebook

The Beautiful Soup notebook includes a full section on ethics and legality: `robots.txt`, terms of
service, rate limiting, personal data, copyright, and the line between scraping a public page and
circumventing access controls. It is not a disclaimer bolted on at the end — it explains the
reasoning and gives a practical checklist to run through before pointing a scraper at a site you
do not own.

Techniques for defeating a site's wishes — rotating proxies to evade blocks, solving CAPTCHAs,
scraping behind logins — are deliberately absent.

## If something does not work

- **`ModuleNotFoundError`** — install the missing package from the list above. For Beautiful Soup,
  note that you install `beautifulsoup4` and import `bs4`.
- **`NameError` on a variable** — a cell earlier in the notebook has not been run. Use
  *Run → Run All Above Selected Cell*.
- **Different numbers from the notebook** — check that you ran the cell that creates the random
  generator, and that you have not re-run a cell that advances it. Every generator is seeded, so
  the values are reproducible in order.
- **The scraping notebook fails to start its server** — something else is using port 8731. Change
  `PORT` in that cell.
- **Plots do not appear** — add `%matplotlib inline` in a cell at the top. Modern Jupyter does not
  need it.
