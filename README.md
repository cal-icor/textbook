# textbook

Jupyter Book Textbook containing modules notebooks organized by subject. All notebooks and content live in **this repository**: [cal-icor/textbook](https://github.com/cal-icor/textbook).

**Live site:** [https://cal-icor.github.io/textbook/](https://cal-icor.github.io/textbook/) — run code in-browser (power button → run cells or Run all), open in [Binder](https://mybinder.org/), or use the rocket menu to launch on your own Jupyter Hub. See [How to use this textbook](https://cal-icor.github.io/textbook/intro.html#how-to-use-this-textbook) in the book for details.

---

## 🚀 How to Add a Notebook  

### 1️⃣ **Upload Your Data Files**  

Before adding your notebook, ensure that all required data files and scripts are uploaded to the **data repository**:  
📌 **Follow the instructions here:** [Data Repository README](https://github.com/cal-icor/textbook.data/blob/main/README.md)  

### 2️⃣ **Make a Pull Request in the Data Repository**  

- Submit a pull request in the **data repo**: [Data PRs](https://github.com/cal-icor/textbook.data/pulls)  
- **Link your data pull request in your notebook pull request** to provide visibility.  

### 3️⃣ **Update Your Notebook to Reference External Data Files**  

Modify your notebook so it properly accesses the uploaded data files via URLs instead of local paths.  

#### 📂 **For CSV, JSON, and Other Data Files**  

**Before:**  

```python
pd.read_csv("iris.csv")
```

**After:**  

```python
pd.read_csv("https://cal-icor.github.io/textbook.data/<YOUR_COURSE_NAME>/iris.csv")
```

#### 🐍 **For Local Python Scripts**  

If your notebook imports a local Python file, update it to load from the data repository.  

**Before:**  

```python
import data101grader  # Assuming data101grader.py exists locally
```

**After:**  

```python
import httpimport
with httpimport.remote_repo("https://cal-icor.github.io/textbook.data/<YOUR_COURSE_NAME>"):
    import data101grader
```

### 4️⃣ **Ensure Your Notebook Runs Without Errors**  

- Execute the entire notebook and confirm that all cells run successfully.  
- Fix any broken links, incorrect imports, or missing dependencies.  

### 5️⃣ **Update `requirements.txt`**  

If your notebook requires additional Python packages, **add them to** [`requirements.txt`](https://github.com/cal-icor/textbook/blob/main/requirements.txt).  

### 6️⃣ **Update the Table of Contents (`myst.yml`)**  

The table of contents is defined in [`myst.yml`](https://github.com/cal-icor/textbook/blob/main/myst.yml) under `project.toc`. Update it to include your new notebook.  

#### 📌 **If the Subject Already Exists**  

- Find the subject block in `project.toc` (each has `title:` and `children:`).  
- Add your notebook under that section’s `children` list: `- file: modules/<YOUR_NOTEBOOK>.ipynb`.  

**Example: Adding a new ESPM notebook**  

```yaml
- title: Environmental Science, Policy, and Management
  children:
    - file: modules/biodiversity.ipynb
    - file: modules/espm-regression.ipynb
    - file: modules/<your-notebook>.ipynb
```

#### 📌 **If the Subject Does Not Exist**  

- Add a new section at the end of `project.toc` in `myst.yml`.  
- Use `title:` for the section name and `children:` with `- file: modules/<NOTEBOOK>.ipynb` for each notebook.  

**Example: Adding a new subject (e.g., Data Ethics)**  

```yaml
- title: Data Ethics
  children:
    - file: modules/data-ethics-intro.ipynb
    - file: modules/fairness-in-ml.ipynb
```

---

## 🤝 Contribution Guidelines  

Want to add a new notebook? Follow these steps:  

1. **Fork** this repository.  
2. **Clone** the forked repo to your local machine.  
3. **Create a new branch** named after your course (`git checkout -b <YOUR_COURSE_NAME>`).  
4. **Make your changes** (update your notebook, fix errors, update `myst.yml`, etc.).  
5. **Commit & Push** your changes (`git push origin <YOUR_BRANCH_NAME>`).  
6. **Open a Pull Request** (PR) in this repository.  
7. **Link your data PR** from the [data repo](https://github.com/cal-icor/textbook.data/pulls).  
8. Wait for review and approval before merging. 🚀  

---

## 📝 Example Pull Request Format  

When submitting a PR, include the following details:  

### 📌 Summary  

- Added a new notebook: `<NOTEBOOK_NAME>.ipynb`  
- Updated `myst.yml` to include the notebook  
- Updated `requirements.txt` to include new dependencies  
- Linked data PR: [Data PR #123](https://github.com/cal-icor/textbook.data/pull/123)  

### ✅ Checklist  

- [ ] My notebook runs without errors  
- [ ] All external file references use URLs  
- [ ] Updated `myst.yml`  
- [ ] Updated `requirements.txt` (if necessary)  
- [ ] Linked the corresponding data PR  

---

## 🛠 Common Issues & Troubleshooting  

🔹 **Issue: Notebook fails to load data**  
✅ **Solution:** Ensure that the file URL is correct and formatted as:  

```python
pd.read_csv("https://cal-icor.github.io/textbook.data/<YOUR_COURSE_NAME>/your_file.csv")
```

🔹 **Issue: ImportError for a local Python file**  
✅ **Solution:** Use `httpimport` to import files from the data repo. Example: 

```python
import httpimport
with httpimport.remote_repo("https://cal-icor.github.io/textbook.data/<YOUR_COURSE_NAME>"):
    import my_script
```

🔹 **Issue: Missing Packages When Running the Notebook**  
✅ **Solution:** Add required packages to `requirements.txt` and ensure it's up to date.  

🔹 **Issue: Notebook Not Listed in Table of Contents**  
✅ **Solution:** Double-check `myst.yml` (under `project.toc`) and make sure your notebook is added under the correct subject’s `children` list with `file: modules/<your-notebook>.ipynb`.  

---

## 📜 License  

This repository is licensed under the MIT License. See [LICENSE](LICENSE) for details.  

---

## 🎯 Final Checklist Before Submitting Your Pull Request  

✅ **Linked your data PR in your notebook PR**  
✅ **Updated all data references to use URLs**  
✅ **Ensured the notebook runs without errors**  
✅ **Updated `requirements.txt` if needed**  
✅ **Updated `myst.yml` to include the new notebook(s)**  

---

## 🛠 Need Help?

If you encounter any issues, feel free to ask for help by opening an issue or reaching out to jonathanferrari AT berkeley.edu.


📌 **Reminder:** PRs that do not follow these guidelines may be rejected.  

