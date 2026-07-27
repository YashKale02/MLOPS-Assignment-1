# Assignment 1: Dataset Versioning using DVC

## 📌 Project Overview
This project demonstrates dataset version control using **DVC (Data Version Control)** integrated with **Git** for MLOps. It tracks multiple versions of a student dataset (`student.csv`), demonstrates how to restore previous data versions, and configures local remote storage for dataset backups.

* **GitHub Repository:** [YashKale02/MLOPS-Assignment-1](https://github.com/YashKale02/MLOPS-Assignment-1)

---

## 🎯 Learning Objectives
* Understand the necessity of dataset versioning in MLOps workflows.
* Initialize and configure DVC in a Git-tracked project repository.
* Track dataset changes using `.dvc` tracking files.
* Revert to previous versions of datasets using `dvc checkout`.
* Set up DVC remote storage (`dvc remote add`) and push data (`dvc push`).

---

## 📁 Project Structure
```text
DatasetVersioning/
│
├── .dvc/                   # DVC configuration and metadata
├── data/
│   ├── student.csv         # Raw dataset (tracked by DVC, ignored by Git)
│   ├── student.csv.dvc     # DVC pointer/metadata file (tracked by Git)
│   └── .gitignore          # Ignores student.csv in Git
├── .dvcignore             # Files ignored by DVC
├── .gitignore              # Files ignored by Git
└── README.md               # Project documentation
```

---

## 🚀 Step-by-Step Execution Guide

### Step 1: Initialize Git and DVC
```bash
git init
dvc init
git add .
git commit -m "Initialize Git and DVC for DatasetVersioning"
```

### Step 2: Create Initial Dataset (Version 1)
Created dataset at `data/student.csv`:
```csv
RollNo,Name,Marks
1,Amit,80
2,Priya,91
3,Rahul,75
4,Sneha,88
```

### Step 3: Track Dataset using DVC
```bash
dvc add data/student.csv
git add data/student.csv.dvc data/.gitignore
git commit -m "Track dataset using DVC (Version 1)"
git push -u origin main
```

### Step 4: Modify Dataset (Version 2)
Updated `data/student.csv` (removed record 1, updated record 3, added records 5, 6, 7):
```csv
RollNo,Name,Marks
2,Priya,91
3,Rahul,82
4,Sneha,88
5,Neha,77
6,Karan,85
7,Ananya,92
```

Track and commit Version 2:
```bash
dvc add data/student.csv
git add data/student.csv.dvc
git commit -m "Dataset Version 2"
git push
```

### Step 5: Restore Previous Dataset Version (Version 1)
To restore Version 1 from Git commit history:
```bash
git checkout 0c1cd8a -- data/student.csv.dvc
dvc checkout
```

### Step 6: Configure Remote Storage & Push
Configure local remote storage and push dataset cache:
```bash
mkdir ../dvc-storage
dvc remote add -d localstorage ../dvc-storage
git add .dvc/config
git commit -m "Configure DVC remote"
git push

# Push dataset to remote storage
dvc push
```

---

## 📜 Git Commit History
```text
721fb7e Configure DVC remote
794145c Dataset Version 2
0c1cd8a Track dataset using DVC (Version 1)
41d92f5 Initialize Git and DVC for DatasetVersioning
```

---

## 💡 Reflection Questions & Answers

1. **Why is Git alone not suitable for versioning datasets?**
   > Git is designed for text-based code. Tracking large binary datasets directly in Git rapidly inflates repository size and degrades performance. DVC separates data management by storing metadata `.dvc` files in Git while storing actual datasets in dedicated remote storage.

2. **What is the purpose of the `.dvc` file?**
   > The `.dvc` file (e.g., `student.csv.dvc`) contains metadata such as the file path, size, and md5 hash of the dataset, allowing Git to track data versions without storing the large data files directly.

3. **Explain the difference between `git add` and `dvc add`.**
   > `dvc add <file>` calculates the dataset's md5 hash, caches the file in `.dvc/cache`, generates a `.dvc` tracking pointer file, and adds the raw data file to `.gitignore`.  
   > `git add <file>` stages text files or `.dvc` tracking files into Git for version control.

4. **What happens when `dvc checkout` is executed?**
   > `dvc checkout` restores the exact version of the dataset files in the workspace matching the md5 hashes recorded in the current `.dvc` tracking files.

5. **How does DVC improve reproducibility in ML projects?**
   > DVC couples dataset versions directly to Git commit hashes. Checking out any Git commit and running `dvc checkout` recreates the exact data state used during that training run, ensuring 100% reproducibility.
