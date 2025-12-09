# Nevada GDE Water Needs Explorer (Streamlit)

Guide for running and understanding the Streamlit application that powers the Nevada Groundwater-Dependent Ecosystems (GDE) Water Needs Explorer.

## Local run
- Prereqs: Python 3.11 (see `runtime.txt`), `pip`, and a Google Earth Engine (GEE) service account with access to the referenced assets.
- Clone the repo and move into the app: `cd streamlit_app`.
- Create a virtual environment (recommended):
  - macOS/Linux: `python3 -m venv .venv && source .venv/bin/activate`
  - Windows: `python -m venv .venv && .venv\\Scripts\\activate`
- Install dependencies: `pip install -r requirements.txt`.
- Configure secrets: create `.streamlit/secrets.toml` with a `GEE_CREDS` block containing your service-account JSON (keep it out of version control). The app reads `st.secrets["GEE_CREDS"]` during startup to initialize Earth Engine.
- Launch: `streamlit run watersmart_streamlit_app.py`.

## File layout (key items)
- `watersmart_streamlit_app.py` — main Streamlit app with the GDE Explorer and Definitions tabs, map + data retrieval logic, modeling computations, charting, and PDF export.
- `.streamlit/config.toml` — Streamlit server/theme settings; `.streamlit/secrets.toml` — expected place for GEE credentials.
- `requirements.txt` — runtime Python dependencies; `runtime.txt` — pinned Python version for hosted platforms.
- `MixedEffectsModelCoefficients*.csv` — model coefficients joined to climate variables to calculate LAI, AET, groundwater subsidy, etc.
- `definitions_references.py` — HTML snippets for definitions/disclaimers/references used in the downloadable PDF.
- `main.css`, `color_palettes.py` — UI styling helpers for the primary app.
- `app_def/` — self-contained definitions/disclaimers page (header/footer components, CSS, assets).
- `deprecated/` — earlier prototypes kept for reference.

## Architecture notes
- Startup: `watersmart_streamlit_app.py` calls `get_auth()` to initialize GEE using the service-account info from secrets, then configures the Streamlit page and patches Folium with an Earth Engine layer helper.
- UI structure: two tabs. **GDE Explorer** hosts the interactive map and analysis workflow; **Definitions** renders the content from `app_def` for terminology and disclaimers.
- Map & data selection: sidebar controls manage lat/long inputs, layer toggles (soil texture, precipitation, PET, PWD, admin boundaries), and a “Get Data” trigger. The map is built with Folium/streamlit-folium, shows optional EE rasters/vectors, and syncs clicks back into Streamlit state.
- Data + modeling: once “Get Data” is pressed, the app samples GRIDMET climate (precip, PET, PWD) and soil texture at the chosen point, intersects admin basin boundaries, and builds a water-year time series. User inputs for rooting depth and soil texture filter the coefficient table, which is merged with climate stats to compute LAI, AET (total and groundwater component), and groundwater subsidy across water-table depths.
- Visualization & export: results are plotted with Plotnine (bars/lines/boxplots). A PDF export stitches together a cover page, the plot set, and the definitions/reference content (PyPDF2/PdfPages + PIL). A static map snapshot is embedded for location context.
- Assets & references: several legends/logos and the coefficients CSV are pulled from GitHub URLs (`PATH_*` constants near the top of `watersmart_streamlit_app.py`). Update these if hosting assets elsewhere or if offline access is needed.

## Operational notes
- GEE access errors usually mean the service account lacks permission to the asset IDs listed in `layer_assets` or the credentials are missing/incorrect in `.streamlit/secrets.toml`.
- Some plotting/export libraries (e.g., `pdf2image`) may require system packages on fresh machines; if PDF export fails, verify native dependencies for your OS.
