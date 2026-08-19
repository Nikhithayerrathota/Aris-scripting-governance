---

## 1. Core Context & Database APIs

These global environment objects establish the entry point for report execution, obtaining active repository handles and report configurations.

| API Class / Object | Method | Return Type | Description / Usage |
| :--- | :--- | :--- | :--- |
| `ArisData` | `getSelectedDatabases()` | `Database[]` | Returns an array of databases currently selected by the user. |
| `ArisData` | `getSelectedModels()` | `Model[]` | Returns models selected directly in the ARIS Explorer or Portal. |
| `ArisData` | `getSelectedGroups()` | `Group[]` | Returns folder groups selected for batch processing. |
| `Database` | `RootGroup()` | `Group` | Accesses the top-level root folder of the active ARIS database. |
| `Database` | `LanguageList()` | `Language[]` | Retrieves supported database languages for multilingual attribute queries. |
| `Context` | `getSelectedLanguage()` | `Number` | Obtains the locale ID (LCID) for the current user session (e.g., `1033` for English). |
| `Context` | `createOutputObject()` | `OutputObject` | Instantiates the report output builder (HTML, PDF, XLS, TXT). |

---

## 2. Hierarchy & Traversal APIs

Methods used to navigate the organizational folder tree and inspect model canvases (`Model`).

| API Class | Method | Return Type | Description / Usage |
| :--- | :--- | :--- | :--- |
| `Group` | `Childs()` | `Group[]` | Returns all immediate sub-folders (sub-groups). |
| `Group` | `ModelList(Boolean bRecursive)` | `Model[]` | Retrieves models within the group (and optionally nested sub-groups). |
| `Model` | `Name(Number nLocale)` | `String` | Returns the localized display name of the model. |
| `Model` | `TypeNum()` | `Number` | Returns the model type constant (e.g., `Constants.MT_EEPK` for EPC, `Constants.MT_BPMN_PROCESS_DIAGRAM` for BPMN). |
| `Model` | `ObjOccList()` | `ObjOcc[]` | Returns all visual object occurrences placed on the model canvas. |

---

## 3. Metamodel APIs (`ObjOcc` vs. `ObjDef`)

Crucial methods that decouple visual representations (`ObjOcc`) on the canvas from underlying master data definitions (`ObjDef`) stored in the repository.

| API Class | Method | Return Type | Description / Usage |
| :--- | :--- | :--- | :--- |
| `ObjOcc` | `ObjDef()` | `ObjDef` | **Primary Metamodel Bridge:** Navigates from a visual canvas shape to its underlying central master definition. |
| `ObjOcc` | `SymbolNum()` | `Number` | Returns visual symbol identifier (e.g., Function symbol vs. Event symbol). |
| `ObjDef` | `Name(Number nLocale)` | `String` | Gets the central master object name. |
| `ObjDef` | `TypeNum()` | `Number` | Returns object type identifier (e.g., `Constants.OT_FUNC` for Functions/Tasks, `Constants.OT_EVT` for Events). |
| `ObjDef` | `AttributeList(Number nLocale)`| `Attribute[]` | Returns all populated attributes on the master definition. |
| `ObjDef` | `CxtXRefList()` | `CxtXRef[]` | Returns connected objects (e.g., Risk and Control objects linked to a Task). |

---

## 4. Attribute Governance & Inspection APIs

The core evaluation engine methods used to test attribute existence, value completeness, and risk/control linkages.

| API Class | Method | Return Type | Description / Usage |
| :--- | :--- | :--- | :--- |
| `ObjDef` | `Attribute(Number nAttrType, Number nLocale)` | `Attribute` | Fetches a specific attribute instance by its ARIS constant ID. |
| `Attribute` | `IsMaintained()` | `Boolean` | **Key Governance Method:** Evaluates `true` if the attribute contains data; `false` if null or uninitialized. |
| `Attribute` | `GetValue()` | `String` | Returns text or formatted value from the attribute. |
| `Attribute` | `MeasureValue()` | `Number` | Returns numeric value for quantitative metrics. |

---

## 5. Output & Report Generation APIs

Methods provided by `OutputObject` to structure, format, and generate compliance summary reports.

| API Class | Method | Return Type | Description / Usage |
| :--- | :--- | :--- | :--- |
| `OutputObject` | `DefineFNT(String sName, Number nSize, ...)` | `Void` | Registers font styling rules for report headers and tables. |
| `OutputObject` | `BeginTable(Number nWidth, ...)` | `Void` | Initializes an output data table. |
| `OutputObject` | `TableRow()` | `Void` | Creates a new table row. |
| `OutputObject` | `TableCell(String sText, ...)` | `Void` | Populates a table cell with text, background color, and alignment. |
| `OutputObject` | `EndTable(String sTitle, ...)` | `Void` | Finalizes table structure. |
| `OutputObject` | `WriteReport()` | `Void` | Writes data stream to file and delivers report to the user interface. |

