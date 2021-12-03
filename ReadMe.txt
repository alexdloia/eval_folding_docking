Hunter Liu & Alex Loia
BIOE/CS/CME/BIOPHYS/BIOMEDIN 279 Fall 2021
Prof. Ron Dror
3 December 2021
Evaluating Folding & Docking Programs Using COVID-19 Antibody-Spike Protein Interactions


General Flow for Folding and Docking
------------------------------------
1. Take observed structures as structure baseline and relative positions as docking baseline
2. Run antibody and RBD sequences through folding independently 
3. Run structure baseline through docking
4. Evaluate quality of docking compared to docking baseline with RMSD (PyMOl `align`)
5. Run folded structures through docking
     - Compare to docking baseline and structure baseline docking

(Extra) Run folding programs on all three sequences (heavy chain, light chain, RBD)
     - Treat result like docking and evaluate as such


Cleaning and Preparing PDBs for Docking with `pdb_tools`
--------------------------------------------------------
HADDOCK requires PDBs to have non-overlapping residue numbers between chains and also recommends 
the removal of heteroatoms. Thus, after installing pdb_tools (`pip install pdbtools`), we ran `pdb_reres` 
on the antibody and spike files to be docked to renumber their chains sequentially and ran `pdb_delhetatm` 
to remove the HETATM records that could interfere in the docking process. We also used combinations of 
`pdb_setchain` and `pdb_selchain` to construct PDBs with different selected chains from the original RCSB 
PDBS. We also used `pdb_tidy` and `pdb_validate` before uploading the PDBS to the HADDOCK server to ensure
the required format restraints are satisifed.

Installing and Running ColabFold on Amazon EC2 Instance
---------------------------------------------------------------
1. Create EC2 Instance (g4dn.xlarge instance in this case with Amazon Deep Learning Ubuntu)
2. Create and activate Anaconda environment
3. Install ColabFold:
    - pip install "colabfold[alphafold] @ git+https://github.com/sokrypton/ColabFold"
    - pip install --upgrade "jax[cuda]" -f https://storage.googleapis.com/jax-releases/jax_releases.html  # Note: wheels only available on linux.
4. Create directories with FASTA sequences for antibody and RBD (separately)
5. Run `colabfold_batch <directory_with_fasta_files> <result_dir>`
6. Collect generated PDBs from result directories using `scp` to local computer
7. Analyze with PyMOL

Retrieving Antigen Binding Regions (ABRs) for Antibodies
-------------------------------------------------
1. Navigate to Paratome (https://ofranservices.biu.ac.il/site/services/paratome)
2. Preprocess PDBs as required (for consistent numbering for instance)
3. Upload antibody PDB
4. Open output file and collect sequence data for ABRs
5. Input ABRs as binding regions into docking programs