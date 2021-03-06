Command-Line Interface
======================

.. module:: glimpse.glab.cli

The glab command provides a high-level interface to run experiments from the
command line. The command-line interface to the GLAB API can be run as follows.

.. code-block:: bash

   $ glab [ARGUMENTS]


.. _glab-path-note:

.. note::

  The location of the `glab` script is controlled by the `pip` installer. On
  Linux, this location is ``$HOME/.local/bin``. If `glab` can not be found,
  try adding this location to your system path.

  As an alternative, this script can always be run as:

  .. code-block:: sh

     $ python -m glimpse.glab.cli [ARGUMENTS]


Arguments
---------

Arguments to this command are described below, where they are grouped
depending on their functionality.


**Corpus arguments**

  -C DIR, --corpus-subdir=DIR         Use DIR as a corpus sub-directory (use -C repeatedly to specify all sub-directories in corpus)
  -c DIR, --corpus=DIR                Use DIR as corpus directory.
  -b, --balance                       Choose equal number of images per class
  --corpus-name=NAME                  Choose the sample corpus `NAME`.


**Extraction arguments**

  -A, --save-all              Save activity for all layers, rather than just the layers from which features are extracted.
  -N, --no-activity           Do not compute activity model activity for each
                              image (implies no classifier). This can be used to
                              learn prototypes without immediately evaluating
                              them.
  -n NUM, --num-prototypes=NUM        Generate `NUM` S2 prototypes (default: 10)
  -O FILE, --options=FILE             Read model options from `FILE`


**Prototype learning arguments**

  --base-weight=w                     Add a weight of `w` for all training patches (default: 0.0)
  --high=VALUE                        Use VALUE as the high end of uniform distribution for random prototypes (default: 1.0)
  --low=VALUE                         Use `VALUE` as the low end of uniform distribution for random prototypes (default: 0.0)
  --masks=DIR                         Set mask directory for object_mask_wkmeans to `DIR` (default: '')
  -P FILE, --prototypes=FILE          Read S2 prototypes from `FILE` (overrides -p)
  -p ALG, --prototype-algorithm=ALG   Use prototype learning algorithm `ALG` (one of: histogram, ica, imprint, kmeans, kmedoids, meta_feature_wkmeans, nearest_kmeans, nmf, normal, object_mask_wkmeans, pca, shuffle, sparse_pca, uniform)
  --regr-samples=NUM                  Sample `NUM` patches  when training regression model for meta_feature_wkmeans (default: 0)
  --samples=NUM                       Sample `NUM` training patches for S2 prototype learning (default: 0)


**Evaluation arguments**

  -E, --evaluate              Train and test a classifier
  -f NUM, --num-folds=NUM             Use `NUM` folds for cross-validation (default: 10)
  -H, --hist-features         Use histograms (accumulated over space and scale)
                              for each feature band (requires spatial features,
                              such as C1)
  -l LAYER, --layer=LAYER               Extract image features from model layer LAYER (default: 'C2')
  -L ALG, --learner=ALG                 Use learning algorithm `ALG` to use for classification. `ALG` can be a Python expression, or one of 'svm' or 'logreg') (default: 'svm')
  --predictions                         Print the classifier's per-image predictions.
  -S FUNC, --score-function=FUNC        Use scoring function `FUNC` for classifier evaluation (one of: `accuracy` or `auc`) (default: 'accuracy')
  -T SIZE, --train-size=SIZE            Set the size of the training set to `SIZE` (number of instances or fraction of total)
  -x, --cross-validate                  Compute test accuracy via (10x10-way)cross-validation instead of fixed training/testing split


**Other arguments**

  --command=cmd                         Execute `cmd` after running the experiment (but before results are saved
  -h, --help                            Print this help and exit
  -i FILE, --input=FILE                 Read initial experiment data from FILE.
  -o FILE, --output=FILE                Store results to `FILE`
  -t TYPE, --pool-type=TYPE             Set the worker pool type to `TYPE` (one of: s, singlecore, m, multicore, c, cluster)
  -v, --verbose                         Enable verbose logging


Examples
--------

Evaluate with a logistic regression classifier using 100 C2 features based on imprinted prototypes.

.. code-block:: bash

    $ glab -v -n 100 -p imprint --corpus-name easy --learner=logreg -E --predictions
    INFO:root:Reading class sub-directories from: easy
    INFO:root:Reading images from class directories: ['easy/circle', 'easy/cross']
    INFO:root:Using pool: MulticorePool
    INFO:root:Learning 2 prototypes at 1 sizes from 4 images by imprinting
    Time: 0:00:01   |##################################################|   Speed:  3.10  unit/s
    INFO:root:Learning prototypes took 1.294s
    INFO:root:Computing C2 activation maps for 8 images
    Time: 0:00:02   |##################################################|   Speed:  3.32  unit/s
    INFO:root:Computing activation maps took 1.712s
    INFO:root:Evaluating classifier on fixed train/test split on 10 images using 2 features from layer(s): C2
    INFO:root:Training on 4 images took 0.003s
    INFO:root:Classifier is Pipeline(learner=LogisticRegression([...OUTPUT REMOVED...]))
    INFO:root:Classifier accuracy on training set is 1.000000
    INFO:root:Scoring on training set (4 images) took 0.001s
    INFO:root:Scoring on testing set (6 images) took 0.001s
    INFO:root:Classifier accuracy on test set is 0.833333

    Classifier Predictions
    ======================

    Each line gives the true and predicted labels (in that order) for an image in the corpus.

    Training Set Predictions
    ------------------------
    easy/circle/4_circle_r-25s0.834y-14x-06_white.jpg circle circle
    easy/circle/5_circle_r-05s0.744y011x-16_white.jpg circle circle
    easy/cross/2_cross_r015s0.473y025x-12_white.jpg cross cross
    easy/cross/3_cross_r000s0.496y022x-22_white.jpg cross cross

    Test Set Predictions
    --------------------
    easy/circle/4_circle_r-25s0.834y-14x-06_white.jpg circle circle
    easy/circle/5_circle_r-05s0.744y011x-16_white.jpg circle circle
    easy/cross/2_cross_r015s0.473y025x-12_white.jpg cross circle
    easy/cross/3_cross_r000s0.496y022x-22_white.jpg cross cross

