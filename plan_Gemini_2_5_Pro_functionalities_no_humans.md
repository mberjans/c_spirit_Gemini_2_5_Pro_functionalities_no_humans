

# **A Comprehensive Development Plan for the AIM2 Backbone Annotated Metabolites Network**

## **Part I: Foundational Architecture and Project Setup**

The successful development of the AIM2 project, an ambitious initiative to construct a backbone annotated metabolites network using Large Language Models (LLMs), hinges on a robust and scalable software architecture. This initial phase of the development plan is dedicated to establishing the project's technical foundation. The choices made here—regarding project structure, environment management, and the core software stack—are not preliminary administrative tasks but strategic decisions that will fundamentally dictate the project's capacity for automation, reproducibility, and long-term maintainability. This section outlines a meticulously planned architecture designed to support a complex, multi-module computational biology workflow, ensuring that the system can operate with the full automation stipulated by the project's core requirements.

### **Section 1: A Reproducible Project Structure for Computational Science**

A logical, standardized, and reproducible project structure is the bedrock of any serious computational science endeavor. For a project of AIM2's complexity, which involves distinct stages of data acquisition, processing, modeling, and evaluation, an ad-hoc directory structure would inevitably lead to irreproducible results, broken dependencies, and significant maintenance overhead. To avert these issues, the project will be initiated using a battle-tested, community-standard template.

#### **1.1 Adopting the Cookiecutter Data Science (CCDS) Template**

The project will adopt the Cookiecutter Data Science (CCDS) template as its foundational structure.1 This template is not merely a suggestion for organizational tidiness; it is a formal framework that enforces a critical separation of concerns. By programmatically segregating raw data, intermediate files, processed data, source code, models, and reports, CCDS provides a logical and predictable layout that is essential for building the automated, multi-stage pipelines required by AIM2. This standardized structure ensures that any researcher or developer joining the project can immediately understand the data flow and locate relevant assets, drastically reducing onboarding time and facilitating collaboration.

#### **1.2 Mapping AIM2 Components to the CCDS Structure**

The modular nature of the AIM2 project maps cleanly onto the CCDS directory structure. This explicit mapping provides a clear blueprint for where each component's inputs and outputs should reside, forming the basis of the automated workflow.

