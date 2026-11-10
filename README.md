📌 Overview
This tool is a Python-based implementation of the IF-MACTOR framework (Intuitionistic Fuzzy Sets combined with the MACTOR method), as proposed in Yilmaz (2024). It is designed for Sustainable Flood Risk Management (SFRM) and other complex, multi-stakeholder decision-making processes.

The tool takes linguistic expert surveys as input, aggregates the fuzzy opinions, calculates the hidden power dynamics, and generates strategic visual dashboards and automated policy insights for decision-makers.

🛠️ Core Features
Expert Aggregation (IFWG): Handles multiple experts with customizable weightings, mapping linguistic terms (e.g., "Very High") to Intuitionistic Fuzzy Numbers (μ, ν).

Power Dynamics (MDII): Calculates both Direct and Indirect influence chains using the "weakest link" (min) rule.

Stakeholder Categorization: Plots stakeholders into 4 quadrants: Influential, Leading, Dependent, Autonomous.

Goal Mobilization: Weighs stakeholder power (rᵢ) against goal priorities to find which goals have the most political momentum.

Automated Policy Insights: Generates plain-English executive summaries for policymakers.

High-Quality Visualizations: Exports 5 distinct PNG charts (Histograms, Radar Charts, Quadrant Maps, Flowcharts) and renders an interactive Knowledge Graph.

⚙️ Function Directory
1. Core Math Functions
aggregate_ifs_experts(linguistic_values, weights): Uses the IFWG operator (Eq. 4) to aggregate multiple experts' opinions into a single (μ_agg, ν_agg) tuple.

defuzzify_ifs(ifs_tuple): Converts the fuzzy tuple into a crisp score using μ - ν (Eq. 5).

build_mdi_matrix(expert_surveys, stakeholders, weights): Creates the crisp Matrix of Direct Influences.

compute_mdii(mdi_df): Calculates the Matrix of Direct and Indirect Influences using the minimum-chain rule (Eq. 6).

2. Power Analysis Functions
compute_influence_dependence(mdii_df): Computes row sums (Influence, Iᵢ) and column sums (Dependence, Dᵢ).

compute_balance_of_power(I, D, mdii_df): Calculates the raw Balance of Power (rᵢ) and the Normalized Coefficient (rᵢ*) where 1.0 is the average.

categorize_stakeholders(power_df): Assigns stakeholders to the 4 quadrants based on median I and D values.

3. Goal Analysis Functions
build_stakeholder_goal_matrix(goal_surveys, ...): Aggregates expert surveys to see how much each stakeholder prioritizes each goal.

compute_wmsg(msg_df, r_dict): Multiplies the Goal Matrix by the Power Coefficient (rᵢ).

compute_mobilization(wmsg_df): Sums the columns of the Weighted Matrix to find the final Goal Mobilization scores (Mᵢ).

4. Visualization Functions
plot_power_histogram(): Bar chart showing rᵢ* with a baseline at 1.0.

plot_stakeholder_map(): 2D scatter plot of Influence (Y) vs. Dependence (X).

plot_radar_charts(): Spider charts showing priority spread for each stakeholder.

plot_mobilization_bars(): Horizontal bar chart ranking goals.

plot_knowledge_graph(): NetworkX force-directed graph showing all edges.

plot_flowchart_diagram(): A structured, hierarchical diagram with curved arrows mimicking the paper's style.

5. Executive Summary Functions
generate_policy_insights(...): Reads all calculated tables and prints a formatted, text-based report categorizing actors into Powerhouses, Managers, and Vulnerable groups, complete with conflict alerts.

run_workflow(...): The Master function. Takes the raw survey dictionaries, runs all math, generates all charts, saves the PNGs to a directory, and displays the result tables.


📥 Input Data Format
The tool requires two primary dictionaries (or JSON/Excel files parsed into dictionaries) for expert surveys.

1. Stakeholder-to-Stakeholder Survey (influence_surveys)
python
influence_surveys = {
    'Expert_1': {
        'S1': {'S1': '-', 'S2': 'VH', 'S3': 'H'},
        'S2': {'S1': 'MH', 'S2': '-', 'S3': 'VH'},
        'S3': {'S1': 'L',  'S2': 'ML', 'S3': '-'}
    },
    'Expert_2': { ... }
}
2. Stakeholder-to-Goal Survey (goal_surveys)
python
goal_surveys = {
    'Expert_1': {
        'S1': {'G1': 'VH', 'G2': 'H'},
        'S2': {'G1': 'VH', 'G2': 'MH'},
        'S3': {'G1': 'H',  'G2': 'L'}
    }
}
3. Expert Weights (expert_weights)
python
expert_weights = {'Expert_1': 0.60, 'Expert_2': 0.40} # Must sum to 1.0
🚀 How to Run
python
# 1. Define your data
stakeholders = ['S1', 'S2', 'S3']
goals = ['G1', 'G2']
labels_s = {'S1': 'Government', 'S2': 'NGO', 'S3': 'Citizens'}
labels_g = {'G1': 'Protect Lives', 'G2': 'Environment'}

# 2. Run the Master Workflow
results = run_workflow(
    influence_surveys=influence_surveys,
    goal_surveys=goal_surveys,
    stakeholders=stakeholders,
    goals=goals,
    expert_weights=expert_weights,
    stakeholder_labels=labels_s,
    goal_labels=labels_g,
    output_dir='/content/flood_results'
)

# 3. Generate Policy Insights
generate_policy_insights(
    power_df=results['Power & Dependence'],
    msg_df=results['MSG (Stakeholder-Goal)'],
    mobil_df=results['Goal Mobilization'],
    categories={s: cat for s, cat in results['Stakeholder Categories']['Category'].items()},
    stakeholder_labels=labels_s,
    goal_labels=labels_g
)
🔤 Appendix: Linguistic to IFS Mapping (Table 1 from Paper)
Abbreviation	Full Term	IFS Tuple (μ, ν)	Meaning
AL	Absolutely Low	(0.05, 0.85)	Almost zero influence/priority
VL	Very Low	(0.15, 0.75)	Minimal influence/priority
L	Low	(0.25, 0.65)	Slight influence/priority
ML	Medium Low	(0.35, 0.55)	Below-average influence
AE	Almost Equal	(0.45, 0.45)	Neutral; no significant effect
MH	Medium High	(0.55, 0.35)	Above-average influence
H	High	(0.65, 0.25)	Strong influence/priority
VH	Very High	(0.75, 0.15)	Extremely strong influence/priority
AH	Absolutely High	(0.85, 0.05)	Maximum possible influence
📦 Outputs Directory
When you run run_workflow(), it creates an output_dir containing:

01_power_histogram.png

02_stakeholder_map.png

03_radar_charts.png

04_mobilization_bars.png

05_knowledge_graph.png

06_relational_diagram.png (if flowchart function is called)
