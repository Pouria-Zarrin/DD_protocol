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

---

## 5. Calculate Morgan Fingerprints

<pre>
# Create an output folder for Morgan fingerprints
mkdir -p morgan_test_1000

# Run the fingerprint calculation script
python utilities/morgan_fp.py --smile_folder_path smiles_library --folder_name morgan_test_1000 --tot_process 1

# (Optional) Rename the output file if you want a specific name
mv morgan_test_1000/small_test_1000.txt morgan_test_1000/morgan_test_output
</pre>

## Phase 1: Random Sampling of Molecules

In this phase, molecules are randomly sampled from the database to generate or augment the training set. During the first iteration, this phase also samples molecules for the validation and test sets.

### Steps:

1. Create a project folder (e.g., `dd_project/iteration_1`).

2. Run the following commands sequentially with the conda environment activated.

<pre>
python scripts_1/molecular_file_count_updated.py \
  --project_name dd_project \
  --n_iteration 1 \
  --data_directory morgan_test_1000 \
  --tot_process 1 \
  --tot_sampling 1000
</pre>

This script determines the number of molecules to be sampled from each file of the database. The sample sizes (per million) are saved in a CSV file in the `morgan_test_1000` directory.

<pre>
python scripts_1/sampling.py \
  --project_name dd_project \
  --file_path dd_project/iteration_1 \
  --n_iteration 1 \
  --data_director morgan_test_1000 \
  --tot_process 1 \
  --train_size 800 \
  --val_size 100
</pre>

This script randomly samples molecules for the training (800 molecules) and validation (100 molecules) sets. *Note:* The test set is not explicitly set here; it may be handled internally or in subsequent iterations.

<pre>
python scripts_1/sanity_check.py \
  --project_name dd_project \
  --file_path dd_project/iteration_1 \
  --n_iteration 1
</pre>

This script removes any overlaps between training and validation sets.

<pre>
python scripts_1/extracting_morgan.py \
  --project_name dd_project \
  --file_path dd_project/iteration_1 \
  --n_iteration 1 \
  --morgan_directory morgan_test_1000 \
  --tot_process 1
</pre>

Extracts Morgan fingerprints for the randomly sampled molecules and saves them inside a `morgan` folder in the current iteration directory.

<pre>
python scripts_1/extracting_smiles.py \
  --project_name dd_project \
  --file_path dd_project/iteration_1 \
  --n_iteration 1 \
  --smile_directory smiles_library/small_test_1000.txt \
  --tot_process 1
</pre>

Extracts SMILES for the randomly sampled molecules and saves them inside a `smiles` folder in the current iteration directory.

---

**Important Notes:**

- For the first iteration, `--data_directory` (or `--data_director`) should point to the directory containing Morgan fingerprints of the library (`morgan_test_1000` in this case).

- For subsequent iterations, this should be changed to the `morgan_1024_predictions` folder from the previous iteration to progressively sample better-scoring subsets.

- Adjust `--tot_process` to the number of CPUs you want to use for multiprocessing.

- Training, validation, and testing sizes should sum up to the total molecules you want to sample per iteration.