* data/raw/: This directory will serve as the immutable repository for all initial data inputs. It will house the downloaded PubMed Central XML files, retrieved full-text PDFs, and the original source ontology files (in OWL, CSV, or other formats). By policy, files in this directory will never be modified by any script, ensuring the original data is always preserved for reproducibility.1  
* data/interim/: This directory will act as a staging area for intermediate data artifacts. It will store parsed and cleaned text content from PDFs and XMLs, chunked documents prepared for LLM input, and the raw, un-normalized JSON outputs from the NER and RE models.  
* data/processed/: This is the destination for the final, high-value outputs of the pipeline. The fully populated and versioned AIM2 ontology (in both OWL and Owlready2's SQLite format) and the comprehensive knowledge base of normalized, deduplicated triples (in CSV format) will be stored here.  
* src/aim2/: The core of the project will be developed as a modular and importable Python package located in this directory. This design choice is critical for creating stand-alone yet interoperable programs. The sub-module structure will be as follows:  
  * src/aim2/ontology/: Modules dedicated to ontology creation, programmatic trimming, merging, and the definition of custom relationships (Modules 1-4).  
  * src/aim2/corpus/: Modules responsible for the entire literature acquisition and parsing pipeline, from API querying to PDF text extraction (Modules 5-7).  
  * src/aim2/extraction/: The core AI modules for performing Named Entity Recognition and Relationship Extraction using fine-tuned transformers and LLMs (Modules 8-9).  
  * src/aim2/synthesis/: Modules for generating synthetic data for model training and implementing the quality control workflow (Modules 10-11).  
  * src/aim2/linking/: Modules for entity normalization via fuzzy matching and linking to the AIM2 ontology (Modules 12-13).  
  * src/aim2/evaluation/: Modules for creating the gold-standard dataset and running the final benchmarking reports (Modules 14-15).  
* models/: This directory will store the serialized, trained machine learning models. This includes the fine-tuned BioBERT model checkpoints for NER and any cached LLM models if using local instances to reduce API costs.  
* notebooks/: Jupyter notebooks will be used exclusively for exploratory data analysis, prototyping algorithms, and debugging. To maintain a clear record of the research process, a strict naming convention will be enforced (e.g., 01\_ontology\_trimming\_exploration.ipynb, 02\_llm\_prompt\_testing.ipynb), as recommended by best practices.1  
* reports/: This directory is the designated output location for all generated analyses, including the final LLM benchmarking reports, performance metrics, and any data visualizations produced by the pipeline.

The adoption of this specific project structure is a foundational step toward achieving the "no manual effort" constraint. A well-defined file structure is a prerequisite for robust automation, as it allows for the creation of deterministic data processing pipelines. A master script or a Makefile can be configured to understand the dependencies between these directories. For instance, the execution of an extraction module (src/aim2/extraction/) can be automatically triggered only when new parsed text files appear in the data/interim/ directory. The output of this module will, in turn, deterministically populate the data/processed/ directory. This transforms the project from a mere collection of disconnected scripts into a cohesive, automated, and reproducible scientific instrument. The structure itself becomes an enabler of the project's core philosophy. This is because automation requires a clear, predictable flow of data and execution. In a flat or ad-hoc directory structure, defining clear dependencies between scripts and data artifacts becomes impossible, leading to fragile workflows where a script might accidentally read from or write to an incorrect location, breaking the entire processing chain. The CCDS template's separation of data by its processing stage (raw, interim, processed) provides the necessary foundation for a rules-based workflow management system, effectively creating a Directed Acyclic Graph (DAG) of operations that can be executed automatically and reliably.

### **Section 2: The Python Ecosystem for the AIM2 Project**

The selection of the Python software stack is a critical architectural decision that directly impacts the project's performance, capabilities, and development velocity. The AIM2 project requires a carefully curated set of libraries that are robust, well-maintained, and specifically suited for the high-performance, automated nature of large-scale ontology engineering and natural language processing.

#### **2.1 Core Libraries and Environment Management**

The project will standardize on Python 3.9 or higher to leverage modern language features and ensure compatibility with the latest versions of key libraries. All dependencies will be meticulously managed via a requirements.txt file, a standard practice enforced by the CCDS template that is essential for guaranteeing a reproducible computational environment across different systems and collaborators.1

#### **2.2 Proposed Key Libraries and Justification**

The following table outlines the recommended Python libraries that will form the technical backbone of the AIM2 project. Each library has been selected based on a thorough evaluation of its features, performance, and suitability for the specific, demanding tasks required by the project's objectives. This curated stack represents a best-in-class ecosystem for automated knowledge extraction in the biomedical domain.

| Category | Library | Version | Role in AIM2 | Justification & Key Snippets |
| :---- | :---- | :---- | :---- | :---- |
| **Ontology** | Owlready2 | \>=0.48 | Core ontology manipulation: loading, creating classes/properties, reasoning, saving. | Provides a Pythonic, object-oriented interface to OWL ontologies, backed by a performant SQLite3 quadstore suitable for large ontologies.2 Its ability to define classes and properties directly in Python 2 is central to programmatic ontology construction. |
| **Ontology** | EMMOntoPy | \>=0.8.0 | High-level ontology management: merging, conversion, documentation. | Extends Owlready2 with crucial automation features. The ontoconvert tool for merging/squashing ontologies is essential for integrating terms from multiple sources.6 Accessing entities by label simplifies code.6 |
| **Lit. Acq.** | pymed (pymed\_paperscraper) | \>=0.2.2 | PubMed abstract and metadata querying. | Simplifies interaction with the PubMed API by handling query construction, request batching, and parsing of results, which is critical for building the corpus efficiently.7 |
| **Lit. Acq.** | unpywall | Latest | Legal retrieval of Open Access full-text articles. | Provides a direct, API-driven method to find OA versions of articles identified by DOI, serving as the primary, ethical route for full-text acquisition.8 |
| **Lit. Acq.** | Playwright | Latest | Programmatic browser automation for paywalled content. | For accessing institutionally-subscribed articles not available via Unpaywall. Playwright is a modern alternative to Selenium and can be combined with specialized tools like Botasaurus to handle advanced bot detection.11 |
| **Doc. Parsing** | PyMuPDF (Fitz) | Latest | High-fidelity PDF text and layout extraction. | Superior to other libraries for scientific documents with complex, multi-column layouts and tables. Its ability to preserve text positioning is critical for contextual understanding by LLMs.14 |
| **Doc. Parsing** | lxml | Latest | Efficient parsing of PubMed Central XML files. | A high-performance and robust library for parsing large XML files, essential for processing the bulk XML data from PMC. |
| **NLP/LLM** | transformers | Latest | Accessing and fine-tuning models from the Hugging Face Hub (e.g., BioBERT). | The de-facto standard for working with transformer models. Essential for Module 8 (fine-tuning NER) and Module 15 (benchmarking).17 |
| **NLP/LLM** | langchain | Latest | Structuring interactions with LLMs for extraction. | Provides tools to create structured prompts and, crucially, to enforce structured output (e.g., JSON via Pydantic models), which is vital for reliable, automated relationship extraction.20 |
| **Onto. Mapping** | TheFuzz / RapidFuzz | Latest | Fuzzy string matching for entity normalization. | Essential for canonicalizing extracted entity names that may have minor variations (e.g., "resveratrol", "resveratrols"). RapidFuzz is a faster alternative for large-scale processing.21 |
| **Onto. Mapping** | BioEL | Latest | Biomedical Entity Linking to custom ontologies. | A specialized Python package for linking biomedical entities to ontologies. Its support for custom ontologies and integration with resources like BigBIO makes it ideal for mapping extracted entities to the AIM2 ontology.24 |
| **Evaluation** | seqeval | Latest | Calculating token-level NER/RE evaluation metrics. | The standard library for evaluating sequence labeling tasks, providing strict precision, recall, and F1-score calculations necessary for Module 14\. |
| **Data Synth.** | Prodigy | Latest | Semi-automated annotation and active learning. | While the project specifies "no manual effort," Prodigy's active learning capabilities allow for a highly efficient "human-in-the-loop" review of *synthetic* data, dramatically improving quality with minimal human input. It bridges the gap between fully automated (but potentially low-quality) and fully manual (but slow) annotation.25 |
| **Taxonomy** | ncbi-taxonomist | Latest | NCBI Taxonomy data fetching and management. | A dedicated client for the NCBI Taxonomy database that can locally cache data in SQLite, providing a robust and efficient way to resolve species names and hierarchies without repeatedly hitting the API.27 |

## **Part II: Programmatic Ontology Development and Management**

This part of the plan details the programmatic construction of the AIM2 ontology, the intellectual core of the project. The approach is designed to be entirely automated, aligning with the "no manual effort" constraint. By leveraging the Python-native capabilities of Owlready2 and the automation-focused extensions provided by EMMOntoPy, the ontology itself becomes a reproducible, version-controlled software artifact rather than a static data file. This represents a paradigm shift from traditional ontology engineering, enabling a level of dynamism and rigor appropriate for a cutting-edge computational research project.

### **Section 3: Engineering the AIM2 Ontology with Owlready2 and EMMOntoPy**

The development of the AIM2 ontology will proceed through a series of automated modules, each responsible for a specific stage of its construction and refinement.

#### **3.1 Module 1: Ontology Scaffolding**

**Objective:** To create the foundational file structure and define the core hierarchical classes of the AIM2 ontology.

**Implementation:** A dedicated Python script will utilize the Owlready2 library to perform the initial setup.2 The script will first initialize a new

World object, which manages the entire set of ontologies, and then create the primary AIM2 Ontology with a unique and persistent Internationalized Resource Identifier (IRI), such as http://aim2.org/ontology.owl.

Within a with onto: context manager, which ensures all new classes and properties are correctly associated with the AIM2 ontology, the script will define the three top-level classes that form the primary organizational schema: StructuralAnnotation, SourceAnnotation, and FunctionalAnnotation. These will be defined as Python classes inheriting from owl.Thing, the root class in any OWL ontology. To manage entities that will be imported from external ontologies, the script will also pre-define namespaces for each source (e.g., pmn \= get\_namespace("..."), go \= get\_namespace("...")). This practice is essential for preventing IRI conflicts and maintaining a clean, organized structure when terms from different sources are integrated.2 Finally, the script will save the nascent ontology as an OWL file, which will be committed to the project's GitHub repository, establishing the first version-controlled artifact.

#### **3.2 Module 2: Automated Term Integration and Merging**

**Objective:** To programmatically load, integrate, and combine terms from multiple, disparate source ontologies into the AIM2 framework.

**Challenge:** The source ontologies specified for the project—such as Plant Ontology, Gene Ontology, Chemont, and others—exist in various formats (OWL, RDF/XML, CSV), possess different structural conventions, and contain overlapping but distinct conceptualizations. The project's constraints forbid the use of manual integration tools like Protégé, necessitating a fully automated solution.

**Implementation:** A two-tiered strategy will be employed. For initial bulk integration, a Python script will use the subprocess module to execute the ontoconvert command-line tool provided by EMMOntoPy.6 This powerful utility can "squash" multiple source ontology files into a single, merged OWL file, providing an efficient first pass to consolidate all external terms into a unified workspace.

For more granular and controlled integration, a second Python script will be developed. This script will use Owlready2's get\_ontology("...").load() method to load the primary AIM2 ontology and each source ontology into memory as distinct objects.2 The script will then iterate through the classes and properties of each source ontology. Based on predefined mapping rules, it will programmatically create corresponding new classes within the AIM2 ontology, ensuring they are placed under the correct high-level parent (

StructuralAnnotation, SourceAnnotation, or FunctionalAnnotation). This method offers precise control over the integration process, allowing for term renaming and restructuring as needed.

#### **3.3 Module 3: Algorithmic Trimming and Filtering**

**Objective:** To algorithmically reduce large, comprehensive source ontologies to a manageable and functionally relevant subset, as exemplified by the user's requirement to trim 2,008 anatomical terms from the Plant Ontology down to a core set of 293\.

**Implementation:** This critical task requires a sophisticated, multi-faceted filtering algorithm that goes far beyond simple keyword matching. A Python function will be designed to implement the following steps:

