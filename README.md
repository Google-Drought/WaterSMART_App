# WaterSMART App

The Nevada Groundwater-Dependent Ecosystems Water Needs Explorer is a Streamlit application for exploring climate, soil, and modeled groundwater-dependence information. Application source and local setup documentation are in [`streamlit_app/`](streamlit_app/README.md).

## Deployment

The production app is deployed through Streamlit Community Cloud's native GitHub integration under the `google-drought` workspace. It tracks the `main` branch of this repository and uses `streamlit_app/watersmart_streamlit_app.py` as its entrypoint. Pushing changes to `main` causes Streamlit to update the deployed app automatically.

Python dependencies and the runtime version are configured in `streamlit_app/requirements.txt` and `streamlit_app/runtime.txt`, respectively. Changes to either file cause the hosted environment to be rebuilt. Google Earth Engine credentials are stored in the app's Streamlit secrets settings and must not be committed to the repository. Deployment status, logs, secrets, and manual reboots are managed from the app's Streamlit Community Cloud dashboard.
