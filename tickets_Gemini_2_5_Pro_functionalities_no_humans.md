Of course. Here is a detailed list of software development tickets based on the plan, complete with unique IDs, descriptions, and dependencies.

### **Phase 1: Foundation and Data Ingestion**

**Ticket ID: AIM2-001**

* **Title:** Initialize Project Structure  
* **Description:** Set up the project's foundational directory structure using the Cookiecutter Data Science (CCDS) template.1 This includes creating the standard directories for data (  
  raw, interim, processed), source code (src/aim2), models, notebooks, and reports. This standardized structure is critical for ensuring the project is reproducible and scalable.  
* **Programmable Independently:** Yes.  
* **Dependencies:** None.

**Ticket ID: AIM2-002**

* **Title:** Establish Python Environment and Core Dependencies  
* **Description:** Create and populate the requirements.txt file with the core Python libraries required for the project. This includes Owlready2 2 for ontology manipulation,  
  EMMOntoPy 4 for advanced ontology management,  
  pymed\_paperscraper 5 for PubMed API access,  
  unpywall 6 for Open Access article retrieval,  
  PyMuPDF 7 for high-fidelity PDF parsing,  
  lxml for XML parsing, transformers for AI models, langchain for LLM interaction, TheFuzz/RapidFuzz 9 for string matching,  
  BioEL 11 for entity linking, and  
  seqeval for evaluation.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-001.

**Ticket ID: AIM2-003**

* **Title:** Module 1: Create AIM2 Ontology Scaffold  
* **Description:** Develop a Python script using Owlready2 to create the initial aim2.owl file. This script will define the base IRI, establish the core hierarchical classes (StructuralAnnotation, SourceAnnotation, FunctionalAnnotation), and pre-define namespaces for external ontologies to prevent IRI conflicts.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-002.

**Ticket ID: AIM2-004**

* **Title:** Module 5: Implement PubMed Metadata Retrieval  
* **Description:** Build a script to query the PubMed API using the pymed\_paperscraper library.5 The script will use keywords derived from the ontology to search for relevant articles and will store all retrieved metadata (PMID, PMCID, DOI, etc.) in a local SQLite database to track processing status and avoid duplicate downloads. The  
  Bio.Entrez module may be used as a supplementary tool.12  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-002, AIM2-003.

**Ticket ID: AIM2-005**

* **Title:** Module 6: Implement Open Access Full-Text Acquisition  
* **Description:** Develop a module that uses the unpywall Python client to query the Unpaywall API for legally available Open Access versions of articles identified by their DOIs in the metadata database.6 This will be the primary method for full-text retrieval.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-004.

**Ticket ID: AIM2-006**

* **Title:** Module 6: Develop Paywall Navigation Scraper  
* **Description:** (To be used only for institutionally subscribed content). Create a sophisticated web scraping module using Playwright to programmatically access and download articles behind paywalls. This module must implement advanced bot-detection evasion techniques, including rotating residential proxies, realistic HTTP headers, and human-like interaction patterns.15  
* **Programmable Independently:** Yes, can be developed in parallel with AIM2-005.  
* **Dependencies:** AIM2-004.

**Ticket ID: AIM2-007**

* **Title:** Module 7: Implement Document Parsing and Normalization  
* **Description:** Create a robust parsing pipeline that processes downloaded files. Use PyMuPDF (Fitz) for its superior handling of complex, multi-column PDF layouts.7 Use  
  lxml for efficient parsing of PubMed Central XML files. The script must clean the extracted text, normalize whitespace, and divide the content into overlapping chunks suitable for LLM processing.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-005, AIM2-006.

### **Phase 2: AI Model Development and Bootstrapping**

**Ticket ID: AIM2-008**

* **Title:** Module 2: Automate External Ontology Term Integration  
* **Description:** Develop a script to programmatically integrate terms from various source ontologies (Plant Ontology, GO, etc.). Use the ontoconvert tool from EMMOntoPy for initial bulk merging and a more granular Owlready2 script for controlled mapping of external terms into the AIM2 ontology structure.4  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-003.

**Ticket ID: AIM2-009**

* **Title:** Module 10: Implement Synthetic Data Generation for NER/RE  
* **Description:** Create a script that uses a powerful generative LLM (e.g., GPT-4o) to produce a large-scale synthetic dataset. The LLM will be prompted with the AIM2 ontology schema to generate scientifically plausible sentences with corresponding entity and relationship annotations in JSON format. Implement an "Evol-Instruct" methodology to iteratively increase the complexity and diversity of the generated examples.18  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-008 (requires a mature ontology schema).

**Ticket ID: AIM2-010**

