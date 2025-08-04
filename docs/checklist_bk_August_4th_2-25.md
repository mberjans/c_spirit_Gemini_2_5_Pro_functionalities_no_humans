Of course. Here is a detailed checklist of tasks for the software development team, broken down by each ticket. Each task includes a unique ID and is structured to begin with unit test development and end with conducting those tests, ensuring a test-driven development approach.

### **Phase 1: Foundation and Data Ingestion**

**Ticket ID: AIM2-001: Initialize Project Structure**

* \[ \] **AIM2-001-T1:** Develop unit tests to verify the existence and correct naming of all required directories (data/raw, src/aim2, models, etc.) as specified by the CCDS template.1  
* \[ \] **AIM2-001-T2:** Write a script to programmatically generate the project structure using the cookiecutter command-line tool.  
* \[ \] **AIM2-001-T3:** Initialize a Git repository in the project's root directory.  
* \[ \] **AIM2-001-T4:** Create an initial .gitignore file to exclude common Python artifacts, data files, and environment directories.  
* \[ \] **AIM2-001-T5:** Conduct unit tests to confirm the project structure is created correctly.

**Ticket ID: AIM2-002: Establish Python Environment and Core Dependencies**

* \[ \] **AIM2-002-T1:** Develop unit tests to check for the presence and correct versioning of key libraries in a virtual environment.  
* \[ \] **AIM2-002-T2:** Create the requirements.txt file in the project root.  
* \[ \] **AIM2-002-T3:** Add all specified libraries (Owlready2 2,  
  EMMOntoPy 4,  
  pymed\_paperscraper 5,  
  unpywall 6,  
  PyMuPDF 8,  
  lxml, transformers 9,  
  langchain 10,  
  RapidFuzz 11,  
  BioEL 12,  
  seqeval) with appropriate version constraints.  
* \[ \] **AIM2-002-T4:** Create a script or Makefile command to automate the setup of a virtual environment and installation of all dependencies.  
* \[ \] **AIM2-002-T5:** Conduct unit tests to validate the environment setup.

**Ticket ID: AIM2-003: Module 1: Create AIM2 Ontology Scaffold**

* \[ \] **AIM2-003-T1:** Develop unit tests to check if the output OWL file exists, is valid XML, and contains the three top-level classes (StructuralAnnotation, SourceAnnotation, FunctionalAnnotation).  
* \[ \] **AIM2-003-T2:** Write the Python script using Owlready2 to initialize the ontology World and define the base IRI for the AIM2 ontology.2  
* \[ \] **AIM2-003-T3:** In the script, create the three top-level classes inheriting from owl.Thing.  
* \[ \] **AIM2-003-T4:** In the script, define namespaces for external ontologies (e.g., GO, PMN, PECO).  
* \[ \] **AIM2-003-T5:** Implement the save functionality to write the ontology to data/processed/aim2.owl.  
* \[ \] **AIM2-003-T6:** Conduct unit tests.

**Ticket ID: AIM2-004: Module 5: Implement PubMed Metadata Retrieval**

* \[ \] **AIM2-004-T1:** Develop unit tests that mock the pymed API call and verify that the script correctly processes the mock response and populates a test SQLite database.  
* \[ \] **AIM2-004-T2:** Design and implement the schema for the local SQLite database to track articles (e.g., articles table with columns for PMID, DOI, status, etc.).  
* \[ \] **AIM2-004-T3:** Implement the core query function using pymed\_paperscraper that takes a keyword list and max\_results.5  
* \[ \] **AIM2-004-T4:** Implement logic to parse the results and insert them into the SQLite database, handling potential duplicates.  
* \[ \] **AIM2-004-T5:** Integrate Bio.Entrez as a supplementary tool for more complex queries or cross-referencing.14  
* \[ \] **AIM2-004-T6:** Conduct unit tests.

**Ticket ID: AIM2-005: Module 6: Implement Open Access Full-Text Acquisition**

* \[ \] **AIM2-005-T1:** Develop unit tests that mock the unpywall API and test the logic for updating the article status in the SQLite database upon a successful mock download.  
* \[ \] **AIM2-005-T2:** Write a script to read DOIs from the SQLite database for articles needing full-text acquisition.  
* \[ \] **AIM2-005-T3:** Implement a function using unpywall to query the Unpaywall API and retrieve the best OA URL for a given DOI.6  
* \[ \] **AIM2-005-T4:** Implement logic to download the content from the retrieved URL and save it to the data/raw/ directory.  
* \[ \] **AIM2-005-T5:** Implement logic to update the article's status in the SQLite database (e.g., 'fulltext\_acquired\_oa' or 'fulltext\_not\_found\_oa').  
* \[ \] **AIM2-005-T6:** Conduct unit tests.