1. **Seed Selection:** The process will begin by selecting an initial set of "seed" terms from the source ontology. This selection will be based on a curated list of keywords and synonyms highly relevant to plant metabolomics and resilience (e.g., "flavonoid," "drought stress," "leaf," "root").  
2. **Hierarchical Expansion:** For each seed term identified, the algorithm will perform a controlled traversal of its ontological hierarchy. It will navigate upwards to include parent terms (hypernyms) to provide necessary context, and downwards to include more specific child terms (hyponyms) up to a predefined depth. The get\_ancestors() and get\_descendants() methods available in EMMOntoPy are well-suited for this task.28  
3. **Annotation-Based Pruning:** The expanded set of terms will then be filtered based on their metadata or annotations. For instance, any term annotated as 'obsolete' or 'deprecated' will be programmatically excluded. Owlready2 provides direct attribute-like access to annotations (e.g., term.comment), making this filtering straightforward.29  
4. **Literature Cross-Validation (Advanced):** As an optional, more advanced filtering step, the relevance of each candidate term can be scored by its frequency of occurrence within the project's literature corpus (constructed in Part III). Terms that are frequently mentioned in relevant scientific literature are more likely to be important and will be prioritized for inclusion.  
5. **Programmatic Removal:** Once the final set of terms to be excluded is identified, they must be programmatically removed from the ontology. Owlready2's destroy\_entity() function is the most robust method for this, as it correctly removes the entity itself along with all associated RDF triples, preventing orphaned data and ensuring the ontology remains consistent.3 Simpler approaches, like trying to dynamically modify a class's properties, are less reliable.4

#### **3.4 Module 4: Defining Custom Hierarchical Relationships**

**Objective:** To formally define the custom semantic relationships—such as is\_a, made\_via, accumulates\_in, and affects—that will form the relational backbone of the annotated metabolite network.

**Implementation with Owlready2:** A dedicated script will define these new relationships as OWL object properties. This is achieved by creating Python classes that inherit from ObjectProperty.30 The script will precisely define the semantics of each property by specifying its domain (the class of the subject) and range (the class of the object).

For example, the accumulates\_in property, which links a metabolite to a plant anatomical structure, would be defined as follows:

Python

from owlready2 import \*

\# Assume 'onto' is the loaded AIM2 ontology  
\# Assume 'Metabolite' and 'AnatomicalStructure' are already defined classes within 'onto'

with onto:  
    class accumulates\_in(ObjectProperty):  
        """Defines the relationship where a metabolite is found in high  
        concentration within a specific anatomical part of a plant."""  
        domain \= \[Metabolite\]  
        range \=  
        \# Custom annotations can be added directly  
        comment \=

Hierarchical relationships *between* these properties will also be defined. For instance, a more specific relationship like upregulates can be defined as a subproperty of the more general affects relationship by setting its is\_a attribute. The fundamental is\_a relationship between classes is handled implicitly through standard Python class inheritance.5 Custom annotations, such as definitions or usage examples, will be added to each new property to ensure the ontology is well-documented and its semantics are unambiguous.29

This programmatic approach fundamentally transforms the nature of the ontology. Traditionally, an ontology is a static data file (e.g., an OWL file) that is consumed by software. The methodology proposed here inverts this relationship: the Python scripts themselves become the canonical, executable definition of the ontology. This shift has profound benefits for the project. Firstly, it places the ontology's structure under Git version control, just like any other software component. Recreating a specific version of the ontology becomes as simple as checking out a specific commit and re-running the generation script. Secondly, it enables a test-driven development paradigm for ontology engineering. Unit tests can be written using a framework like pytest to assert that the ontology conforms to specific structural requirements—for example, a test can verify that after the trimming module runs, the number of anatomical terms is precisely 293, or that the domain of the affects property correctly includes the Metabolite class. Finally, this approach provides unparalleled dynamic adaptability. If a new source ontology needs to be integrated or a filtering criterion is modified, the entire AIM2 ontology can be regenerated automatically as part of the data pipeline, completely obviating the need for manual, error-prone edits in a graphical tool. This fully realizes the "no manual effort" mandate at a systemic level, turning the ontology from a static model into a dynamic, verifiable, and reproducible component of the research system.

## **Part III: The Automated Information Extraction Pipeline**

This part of the development plan outlines the design of an industrial-scale data ingestion and processing engine. The pipeline is engineered for high throughput and robustness, capable of systematically acquiring and processing a massive corpus of scientific literature. It addresses the significant technical challenges of automated web-based data acquisition, including the navigation of bot-protection mechanisms, and the parsing of heterogeneous and structurally complex scientific document formats. The ultimate goal of this pipeline is to convert a vast body of unstructured text into a clean, normalized, and contextually rich format suitable for sophisticated analysis by Large Language Models.

### **Section 4: Corpus Construction: Automated Literature Acquisition and Processing**

The foundation of the AIM2 knowledge base is a comprehensive corpus of plant chemical literature. The following modules are designed to build this corpus in a fully automated fashion.

#### **4.1 Module 5: High-Throughput Metadata and Abstract Retrieval**

**Objective:** To systematically identify and retrieve metadata for all potentially relevant scientific articles from PubMed, creating a comprehensive database of candidate documents for full-text acquisition.

**Implementation:** A Python script will serve as the primary interface to the NCBI Entrez databases. It will leverage the pymed library (specifically, the pymed\_paperscraper fork) for its efficient and simplified access to the PubMed API.7 The search process will be driven by a dynamic set of keywords derived from the core concepts defined in the AIM2 ontology (e.g., names of key metabolites, plant species, and resilience-related traits like "salinity" or "pathogen resistance").

A key advantage of pymed is its built-in handling of request batching, which is critical for performance and for adhering to the NCBI's API usage guidelines, which limit the rate of requests.7 The script will retrieve essential metadata for each article—including PMID, PMCID, DOI, title, abstract, authors, and journal—and store this information in a local SQLite database. This database will act as a central registry, tracking the processing status of each article (e.g., 'metadata\_retrieved', 'fulltext\_acquired', 'processed') and preventing redundant downloads. As a secondary tool for more complex queries or for cross-referencing different identifier types, the script will also incorporate the

Bio.Entrez module from the Biopython library.33

#### **4.2 Module 6: Full-Text Acquisition and Ethical Paywall Navigation**

**Objective:** To acquire the full text of the articles identified in the previous module. Full-text is non-negotiable, as abstracts frequently lack the detailed experimental context, methods, and results required for accurate and deep relationship extraction.

**Implementation:** A dual-pronged strategy will be implemented to maximize acquisition success while adhering to ethical and legal standards.

1. **Open Access (OA) First Strategy:** The primary and preferred method for full-text retrieval will be through legal OA channels. For each article in the database (identified by its DOI), a script will query the Unpaywall service using the unpywall Python client.8 Unpaywall is a database that aggregates information about OA versions of scholarly articles, providing direct links to legally hosted full-texts on platforms like PubMed Central, institutional repositories, and publisher websites.34 This approach is fast, reliable, and ensures compliance with copyright.  
2. **Programmatic Access to Institutional Subscriptions:** For articles that are not available via OA and are behind publisher paywalls, a more sophisticated web scraping module will be developed. This is a technically and ethically sensitive operation that must be implemented with care.  
   * **Technical Tooling:** The Playwright library is recommended for browser automation. It is more modern and generally less susceptible to detection than older tools like Selenium. For websites with advanced bot-detection systems (e.g., Cloudflare), emerging specialized libraries like Botasaurus, which are designed explicitly for evasion, will be evaluated and potentially integrated.13  
   * **Advanced Evasion Techniques:** To operate reliably, the scraper must simulate human browsing behavior and evade common bot-detection mechanisms. The implementation will include a suite of advanced techniques:  
     * **IP Rotation:** Utilize a commercial, rotating residential proxy service to make requests appear as if they are coming from different users in different geographic locations.11  
     * **Realistic HTTP Headers:** Maintain a list of current User-Agent strings from popular browsers and rotate them with each request. The scraper will also set realistic Referer and Accept-Language headers to mimic legitimate browser traffic.11  
     * **Human-Like Interaction Patterns:** The scraper will incorporate randomized delays between page loads and actions. It will simulate human-like mouse movements and scrolling behavior to defeat user behavior analysis (UBA) systems.12  
     * **CAPTCHA Handling:** While the primary goal is to avoid triggering CAPTCHAs, the module will be designed to integrate with an external CAPTCHA-solving service via its API as a fallback mechanism if a challenge is encountered.12  
   * **Ethical and Legal Disclaimer:** It is imperative to state that this module must be used responsibly. It is designed solely to automate the process of accessing content for which the user's institution holds a valid, active subscription. The module must be configured to respect publishers' robots.txt files where applicable and must operate in full compliance with the relevant terms of service. Abusive or unauthorized scraping is unethical, may be illegal, and is not the intended use of this component.

