import matplotlib

matplotlib.use('Agg')
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import streamlit as st

st.set_page_config(page_title="TIEM UAE", layout="wide")

st.markdown("""
    
""", unsafe_allow_html=True)

st.title("Trace UAE: Industrial Effluent Management")
st.markdown(
    "Simulating smart telemetry, GPS truck geofencing, and resource recovery"
    " algorithms across UAE industrial sectors using realistic loss-mitigation"
    " and estimated corporate effluent bounds."
)

with st.sidebar:
  st.header("Control Panel")
  st.markdown("---")

  st.subheader("1. Temporal View Mode")
  time_mode = st.radio(
      "Select Analysis Scale",
      [
          "Month-to-Month (Sine Wave Simulation)",
          "Year-to-Year (Historical Multi-Year Disclosures)",
      ],
  )

  st.markdown("---")
  st.subheader("2. Industrial Entity Selection")

  presets = {
      "ADNOC Group (Oil & Gas)": 154943,
      "Emirates Global Aluminium - EGA (Smelting)": (
          125000
      ),
      "Borouge (Petrochemicals)": (
          45000
      ), 
      "Union Cement Company (Manufacturing)": (
          85000
      ),
  }

  company_choice = st.selectbox(
      "Choose Target Organization", list(presets.keys()) + ["Custom Enterprise"]
  )

  if company_choice == "Custom Enterprise":
    st.subheader("Custom Company Parameters")
    custom_name = st.text_input("Company Name", "e.g., National Energy Corp")
    base_monthly_waste = st.number_input(
        "Baseline Scale Value (Tonnes / Year)",
        min_value=100.0,
        max_value=200000.0,
        value=50000.0,
        step=500.0,
    )
    active_company_name = custom_name if custom_name else "Custom Enterprise"
  else:
    active_company_name = company_choice
    base_monthly_waste = presets[company_choice]

  st.markdown("---")
  st.subheader("3. Technology Parameters (Realistic Optimization Caps)")

  sensor_adoption = st.slider(
      "IoT Tank Alarms & Telemetry (%)",
      0,
      100,
      80,
      step=5,
      help="Prevents accidental storage overflow & fugitive leaks (Max ~5%).",
  )

  gps_compliance = st.slider(
      "GPS Fleet Geofencing & Routing (%)",
      0,
      100,
      85,
      step=5,
      help="Eliminates unauthorized transit route discrepancies (Max ~5%).",
  )

  recovery_rate = st.slider(
      "Circular Useful Waste Extraction (%)",
      0,
      50,
      25,
      step=5,
      help=(
          "Treats and recovers high-value byproducts back into secondary loops"
          " (Max ~10%)."
      ),
  )

if "Month-to-Month" in time_mode:
  monthly_base = base_monthly_waste / 12.0
  timeline_labels = [
      "Jan",
      "Feb",
      "Mar",
      "Apr",
      "May",
      "Jun",
      "Jul",
      "Aug",
      "Sep",
      "Oct",
      "Nov",
      "Dec",
  ]
  x_vals = np.linspace(0, 2 * np.pi, 12)
  seasonal_fluctuation = np.sin(x_vals) * (monthly_base * 0.08)
  baseline_trajectory = monthly_base + seasonal_fluctuation
  x_label_name = "Operational Timeline (Monthly Breakdown)"
else:
  timeline_labels = ["2021", "2022", "2023", "2024", "2025"]
  yoy_scaling = {
      "ADNOC Group (Oil & Gas)": [1.08, 1.04, 1.00, 0.97, 0.94],
      "Emirates Global Aluminium - EGA (Smelting)": [1.05, 1.02, 1.00, 0.98, 0.95],
      "Borouge (Petrochemicals)": [0.90, 0.94, 0.98, 1.00, 0.96],
      "Union Cement Company (Manufacturing)": [1.02, 1.04, 1.01, 0.99, 0.95],
  }
  if company_choice in yoy_scaling:
    baseline_trajectory = (
        np.array(yoy_scaling[company_choice]) * base_monthly_waste
    )
  else:
    baseline_trajectory = (
        np.array([1.08, 1.04, 1.00, 0.96, 0.92]) * base_monthly_waste
    )
  x_label_name = "Multi-Year Reporting Timeline"

tank_mitigation = (
    sensor_adoption / 100.0
) * 0.05 
gps_mitigation = (gps_compliance / 100.0) * 0.05 
recovery_mitigation = (
    recovery_rate / 50.0
) * 0.10 

total_reduction_factor = tank_mitigation + gps_mitigation + recovery_mitigation
optimized_trajectory = baseline_trajectory * (1 - total_reduction_factor)

total_baseline_cumulative = sum(baseline_trajectory)
total_optimized_cumulative = sum(optimized_trajectory)
total_waste_diverted = total_baseline_cumulative - total_optimized_cumulative
pct_saved_total = (
    (total_waste_diverted / total_baseline_cumulative) * 100
    if total_baseline_cumulative > 0
    else 0
)

col1, col2, col3, col4 = st.columns([1.5, 1.2, 1.3, 1.3])

col1.metric("Selected Enterprise", active_company_name)
col2.metric("Baseline Aggregate", f"{total_baseline_cumulative:,.0f} Tonnes")
col3.metric(
    "Optimized Output",
    f"{total_optimized_cumulative:,.0f} Tonnes",
    delta=f"-{pct_saved_total:.1f}% Efficiency",
)
col4.metric(
    "Effluent Diverted",
    f"{total_waste_diverted:,.0f} Tonnes",
    help="Volume successfully kept out of the surrounding ecosystem.",
)

