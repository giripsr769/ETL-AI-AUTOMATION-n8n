# ETL AI Automation Workflow (n8n)

An intelligent end-to-end **ETL (Extract, Transform, Load) Pipeline** built in **n8n**. This workflow ingests student enrollment CSV data via a Web Form trigger, uses **OpenAI (gpt-4.1-mini)** to clean and normalize raw input data, parses JSON results with JavaScript, loops through items in batches, and automatically segments and routes processed records into **4 specialized CSV outputs** based on location (**Chennai** vs **Other Cities**) and email validity (**Valid** vs **INVALID_EMAIL**).

---

## 📌 Features & Key Capabilities

- **Form Trigger (`CSV_File_Upload`)**: Web form interface for seamless CSV file submission.
- **Data Extraction (`Extract from File`)**: Converts raw uploaded binary files into JSON arrays.
- **AI Normalization (`Basic LLM Chain` + `OpenAI Chat Model`)**:
  - Standardizes **Name** & **City** to Title Case.
  - Validates **Email** structure (flags syntax errors as `INVALID_EMAIL`).
  - Standardizes **Phone** (sets missing/incomplete numbers to `MISSING`).
  - Normalizes **Course** names to `Python`, `Machine Learning`, or `Data Science`.
  - Normalizes **Fee_Paid** to booleans (`true`/`false`).
  - Standardizes **Enrolled_Date** to standard `YYYY-MM-DD` format.
- **Batch Processing (`Loop Over Items`)**: Iterates through cleansed items for reliable downstream processing.
- **JavaScript Parsing (`Code in JavaScript`)**: Strips markdown code blocks, cleanses white-spaces, and handles raw JSON parse failures.
- **Multi-Branch Conditional Routing (`If`, `If_Chennai_city`, `If_other_city`)**:
  - Dynamically routes records by **City** (`Chennai` vs Non-Chennai).
  - Categorizes records by **Email Quality** (`INVALID_EMAIL` vs Valid Email).
- **Automated CSV Categorization Output Files**:
  1. `chennai_invalid_email.csv`
  2. `chennai_valid_email.csv`
  3. `other_invalid_email.csv`
  4. `other_valid_email.csv`

---

## 🛠️ Workflow Architecture Diagram

```text
               [ On Form Submission (CSV Upload) ]
                                │
                                ▼
                   [ Extract from File (CSV -> JSON) ]
                                │
                                ▼
              [ Basic LLM Chain (OpenAI gpt-4.1-mini) ]
                                │
                                ▼
                       [ Loop Over Items ]
                                │
                                ▼
                     [ Code in JavaScript ]
                                │
                                ▼
                         [ If: City == Chennai? ]
                        /                                         (TRUE)                      (FALSE)
                     /                                  [ If_Chennai_city ]                      [ If_other_city ]
        /              \                         /                (TRUE)            (FALSE)                (TRUE)          (FALSE)
     /                  \                     /                [chennai_invalid_email] [chennai_valid_email] [other_city_invalid_email] [other_city_valid_email]
```

---

## 📂 Segmented CSV Output Schema

| Output Binary Name | Target City | Email Status | Output File Name |
| :--- | :--- | :--- | :--- |
| **`chennai_invalid_email`** | `Chennai` | `INVALID_EMAIL` | `chennai_invalid_email.csv` |
| **`chennai_valid_email`** | `Chennai` | Valid | `chennai_valid_email.csv` |
| **`other_city_invalid_email`** | Other | `INVALID_EMAIL` | `other_invalid_email.csv` |
| **`other_city_valid_email`** | Other | Valid | `other_valid_email.csv` |

---

## 🚀 How to Setup & Run in n8n

### 1. Prerequisites
- Running instance of **n8n** (Cloud or Docker host).
- Active **OpenAI API Key** configured under n8n Credentials.

### 2. Import Steps
1. In n8n dashboard, select **Workflows** > **Add Workflow**.
2. Click the top-right menu icon (`...`) and click **Import from File**.
3. Choose the `ETL AI Automation.json` file.
4. Set up the **OpenAI Chat Model** node with your OpenAI credentials.
5. Save and toggle the workflow status to **Active**.
6. Open the Form Trigger link to upload CSV files and execute the pipeline!

---

## 📄 License
This project is open-source under the MIT License.