#### **4.3 Module 7: Document Parsing and Content Normalization**

**Objective:** To convert the raw downloaded files, which exist in heterogeneous formats (PDF, XML), into a standardized, clean, and structured text format that is optimized for processing by LLMs.

**Implementation:** The parsing module will handle different file types with specialized tools.

* **PDF Processing:** For PDF documents, the PyMuPDF (Fitz) library is the unequivocal choice.14 Scientific papers are notorious for their complex, multi-column layouts, embedded tables, and figures with captions. Simpler libraries like  
  PyPDF2 consistently fail to extract text in the correct logical reading order from such documents, which would corrupt the semantic context and render the text useless for relationship extraction.14  
  PyMuPDF excels in this area due to its ability to understand the document layout and preserve high-precision text positioning, ensuring that sentences and paragraphs are extracted coherently.16 The script will use  
  PyMuPDF to extract the main body text and to specifically identify and extract the content of tables, which can be passed to the LLM with distinct formatting to signal their structured nature.  
* **XML Processing:** A significant portion of the full-text articles from PubMed Central are available in a structured JATS (Journal Article Tag Suite) XML format. The lxml library will be used for its high-performance and memory-efficient XML parsing capabilities. The script will leverage the XML structure to selectively extract text from high-value sections, such as "Methods," "Results," and figure captions, which are often dense with the specific experimental details required for accurate fact extraction.  
* **Text Cleaning and Chunking:** Regardless of the source format, all extracted text will undergo a rigorous cleaning and normalization process. This includes removing spurious line breaks and hyphenation artifacts from PDF conversions, normalizing all whitespace to single spaces, and removing non-standard characters. Following cleaning, the full text of each document will be divided into smaller, overlapping chunks (e.g., 512 tokens with a 128-token overlap). This chunking strategy is a standard and necessary preprocessing step for LLMs, as it ensures that each text segment fits within the model's context window while maintaining semantic continuity between adjacent chunks.35

### **Section 5: LLM-Powered Knowledge Extraction**

With a clean and structured corpus in place, the next stage is to apply state-of-the-art LLMs to perform the core tasks of Named Entity Recognition (NER) and Relationship Extraction (RE). The architecture described here is designed to maximize both accuracy and cost-effectiveness, creating a feasible solution for large-scale processing.

#### **5.1 Module 8: Multi-Stage Named Entity Recognition (NER)**

**Objective:** To identify and classify all relevant entities from the text (e.g., chemicals, genes, species, plant traits) with the highest possible accuracy.

**Implementation:** A naive approach of sending all text to a single, powerful LLM would be prohibitively expensive and slow. Therefore, a hybrid, two-stage architecture is proposed to optimize the trade-off between cost, speed, and accuracy.

1. **Stage 1: High-Recall Candidate Generation with a Fine-Tuned Transformer:** The first stage will employ a computationally efficient, domain-specific transformer model. The recommended model is dmis-lab/biobert-v1.1, which is pre-trained on a large corpus of biomedical literature and has demonstrated strong performance on NER tasks.36 This model will be fine-tuned on the project-specific synthetic dataset (generated by Module 10\) to recognize the exact entity types defined in the AIM2 ontology. The fine-tuning process will be implemented using the Hugging Face  
   Trainer API, which provides a standardized and powerful framework for this task.19 The primary goal of this stage is high recall: to quickly scan the entire corpus and identify a broad set of  
   *potential* entities. It acts as an intelligent, cost-effective filter, drastically reducing the amount of text that requires more intensive analysis.  
2. **Stage 2: High-Precision Validation and Novel Entity Discovery with a Generative LLM:** The text chunks, now enriched with the candidate entity tags from Stage 1, will be passed to a powerful, general-purpose generative LLM (e.g., GPT-4o, Llama 3 70B). The prompt for this stage will be carefully engineered to instruct the LLM to perform two critical tasks: (a) validate, correct, or reject the candidate entities identified by the fine-tuned model, and (b) identify any novel or complex entities that the more specialized model may have missed. This approach leverages the distinct strengths of each model type: the fine-tuned transformer provides speed and domain-specificity, while the large generative model provides superior zero-shot and few-shot reasoning capabilities for handling ambiguity and novelty.38 Recent benchmarks confirm that while fine-tuned models excel, large LLMs can achieve competitive or even superior performance in certain zero-shot biomedical tasks, making this hybrid approach particularly promising.39

#### **5.2 Module 9: Advanced Relationship Extraction (RE) with Hierarchical Prompting**

**Objective:** To move beyond simple co-occurrence and extract complex, directional, and hierarchical relationships between the identified entities (e.g., identifying that "Metabolite A" *induces the expression of* "Gene B," which is a more specific instance of the general "affects" relationship).

**Implementation:** This module will leverage advanced prompt engineering techniques to guide a powerful LLM to perform nuanced relational reasoning.

* **Enforced Structured Output:** To ensure the output of the LLM is machine-readable and can be integrated directly into the automated pipeline, all RE prompts will enforce a structured output format, specifically JSON. This will be implemented using the LangChain library's with\_structured\_output functionality, which links a Pydantic schema (a Python class defining the desired data structure) to the LLM call.20 This is a non-negotiable requirement for a fully automated system, as it eliminates the need for fragile parsing of free-text LLM responses.  
* **Chain-of-Thought (CoT) Prompting:** To enhance the model's reasoning process, the prompt will incorporate the "Let's think step by step" instruction.41 The full prompt will guide the LLM through a logical sequence of operations: first, list all validated entities in the text; second, generate all plausible pairs of entities that could be related; and third, for each pair, determine the most precise relationship that exists between them, explicitly citing the textual evidence (the exact quote) that supports the conclusion.42 This structured reasoning process has been shown to significantly improve accuracy on complex tasks.  
* **Hierarchical Relationship Prompting:** Capturing the distinction between a general relationship (e.g., affects) and a specific one (e.g., upregulates) requires a more sophisticated prompting strategy. An iterative, multi-stage prompting technique will be employed. The initial prompt will ask the LLM to identify the general relationship between an entity pair. If a general relationship like affects is found, a follow-up, conditional prompt will be triggered: *"Given that the text states \[Entity A\] 'affects', does the text provide a more specific mechanism for this interaction? Choose from the following, if applicable: upregulates, downregulates, inhibits, induces expression of, or state 'no specific mechanism' if none is mentioned."* This iterative refinement, inspired by advanced prompting techniques that involve breaking down complex problems and simulating multi-perspective analysis 44, allows the system to extract knowledge at multiple levels of granularity. The conceptual framework of the  
  LLM-IE package, which separates entity (frame) extraction from relation extraction, provides a valuable model for this structured approach.46