* **Title:** Module 8 (Stage 1): Fine-Tune BioBERT for Candidate NER  
* **Description:** Use the synthetic dataset from AIM2-009 to fine-tune a dmis-lab/biobert-v1.1 model for the NER task.20 The implementation will use the Hugging Face  
  Trainer API for a standardized and efficient training process.22 The goal of this model is to serve as a high-recall, low-cost candidate generator.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-009.

**Ticket ID: AIM2-011**

* **Title:** Module 8 (Stage 2): Implement LLM-Based NER Validation  
* **Description:** Develop the second stage of the hybrid NER system. This script will take text chunks with candidate entities identified by the fine-tuned BioBERT model (from AIM2-010) and use a powerful generative LLM to validate, correct, or reject these candidates, as well as identify novel entities the first-stage model may have missed.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-007, AIM2-010.

**Ticket ID: AIM2-012**

* **Title:** Module 9: Develop Advanced Relationship Extraction  
* **Description:** Implement the relationship extraction module using a powerful LLM. Utilize LangChain to enforce structured JSON output via Pydantic models.23 Engineer a hierarchical, Chain-of-Thought (CoT) prompt that instructs the model to first identify general relationships and then conditionally probe for more specific mechanisms (e.g.,  
  upregulates as a sub-type of affects).24  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-011.

### **Phase 3: Integration, Refinement, and Knowledge Base Population**

**Ticket ID: AIM2-013**

* **Title:** Module 3: Implement Algorithmic Ontology Trimming  
* **Description:** Develop a Python function to programmatically filter and trim large source ontologies. The algorithm will use a list of seed keywords to perform a controlled hierarchical expansion (get\_ancestors/get\_descendants from EMMOntoPy 27) and then prune the resulting set based on metadata (e.g., removing 'obsolete' terms).  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-008.

**Ticket ID: AIM2-014**

* **Title:** Module 4: Define Custom Hierarchical Relationships  
* **Description:** Create a script to formally define custom semantic relationships (made\_via, accumulates\_in, affects, etc.) as ObjectProperty classes in Owlready2.2 The script must specify the  
  domain and range for each property and define sub-property hierarchies (e.g., upregulates is\_a affects).  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-013.

**Ticket ID: AIM2-015**

* **Title:** Module 11: Set Up Semi-Automated Quality Control Workflow  
* **Description:** Implement a Human-in-the-Loop (HITL) workflow using the Prodigy annotation tool.29 This is not for manual annotation from scratch, but for a rapid, model-guided review of the  
  *synthetic* data. The script will use active learning to present only the most uncertain predictions from the NER model for quick human correction, which will then be fed back to improve the model.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-010.

**Ticket ID: AIM2-016**

* **Title:** Module 12: Implement Entity Normalization and Linking  
* **Description:** Build the entity linking pipeline. First, use TheFuzz or RapidFuzz for syntactic normalization of extracted text strings.9 Second, use the  
  BioEL package to perform semantic linking of the normalized strings to their canonical concepts in the final AIM2 ontology.11  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-012, AIM2-014.

**Ticket ID: AIM2-017**

* **Title:** Module 13: Implement Knowledge Base Population and Management  
* **Description:** Create the final scripts to manage the extracted knowledge triples. This includes implementing a hashing mechanism for deduplication, a rule-based system for conflict resolution, and persisting the final, curated triples into both version-controlled CSV files and the Owlready2 SQLite-backed knowledge base.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-016.

### **Phase 4: Evaluation and Finalization**

**Ticket ID: AIM2-018**

* **Title:** Create Gold Standard Evaluation Dataset  
* **Description:** A non-coding task to manually annotate a curated set of \~25 scientific papers according to a strict guideline. This will produce the ground truth dataset required for all subsequent performance evaluations.  
* **Programmable Independently:** Yes, can be performed in parallel after AIM2-007 is complete.  
* **Dependencies:** AIM2-007.

**Ticket ID: AIM2-019**

* **Title:** Module 14: Develop Automated Evaluation Framework  
* **Description:** Write a Python script that automates the performance evaluation of the NER and RE models. The script will use the seqeval library to calculate and report strict, token-level precision, recall, and F1-scores by comparing the system's output against the gold standard dataset from AIM2-018.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-018.

**Ticket ID: AIM2-020**

* **Title:** Module 15: Conduct Comparative LLM Benchmarking  
* **Description:** Execute the entire end-to-end pipeline (Modules 5 through 13\) using various configurations of LLMs (e.g., Llama 3, GPT-4o, BioBERT) and prompting strategies. Use the evaluation framework from AIM2-019 to systematically measure the F1-score, inference speed, and API cost for each configuration. Compile the results into a final report to select the optimal production model.  
* **Programmable Independently:** No.  
* **Dependencies:** AIM2-017, AIM2-019.