**Ticket ID: AIM2-006: Module 6: Develop Paywall Navigation Scraper**

* \[ \] **AIM2-006-T1:** Develop unit tests for individual evasion techniques (e.g., a test to confirm the User-Agent is being rotated correctly from a predefined list).  
* \[ \] **AIM2-006-T2:** Set up the Playwright environment and integrate a specialized library like Botasaurus for advanced evasion.20  
* \[ \] **AIM2-006-T3:** Implement IP rotation using a commercial proxy service API.  
* \[ \] **AIM2-006-T4:** Implement a function to manage and rotate realistic HTTP headers (User-Agent, Referer, Accept-Language).21  
* \[ \] **AIM2-006-T5:** Implement functions to simulate human-like interactions (randomized delays, scrolling).  
* \[ \] **AIM2-006-T6:** Develop the core scraping logic to navigate to an article page and trigger the download.  
* \[ \] **AIM2-006-T7:** Conduct unit tests on a safe, non-paywalled target to verify functionality.

**Ticket ID: AIM2-007: Module 7: Implement Document Parsing and Normalization**

* \[ \] **AIM2-007-T1:** Develop unit tests with sample PDF and XML files to verify correct text extraction, cleaning (e.g., hyphen removal), and chunking.  
* \[ \] **AIM2-007-T2:** Implement a PDF parsing function using PyMuPDF (Fitz) to extract text while preserving the logical reading order from complex layouts.8  
* \[ \] **AIM2-007-T3:** Implement an XML parsing function using lxml to extract text from relevant sections (e.g., "Methods", "Results").  
* \[ \] **AIM2-007-T4:** Create a text cleaning function to normalize whitespace and remove conversion artifacts.  
* \[ \] **AIM2-007-T5:** Implement a chunking function that splits text into overlapping segments of a specified token length.  
* \[ \] **AIM2-007-T6:** Write the main script to orchestrate reading from data/raw, applying the correct parser, and saving chunked text to data/interim.  
* \[ \] **AIM2-007-T7:** Conduct unit tests.

### **Phase 2: AI Model Development and Bootstrapping**

**Ticket ID: AIM2-008: Module 2: Automate External Ontology Term Integration**

* \[ \] **AIM2-008-T1:** Develop unit tests to check if a merged ontology file contains terms from multiple mock source ontologies.  
* \[ \] **AIM2-008-T2:** Implement a script using subprocess to call EMMOntoPy's ontoconvert tool for bulk merging ("squashing") of source ontologies.4  
* \[ \] **AIM2-008-T3:** Develop a more granular script using Owlready2 to load the AIM2 ontology and source ontologies into memory.  
* \[ \] **AIM2-008-T4:** Implement logic within the granular script to iterate through source terms and programmatically create corresponding classes under the correct AIM2 parent class.  
* \[ \] **AIM2-008-T5:** Conduct unit tests.

**Ticket ID: AIM2-009: Module 10: Implement Synthetic Data Generation for NER/RE**

* \[ \] **AIM2-009-T1:** Develop unit tests to verify the format and content of the generated JSON output (e.g., ensuring 'text' and 'spans' keys exist and are correctly formatted).  
* \[ \] **AIM2-009-T2:** Develop a function to load the AIM2 ontology schema to inform the generation process.  
* \[ \] **AIM2-009-T3:** Engineer structured prompts for an LLM to generate scientifically plausible sentences with specific entities and relationships.25  
* \[ \] **AIM2-009-T4:** Implement an "Evol-Instruct" methodology with follow-up prompts to iteratively increase the complexity and diversity of the generated sentences.26  
* \[ \] **AIM2-009-T5:** Write the main script to orchestrate the generation process at scale, saving the output as a JSONL file.  
* \[ \] **AIM2-009-T6:** Conduct unit tests.

**Ticket ID: AIM2-010: Module 8 (Stage 1): Fine-Tune BioBERT for Candidate NER**