The design of this entire extraction pipeline is fundamentally an exercise in cost-accuracy optimization. A naive implementation that simply sends every document chunk to the most powerful (and most expensive) LLM available would be financially unsustainable for a corpus containing millions of articles. The proposed hybrid NER architecture and the focused RE process are therefore not merely for improving accuracy; they represent a critical cost-control strategy. The cheaper, fine-tuned BioBERT model acts as a massive computational filter, performing the low-value task of scanning text for potential entities and reducing the workload for the expensive generative LLM by orders of magnitude. This cascade—from cheap, high-recall filtering to expensive, high-precision reasoning—is what makes the AIM2 project's goals computationally and financially feasible at the required scale.

## **Part IV: Closing the Loop: Automation, Integration, and Evaluation**

This final technical part of the plan addresses the crucial steps that complete the knowledge extraction lifecycle. It details the methodologies for bootstrapping the AI models without manual data creation, integrating the extracted knowledge into the formal AIM2 ontology, and rigorously evaluating the performance of the entire system. These modules ensure the project's adherence to its core "no manual effort" principle while maintaining scientific validity through robust, data-driven evaluation.

### **Section 6: Bootstrapping with AI: Synthetic Data Generation**

The project's directive to eliminate manual annotation effort necessitates an AI-driven approach to create the training data required for the fine-tuned NER model. This will be achieved by leveraging a powerful LLM to generate a large-scale, high-quality synthetic dataset.

#### **6.1 Module 10: Generating a Synthetic Corpus for NER and RE**

**Objective:** To programmatically create a large, diverse, and accurately annotated dataset for training and validating the custom NER model (Module 8\) without any human labeling effort.

**Methodology:** The implementation will follow recent advancements in synthetic data generation using LLMs.35 The process is as follows:

1. **Seeding the Generation Process:** The process begins by providing a state-of-the-art generative LLM (e.g., GPT-4o) with the complete AIM2 ontology schema. This includes the full list of target entity types (e.g., Metabolite, Gene, PlantTrait) and the definitions of the relationships connecting them.  
2. **Guided Sentence Generation:** The LLM will be given structured prompts designed to elicit realistic sentences that exemplify the desired annotations. For example, a prompt could be: *"You are a biomedical research scientist writing a manuscript. Generate a scientifically plausible sentence for a plant biology paper that contains a 'Metabolite' entity, a 'Plant Trait' entity, and explicitly states an 'affects' relationship between them. Provide both the sentence and a corresponding JSON object that annotates the entities and their relationship."*  
3. **Iterative Complexity Enhancement (Evol-Instruct):** To avoid generating a homogenous and simplistic dataset, the initial sentences will be "evolved." This technique, inspired by methods like Evol-Instruct 35, involves using the LLM to iteratively increase the complexity of the generated examples. Follow-up prompts will instruct the model to: "Rephrase the previous sentence to be more complex," "Add a confounding entity that is not part of the primary relationship," or "Rewrite this statement in the passive voice as it might appear in a 'Methods' section." This ensures the resulting training data is diverse, challenging, and more representative of real-world scientific text.  
4. **Large-Scale Production:** This automated generation and evolution process will be executed at scale to produce tens of thousands of unique, annotated sentences. This synthetic corpus will form the training, validation, and test sets for fine-tuning the BioBERT model. Research has shown that this approach can significantly boost model performance, particularly in specialized domains or low-resource settings where manually annotated data is scarce.47

#### **6.2 Module 11: Quality Control with a Semi-Automated Human-in-the-Loop (HITL) Workflow**

**Objective:** To pragmatically address the potential for subtle errors, biases, or lack of diversity in purely synthetic data. While the "no manual effort" constraint is paramount, a minimal, highly targeted human review cycle can yield disproportionately large improvements in data quality and final model performance.

**Implementation with Prodigy:** The Prodigy annotation tool is perfectly suited for this task because it is fully scriptable and designed for active learning workflows.25 This is not "manual annotation" in the traditional sense of labeling from scratch; it is a rapid, model-guided validation process.

* **Active Learning Workflow:**  
  1. The fine-tuned NER model (from Module 8, trained on the initial synthetic data) is used to make predictions on a new, unlabeled batch of text from the literature corpus.  
  2. Prodigy's active learning recipes will automatically select and present only the examples where the model is most *uncertain* in its predictions. These are the most informative examples for the model to learn from.  
  3. A human domain expert is then presented with these few, high-value examples in a simple web interface. Their task is not to label, but to quickly *correct* the model's existing predictions (accept, reject, or modify a label). This is orders of magnitude faster than manual annotation.  
  4. These corrected, high-impact annotations are then programmatically fed back into the training dataset, and the model is updated.

This Human-in-the-Loop (HITL) process ensures that precious human expertise is focused exclusively on correcting the model's most significant weaknesses, maximizing the impact of minimal human oversight.50 This workflow respects the spirit of the automation constraint while providing a crucial quality assurance mechanism that bridges the gap between purely synthetic data and robust, real-world model performance.

### **Section 7: Ontology Mapping and Knowledge Base Population**

Once entities and relationships have been extracted, they must be normalized, linked to the formal AIM2 ontology, and stored in a structured, queryable knowledge base.

#### **7.1 Module 12: Entity Normalization and Linking**

**Objective:** To map the raw text strings extracted by the LLM (e.g., "L-ascorbic acid," "ascorbate") to the single, canonical entity representation in the AIM2 ontology (e.g., the entity for Vitamin C).

**Implementation:** A two-step process will ensure both syntactic and semantic normalization.

1. **Syntactic Normalization:** All extracted entity strings will first be pre-processed (converted to lowercase, stripped of leading/trailing whitespace, and with punctuation removed). Then, fuzzy string matching will be employed to group syntactic variations. The TheFuzz library 21 or its high-performance C++ backed alternative,  
   RapidFuzz 22, will be used. These libraries calculate string similarity scores (e.g., token set ratio) to identify and cluster similar strings, mapping them to a single canonical name.  
2. **Semantic Entity Linking:** The canonicalized entity names will then be formally linked to their corresponding concepts in the AIM2 ontology. For this task, the BioEL Python package is the recommended tool.24  
   BioEL is specifically designed for biomedical entity linking and offers several key advantages: it supports the use of custom ontologies, integrates with large biomedical data resources like BigBIO, and provides multiple linking models (from simple alias matching to more complex contextualized models). This makes it an ideal fit for mapping the extracted entities to the AIM2 ontology. Should the specific requirements of the project exceed the capabilities of BioEL, a more general-purpose ontology alignment toolkit like OntoAligner could be employed as an alternative. OntoAligner provides a flexible framework with support for fuzzy matching, retrieval-based, and LLM-based alignment methods.51

#### **7.2 Module 13: Fact Triplestore Management**

**Objective:** To store the final, cleaned, normalized, and linked knowledge as a coherent and persistent knowledge base.

**Implementation:** The final set of knowledge triples (Subject Entity, Predicate, Object Entity) will be managed through a series of automated steps.

* **Deduplication:** A script will identify and merge duplicate facts. A simple and efficient method is to generate a unique hash (e.g., an MD5 hash) for each canonicalized triple (Subject CUI, Predicate IRI, Object CUI). This hash can be used as a primary key to quickly detect and discard redundant extractions.  
* **Conflict Resolution:** The pipeline must have a defined strategy for handling conflicting information (e.g., one paper states metabolite A *upregulates* gene B, while another states it *inhibits* it). Initially, a simple rule-based approach will be implemented, such as prioritizing facts from more recent publications or from journals with higher impact factors. More advanced future strategies could involve scoring facts based on the strength of experimental evidence described in the source text.  
* **Dual-Format Storage:** The final, curated set of triples will be persisted in two formats to meet different project needs:  
  1. **CSV Files on GitHub:** A set of simple, human-readable CSV files will be generated and stored in the project's GitHub repository. This provides easy access, version control, and interoperability with a wide range of data analysis tools.  
  2. **Owlready2 Knowledge Base:** The triples will be programmatically loaded into the Owlready2 environment. For each entity, an Individual instance will be created, and the extracted relationships will be asserted between these individuals.2 This populates the ontology's ABox (Assertional Box), creating a fully queryable knowledge graph within the  
     Owlready2 SQLite backend, accessible directly from Python.

