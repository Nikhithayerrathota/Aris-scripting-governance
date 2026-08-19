# ARIS Metamodel & Process Governance: Core Definitions Reference

An authoritative terminology and definition guide covering the fundamental concepts of ARIS metamodel architecture, repository structure, process governance, and JavaScript scripting engine mechanics.

---

## 📋 Table of Contents
1. [ARIS Metamodel Architecture Concepts](#1-aris-metamodel-architecture-concepts)
2. [Data Traversal Layer Definitions](#2-data-traversal-layer-definitions)
3. [Process Governance & Audit Concepts](#3-process-governance--audit-concepts)
4. [ARIS Scripting & API Mechanics](#4-aris-scripting--api-mechanics)
5. [Process Modeling Notation Concepts (EPC vs. BPMN)](#5-process-modeling-notation-concepts-epc-vs-bpmn)

---

## 1. ARIS Metamodel Architecture Concepts

### **1.1. Metamodel (Method Filter)**
The underlying structural schema of ARIS that defines allowed object types, connection types, model types, and attribute types. The method filter enforces modeling rules so users only utilize permitted syntax.

### **1.2. Object Definition (`ObjDef`)**
The single, central master instance of an element (e.g., a specific Task, Risk, or Role) stored within the ARIS database repository. It holds all master attribute values (description, author, risk metrics) independently of where or how many times it appears visually.

### **1.3. Object Occurrence (`ObjOcc`)**
A visual representation or shape placed on a specific model canvas that points back to a master Object Definition (`ObjDef`). A single `ObjDef` can have multiple `ObjOcc` visual instances across different diagrams.

### **1.4. Symbol (`SymbolNum`)**
The graphical representation assigned to an `ObjOcc` on the canvas (e.g., a green rounded rectangle for a Function, a pink hexagon for an Event).

### **1.5. Connection Definition (`CxDef`) vs. Connection Occurrence (`CxOcc`)**
* **Connection Definition (`CxDef`):** The underlying semantic relationship established between two master object definitions (e.g., Task *carries out* Role).
* **Connection Occurrence (`CxOcc`):** The visual line or arrow rendered on a model canvas connecting two object occurrences.

---

## 2. Data Traversal Layer Definitions

### **2.1. Layer 1: Database Layer (`Database`)**
The root storage environment containing all groups, models, objects, cross-references, and language translation tables.

### **2.2. Layer 2: Hierarchy Layer (`Group` & `Model`)**
The folder tree structure (`Group`) used to organize enterprise process architectures into logical domains, sub-folders, and individual process diagrams (`Model`).

### **2.3. Layer 3: Metamodel Layer (`ObjOcc` to `ObjDef` Bridge)**
The structural layer where visual elements (`ObjOcc`) are programmatically decoupled to access central master data definitions (`ObjDef`) and their associated cross-references (`CxtXRef`).

### **2.4. Layer 4: Scripting & Governance Layer (`OutputObject`)**
The logic execution engine where JavaScript rules evaluate attribute completeness (`IsMaintained()`) and render output reports.

---

## 3. Process Governance & Audit Concepts

### **3.1. Process Governance**
The framework of rules, roles, and automated checks established to ensure process models adhere to organizational standards, data quality rules, and regulatory compliance requirements.

### **3.2. Mandatory Attributes**
Key metadata fields required on process objects to pass compliance checks, such as `AT_NAME` (Name), `AT_DESC` (Description), `AT_AUTHOR` (Process Owner), and risk/control links.

### **3.3. Attribute Completeness (`IsMaintained`)**
A boolean evaluation (`true`/`false`) testing whether a designated attribute field contains populated data or remains null/empty.

### **3.4. Compliance Red Flag**
An automated exception condition raised during script execution when a mandatory governance attribute or risk/control link is missing from a process element.

---

## 4. ARIS Scripting & API Mechanics

### **4.1. ARIS Scripting Engine**
The embedded JavaScript execution environment inside ARIS Server/Architect used to automate administrative tasks, traverse model hierarchies, and generate reports.

### **4.2. `ArisData` Global Object**
The primary entry-point context object used in ARIS scripts to retrieve user selection handles (selected databases, models, or groups).

### **4.3. `Context` Object**
The environment controller used to extract execution properties (like active user language locale IDs) and instantiate report output builders (`createOutputObject()`).

### **4.4. `OutputObject`**
The specialized report generation engine used to construct formatted output documents (HTML, PDF, XLS, TXT) via programmatic table building.

---

## 5. Process Modeling Notation Concepts (EPC vs. BPMN)

### **5.1. Event-Driven Process Chain (EPC / EEPK)**
A business-focused process modeling notation enforcing strict state-driven alternating sequences: `[Event] -> [Function] -> [Event]`.

### **5.2. Business Process Model and Notation (BPMN 2.0)**
An open standard modeling notation optimized for both business process visualization and technical execution using sequence flows, pools, swimlanes, and gateways.

### **5.3. Function / Task (`OT_FUNC`)**
An active operational step or activity within a process that transforms inputs into outputs.

### **5.4. Event (`OT_EVT`)**
A passive state change or condition that triggers or results from a process function.

---
*Created by **Nikhitha Yerrathota** | Business Process Management & Governance Analyst*