st.markdown("---")

st.subheader(f"Trajectory Analysis ({time_mode}): {active_company_name}")

fig, ax = plt.subplots(figsize=(10, 4.2))

ax.plot(
    timeline_labels,
    baseline_trajectory,
    label="Previous System (Unmanaged Discharges / High Leakage)",
    color="#d9534f",
    linewidth=2.5,
    linestyle="--",
    marker="o",
)
ax.plot(
    timeline_labels,
    optimized_trajectory,
    label=(
        "Proposed System (Smart Tanks + GPS Geofencing + Resource Recovery)"
    ),
    color="#2e7d32",
    linewidth=3.5,
    marker="s",
)

if "Month-to-Month" in time_mode:
  ax.set_ylim(0, 20000) 
else:
  ax.set_ylim(
      0, 180000
  ) 

ax.set_title(
    f"Environmental Impact Projection — {active_company_name} [{time_mode}]",
    fontsize=12,
)
ax.set_ylabel("Effluent Volume / Scale (Tonnes)", fontsize=11)
ax.set_xlabel(x_label_name, fontsize=11)
ax.grid(True, linestyle=":", alpha=0.6)
ax.legend(fontsize=10, loc="upper right")

st.pyplot(fig)


st.markdown("---")

with st.expander(
    "Real-Time Central Database & Telemetry Stream (Simulated Live Feeds)"
):
  st.markdown(
      "Live database ledger verifying incoming feeds from automated facility"
      " storage units and logistics transport trucks:"
  )

  st.info(
      "ℹ **Prototype Status:** This telemetry log stream is **hard-coded** for"
      " MVP demonstration purposes and is structured to map to live MQTT/REST"
      " endpoints in a full production deployment."
  )

  simulation_db = pd.DataFrame({
      "Log ID": [
          "LOG-9012",
          "LOG-9013",
          "LOG-9014",
          "LOG-9015",
          "LOG-9016",
      ],
      "Facility / Unit": [
          f"{active_company_name} - Tank A",
          f"{active_company_name} - Tank B",
          "Logistics Fleet - Truck #4",
          "Logistics Fleet - Truck #9",
          f"{active_company_name} - Recovery Unit",
      ],
      "Event Trigger": [
          "Telemetry Check: Normal",
          "IoT Alert: High Threshold (Auto-Routed)",
          "GPS Route Matching: 100% Compliant",
          "Geofence Warning: Minor Deviation (Corrected)",
          "Useful Compound Extraction: Active",
      ],
      "Volume Impact (Tonnes)": [
          f"-{(baseline_trajectory[0] * 0.002):.1f}",
          f"-{(baseline_trajectory[0] * 0.003):.1f}",
          f"-{(baseline_trajectory[0] * 0.004):.1f}",
          f"-{(baseline_trajectory[0] * 0.001):.1f}",
          f"-{(baseline_trajectory[0] * 0.008):.1f}",
      ],
      "Status": [
          "Secured",
          "Mitigated (No Spill)",
          "Verified",
          "Rerouted Safely",
          "Recycled",
      ],
  })

  st.dataframe(simulation_db, use_container_width=True)

st.markdown("###Scientific Methodology: Mass-Balance Optimization Bounds")
st.markdown(
    "To respect industrial thermodynamics and process chemistry, the"
    " simulation model limits maximum combined optimization to **~20%** at full"
    " adoption. Core industrial output is dictated by production scale; these"
    " controls target preventable fugitive losses and secondary recovery"
    " loops:"
)

col_m1, col_m2, col_m3 = st.columns(3)
with col_m1:
  st.success(
      "**1. IoT Tank Alarms**\n\n* **Max Impact:** 5%\n* **Target:**"
      " Uncontained storage overflow & pipe leak discrepancies."
  )
with col_m2:
  st.success(
      "**2. GPS Fleet Geofencing**\n\n* **Max Impact:** 5%\n* **Target:**"
      " Transit loss & unauthorized route deviation risks."
  )
with col_m3:
  st.success(
      "**3. Circular Recovery**\n\n* **Max Impact:** 10%\n* **Target:** On-site"
      " chemical valorization & wastewater recycling loops."
  )

st.markdown("---")

with st.expander(
    "Research Benchmarks & Realistic Baseline Estimation Context"
):
  st.markdown(
      "Simulation baselines are estimated to be realistic models, calibrated"
      " using operational insights gathered from corporate research during our"
      " development process:"
  )

  ds_col1, ds_col2 = st.columns(2)
  with ds_col1:
    st.markdown("""
        * **ADNOC Group (Oil & Gas)**
          * Estimated Baseline: **154,943 metric tonnes**
          * *Research Context:* Informed by group-wide operational waste disclosures.
        * **Emirates Global Aluminium (Smelting)**
          * Estimated Baseline: **125,000 metric tonnes**
          * *Research Context:* Benchmarked against industrial smelting byproduct profiles.
        """)
  with ds_col2:
    st.markdown("""
        * **Borouge (Petrochemicals)**
          * Estimated Baseline: **45,000 metric tonnes**
          * *Research Context:* Guided by petrochemical hazardous and effluent datasets.
        * **Union Cement Company (Manufacturing)**
          * Estimated Baseline: **85,000 metric tonnes**
          * *Research Context:* Scaled from regional manufacturing capacity and kilning benchmarks.
        """)