### **Section 8: Rigorous Performance Benchmarking**

To ensure the scientific validity and reliability of the AIM2 system, a rigorous and unbiased evaluation framework is essential. This involves creating a high-quality "gold standard" test set and systematically benchmarking the performance of different models and techniques.

#### **8.1 Module 14: Gold Standard Creation and Evaluation Framework**

**Objective:** To create a high-quality, manually annotated test set to serve as the ground truth for evaluating the performance of the entire information extraction pipeline.

**Implementation:**

* **Gold Standard Annotation:** While the project's operational pipeline is fully automated, the creation of a small, static gold standard for validation is a non-negotiable scientific requirement. This is a one-time, upfront investment in quality assurance. Following the user's suggestion, a set of approximately 25 representative papers will be selected. These papers will be manually annotated by at least two domain experts following a strict, predefined annotation guideline to ensure consistency. The resulting annotations will be adjudicated and converted into the standard IOB (Inside-Outside-Beginning) format for evaluation.  
* **Automated Evaluation Script:** A Python script will be developed to automate the evaluation process. This script will take the system's predicted annotations and the gold standard annotations as input. It will use the seqeval library, the standard tool for evaluating sequence labeling tasks, to calculate strict, token-level performance metrics: precision, recall, and the F1-score. The evaluation will be performed for both the NER task (entity identification and classification) and the RE task (by treating relations as labeled dependencies between entities).

#### **8.2 Module 15: Comparative Analysis of LLMs and Prompting Strategies**

**Objective:** To empirically determine the optimal combination of LLM, prompting strategy, and model architecture that provides the best balance of accuracy, speed, and cost for the AIM2 project's specific needs.

**Methodology:** A systematic and comprehensive benchmark will be conducted using the gold standard dataset.

* **Models for Comparison:** The benchmark will include a range of models representing different architectures and scales: the fine-tuned BioBERT model, open-source LLMs like Llama 3 (70B) and Gemma, and a state-of-the-art proprietary model like GPT-4o.38  
* **Prompting Strategies:** Different prompting techniques will be evaluated for the LLMs, including basic zero-shot and few-shot prompts, as well as the more advanced Chain-of-Thought (CoT) with hierarchical refinement strategy developed in Module 9\.  
* **Benchmarking Procedure:** Each combination of model and prompting strategy will be run on the full 25-paper gold standard set. The automated evaluation script (Module 14\) will be used to compute the performance metrics for each run. In addition to accuracy, the script will also measure and record the wall-clock inference time and, for API-based models, the total cost incurred.  
* **Final Report and Analysis:** The results will be compiled into a final benchmarking report, summarized in the table below. This report will provide a data-driven comparison of the different configurations, analyzing the trade-offs between F1-score, inference speed, and financial cost. This empirical evidence will allow the AIM2 project team to make an informed, strategic decision on which model and configuration to deploy in the final, production-scale pipeline.

**Table: LLM Information Extraction Model Benchmarking**

| Model Configuration | Prompting Strategy | NER Precision | NER Recall | NER F1 | RE F1 | Avg. Cost / 1k Docs | Inference Speed (docs/min) |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| BioBERT (fine-tuned) | N/A |  |  |  |  |  |  |
| Llama 3 70B | Zero-Shot |  |  |  |  |  |  |
| Llama 3 70B | Few-Shot |  |  |  |  |  |  |
| Llama 3 70B | CoT \+ Hierarchical |  |  |  |  |  |  |
| Gemma 7B | Few-Shot |  |  |  |  |  |  |
| GPT-4o | Few-Shot |  |  |  |  |  |  |
| GPT-4o | CoT \+ Hierarchical |  |  |  |  |  |  |

## **Part V: Conclusion and Strategic Recommendations**

This development plan has outlined a comprehensive, modular, and fully automated system for constructing the AIM2 backbone annotated metabolites network. By integrating state-of-the-art Python libraries for ontology management, a robust pipeline for literature acquisition, and advanced AI techniques for knowledge extraction and synthetic data generation, the proposed architecture directly addresses the project's ambitious goals. The following sections provide a strategic roadmap for implementation and discuss the long-term value of the system as an extensible research platform.

### **Section 9: Implementation Roadmap and Phased Rollout**

To manage the complexity of the project and ensure steady progress, a phased implementation approach is recommended. The 15 defined modules can be grouped into four logical phases, allowing for iterative development, testing, and refinement.

* **Phase 1: Foundation and Data Ingestion (Modules 1, 2, 5, 7\)**  
  * **Focus:** Establishing the project's core infrastructure.  
  * **Activities:** Set up the Cookiecutter Data Science project structure and version control with Git. Develop the initial ontology scaffolding scripts (Module 1\) and the basic term integration logic (Module 2). Concurrently, build the foundational literature acquisition pipeline, focusing on metadata retrieval with pymed (Module 5\) and document parsing for PDF and XML with PyMuPDF and lxml (Module 7).  
  * **Deliverable:** A functional pipeline that can populate the data/raw and data/interim directories with parsed literature, and a basic, version-controlled AIM2 ontology file.  
* **Phase 2: Bootstrapping and Core AI Development (Modules 10, 8, 9\)**  
  * **Focus:** Developing the core artificial intelligence components.  
  * **Activities:** Implement the synthetic data generation pipeline (Module 10\) to create the initial training corpus. Use this data to fine-tune the BioBERT model for high-recall NER (Module 8). Develop and test the advanced prompting strategies (CoT, hierarchical) for high-precision validation and relationship extraction using a powerful LLM (Module 9).  
  * **Deliverable:** A trained NER model and a functional, end-to-end extraction script that can process text from data/interim and produce raw JSON-formatted entity and relation data.  
* **Phase 3: Integration, Refinement, and Knowledge Base Population (Modules 3, 4, 11, 12, 13\)**  
  * **Focus:** Connecting the extracted knowledge to the formal ontology and refining both.  
  * **Activities:** Develop the algorithmic trimming and filtering logic for the ontology (Module 3\) and define the final set of custom relationships (Module 4). Implement the semi-automated HITL quality control loop with Prodigy to refine the synthetic data and the NER model (Module 11). Build the entity normalization and linking pipeline using TheFuzz and BioEL (Module 12\) and the final fact triplestore management system for deduplication and storage (Module 13).  
  * **Deliverable:** A fully populated knowledge base in both CSV and Owlready2 formats, linked to a mature and refined AIM2 ontology.  
* **Phase 4: Evaluation, Optimization, and Finalization (Modules 14, 15\)**  
  * **Focus:** Rigorously evaluating the system's performance and finalizing the production pipeline.  
  * **Activities:** Execute the one-time manual annotation effort to create the 25-paper gold standard (Module 14). Run the comprehensive benchmark across all target LLMs and prompting strategies (Module 15). Analyze the results to select the most effective and cost-efficient configuration for the final system.  
  * **Deliverable:** A final benchmarking report with a clear recommendation for the production model, and a fully integrated, end-to-end automated workflow script that executes all modules in the correct sequence.

### **Section 10: Future-Proofing and System Extensibility**

The modular, package-based architecture proposed in this plan (src/aim2/) is designed not just to meet the immediate goals of the project but also to ensure its long-term viability and extensibility as a research platform. By enforcing a strict separation of concerns, each major component of the system—ontology management, corpus construction, knowledge extraction, and entity linking—exists as a distinct, importable Python sub-package.

