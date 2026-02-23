### 📌 Pull Request Checklist  

#### 1️⃣ **Data Repository Submission**  
- [ ] I have uploaded all necessary data files and scripts to the **data repository**: [Data Repository](https://github.com/cal-icor/textbook.data)  
- [ ] I have created a **pull request** in the data repository and linked it below:  
  - **Data PR:** [textbook.data PR](https://github.com/cal-icor/textbook.data/blob/main/.github/pull_request_template.md)

#### 2️⃣ **Notebook Updates**  
- [ ] My notebook **references data files via URLs** instead of local file paths  
  - Example:  
    ```python
    pd.read_csv("https://cal-icor.github.io/textbook.data/<YOUR_INTITUTION_NAME>/<YOUR_COURSE_NAME>/your_file.csv")
    ```
- [ ] If my notebook imports a local Python script, I have used `httpimport`  
  - Example:  
    ```python
    import httpimport
    with httpimport.remote_repo("https://cal-icor.github.io/textbook.data/<YOUR_INTITUTION_NAME>/<YOUR_COURSE_NAME>"):
        import my_script
    ```

#### 3️⃣ **Execution & Validation**  
- [ ] I have run the notebook **from start to finish** and confirmed that it executes **without errors**  
- [ ] All outputs are correct and expected  

#### 4️⃣ **Repository Updates**  
- [ ] I have updated [`requirements.txt`](https://github.com/cal-icor/textbook/blob/main/requirements.txt) with any additional packages needed for my notebook  
- [ ] I have updated the **Table of Contents** in [`myst.yml`](https://github.com/cal-icor/textbook/blob/main/myst.yml) (under `project.toc`)  

  **If my subject already exists:**  
  - I added my notebook to the appropriate section’s `children` list  
  - Example:
    ```yaml
    - title: Environmental Science, Policy, and Management
      children:
        - file: modules/biodiversity.ipynb
        - file: modules/espm-regression.ipynb
        - file: modules/<your-notebook>.ipynb
    ```

  **If my subject is new:**  
  - I added a new section at the end of `project.toc` in `myst.yml`  
  - Example:
    ```yaml
    - title: <YOUR SUBJECT>
      children:
        - file: modules/<NOTEBOOK_1_NAME>.ipynb
        - file: modules/<NOTEBOOK_2_NAME>.ipynb
    ```

#### 5️⃣ **Final Review**  
- [ ] I have **reviewed my changes** to ensure all files are correct and formatted properly  
- [ ] I have **linked my data PR** in this submission  
- [ ] I acknowledge that PRs that do not follow these guidelines **may be rejected**  

---

### 🔗 **Pull Request Summary**  
- **Notebook Name:** `<NOTEBOOK_NAME>.ipynb`  
- **Linked Data PR:** [Insert link here]  
- **New Dependencies (if any):** `<List any added packages>`  
- **TOC Updated:** `Yes / No`  

---

## 🚀 Need Help?  
If you encounter any issues, check the [README](https://github.com/cal-icor/textbook/blob/main/README.md) or ask for assistance.  
