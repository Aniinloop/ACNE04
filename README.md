Audit and Reanalysis of the ACNE04 Severity Benchmark

Every number in the paper is produced by one of the notebooks below. The table maps each claim to the notebook that generates it, so a specific figure can be checked without running everything.

1. Data

The ACNE04 images are not redistributed here. Obtain them from the original release (Wu et al., ICCV 2019) and place them so the directory looks like:

data/
  ACNE04/
    Classification/
      JPEGImages/          # 1,457 .jpg files
      NNEW_trainval_*.txt  # annotation files
      NNEW_test_*.txt

Then set DATA_ROOT at the top of each notebook to the path of ACNE04/. No other path needs changing.

2. Environment
python >= 3.10
torch
torchvision
numpy
pandas
scipy
scikit-learn
pillow
imagehash

The audit notebooks run on CPU in a few minutes. The training notebooks were run on a single GPU via Kaggle, each arm takes roughly 40 minutes.

3. Notebook map

Audit, no training required, CPU only:

Paper section	Notebook	Produces
II-B1 Label consistency	acne-pynb (2).ipynb	grade == rule(count) check; exhaustive cut-point search
II-B2 Image uniqueness	verifyduplicates-ipynb (2).ipynb	duplicate groups, cross-grade contradictions
II-B3 Split integrity	foldaudit-ipynb.ipynb	perceptual-hash grouping, leakage in released vs random splits
II-B4 Annotation reliability	repeatannotation-ipynb.ipynb	count deltas within and across grade boundaries

Experiments, GPU:

Paper section	Notebook	Produces
II-C Arms A–D	consequencev2-ipynb (2).ipynb	Table I, per-grade error rates, threshold sweep
Confidence analysis	traingraderv2-ipynb (1).ipynb	AUROC, AUGRC, likelihood-ratio test

Output cells are kept in each notebook, so the reported figures can be read without re-running anything.

4. Expected output

Use these to confirm a successful run. The audit figures are exact and should reproduce bit for bit; the training figures will not, see Section 5.

Audit:

grade == rule(count)                       1457 / 1457
cut-point sets with 0 errors               (5,20,50) and (5,20,42)
byte-identical duplicate groups            38   (all size 2, 76 files)
file_md5 == pixel_md5                      38 / 38
cross-grade contradictions                 9    (all between adjacent grades)
distinct photographs                       1419
leakage, released folds                    6.3 %
leakage, random splits                     6.5 %
duplicate pairs differing in count         26 / 38

Experiments, pooled out-of-fold:

Arm A   CORAL,   random split, duplicates kept     QWK 0.8523   acc 0.7625
Arm B   CORAL,   subject split, duplicates removed QWK 0.8602   acc 0.7639
Arm C   Poisson, subject split, duplicates removed QWK 0.8364   acc 0.7491
Arm D   softmax, subject split, duplicates removed QWK 0.8492   acc 0.7667
5. Reproducibility and expected variance

Seeds are fixed in every notebook, and the audit results are deterministic.

Training results are not bit-reproducible across hardware. Two runs of the same configuration under an identical seed, differing only in training length, gave QWK 0.8492 and 0.8351. A reproduction landing anywhere in that range is a successful one. Differences below roughly 0.014 QWK should not be read as an effect; the paper interprets a comparison only where its paired bootstrap interval excludes zero.

6. Licence and citation

Code in this repository: MIT.

ACNE04 remains under the licence of its original release and is not redistributed here.
