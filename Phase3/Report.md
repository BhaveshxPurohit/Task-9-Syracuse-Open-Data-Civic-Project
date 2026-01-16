**Report: Syracuse Road Resilience and Infrastructure Equity Framework**
**Phase 3: Development (Weeks 5-10)**

This report documents the core technical development phase of the project, transitioning from proposal to a fully functional, equity-aware decision-support system for the City of Syracuse. The work focused on building a reproducible analysis pipeline, integrating LLM-generated policy narratives with rigorous validation, and implementing a comprehensive quality assurance framework.

---

### **1. Technical Development: Analysis Pipeline**

A foundational requirement was establishing a **reproducible data processing pipeline** with a clear separation of concerns, modeled on a "Medallion" architecture to ensure traceable data lineage.

**Data Acquisition & Ingestion (Bronze Tier):** Raw CSV files from the Municipal Open Data Portal (Pavement Ratings 2020, Emergency Snow Routes) were ingested and preserved immutably using Pandas. Initial schema validation confirmed the presence of critical fields like `Rating_202` and `WARD`.

**Transformation & Feature Engineering (Silver Tier):** This layer resolved key data integration challenges:
*   **Street Name Matching:** Implemented a **fuzzy matching algorithm** (Levenshtein distance) to reconcile naming inconsistencies (e.g., "Adams St" vs. "East Adams Street") with a 92% success rate.
*   **Data Cleaning:** Addressed issues like zero-width records (via median imputation) and unknown ward tags (via spatial logic).
*   **Equity Score Calculation:** Engineered a key metric by aggregating census tract-level indicators: poverty rate (29.6% city-wide), rent burden (65.4%), and elderly population (14.3%).

**Analytical Modeling & Presentation (Gold Tier):** The cleaned data is synthesized into a prioritized reconstruction list using an algorithm that implements the city’s **50/50 weighting mandate**—balancing pavement health with socio-economic equity. The presentation layer, built with **Streamlit**, provides an interactive dashboard for planners to adjust weighting parameters and visualize impacts in real-time.

---

### **2. LLM Integration & Narrative Synthesis**

A core innovative component is the integration of a Large Language Model to translate data into actionable policy narratives.

**Prompt Engineering Principles:** We employed **"Context Engineering"** and **persona assignment** (e.g., "Senior Municipal Infrastructure Policy Advisor") to guide the LLM. Prompts were structured to:
1.  Identify the modal pavement rating for a ward.
2.  Highlight "Poor" rated segments on Emergency Snow Routes.
3.  Correlate findings with ward-level poverty and rent-burden data.
4.  Suggest appropriate maintenance strategies based on official condition descriptions.

**Iterative Prompt Development & Validation:** We documented a cycle of refinements:
*   **v1.0 (Zero-shot):** Produced broad, sometimes inaccurate summaries.
*   **v1.3 (Chain-of-Thought):** Required the LLM to reason step-by-step (e.g., "Calculate the percentage of failed segments first"), drastically improving logical soundness.
*   **v1.4 (Ground-truth verification):** Integrated a **deterministic validation layer** where every LLM claim (e.g., "Ward 9 has the most failed emergency routes") is fact-checked against the underlying Pandas dataframe to eliminate hallucinations.

**Uncertainty Communication:** The system instructs the LLM to explicitly flag "Unrated" segments (e.g., bridge decks) as "Data Insufficient," ensuring transparency where the data is incomplete.

---

### **3. Quality Assurance Framework**

We established a **"Zero-Trust" QA protocol** to ensure robustness and accuracy.

**Unit Testing:** Using **Pytest**, we automated tests for critical calculations:
*   **Boundary Validation:** Ensuring priority scores remain within a 0-1 normalized range.
*   **Weight Consistency:** Verifying the 50/50 equity-infrastructure split functions correctly.
*   **Edge Case Handling:** Testing logic for imputing missing width values and handling industrial zones with low residential population but high traffic.

**Validation of Outputs:**
*   **Narrative Claims:** Implemented checks for semantic alignment and factual consistency against human-annotated "Gold Standard" findings.
*   **Visualizations:** Added "shape checks" in the Streamlit dashboard to prevent misleading empty charts, ensuring informative messages display if a selected ward has no snow routes, for example.

---

### **4. Progress Checkpoints & System Architecture**

**Week 6: Architecture Review**
We prepared a technical blueprint emphasizing **modularity and scalability**.
*   **System Architecture:** Designed as a modular **Directed Acyclic Graph (DAG)** with four decoupled nodes: Ingestion, Processing, Insight (LLM), and UI (Streamlit). This separation ensures data processing does not impede dashboard responsiveness.
*   **Key Design Decision:** Chose a **containerized Docker deployment** for consistent execution across municipal servers.
*   **Primary Blocker Identified:** The 2020 pavement dataset lacked granular "dominant distress" data. Our architecture was made **"forward-compatible"** to ingest future, more detailed data without core redesign.

**Week 8: Working Prototype**
This checkpoint demonstrated a functional proof-of-concept.
*   **Core Pipeline Operational:** Successfully processed 3,675 pavement records, mapping them to 19 wards with the fuzzy-matching logic active.
*   **Interface Established:** Delivered a multi-page Streamlit dashboard with an executive overview, ward deep-dive, and a first iteration of the LLM narrative generator.
*   **Feedback Incorporated:** User input highlighted the need for more granular distress context, which informed the final feature set.

**Week 10: Feature Complete**
The system achieved all planned functionality:
*   **Fully Reproducible Pipeline:** A single command executes the entire workflow from raw data to insights.
*   **Validated LLM Integration:** Narrative generator with built-in fact-checking.
*   **Comprehensive Visualization Suite:** Interactive maps, correlation matrices, and a prioritization sandbox.
*   **Documentation & Traceability:** Complete technical log of all data transformations for auditability.

---

### **5. Strategic Findings & Technical Conclusion**

The analysis reveals significant disparities: while the city's modal road rating is "Good," **46% of the network is in "Fair" or "Poor" condition.** The integrated model identifies a **"Cumulative Vulnerability" pattern**, where wards with high poverty rates (e.g., Ward 9) exhibit a higher density of failed critical emergency routes.

**Key Technical Conclusions:**
1.  **Reproducible Integration is Achievable:** A modular Python pipeline can successfully unify disparate municipal and federal datasets.
2.  **Equity Weighting Transforms Priorities:** The 50/50 model shifts resource allocation meaningfully toward historically underserved neighborhoods.
3.  **LLMs Enhance Accessibility:** When rigorously validated, LLM narratives make complex data actionable for non-technical stakeholders.
4.  **QA is Essential for Trust:** Automated testing and validation protocols are non-negotiable for high-stakes public policy tools.

**Actionable Recommendations:**
*   **Immediate Target:** Prioritize the 18 segments of failed emergency snow routes in **Ward 9 (Near Westside)** for the 2026 reconstruction cycle.
*   **Formalize Equity Protocol:** Adopt the multi-factor equity calculation across all city infrastructure departments.
*   **Expand Data Collection:** Move from a 25% annual pavement rating sample to a 100% survey to prevent model drift.

The Phase 3 development cycle has delivered a robust, transparent, and equitable decision-support framework, providing Syracuse with a scientifically grounded roadmap for investing in both its physical infrastructure and the social well-being of its residents.