* \[ \] **AIM2-010-T1:** Develop unit tests to check the data loading and preprocessing steps for the Trainer API.  
* \[ \] **AIM2-010-T2:** Write a script to load the synthetic dataset (from AIM2-009) using the Hugging Face datasets library.  
* \[ \] **AIM2-010-T3:** Implement the tokenization and label alignment logic required for token classification models.  
* \[ \] **AIM2-010-T4:** Configure the TrainingArguments for the fine-tuning job, including output directory, evaluation strategy, and learning rate.27  
* \[ \] **AIM2-010-T5:** Instantiate the Trainer with the dmis-lab/biobert-v1.1 model, arguments, and datasets.28  
* \[ \] **AIM2-010-T6:** Implement the call to trainer.train() and save the final model to the models/ directory.  
* \[ \] **AIM2-010-T7:** Conduct unit tests.

**Ticket ID: AIM2-011: Module 8 (Stage 2): Implement LLM-Based NER Validation**

* \[ \] **AIM2-011-T1:** Develop unit tests to verify that the LLM prompt is correctly constructed with candidate entities from a mock BioBERT output.  
* \[ \] **AIM2-011-T2:** Write a function to run the fine-tuned BioBERT model (from AIM2-010) on a text chunk to get candidate entities.  
* \[ \] **AIM2-011-T3:** Engineer the prompt for the generative LLM, instructing it to validate, correct, or reject the candidate entities.  
* \[ \] **AIM2-011-T4:** Implement the LLM API call to process the text chunk and its candidates.  
* \[ \] **AIM2-011-T5:** Write a function to parse the LLM's response and produce a final, validated list of entities for that chunk.  
* \[ \] **AIM2-011-T6:** Conduct unit tests.

**Ticket ID: AIM2-012: Module 9: Develop Advanced Relationship Extraction**

* \[ \] **AIM2-012-T1:** Develop unit tests to validate the Pydantic schema and ensure the LLM output can be correctly parsed into the defined objects.  
* \[ \] **AIM2-012-T2:** Define the Pydantic schemas for the structured JSON output.  
* \[ \] **AIM2-012-T3:** Implement the Chain-of-Thought (CoT) prompt using LangChain, guiding the LLM to "think step by step".30  
* \[ \] **AIM2-012-T4:** Implement the hierarchical prompting logic: an initial prompt for general relations, followed by a conditional prompt for more specific mechanisms.34  
* \[ \] **AIM2-012-T5:** Integrate the with\_structured\_output feature to enforce the Pydantic schema.10  
* \[ \] **AIM2-012-T6:** Conduct unit tests.

### **Phase 3: Integration, Refinement, and Knowledge Base Population**

**Ticket ID: AIM2-013: Module 3: Implement Algorithmic Ontology Trimming**

* \[ \] **AIM2-013-T1:** Develop unit tests with a sample ontology to ensure the trimming function correctly removes specified classes while leaving required parent classes and other terms intact.  
* \[ \] **AIM2-013-T2:** Implement the seed term selection and hierarchical expansion using EMMOntoPy's get\_ancestors and get\_descendants methods.38  
* \[ \] **AIM2-013-T3:** Implement annotation-based pruning (e.g., removing terms marked as 'obsolete').  
* \[ \] **AIM2-013-T4:** Implement the final removal step using Owlready2's destroy\_entity() function to ensure clean removal of entities and their associated triples.3  
* \[ \] **AIM2-013-T5:** Conduct unit tests.

**Ticket ID: AIM2-014: Module 4: Define Custom Hierarchical Relationships**

* \[ \] **AIM2-014-T1:** Develop unit tests to load the modified ontology and assert that the custom properties exist with the correct domain, range, and sub-property relationships.  
* \[ \] **AIM2-014-T2:** Write a script using Owlready2 to define new classes inheriting from ObjectProperty.2  
* \[ \] **AIM2-014-T3:** For each new property, set its domain and range attributes.  
* \[ \] **AIM2-014-T4:** For hierarchical properties, set the is\_a attribute to link to a parent property.  
* \[ \] **AIM2-014-T5:** Add annotations (e.g., comment) to each property for documentation.40  
* \[ \] **AIM2-014-T6:** Conduct unit tests.

**Ticket ID: AIM2-015: Module 11: Set Up Semi-Automated Quality Control Workflow**

* \[ \] **AIM2-015-T1:** Develop unit tests for the custom Prodigy recipe script to ensure it loads data and streams it in the expected format.  
* \[ \] **AIM2-015-T2:** Write a custom Prodigy recipe script (.py file).42  
* \[ \] **AIM2-015-T3:** In the recipe, implement logic to load the NER model and make predictions on a stream of data.  
* \[ \] **AIM2-015-T4:** Use a Prodigy sorter (e.g., prefer\_uncertain) to select the most informative examples for review.  
* \[ \] **AIM2-015-T5:** Configure the recipe to use the ner\_manual interface for correction.  
* \[ \] **AIM2-015-T6:** Write a script to export the corrected annotations from the Prodigy database and merge them back into the training set.  
* \[ \] **AIM2-015-T7:** Conduct unit tests.

