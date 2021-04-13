# Lerna
Automated tuning of EC tools

The workflow is simplified as follows:
1) The uncorrected reads are converted to torch tensors on which the Language model is trained
2) The reads are corrected (for multiple 'k' values) using a standard Error correcting tool, for example, LoRDEC
3) The corrected reads are converted to tensors on which the Language model evaluation is done
4) Simulated annealing is run on the perplexity values generated by evaluation where the 'k' value corresponding to minimum perplexity is chosen

Requirements:
--PyTorch

File "pbsim_10k.fasta" contains 10000 uncorrected reads generated using a PacBio simulator on the E-coli genome.
File "create_train_tensors.py" processes the uncorrected reads to generate torch tensors for training
  Usage: python create_train_tensors.py --w --file
    where 'w' is the word length and 'file' contains the uncorrected reads in fasta format
  Example: python create_train_tensors.py --4 --pbsim_10k.fasta
  This generates and saves two tensors, "train_data.pt" and "train_targets.pt" which will be used by the Language model for training
File "train.py" trains the language model for 200 epochs and saves it as "LM.pkl". It also generates a log file as "log.txt"
  Usage: python train.py
We then run an Error correcting tool LoRDEC for multiple values of 'k' to generate the corrected reads which we have uploaded here with subscripts 'cor.fasta'
File "create_test_tensors.py" processes the corrected reads to generate torch tensors for testing
  Usage: python create_test_tensors.py --w
  By default, it does this for k = 15, 17, 19, 21, 23, 25, 27, 31, 37, 45, 55, 67, 75, 81, 89, 97. This can be modified by editing the k_vals list in the code
File "test.py" evaluates the Language model on the test data
  Usage: python test.py --data_file --targets_file
  Example: python test.py --data_k27.pt --targets_k27.pt
  
