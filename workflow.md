# **workflow notes for running dd protocol**

## **installed dependencies**

<pre> conda install rdkit matplotlib scikit-learn pandas numpy matplotlib </pre>
<pre> pip install tensorflow>=1.14.0 keras </pre>

---

## **downloaded a chunk of the main library**

<pre> wget https://files.docking.org/zinc20-ML/smiles/ZINC20_smiles_chunk_1.tar.gz </pre>

---

## 1. Extract the ZINC20 Chunk

<pre>
# Download the chunk (if not already downloaded)
wget https://files.docking.org/zinc20-ML/smiles/ZINC20_smiles_chunk_1.tar.gz

# Extract the archive
tar -xzvf ZINC20_smiles_chunk_1.tar.gz

# Go into the extracted directory (if needed)
cd ZINC20_smiles_chunk_1
</pre>

---

## 2. Check Available Files

<pre>
ls
# Example output:
# smiles_all_00.txt  smiles_all_01.txt  smiles_all_02.txt  ...
</pre>

---

## 3. Sample 1000 Molecules for a Test File

**Option A: Take the first 1000 molecules**
<pre>
head -n 1000 smiles_all_00.txt > ../../smiles/small_test_1000.txt
</pre>

**Option B: Take a random sample of 1000 molecules**
<pre>
shuf smiles_all_00.txt | head -n 1000 > ../../smiles/small_test_1000.txt
</pre>

---

## 4. Verify the Test File

<pre>
wc -l ../../smiles/small_test_1000.txt
# Output should be: 1000 ../../smiles/small_test_1000.txt
</pre>