---

## 6. Essential ARIS Constants Reference

| ARIS Constant Name | Category | Description |
| :--- | :--- | :--- |
| `Constants.MT_EEPK` | Model Type | Event-Driven Process Chain (EPC). |
| `Constants.MT_BPMN_PROCESS_DIAGRAM` | Model Type | BPMN 2.0 Process Diagram. |
| `Constants.OT_FUNC` | Object Type | Task / Function object. |
| `Constants.OT_EVT` | Object Type | Event object. |
| `Constants.OT_RISK` | Object Type | Risk object. |
| `Constants.OT_CTRL` | Object Type | Control object. |
| `Constants.AT_NAME` | Attribute Type | Name attribute. |
| `Constants.AT_DESC` | Attribute Type | Description / Documentation attribute. |
| `Constants.AT_AUTHOR` | Attribute Type | Process Owner / Author attribute. |

---

## 7. End-to-End Execution Script Example

Below is a complete ARIS JavaScript implementation demonstrating how these mandatory APIs work together in a governance pipeline:

```javascript
/**
 * ARIS Governance Report - O2C Attribute Compliance Checker
 * Language: JavaScript (ARIS Scripting Engine)
 */

function main() {
    var nLocale = Context.getSelectedLanguage();
    var oOutput = Context.createOutputObject();
    
    // Initialize Output Format
    oOutput.DefineFNT("STD", 10, "Arial");
    oOutput.DefineFNT("BOLD", 10, "Arial", Constants.FMT_BOLD);
    oOutput.BeginTable(100, Constants.C_BLACK, Constants.C_WHITE, Constants.FMT_LEFT, 0);
    
    // Table Header
    oOutput.TableRow();
    oOutput.TableCell("Model Name", 25, "BOLD", Constants.C_WHITE, Constants.C_NAVY, Constants.FMT_LEFT, 0);
    oOutput.TableCell("Object Name", 25, "BOLD", Constants.C_WHITE, Constants.C_NAVY, Constants.FMT_LEFT, 0);
    oOutput.TableCell("Description Maintained", 25, "BOLD", Constants.C_WHITE, Constants.C_NAVY, Constants.FMT_LEFT, 0);
    oOutput.TableCell("Compliance Status", 25, "BOLD", Constants.C_WHITE, Constants.C_NAVY, Constants.FMT_LEFT, 0);
    
    // Layer 1: Database Access
    var aModels = ArisData.getSelectedModels();
    
    for (var i = 0; i < aModels.length; i++) {
        var oModel = aModels[i];
        var sModelName = oModel.Name(nLocale);
        
        // Layer 2 & 3: Model Traversal & Metamodel Decoupling
        var aObjOccs = oModel.ObjOccList();
        
        for (var j = 0; j < aObjOccs.length; j++) {
            var oOcc = aObjOccs[j];
            var oDef = oOcc.ObjDef(); // Decouple visual occurrence to master definition
            
            // Filter: Inspect Functions/Tasks only
            if (oDef.TypeNum() == Constants.OT_FUNC) {
                var sObjName = oDef.Name(nLocale);
                
                // Layer 4: Attribute Governance Check
                var oDescAttr = oDef.Attribute(Constants.AT_DESC, nLocale);
                var bIsMaintained = oDescAttr.IsMaintained();
                
                var sStatus = bIsMaintained ? "PASS" : "FAIL (Red Flag)";
                var nBgColor = bIsMaintained ? Constants.C_LIGHT_GREEN : Constants.C_LIGHT_RED;
                
                // Render Row
                oOutput.TableRow();
                oOutput.TableCell(sModelName, 25, "STD", Constants.C_BLACK, Constants.C_WHITE, Constants.FMT_LEFT, 0);
                oOutput.TableCell(sObjName, 25, "STD", Constants.C_BLACK, Constants.C_WHITE, Constants.FMT_LEFT, 0);
                oOutput.TableCell(bIsMaintained ? "Yes" : "No", 25, "STD", Constants.C_BLACK, Constants.C_WHITE, Constants.FMT_LEFT, 0);
                oOutput.TableCell(sStatus, 25, "BOLD", Constants.C_BLACK, nBgColor, Constants.FMT_CENTER, 0);
            }
        }
    }
    
    oOutput.EndTable("O2C Governance Summary", 0);
    oOutput.WriteReport();
}

main();