This design has several key advantages for future development:

* **Independent Upgradability:** Each module can be updated or replaced without disrupting the entire system. For example, as new and more powerful LLMs become available, a new extraction function can be added to the src/aim2/extraction/ module and integrated into the benchmarking workflow with minimal effort. The core data flow remains unchanged.  
* **Expansion to New Data Types:** The system can be easily extended to incorporate new types of data. To add transcriptomics data, for instance, a new data acquisition module could be created within src/aim2/corpus/ to handle the specific data sources and formats, while the downstream extraction and linking modules could be adapted to process this new information stream.  
* **Facilitation of New Research Questions:** The modular structure allows researchers to easily repurpose components for new projects. The literature acquisition and parsing pipeline, for example, is a valuable asset that could be readily adapted for any biomedical text mining project, not just metabolomics.

Ultimately, this architectural philosophy ensures that the AIM2 system is not a static, monolithic application but a dynamic and evolving research platform. It is designed to grow with the field, accommodate new technologies, and serve as a foundational tool for metabolomics research for years to come.

#### **Works cited**

1. drivendataorg/cookiecutter-data-science: A logical ... \- GitHub, accessed August 1, 2025, [https://github.com/drivendata/cookiecutter-data-science](https://github.com/drivendata/cookiecutter-data-science)  
2. Introduction — Owlready2 0.48 documentation, accessed August 1, 2025, [https://owlready2.readthedocs.io/en/latest/intro.html](https://owlready2.readthedocs.io/en/latest/intro.html)  
3. owlready2 \- PyPI, accessed August 1, 2025, [https://pypi.org/project/owlready2/](https://pypi.org/project/owlready2/)  
4. Welcome to Owlready2's documentation\! — Owlready2 0.48 documentation, accessed August 1, 2025, [https://owlready2.readthedocs.io/](https://owlready2.readthedocs.io/)  
5. Classes and Individuals (Instances) \- Owlready2's documentation\! \- Read the Docs, accessed August 1, 2025, [https://owlready2.readthedocs.io/en/latest/class.html](https://owlready2.readthedocs.io/en/latest/class.html)  
6. emmo-repo/EMMOntoPy: Library for representing and working with ontologies in Python, accessed August 1, 2025, [https://github.com/emmo-repo/EMMOntoPy](https://github.com/emmo-repo/EMMOntoPy)  
7. jannisborn/pymed: PyMed is a Python library that provides ... \- GitHub, accessed August 1, 2025, [https://github.com/jannisborn/pymed](https://github.com/jannisborn/pymed)  
8. unpywall \- Release MIT, accessed August 1, 2025, [https://unpywall.readthedocs.io/\_/downloads/en/latest/pdf/](https://unpywall.readthedocs.io/_/downloads/en/latest/pdf/)  
9. unpywall: Interfacing the Unpaywall Database with Python — unpywall MIT documentation, accessed August 1, 2025, [https://unpywall.readthedocs.io/](https://unpywall.readthedocs.io/)  
10. unpywall/unpywall: Interfacing the Unpaywall Database with Python \- GitHub, accessed August 1, 2025, [https://github.com/unpywall/unpywall](https://github.com/unpywall/unpywall)  
11. 7 Anti-Scraping Techniques (And How to Bypass Them) | Medium, accessed August 1, 2025, [https://medium.com/@datajournal/anti-scraping-techniques-2cba92f700a6](https://medium.com/@datajournal/anti-scraping-techniques-2cba92f700a6)  
12. How To Bypass Anti-Bots With Python | ScrapeOps, accessed August 1, 2025, [https://scrapeops.io/python-web-scraping-playbook/python-how-to-bypass-anti-bots/](https://scrapeops.io/python-web-scraping-playbook/python-how-to-bypass-anti-bots/)  
13. Open Source Web Scraping Libraries to Bypass Anti-Bot Systems | ScrapingAnt, accessed August 1, 2025, [https://scrapingant.com/blog/open-source-web-scraping-libraries-bypass-anti-bot](https://scrapingant.com/blog/open-source-web-scraping-libraries-bypass-anti-bot)  
14. A Guide to PDF Extraction Libraries in Python \- Metric Coders, accessed August 1, 2025, [https://www.metriccoders.com/post/a-guide-to-pdf-extraction-libraries-in-python](https://www.metriccoders.com/post/a-guide-to-pdf-extraction-libraries-in-python)  
15. An Evaluation of Python PDF to Text Parser Libraries \- Unstract, accessed August 1, 2025, [https://unstract.com/blog/evaluating-python-pdf-to-text-libraries/](https://unstract.com/blog/evaluating-python-pdf-to-text-libraries/)  
16. Extract text from PDF File using Python \- GeeksforGeeks, accessed August 1, 2025, [https://www.geeksforgeeks.org/python/extract-text-from-pdf-file-using-python/](https://www.geeksforgeeks.org/python/extract-text-from-pdf-file-using-python/)  
17. Large-scale application of named entity recognition to biomedicine and epidemiology \- PMC, accessed August 1, 2025, [https://pmc.ncbi.nlm.nih.gov/articles/PMC9931203/](https://pmc.ncbi.nlm.nih.gov/articles/PMC9931203/)  
18. Fine-Tuning BERT for Named Entity Recognition (NER) | by why amit \- Medium, accessed August 1, 2025, [https://medium.com/@whyamit101/fine-tuning-bert-for-named-entity-recognition-ner-b42bcf55b51d](https://medium.com/@whyamit101/fine-tuning-bert-for-named-entity-recognition-ner-b42bcf55b51d)  
19. Fine-tuning \- Hugging Face, accessed August 1, 2025, [https://huggingface.co/docs/transformers/training](https://huggingface.co/docs/transformers/training)  
20. Build an Extraction Chain | 🦜️ LangChain, accessed August 1, 2025, [https://python.langchain.com/docs/tutorials/extraction/](https://python.langchain.com/docs/tutorials/extraction/)  
21. Fuzzy String Matching in Python Tutorial | DataCamp, accessed August 1, 2025, [https://www.datacamp.com/tutorial/fuzzy-string-python](https://www.datacamp.com/tutorial/fuzzy-string-python)  
22. Unlocking the Power of Fuzzy Matching in Python: A Practical Guide | by Jay Kim \- Medium, accessed August 1, 2025, [https://medium.com/@bravekjh/unlocking-the-power-of-fuzzy-matching-in-python-a-practical-guide-ec37ebd8f3eb](https://medium.com/@bravekjh/unlocking-the-power-of-fuzzy-matching-in-python-a-practical-guide-ec37ebd8f3eb)  
23. Fuzzy String Matching in Python | StreamHacker, accessed August 1, 2025, [https://streamhacker.com/2011/10/31/fuzzy-string-matching-python/](https://streamhacker.com/2011/10/31/fuzzy-string-matching-python/)  
24. BioEL: A Comprehensive Python Package for ... \- ACL Anthology, accessed August 1, 2025, [https://aclanthology.org/2025.findings-naacl.93.pdf](https://aclanthology.org/2025.findings-naacl.93.pdf)  
25. Named Entity Recognition · Prodigy · An annotation tool for AI, Machine Learning & NLP, accessed August 1, 2025, [https://prodi.gy/docs/named-entity-recognition](https://prodi.gy/docs/named-entity-recognition)  
26. Data Annotation using Open Source and Proprietary Tools | by Chanaka \- Medium, accessed August 1, 2025, [https://medium.com/@ChanakaDev/data-annotation-using-open-source-and-proprietary-tools-9e83bf035809](https://medium.com/@ChanakaDev/data-annotation-using-open-source-and-proprietary-tools-9e83bf035809)  
27. ncbi-taxonomist \- PyPI, accessed August 1, 2025, [https://pypi.org/project/ncbi-taxonomist/](https://pypi.org/project/ncbi-taxonomist/)  
28. ontology \- EMMOntoPy, accessed August 1, 2025, [https://emmo-repo.github.io/EMMOntoPy/0.6.0/api\_reference/ontopy/ontology/](https://emmo-repo.github.io/EMMOntoPy/0.6.0/api_reference/ontopy/ontology/)  
29. Annotations — Owlready2 0.48 documentation, accessed August 1, 2025, [https://owlready2.readthedocs.io/en/latest/annotations.html](https://owlready2.readthedocs.io/en/latest/annotations.html)  
30. Properties — Owlready2 0.48 documentation, accessed August 1, 2025, [https://owlready2.readthedocs.io/en/latest/properties.html](https://owlready2.readthedocs.io/en/latest/properties.html)  
31. Classes and Instances (Individuals) — Owlready 0.2 documentation \- Pythonhosted.org, accessed August 1, 2025, [https://pythonhosted.org/Owlready/class.html](https://pythonhosted.org/Owlready/class.html)  
32. How to access comment annotation of object properties in owlready2 \- Stack Overflow, accessed August 1, 2025, [https://stackoverflow.com/questions/79360200/how-to-access-comment-annotation-of-object-properties-in-owlready2](https://stackoverflow.com/questions/79360200/how-to-access-comment-annotation-of-object-properties-in-owlready2)  
33. Bio.Entrez package — Biopython 1.76 documentation, accessed August 1, 2025, [https://biopython.org/docs/1.76/api/Bio.Entrez.html](https://biopython.org/docs/1.76/api/Bio.Entrez.html)  
34. Scholarly Communication Analytics: Exploring the Open Access Evidence Base in Unpaywall with Python, accessed August 1, 2025, [https://subugoe.github.io/scholcomm\_analytics/posts/unpaywall\_python/](https://subugoe.github.io/scholcomm_analytics/posts/unpaywall_python/)  
35. Using LLMs for Synthetic Data Generation: The Definitive Guide ..., accessed August 1, 2025, [https://www.confident-ai.com/blog/the-definitive-guide-to-synthetic-data-generation-using-llms](https://www.confident-ai.com/blog/the-definitive-guide-to-synthetic-data-generation-using-llms)  
36. dmis-lab/biobert: Bioinformatics'2020: BioBERT: a pre-trained biomedical language representation model for biomedical text mining \- GitHub, accessed August 1, 2025, [https://github.com/dmis-lab/biobert](https://github.com/dmis-lab/biobert)  
37. BioBERT Pre-trained Weights \- GitHub, accessed August 1, 2025, [https://github.com/naver/biobert-pretrained](https://github.com/naver/biobert-pretrained)  
38. Title Benchmarking large language models for biomedical natural language processing applications and recommendations Author list \- arXiv, accessed August 1, 2025, [https://arxiv.org/pdf/2305.16326](https://arxiv.org/pdf/2305.16326)  
39. A Comprehensive Evaluation of Large Language Models on Benchmark Biomedical Text Processing Tasks \- arXiv, accessed August 1, 2025, [https://arxiv.org/html/2310.04270v3](https://arxiv.org/html/2310.04270v3)  
40. Benchmarking Large Language Models in Biomedical Classification and Named Entity Recognition: Evaluating the Impact of Prompting Techniques and Domain Knowledge \- MarkTechPost, accessed August 1, 2025, [https://www.marktechpost.com/2024/08/26/benchmarking-large-language-models-in-biomedical-classification-and-named-entity-recognition-evaluating-the-impact-of-prompting-techniques-and-domain-knowledge/](https://www.marktechpost.com/2024/08/26/benchmarking-large-language-models-in-biomedical-classification-and-named-entity-recognition-evaluating-the-impact-of-prompting-techniques-and-domain-knowledge/)  
41. Chain-of-Thought Prompting | Prompt Engineering Guide, accessed August 1, 2025, [https://www.promptingguide.ai/techniques/cot](https://www.promptingguide.ai/techniques/cot)  
42. Chain of Thought Prompting Guide \- PromptHub, accessed August 1, 2025, [https://www.prompthub.us/blog/chain-of-thought-prompting-guide](https://www.prompthub.us/blog/chain-of-thought-prompting-guide)  
43. How to Use Chain of Thought Prompting (With Examples) \- ClickUp, accessed August 1, 2025, [https://clickup.com/blog/chain-of-thought-prompting/](https://clickup.com/blog/chain-of-thought-prompting/)  
44. Advanced Prompt Engineering Techniques for 2025: Beyond Basic Instructions \- Reddit, accessed August 1, 2025, [https://www.reddit.com/r/PromptEngineering/comments/1k7jrt7/advanced\_prompt\_engineering\_techniques\_for\_2025/](https://www.reddit.com/r/PromptEngineering/comments/1k7jrt7/advanced_prompt_engineering_techniques_for_2025/)  
45. Using LLM to Extract Knowledge Graph Entities and Relationships \- TiDB, accessed August 1, 2025, [https://www.pingcap.com/article/using-llm-extract-knowledge-graph-entities-and-relationships/](https://www.pingcap.com/article/using-llm-extract-knowledge-graph-entities-and-relationships/)  
46. LLM-IE: a python package for biomedical generative information ..., accessed August 1, 2025, [https://pmc.ncbi.nlm.nih.gov/articles/PMC11901043/](https://pmc.ncbi.nlm.nih.gov/articles/PMC11901043/)  
47. Using Synthetic Health Care Data to Leverage Large Language ..., accessed August 1, 2025, [https://pmc.ncbi.nlm.nih.gov/articles/PMC11962312/](https://pmc.ncbi.nlm.nih.gov/articles/PMC11962312/)  
48. Overcoming Data Scarcity in Named Entity Recognition: Synthetic Data Generation with Large Language Models \- ACL Anthology, accessed August 1, 2025, [https://aclanthology.org/2025.bionlp-1.28/](https://aclanthology.org/2025.bionlp-1.28/)  
49. Does Synthetic Data Help Named Entity Recognition for Low-Resource Languages? \- arXiv, accessed August 1, 2025, [https://arxiv.org/html/2505.16814v2](https://arxiv.org/html/2505.16814v2)  
50. Human-in-the-Loop: The Key to High-Quality Data in Modern AI \- Pangeanic Blog, accessed August 1, 2025, [https://blog.pangeanic.com/the-key-to-high-quality-data-in-modern-ai](https://blog.pangeanic.com/the-key-to-high-quality-data-in-modern-ai)  
51. OntoAligner: A Comprehensive Modular and Robust Python Toolkit for Ontology Alignment, accessed August 1, 2025, [https://arxiv.org/html/2503.21902v1](https://arxiv.org/html/2503.21902v1)  
52. sciknoworg/OntoAligner: OntoAligner: A Python Toolkit for Ontology Alignment https://pypi.org/project/OntoAligner \- GitHub, accessed August 1, 2025, [https://github.com/sciknoworg/OntoAligner](https://github.com/sciknoworg/OntoAligner)  
53. Evaluating the Effectiveness of Cost-Efficient Large Language Models in Benchmark Biomedical Tasks \- arXiv, accessed August 1, 2025, [https://arxiv.org/html/2507.14045v1](https://arxiv.org/html/2507.14045v1)