**Ticket ID: AIM2-016: Module 12: Implement Entity Normalization and Linking**

* \[ \] **AIM2-016-T1:** Develop unit tests for both fuzzy matching and semantic linking with mock data and a test ontology.  
* \[ \] **AIM2-016-T2:** Implement syntactic normalization (lowercase, strip whitespace) and fuzzy string matching using RapidFuzz to cluster entity variations.44  
* \[ \] **AIM2-016-T3:** Implement the semantic linking step using BioEL, configuring it to use the custom AIM2 ontology as the target knowledge base.12  
* \[ \] **AIM2-016-T4:** As an alternative or supplement, investigate OntoAligner for its flexible framework supporting fuzzy, retrieval, and LLM-based alignment methods.46  
* \[ \] **AIM2-016-T5:** Write the main script to process raw extracted entities and output canonicalized, linked entity IDs.  
* \[ \] **AIM2-016-T6:** Conduct unit tests.

**Ticket ID: AIM2-017: Module 13: Implement Knowledge Base Population and Management**

* \[ \] **AIM2-017-T1:** Develop unit tests to check deduplication and conflict resolution logic.  
* \[ \] **AIM2-017-T2:** Implement a function to generate a unique hash for each canonicalized triple for deduplication.  
* \[ \] **AIM2-017-T3:** Implement a rule-based conflict resolution strategy (e.g., based on publication date or journal impact factor).  
* \[ \] **AIM2-017-T4:** Write a script to save the final set of triples to a CSV file in the reports/ directory.  
* \[ \] **AIM2-017-T5:** Write a script to programmatically create Individuals in Owlready2 and assert the relationships between them.  
* \[ \] **AIM2-017-T6:** Conduct unit tests.

### **Phase 4: Evaluation and Finalization**

**Ticket ID: AIM2-018: Create Gold Standard Evaluation Dataset**

* \[ \] **AIM2-018-T1:** Define and document the final annotation guidelines.  
* \[ \] **AIM2-018-T2:** Select the \~25 representative papers for annotation.  
* \[ \] **AIM2-018-T3:** Perform annotation by the first annotator.  
* \[ \] **AIM2-018-T4:** Perform annotation by the second annotator.  
* \[ \] **AIM2-018-T5:** Adjudicate disagreements between annotators to create the final gold standard.  
* \[ \] **AIM2-018-T6:** Convert the final annotations into the standard IOB format and save as a JSONL file.

**Ticket ID: AIM2-019: Module 14: Develop Automated Evaluation Framework**

* \[ \] **AIM2-019-T1:** Develop unit tests with sample predicted and true labels to verify that precision, recall, and F1 scores are calculated correctly by seqeval.  
* \[ \] **AIM2-019-T2:** Write a script to load the gold standard dataset (from AIM2-018).  
* \[ \] **AIM2-019-T3:** Write a function that takes a model's predictions and the gold standard labels as input.  
* \[ \] **AIM2-019-T4:** Implement the core evaluation logic using the seqeval library to calculate precision, recall, and F1-score.  
* \[ \] **AIM2-019-T5:** Format the output into a clear, readable report.  
* \[ \] **AIM2-019-T6:** Conduct unit tests.

**Ticket ID: AIM2-020: Module 15: Conduct Comparative LLM Benchmarking**

* \[ \] **AIM2-020-T1:** Develop unit tests for the benchmarking script's orchestration logic (e.g., ensuring it calls the correct model configuration).  
* \[ \] **AIM2-020-T2:** Create a main benchmarking script that can be configured to run a specific model (BioBERT, Llama 3, GPT-4o, etc.) and prompting strategy.48  
* \[ \] **AIM2-020-T3:** Integrate the end-to-end extraction pipeline (Modules 5 through 13\) into the script.  
* \[ \] **AIM2-020-T4:** Integrate the evaluation framework (from AIM2-019) to calculate metrics for each run.  
* \[ \] **AIM2-020-T5:** Add functionality to measure and log inference time and API costs for each configuration.  
* \[ \] **AIM2-020-T6:** Run the benchmark for all specified model/prompt combinations on the gold standard dataset.  
* \[ \] **AIM2-020-T7:** Generate a final summary report and table with all the results.  
* \[ \] **AIM2-020-T8:** Conduct unit tests.