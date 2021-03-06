Comparison of Hi-C experiments
==============================

Comparison between replicates
-----------------------------

Load previous data
~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    from pytadbit.mapping.analyze import eig_correlate_matrices, correlate_matrices, get_reproducibility
    from pytadbit.parsers.hic_parser import load_hic_data_from_bam
    from matplotlib import pyplot as plt

.. code:: ipython3

    base_path = 'results/fragment/{0}_{1}/03_filtering/valid_reads12_{0}_{1}.bam'
    bias_path = 'results/fragment/{0}_{1}/03_filtering/valid_reads12_{0}_{1}_Vanilla_{2}kb.biases'

Mouse B cell
~~~~~~~~~~~~

We load the hic_data object from the BAM file

.. code:: ipython3

    reso = 100000
    cel1 = 'mouse_B'
    cel2 = 'mouse_PSC'
    rep1  = 'rep1'
    rep2  = 'rep2'

.. code:: ipython3

    hic_data1 = load_hic_data_from_bam(base_path.format(cel1, rep1),
                                      resolution=reso,
                                      biases=bias_path.format(cel1, rep1, reso // 1000),
                                      ncpus=8)
    hic_data2 = load_hic_data_from_bam(base_path.format(cel1, rep2),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep2, reso // 1000),
                                      ncpus=8)


.. ansi-block::

    
      (Matrix size 27269x27269)                                                    [2020-02-06 13:29:05]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 13:29:05]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 13:31:43]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
    
      (Matrix size 27269x27269)                                                    [2020-02-06 13:33:21]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 13:33:21]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 13:35:47]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    


We compare the interactions of the two Hi-C matrices at a given
distance.

The Spearman rank correlation of the matrix diagonals
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the plot we represent the Spearman rank correlation of the diagonals
of the matrices starting from the main diagonal until the diagonal at
10Mbp.

.. code:: ipython3

    ## this part is to "tune" the plot ##
    plt.figure(figsize=(9, 6))
    axe = plt.subplot()
    axe.grid()
    axe.set_xticks(range(0, 55, 5))
    axe.set_xticklabels(['%d Mb' % int(i * 0.2) if i else '' for i in range(0, 55, 5)], rotation=-45)
    #####################################
    
    spearmans, dists, scc, std = correlate_matrices(hic_data1, hic_data2, max_dist=50, show=True, axe=axe)



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_12_0.png


.. code:: ipython3

    ## this part is to "tune" the plot ##
    plt.figure(figsize=(9, 6))
    axe = plt.subplot()
    axe.grid()
    axe.set_xticks(range(0, 55, 5))
    axe.set_xticklabels(['%d Mb' % int(i * 0.2) if i else '' for i in range(0, 55, 5)], rotation=-45)
    #####################################
    
    spearmans, dists, scc, std = correlate_matrices(hic_data1, hic_data2, max_dist=50, show=True, axe=axe, normalized=True)



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_13_0.png


The SCC score as in HiCrep (see https://doi.org/10.1101/gr.220640.117)
is also computed. The value of SCC ranges from −1 to 1 and can be
interpreted in a way similar to the standard correlation

.. code:: ipython3

    print('SCC score: %.4f (+- %.7f)' % (scc, std))


.. ansi-block::

    SCC score: 0.5482 (+- 0.0075563)


.. code:: ipython3

    reso = 1000000
    hic_data1 = hic_data2 = None
    hic_data1 = load_hic_data_from_bam(base_path.format(cel1, rep1),
                                      resolution=reso,
                                      biases=bias_path.format(cel1, rep1, reso // 1000),
                                      ncpus=8)
    hic_data2 = load_hic_data_from_bam(base_path.format(cel1, rep2),
                                      resolution=reso,
                                      biases=bias_path.format(cel1, rep2, reso // 1000),
                                      ncpus=8)


.. ansi-block::

    
      (Matrix size 2738x2738)                                                      [2020-02-06 14:19:49]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 14:19:50]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 14:20:30]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
    
      (Matrix size 2738x2738)                                                      [2020-02-06 14:20:45]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 14:20:45]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 14:21:20]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    


The correlation of the eigenvectors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Since the eigenvectors of a matrix capture its internal correlations
[26], two matrices with highly correlation of eigenvectors are
considered to have similar structure.

In this case we limit the computation to the first 6 eigenvectors

.. code:: ipython3

    corrs = eig_correlate_matrices(hic_data1, hic_data2, show=True, aspect='auto', normalized=True)
    
    for cor in corrs:
        print(' '.join(['%5.3f' % (c) for c in cor]) + '\n')



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_19_0.png


.. ansi-block::

    0.976 0.191 0.032 0.018 0.021 0.003
    
    0.192 0.956 0.194 0.003 0.011 0.013
    
    0.006 0.197 0.975 0.015 0.011 0.003
    
    0.016 0.002 0.015 0.993 0.031 0.016
    
    0.019 0.017 0.006 0.034 0.989 0.039
    
    0.000 0.010 0.007 0.018 0.045 0.931
    


The reproducibility score (Q)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Computed as in HiC-spector
(https://doi.org/10.1093/bioinformatics/btx152), it is also based on
comparing eigenvectors. The reproducibility score ranges from 0 (low
similarity) to 1 (identity).

.. code:: ipython3

    reprod = get_reproducibility(hic_data1, hic_data2, num_evec=20, normalized=True, verbose=False)
    print('Reproducibility score: %.4f' % (reprod))


.. ansi-block::

    Reproducibility score: 0.8629


Mouse iPS cell
~~~~~~~~~~~~~~

We load the hic_data object from the BAM file

.. code:: ipython3

    reso = 100000
    hic_data1 = hic_data2 = None

.. code:: ipython3

    hic_data1 = load_hic_data_from_bam(base_path.format(cel2, rep1),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep1, reso // 1000),
                                      ncpus=8)
    hic_data2 = load_hic_data_from_bam(base_path.format(cel2, rep2),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep2, reso // 1000),
                                      ncpus=8)


.. ansi-block::

    
      (Matrix size 27269x27269)                                                    [2020-02-06 14:23:03]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 14:23:03]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 14:23:47]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
    
      (Matrix size 27269x27269)                                                    [2020-02-06 14:24:57]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 14:24:57]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 14:25:53]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    


We compare the interactions of the two Hi-C matrices at a given
distance.

The Spearman rank correlation of the matrix diagonals
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the plot we represent the Spearman rank correlation of the diagonals
of the matrices starting from the main diagonal until the diagonal at
10Mbp.

.. code:: ipython3

    ## this part is to "tune" the plot ##
    plt.figure(figsize=(9, 6))
    axe = plt.subplot()
    axe.grid()
    axe.set_xticks(range(0, 55, 5))
    axe.set_xticklabels(['%d Mb' % int(i * 0.2) if i else '' for i in range(0, 55, 5)], rotation=-45)
    #####################################
    
    spearmans, dists, scc, std = correlate_matrices(hic_data1, hic_data2, max_dist=50, show=True, axe=axe)



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_30_0.png


The SCC score as in HiCrep (see https://doi.org/10.1101/gr.220640.117)
is also computed. The value of SCC ranges from −1 to 1 and can be
interpreted in a way similar to the standard correlation

.. code:: ipython3

    print('SCC score: %.4f (+- %.7f)' % (scc, std))


.. ansi-block::

    SCC score: 0.6448 (+- 0.0277123)


.. code:: ipython3

    reso = 1000000
    hic_data1 = hic_data2 = None
    hic_data1 = load_hic_data_from_bam(base_path.format(cel2, rep1),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep1, reso // 1000),
                                      ncpus=8)
    hic_data2 = load_hic_data_from_bam(base_path.format(cel2, rep2),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep2, reso // 1000),
                                      ncpus=8)


.. ansi-block::

    
      (Matrix size 2738x2738)                                                      [2020-02-06 14:27:10]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 14:27:10]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 14:27:44]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
    
      (Matrix size 2738x2738)                                                      [2020-02-06 14:27:57]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 14:27:57]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 14:29:00]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    


The correlation of the eigenvectors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Since the eigenvectors of a matrix capture its internal correlations
[26], two matrices with highly correlation of eigenvectors are
considered to have similar structure.

In this case we limit the computation to the first 6 eigenvectors

.. code:: ipython3

    corrs = eig_correlate_matrices(hic_data1, hic_data2, show=True, aspect='auto', normalized=True)
    
    for cor in corrs:
        print(' '.join(['%5.3f' % (c) for c in cor]) + '\n')



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_36_0.png


.. ansi-block::

    0.989 0.088 0.002 0.005 0.005 0.002
    
    0.094 0.983 0.073 0.010 0.006 0.008
    
    0.009 0.071 0.987 0.056 0.034 0.028
    
    0.006 0.014 0.053 0.988 0.025 0.001
    
    0.006 0.007 0.031 0.028 0.985 0.079
    
    0.002 0.008 0.021 0.015 0.081 0.954
    


The reproducibility score (Q)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Computed as in HiC-spector
(https://doi.org/10.1093/bioinformatics/btx152), it is also based on
comparing eigenvectors. The reproducibility score ranges from 0 (low
similarity) to 1 (identity).

.. code:: ipython3

    reprod = get_reproducibility(hic_data1, hic_data2, num_evec=20, normalized=True, verbose=False)
    print('Reproducibility score: %.4f' % (reprod))


.. ansi-block::

    Reproducibility score: 0.5979


Comparison between cell types
-----------------------------

Replicate 1
~~~~~~~~~~~

.. code:: ipython3

    reso = 100000
    hic_data1 = hic_data2 = None
    hic_data1 = load_hic_data_from_bam(base_path.format(cel1, rep1),
                                      resolution=reso,
                                      biases=bias_path.format(cel1, rep1, reso // 1000),
                                      ncpus=8)
    hic_data2 = load_hic_data_from_bam(base_path.format(cel2, rep1),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep1, reso // 1000),
                                      ncpus=8)


.. ansi-block::

    
      (Matrix size 27269x27269)                                                    [2020-02-06 14:30:21]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 14:30:21]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 14:31:06]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
    
      (Matrix size 27269x27269)                                                    [2020-02-06 14:32:51]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 14:32:51]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 14:33:27]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    


.. code:: ipython3

    ## this part is to "tune" the plot ##
    plt.figure(figsize=(9, 6))
    axe = plt.subplot()
    axe.grid()
    axe.set_xticks(range(0, 55, 5))
    axe.set_xticklabels(['%d Mb' % int(i * 0.2) if i else '' for i in range(0, 55, 5)], rotation=-45)
    #####################################
    
    spearmans, dists, scc, std = correlate_matrices(hic_data1, hic_data2, max_dist=50, show=True, axe=axe)



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_43_0.png


We expect a lower SCC score between different cell types

.. code:: ipython3

    print('SCC score: %.4f (+- %.7f)' % (scc, std))


.. ansi-block::

    SCC score: 0.4770 (+- 0.0197731)


.. code:: ipython3

    reso = 1000000
    hic_data1 = load_hic_data_from_bam(base_path.format(cel1, rep1),
                                      resolution=reso,
                                      biases=bias_path.format(cel1, rep1, reso // 1000),
                                      ncpus=8)
    hic_data2 = load_hic_data_from_bam(base_path.format(cel2, rep1),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep1, reso // 1000),
                                      ncpus=8)


.. ansi-block::

    
      (Matrix size 2738x2738)                                                      [2020-02-06 14:34:57]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 14:34:58]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 14:35:25]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
    
      (Matrix size 2738x2738)                                                      [2020-02-06 14:36:09]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 14:36:09]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 14:36:41]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    


.. code:: ipython3

    corrs = eig_correlate_matrices(hic_data1, hic_data2, show=True, aspect='auto', normalized=True)
    
    for cor in corrs:
        print(' '.join(['%5.3f' % (c) for c in cor]) + '\n')



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_47_0.png


.. ansi-block::

    0.875 0.171 0.011 0.048 0.066 0.023
    
    0.251 0.787 0.481 0.072 0.070 0.032
    
    0.117 0.484 0.838 0.063 0.045 0.032
    
    0.013 0.027 0.118 0.970 0.048 0.034
    
    0.003 0.052 0.020 0.059 0.961 0.009
    
    0.013 0.004 0.016 0.016 0.012 0.859
    


.. code:: ipython3

    reprod = get_reproducibility(hic_data1, hic_data2, num_evec=20, normalized=True, verbose=False)
    print('Reproducibility score: %.4f' % (reprod))


.. ansi-block::

    Reproducibility score: 0.2864


Replicate 2
~~~~~~~~~~~

.. code:: ipython3

    reso = 100000
    hic_data1 = hic_data2 = None
    hic_data1 = load_hic_data_from_bam(base_path.format(cel1, rep2),
                                      resolution=reso,
                                      biases=bias_path.format(cel1, rep2, reso // 1000),
                                      ncpus=8)
    hic_data2 = load_hic_data_from_bam(base_path.format(cel2, rep2),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep2, reso // 1000),
                                      ncpus=8)


.. ansi-block::

    
      (Matrix size 27269x27269)                                                    [2020-02-06 14:38:14]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 14:38:14]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 14:39:02]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
    
      (Matrix size 27269x27269)                                                    [2020-02-06 14:40:54]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 14:40:55]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 14:41:44]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    


.. code:: ipython3

    ## this part is to "tune" the plot ##
    plt.figure(figsize=(9, 6))
    axe = plt.subplot()
    axe.grid()
    axe.set_xticks(range(0, 55, 5))
    axe.set_xticklabels(['%d Mb' % int(i * 0.2) if i else '' for i in range(0, 55, 5)], rotation=-45)
    #####################################
    
    spearmans, dists, scc, std = correlate_matrices(hic_data1, hic_data2, max_dist=50, show=True, axe=axe)



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_51_0.png


.. code:: ipython3

    print('SCC score: %.4f (+- %.7f)' % (scc, std))


.. ansi-block::

    SCC score: 0.4696 (+- 0.0185008)


.. code:: ipython3

    reso = 1000000
    hic_data1 = load_hic_data_from_bam(base_path.format(cel1, rep2),
                                      resolution=reso,
                                      biases=bias_path.format(cel1, rep2, reso // 1000),
                                      ncpus=8)
    hic_data2 = load_hic_data_from_bam(base_path.format(cel2, rep2),
                                      resolution=reso,
                                      biases=bias_path.format(cel2, rep2, reso // 1000),
                                      ncpus=8)


.. ansi-block::

    
      (Matrix size 2738x2738)                                                      [2020-02-06 14:43:12]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 14:43:13]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 14:43:43]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
    
      (Matrix size 2738x2738)                                                      [2020-02-06 14:44:30]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 14:44:31]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 14:45:16]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    


.. code:: ipython3

    corrs = eig_correlate_matrices(hic_data1, hic_data2, show=True, aspect='auto', normalized=True)
    
    for cor in corrs:
        print(' '.join(['%5.3f' % (c) for c in cor]) + '\n')



.. image:: ../nbpictures//tutorial_9-Compare_and_merge_Hi-C_experiments_54_0.png


.. ansi-block::

    0.904 0.086 0.018 0.057 0.080 0.028
    
    0.031 0.846 0.445 0.015 0.087 0.033
    
    0.004 0.407 0.855 0.034 0.073 0.089
    
    0.029 0.022 0.058 0.971 0.039 0.030
    
    0.037 0.052 0.015 0.057 0.948 0.086
    
    0.011 0.015 0.022 0.020 0.005 0.776
    


.. code:: ipython3

    reprod = get_reproducibility(hic_data1, hic_data2, num_evec=20, normalized=True, verbose=False)
    print('Reproducibility score: %.4f' % (reprod))


.. ansi-block::

    Reproducibility score: 0.3852


Merge Hi-C experiments
----------------------

Once agreed that experiments are similar, they can be merged.

Here is a simple way to merge valid pairs. Arguably we may want to merge
unfiltered data but the difference would be minimal specially with
non-replicates.

.. code:: ipython3

    from pytadbit.mapping import merge_bams

.. code:: ipython3

    ! mkdir -p results/fragment/mouse_B_both/
    ! mkdir -p results/fragment/mouse_PSC_both/
    ! mkdir -p results/fragment/mouse_B_both/03_filtering/
    ! mkdir -p results/fragment/mouse_PSC_both/03_filtering/

.. code:: ipython3

    cell = 'mouse_B'
    rep1 = 'rep1'
    rep2 = 'rep2'
    
    hic_data1 = 'results/fragment/{0}_{1}/03_filtering/valid_reads12_{0}_{1}.bam'.format(cell, rep1)
    hic_data2 = 'results/fragment/{0}_{1}/03_filtering/valid_reads12_{0}_{1}.bam'.format(cell, rep2)
    hic_data  = 'results/fragment/{0}_both/03_filtering/valid_reads12_{0}.bam'.format(cell)
    
    merge_bams(hic_data1, hic_data2, hic_data)


.. ansi-block::

      - Mergeing experiments
      - Indexing new BAM file


.. code:: ipython3

    cell = 'mouse_PSC'
    rep1 = 'rep1'
    rep2 = 'rep2'
    
    hic_data1 = 'results/fragment/{0}_{1}/03_filtering/valid_reads12_{0}_{1}.bam'.format(cell, rep1)
    hic_data2 = 'results/fragment/{0}_{1}/03_filtering/valid_reads12_{0}_{1}.bam'.format(cell, rep2)
    hic_data  = 'results/fragment/{0}_both/03_filtering/valid_reads12_{0}.bam'.format(cell)
    
    merge_bams(hic_data1, hic_data2, hic_data)


.. ansi-block::

      - Mergeing experiments
      - Indexing new BAM file


Normalizing merged data
-----------------------

.. code:: ipython3

    from pytadbit.mapping.analyze import hic_map

.. code:: ipython3

    ! mkdir -p results/fragment/mouse_B_both/04_normalizing
    ! mkdir -p results/fragment/mouse_PSC_both/04_normalizing

All in one loop to: - filter - normalize - generate intra-chromosome and
genomic matrices

All datasets are analysed at various resolutions.

.. code:: ipython3

    for cell in ['mouse_B','mouse_PSC']:
        print(' -', cell)
        for reso in [1000000, 200000, 100000]:
            print('   *', reso)
            # load hic_data
            hic_data = load_hic_data_from_bam(
                'results/fragment/{0}_both/03_filtering/valid_reads12_{0}.bam'.format(cell),
                reso)
            # filter columns
            hic_data.filter_columns(draw_hist=False, min_count=10, by_mean=True)
            # normalize
            hic_data.normalize_hic(iterations=0)
            # save biases to reconstruct normalization
            hic_data.save_biases('results/fragment/{0}_both/04_normalizing/biases_{0}_both_{1}kb.biases'.format(cell, reso // 1000))
            # save data as raw matrix per chromsome
            hic_map(hic_data, by_chrom='intra', normalized=False,
                    savedata='results/fragment/{1}_both/04_normalizing/{0}_raw'.format(reso, cell))
            # save data as normalized matrix per chromosome
            hic_map(hic_data, by_chrom='intra', normalized=True,
                    savedata='results/fragment/{1}_both/04_normalizing/{0}_norm'.format(reso, cell))
            # if the resolution is low save the full genomic matrix
            if reso > 500000:
                hic_map(hic_data, by_chrom=False, normalized=False, 
                        savefig ='results/fragment/{1}_both/04_normalizing/{0}_raw.png'.format(reso, cell),
                        savedata='results/fragment/{1}_both/04_normalizing/{0}_raw.mat'.format(reso, cell))
    
                hic_map(hic_data, by_chrom=False, normalized=True,
                        savefig ='results/fragment/{1}_both/04_normalizing/{0}_norm.png'.format(reso, cell) ,
                        savedata='results/fragment/{1}_both/04_normalizing/{0}_norm.mat'.format(reso, cell))


.. ansi-block::

     - mouse_B
       * 1000000
    
      (Matrix size 2738x2738)                                                      [2020-02-06 15:09:37]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 15:09:37]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 15:10:54]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    


.. ansi-block::

    
    WARNING: Using twice min_count as the matrix was symmetricized and contains twice as many interactions as the original
    
    WARNING: removing columns having less than 20 counts:
         1     2     3   197   198   199   380   381   382   540   541   542   543   698   699   700   850   851   852  1000
      1001  1002  1146  1147  1148  1276  1277  1278  1401  1402  1403  1532  1533  1534  1654  1655  1656  1657  1776  1777
      1778  1897  1898  1899  2022  2023  2024  2126  2127  2128  2129  2226  2227  2228  2321  2322  2323  2412  2413  2414
      2474  2475  2476  2645  2694  2695  2698  2707  2708  2709  2712  2737
    
    WARNING: removing columns having less than (11301.146+0j) counts:
         1     2     3    67    68    69   197   198   199   372   373   379   380   381   382   540   541   542   543   601
       602   671   698   699   700   792   850   851   852   999  1000  1001  1002  1008  1009  1020  1021  1022  1032  1146
      1147  1148  1276  1277  1278  1401  1402  1403  1532  1533  1534  1602  1605  1619  1620  1621  1654  1655  1656  1657
      1677  1775  1776  1777  1778  1897  1898  1899  1900  1902  1939  2022  2023  2024  2126  2127  2128  2129  2145  2222
      2225  2226  2227  2228  2254  2260  2261  2262  2263  2321  2322  2323  2412  2413  2414  2474  2475  2476  2478  2501
      2502  2503  2504  2508  2598  2599  2622  2623  2644  2645  2646  2647  2648  2649  2650  2651  2652  2653  2654  2655
      2656  2657  2658  2659  2660  2661  2662  2663  2664  2665  2666  2667  2668  2669  2670  2671  2672  2673  2674  2675
      2676  2677  2678  2679  2680  2681  2682  2683  2684  2685  2686  2687  2688  2689  2690  2691  2692  2693  2694  2695
      2696  2697  2698  2699  2700  2701  2702  2703  2704  2705  2706  2707  2708  2709  2710  2711  2712  2713  2714  2715
      2716  2717  2718  2719  2720  2721  2722  2723  2724  2725  2726  2727  2728  2729  2730  2731  2732  2733  2734  2735
      2736  2737


.. ansi-block::

    Found 202 of 2738 columns with poor signal
    iterative correction
      - copying matrix
      - computing biases
    rescaling to factor 1
      - getting the sum of the matrix
        => 2606.178
      - rescaling biases
       * 200000
    
      (Matrix size 13641x13641)                                                    [2020-02-06 15:13:33]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 15:13:33]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 15:14:51]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    


.. ansi-block::

    
    WARNING: Using twice min_count as the matrix was symmetricized and contains twice as many interactions as the original
    
    WARNING: removing columns having less than 20 counts:
         1     2     3     4     5     6     7     8     9    10    11    12    13    14    15   112   113   114   978   979
       980   981   982   983   984   985   986   987   988   989   990   991   992   993  1856  1857  1861  1890  1891  1892
      1893  1894  1895  1896  1897  1898  1899  1900  1901  1902  1903  1904  2093  2690  2691  2692  2693  2694  2695  2696
      2697  2698  2699  2700  2701  2702  2703  2704  2705  2901  2902  2903  2904  2996  2997  3190  3299  3300  3344  3345
      3347  3401  3402  3403  3422  3473  3474  3475  3476  3477  3478  3479  3480  3481  3482  3483  3484  3485  3486  3487
      3488  3550  3551  4131  4132  4133  4134  4135  4233  4234  4235  4236  4237  4238  4239  4240  4241  4242  4243  4244
      4245  4246  4247  4248  4982  4983  4984  4985  4986  4987  4988  4989  4990  4991  4992  4993  4994  4995  4996  4997
      4998  5030  5031  5091  5092  5147  5678  5710  5711  5712  5713  5714  5715  5716  5717  5718  5719  5720  5721  5722
      5723  5724  5725  6358  6359  6360  6361  6362  6363  6364  6365  6366  6367  6368  6369  6370  6371  6372  6373  6982
      6983  6984  6985  6986  6987  6988  6989  6990  6991  6992  6993  6994  6995  6996  7019  7272  7319  7635  7636  7637
      7638  7639  7640  7641  7642  7643  7644  7645  7646  7647  7648  7649  7650  7674  8076  8246  8247  8248  8249  8250
      8251  8252  8253  8254  8255  8256  8257  8258  8259  8260  8261  8794  8848  8849  8850  8851  8852  8853  8854  8855
      8856  8857  8858  8859  8860  8861  8862  9450  9451  9452  9453  9454  9455  9456  9457  9458  9459  9460  9461  9462
      9463  9464  9465  9581  9798 10076 10077 10078 10079 10080 10081 10082 10083 10084 10085 10086 10087 10088 10089 10090
     10269 10270 10366 10367 10596 10597 10598 10599 10600 10601 10602 10603 10604 10605 10606 10607 10608 10609 10610 10611
     10687 10688 10689 11077 11088 11089 11090 11091 11092 11093 11094 11095 11096 11097 11098 11099 11100 11101 11102 11103
     11126 11226 11227 11228 11259 11261 11263 11264 11275 11276 11277 11278 11325 11329 11330 11363 11364 11365 11366 11564
     11565 11566 11567 11568 11569 11570 11571 11572 11573 11574 11575 11576 11577 11578 11953 12018 12019 12020 12021 12022
     12023 12024 12025 12026 12027 12028 12029 12030 12031 12032 12049 12050 12051 12325 12326 12327 12328 12329 12330 12331
     12332 12333 12334 12335 12336 12337 12338 12339 12340 12360 12461 12471 12472 12541 12628 12882 12948 12949 12950 12953
     12954 13030 13031 13177 13178 13181 13184 13197 13199 13218 13219 13220 13221 13240 13244 13246 13247 13248 13251 13254
     13255 13256 13258 13259 13264 13271 13272 13274 13275 13276 13277 13278 13302 13303 13304 13305 13306 13307 13308 13309
     13310 13311 13312 13313 13314 13315 13316 13317 13318 13319 13320 13335 13365 13366 13367 13368 13406 13411 13412 13413
     13420 13421 13422 13423 13424 13425 13426 13427 13428 13429 13430 13431 13432 13433 13434 13435 13437 13438 13439 13440
     13441 13442 13443 13444 13445 13446 13447 13448 13449 13450 13452 13453 13455 13457 13459 13460 13461 13462 13463 13465
     13466 13467 13468 13472 13484 13486 13487 13488 13489 13490 13491 13492 13493 13494 13495 13496 13497 13498 13499 13500
     13501 13502 13503 13505 13506 13507 13508 13509 13510 13511 13512 13513 13514 13515 13516 13517 13529 13530 13531 13532
     13533 13546 13551 13552 13564 13565 13566 13580 13582 13584 13609 13610 13637 13638 13639 13640
    
    WARNING: removing columns having less than 2249.148 counts:
         1     2     3     4     5     6     7     8     9    10    11    12    13    14    15   112   113   114   318   329
       330   331   332   333   334   335   336   337   338   339   340   341   342   343   344   345   653   960   978   979
       980   981   982   983   984   985   986   987   988   989   990   991   992   993  1494  1495  1855  1856  1857  1858
      1859  1860  1861  1862  1863  1867  1889  1890  1891  1892  1893  1894  1895  1896  1897  1898  1899  1900  1901  1902
      1903  1904  1968  2075  2093  2393  2436  2437  2438  2690  2691  2692  2693  2694  2695  2696  2697  2698  2699  2700
      2701  2702  2703  2704  2705  2900  2901  2902  2903  2904  2991  2992  2993  2994  2995  2996  2997  2998  3000  3001
      3190  3299  3300  3301  3302  3341  3342  3343  3344  3345  3347  3348  3401  3402  3403  3407  3408  3420  3421  3422
      3443  3473  3474  3475  3476  3477  3478  3479  3480  3481  3482  3483  3484  3485  3486  3487  3488  3531  3532  3550
      3551  3943  3944  3945  3946  3947  3948  3949  3950  3952  4131  4132  4133  4134  4135  4233  4234  4235  4236  4237
      4238  4239  4240  4241  4242  4243  4244  4245  4246  4247  4248  4648  4836  4851  4952  4953  4954  4957  4958  4962
      4964  4965  4973  4974  4975  4977  4979  4980  4981  4982  4983  4984  4985  4986  4987  4988  4989  4990  4991  4992
      4993  4994  4995  4996  4997  4998  4999  5020  5021  5022  5023  5024  5025  5026  5027  5028  5029  5030  5031  5032
      5084  5085  5086  5087  5088  5089  5090  5091  5092  5093  5094  5095  5096  5097  5098  5145  5146  5147  5148  5176
      5177  5178  5179  5513  5678  5710  5711  5712  5713  5714  5715  5716  5717  5718  5719  5720  5721  5722  5723  5724
      5725  5814  5818  6358  6359  6360  6361  6362  6363  6364  6365  6366  6367  6368  6369  6370  6371  6372  6373  6981
      6982  6983  6984  6985  6986  6987  6988  6989  6990  6991  6992  6993  6994  6995  6996  7019  7272  7319  7391  7635
      7636  7637  7638  7639  7640  7641  7642  7643  7644  7645  7646  7647  7648  7649  7650  7674  7986  7987  7988  7989
      7991  8001  8002  8003  8004  8005  8006  8051  8071  8072  8073  8074  8075  8076  8077  8078  8079  8080  8081  8082
      8083  8084  8246  8247  8248  8249  8250  8251  8252  8253  8254  8255  8256  8257  8258  8259  8260  8261  8339  8344
      8347  8358  8359  8360  8361  8364  8366  8367  8685  8794  8848  8849  8850  8851  8852  8853  8854  8855  8856  8857
      8858  8859  8860  8861  8862  9177  9178  9450  9451  9452  9453  9454  9455  9456  9457  9458  9459  9460  9461  9462
      9463  9464  9465  9467  9468  9469  9470  9474  9475  9476  9477  9478  9479  9486  9581  9662  9663  9664  9665  9715
      9716  9717  9798 10075 10076 10077 10078 10079 10080 10081 10082 10083 10084 10085 10086 10087 10088 10089 10090 10091
     10121 10269 10270 10366 10367 10596 10597 10598 10599 10600 10601 10602 10603 10604 10605 10606 10607 10608 10609 10610
     10611 10686 10687 10688 10689 10690 10691 11068 11070 11071 11072 11073 11074 11077 11088 11089 11090 11091 11092 11093
     11094 11095 11096 11097 11098 11099 11100 11101 11102 11103 11126 11226 11227 11228 11229 11230 11231 11232 11234 11258
     11259 11260 11261 11262 11263 11264 11265 11266 11267 11268 11270 11271 11272 11273 11274 11275 11276 11277 11278 11279
     11280 11325 11329 11330 11331 11363 11364 11365 11366 11564 11565 11566 11567 11568 11569 11570 11571 11572 11573 11574
     11575 11576 11577 11578 11953 12017 12018 12019 12020 12021 12022 12023 12024 12025 12026 12027 12028 12029 12030 12031
     12032 12038 12049 12050 12051 12204 12325 12326 12327 12328 12329 12330 12331 12332 12333 12334 12335 12336 12337 12338
     12339 12340 12343 12344 12345 12347 12349 12350 12351 12360 12450 12451 12461 12462 12463 12464 12465 12466 12467 12468
     12469 12470 12471 12472 12473 12475 12476 12477 12478 12479 12480 12483 12485 12486 12487 12490 12492 12493 12494 12496
     12498 12499 12500 12501 12502 12515 12541 12598 12599 12600 12601 12628 12691 12844 12882 12942 12943 12945 12947 12948
     12949 12950 12951 12952 12953 12954 12955 13030 13031 13065 13066 13067 13068 13069 13071 13072 13073 13075 13176 13177
     13178 13179 13180 13181 13182 13183 13184 13185 13186 13187 13188 13189 13190 13191 13192 13193 13194 13195 13196 13197
     13198 13199 13200 13201 13202 13203 13204 13205 13206 13207 13208 13209 13210 13211 13212 13213 13214 13215 13216 13217
     13218 13219 13220 13221 13222 13223 13224 13225 13226 13227 13228 13229 13230 13231 13232 13233 13234 13235 13236 13237
     13238 13239 13240 13241 13242 13243 13244 13245 13246 13247 13248 13249 13250 13251 13252 13253 13254 13255 13256 13257
     13258 13259 13260 13261 13262 13263 13264 13265 13266 13267 13268 13269 13270 13271 13272 13273 13274 13275 13276 13277
     13278 13279 13280 13281 13282 13283 13284 13285 13286 13287 13288 13289 13290 13291 13292 13293 13294 13295 13296 13297
     13298 13299 13300 13301 13302 13303 13304 13305 13306 13307 13308 13309 13310 13311 13312 13313 13314 13315 13316 13317
     13318 13319 13320 13321 13322 13323 13324 13325 13326 13327 13328 13329 13330 13331 13332 13333 13334 13335 13336 13337
     13338 13339 13340 13341 13342 13343 13344 13345 13346 13347 13348 13349 13350 13351 13352 13353 13354 13355 13356 13357
     13358 13359 13360 13361 13362 13363 13364 13365 13366 13367 13368 13369 13370 13371 13372 13373 13374 13375 13376 13377
     13378 13379 13380 13381 13382 13383 13384 13385 13386 13387 13388 13389 13390 13391 13392 13393 13394 13395 13396 13397
     13398 13399 13400 13401 13402 13403 13404 13405 13406 13407 13408 13409 13410 13411 13412 13413 13414 13415 13416 13417
     13418 13419 13420 13421 13422 13423 13424 13425 13426 13427 13428 13429 13430 13431 13432 13433 13434 13435 13436 13437
     13438 13439 13440 13441 13442 13443 13444 13445 13446 13447 13448 13449 13450 13451 13452 13453 13454 13455 13456 13457
     13458 13459 13460 13461 13462 13463 13464 13465 13466 13467 13468 13469 13470 13471 13472 13473 13474 13475 13476 13477
     13478 13479 13480 13481 13482 13483 13484 13485 13486 13487 13488 13489 13490 13491 13492 13493 13494 13495 13496 13497
     13498 13499 13500 13501 13502 13503 13504 13505 13506 13507 13508 13509 13510 13511 13512 13513 13514 13515 13516 13517
     13518 13519 13520 13521 13522 13523 13524 13525 13526 13527 13528 13529 13530 13531 13532 13533 13534 13535 13536 13537
     13538 13539 13540 13541 13542 13543 13544 13545 13546 13547 13548 13549 13550 13551 13552 13553 13554 13555 13556 13557
     13558 13559 13560 13561 13562 13563 13564 13565 13566 13567 13568 13569 13570 13571 13572 13573 13574 13575 13576 13577
     13578 13579 13580 13581 13582 13583 13584 13585 13586 13587 13588 13589 13590 13591 13592 13593 13594 13595 13596 13597
     13598 13599 13600 13601 13602 13603 13604 13605 13606 13607 13608 13609 13610 13611 13612 13613 13614 13615 13616 13617
     13618 13619 13620 13621 13622 13623 13624 13625 13626 13627 13628 13629 13630 13631 13632 13633 13634 13637 13638 13639
     13640


.. ansi-block::

    Found 1141 of 13641 columns with poor signal
    iterative correction
      - copying matrix
      - computing biases
    rescaling to factor 1
      - getting the sum of the matrix
        => 12819.011
      - rescaling biases
       * 100000
    
      (Matrix size 27269x27269)                                                    [2020-02-06 15:22:32]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 15:22:33]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 15:24:23]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    


.. ansi-block::

    
    WARNING: Using twice min_count as the matrix was symmetricized and contains twice as many interactions as the original
    
    WARNING: removing columns having less than 20 counts:
         1     2     3     4     5     6     7     8     9    10    11    12    13    14    15    16    17    18    19    20
        21    22    23    24    25    26    27    28    29    30   223   224   225   226   227   228  1783  1919  1921  1955
      1956  1957  1958  1959  1960  1961  1962  1963  1964  1965  1966  1967  1968  1969  1970  1971  1972  1973  1974  1975
      1976  1977  1978  1979  1980  1981  1982  1983  1984  1985  2865  3182  3709  3710  3711  3712  3713  3716  3719  3720
      3721  3777  3778  3779  3780  3781  3782  3783  3784  3785  3786  3787  3788  3789  3790  3791  3792  3793  3794  3795
      3796  3797  3798  3799  3800  3801  3802  3803  3804  3805  3806  3807  3935  3936  4184  4185  4186  4285  4286  4912
      5092  5378  5379  5380  5381  5382  5383  5384  5385  5386  5387  5388  5389  5390  5391  5392  5393  5394  5395  5396
      5397  5398  5399  5400  5401  5402  5403  5404  5405  5406  5407  5408  5798  5799  5800  5801  5802  5803  5804  5805
      5806  5982  5988  5989  5990  5991  5992  5993  6376  6377  6378  6594  6595  6596  6597  6598  6601  6602  6680  6681
      6683  6685  6686  6687  6688  6690  6691  6692  6693  6694  6730  6799  6800  6801  6802  6803  6804  6812  6813  6838
      6840  6841  6842  6943  6944  6945  6946  6947  6948  6949  6950  6951  6952  6953  6954  6955  6956  6957  6958  6959
      6960  6961  6962  6963  6964  6965  6966  6967  6968  6969  6970  6971  6972  6973  6974  7096  7097  7098  7099  7100
      8259  8260  8261  8262  8263  8264  8265  8266  8267  8268  8269  8454  8463  8464  8465  8466  8467  8468  8469  8470
      8471  8472  8473  8474  8475  8476  8477  8478  8479  8480  8481  8482  8483  8484  8485  8486  8487  8488  8489  8490
      8491  8492  8493  9187  9901  9902  9903  9950  9955  9956  9957  9958  9960  9961  9962  9963  9964  9965  9966  9967
      9968  9969  9970  9971  9972  9973  9974  9975  9976  9977  9978  9979  9980  9981  9982  9983  9984  9985  9986  9987
      9988  9989  9990  9991  9992  9993  9995 10056 10057 10058 10059 10165 10168 10177 10178 10179 10180 10181 10182 10191
     10192 10289 10290 10291 10352 11352 11353 11354 11416 11417 11418 11419 11420 11421 11422 11423 11424 11425 11426 11427
     11428 11429 11430 11431 11432 11433 11434 11435 11436 11437 11438 11439 11440 11441 11442 11443 11444 11445 11446 11969
     12124 12711 12712 12713 12714 12715 12716 12717 12718 12719 12720 12721 12722 12723 12724 12725 12726 12727 12728 12729
     12730 12731 12732 12733 12734 12735 12736 12737 12738 12739 12740 12741 13800 13957 13958 13959 13960 13961 13962 13963
     13964 13965 13966 13967 13968 13969 13970 13971 13972 13973 13974 13975 13976 13977 13978 13979 13980 13981 13982 13983
     13984 13985 13986 13987 13988 14032 14033 14034 14537 14538 14539 14540 14631 14632 14633 15264 15265 15266 15267 15268
     15269 15270 15271 15272 15273 15274 15275 15276 15277 15278 15279 15280 15281 15282 15283 15284 15285 15286 15287 15288
     15289 15290 15291 15292 15293 15294 15295 15341 15342 16095 16134 16135 16136 16137 16145 16146 16147 16485 16486 16487
     16488 16489 16490 16491 16492 16493 16494 16495 16496 16497 16498 16499 16500 16501 16502 16503 16504 16505 16506 16507
     16508 16509 16510 16511 16512 16513 16514 16515 17037 17580 17581 17687 17688 17689 17690 17691 17692 17693 17694 17695
     17696 17697 17698 17699 17700 17701 17702 17703 17704 17705 17706 17707 17708 17709 17710 17711 17712 17713 17714 17715
     17716 17717 18892 18893 18894 18895 18896 18897 18898 18899 18900 18901 18902 18903 18904 18905 18906 18907 18908 18909
     18910 18911 18912 18913 18914 18915 18916 18917 18918 18919 18920 18921 18922 19153 19154 19160 19161 19586 19587 19588
     20142 20143 20144 20145 20146 20147 20148 20149 20150 20151 20152 20153 20154 20155 20156 20157 20158 20159 20160 20161
     20162 20163 20164 20165 20166 20167 20168 20169 20170 20171 20172 20173 20234 20529 20530 20531 20532 20533 20723 20724
     20725 20726 20727 21180 21181 21183 21184 21185 21186 21187 21188 21189 21190 21191 21192 21193 21194 21195 21196 21197
     21198 21199 21200 21201 21202 21203 21204 21205 21206 21207 21208 21209 21210 21211 21212 21213 21363 21364 21365 21366
     21367 21368 21369 21372 22138 22140 22143 22144 22145 22166 22167 22168 22169 22170 22171 22172 22173 22174 22175 22176
     22177 22178 22179 22180 22181 22182 22183 22184 22185 22186 22187 22188 22189 22190 22191 22192 22193 22194 22195 22196
     22241 22242 22441 22442 22443 22444 22445 22446 22505 22507 22508 22510 22511 22512 22513 22514 22515 22516 22517 22518
     22519 22539 22540 22541 22542 22543 22544 22545 22546 22547 22548 22549 22550 22551 22638 22639 22640 22647 22648 22649
     22650 22651 22714 22715 22716 22717 22718 22719 22720 22721 22722 22723 23116 23117 23118 23119 23120 23121 23122 23123
     23124 23125 23126 23127 23128 23129 23130 23131 23132 23133 23134 23135 23136 23137 23138 23139 23140 23141 23142 23143
     23144 23145 23146 23230 23882 23883 23894 23895 23896 23897 24024 24025 24026 24027 24028 24029 24030 24031 24032 24033
     24034 24035 24036 24037 24038 24039 24040 24041 24042 24043 24044 24045 24046 24047 24048 24049 24050 24051 24052 24053
     24054 24066 24087 24088 24089 24090 24091 24092 24398 24639 24640 24641 24642 24643 24644 24645 24646 24647 24648 24649
     24650 24651 24652 24653 24654 24655 24656 24657 24658 24659 24660 24661 24662 24663 24664 24665 24666 24667 24668 24669
     24708 24709 24889 24891 24910 24911 24913 24915 24930 24931 24932 24933 24934 24945 24947 24950 24954 24958 24987 24989
     25013 25017 25018 25069 25070 25071 25175 25183 25189 25190 25243 25244 25245 25246 25752 25753 25754 25884 25885 25886
     25887 25888 25889 25892 25894 25895 25896 25897 25898 26048 26049 26050 26051 26052 26123 26341 26342 26343 26344 26345
     26349 26350 26351 26355 26356 26366 26367 26380 26381 26382 26383 26385 26386 26387 26422 26423 26424 26425 26426 26427
     26428 26429 26430 26459 26460 26461 26463 26464 26466 26467 26468 26470 26473 26474 26475 26476 26479 26480 26481 26482
     26483 26484 26486 26487 26488 26489 26490 26493 26494 26495 26496 26497 26498 26499 26500 26502 26503 26504 26505 26506
     26509 26510 26511 26512 26513 26514 26515 26516 26519 26520 26521 26522 26523 26526 26528 26529 26530 26531 26532 26533
     26534 26535 26536 26537 26538 26539 26540 26541 26542 26543 26544 26545 26547 26548 26549 26550 26551 26568 26574 26575
     26576 26577 26579 26580 26581 26582 26590 26591 26592 26593 26594 26595 26596 26597 26598 26599 26600 26601 26602 26603
     26604 26605 26606 26607 26608 26609 26610 26611 26612 26613 26614 26615 26616 26617 26618 26619 26620 26621 26622 26623
     26624 26625 26626 26627 26628 26629 26630 26631 26632 26634 26635 26636 26650 26651 26653 26655 26657 26658 26659 26661
     26662 26664 26665 26666 26667 26669 26671 26673 26679 26681 26682 26683 26684 26686 26689 26691 26692 26693 26695 26714
     26716 26717 26718 26719 26720 26721 26722 26723 26724 26778 26779 26780 26799 26800 26801 26802 26804 26806 26807 26808
     26809 26810 26811 26812 26813 26814 26815 26816 26817 26820 26821 26822 26825 26827 26828 26829 26830 26831 26832 26833
     26834 26835 26836 26837 26838 26839 26840 26841 26842 26843 26844 26845 26846 26847 26848 26849 26850 26851 26852 26853
     26854 26855 26856 26857 26858 26859 26860 26861 26862 26863 26864 26865 26866 26867 26868 26869 26870 26871 26872 26873
     26874 26875 26876 26877 26878 26879 26880 26881 26882 26883 26884 26885 26886 26887 26888 26891 26892 26893 26894 26896
     26897 26898 26899 26900 26901 26902 26904 26905 26906 26907 26908 26909 26910 26911 26912 26913 26914 26915 26916 26917
     26918 26919 26920 26921 26922 26923 26924 26925 26927 26930 26931 26932 26937 26941 26942 26949 26953 26954 26955 26956
     26957 26959 26960 26961 26962 26963 26964 26965 26966 26967 26968 26969 26970 26971 26972 26973 26974 26975 26976 26977
     26978 26979 26980 26981 26982 26983 26984 26985 26986 26987 26988 26989 26990 26991 26992 26993 26994 26995 26997 26998
     26999 27000 27001 27002 27003 27004 27005 27006 27007 27008 27009 27010 27011 27012 27013 27014 27015 27016 27017 27018
     27019 27020 27021 27022 27023 27024 27025 27026 27027 27032 27033 27034 27035 27036 27037 27038 27040 27042 27045 27046
     27047 27048 27049 27050 27051 27052 27053 27054 27055 27057 27062 27063 27064 27065 27066 27067 27068 27069 27070 27071
     27073 27074 27075 27076 27079 27080 27082 27088 27089 27090 27091 27092 27097 27115 27116 27117 27118 27119 27120 27121
     27122 27123 27125 27126 27127 27128 27130 27131 27133 27135 27136 27140 27143 27144 27145 27146 27147 27148 27150 27151
     27152 27153 27154 27155 27156 27158 27204 27205 27206 27207 27208 27260 27261 27262 27263 27264 27265 27266 27267 27268
    
    
    WARNING: removing columns having less than 2417.695 counts:
         1     2     3     4     5     6     7     8     9    10    11    12    13    14    15    16    17    18    19    20
        21    22    23    24    25    26    27    28    29    30   223   224   225   226   227   228   229   608   609   610
       611   618   619   635   636   657   658   659   660   661   662   663   664   665   666   667   668   669   670   671
       672   673   674   675   676   677   678   679   680   681   682   683   684   685   686   687   688   689   690   854
       855  1304  1305  1306  1334  1335  1783  1919  1920  1921  1955  1956  1957  1958  1959  1960  1961  1962  1963  1964
      1965  1966  1967  1968  1969  1970  1971  1972  1973  1974  1975  1976  1977  1978  1979  1980  1981  1982  1983  1984
      1985  2180  2859  2865  2961  2962  2963  2986  2987  2988  2989  3182  3708  3709  3710  3711  3712  3713  3714  3715
      3716  3717  3718  3719  3720  3721  3722  3723  3724  3725  3727  3732  3733  3776  3777  3778  3779  3780  3781  3782
      3783  3784  3785  3786  3787  3788  3789  3790  3791  3792  3793  3794  3795  3796  3797  3798  3799  3800  3801  3802
      3803  3804  3805  3806  3807  3934  3935  3936  4146  4147  4148  4149  4150  4151  4152  4183  4184  4185  4186  4285
      4286  4700  4717  4763  4782  4783  4784  4785  4787  4788  4789  4790  4814  4815  4816  4817  4869  4870  4871  4872
      4873  4874  4875  4911  4912  5091  5092  5378  5379  5380  5381  5382  5383  5384  5385  5386  5387  5388  5389  5390
      5391  5392  5393  5394  5395  5396  5397  5398  5399  5400  5401  5402  5403  5404  5405  5406  5407  5408  5797  5798
      5799  5800  5801  5802  5803  5804  5805  5806  5978  5979  5980  5981  5982  5983  5984  5985  5986  5987  5988  5989
      5990  5991  5992  5993  5994  5995  5996  5997  5998  5999  6000  6115  6376  6377  6378  6594  6595  6596  6597  6598
      6599  6600  6601  6602  6603  6663  6664  6665  6666  6667  6669  6670  6671  6672  6673  6674  6675  6676  6677  6678
      6679  6680  6681  6682  6683  6684  6685  6686  6687  6688  6690  6691  6692  6693  6694  6695  6730  6787  6799  6800
      6801  6802  6803  6804  6805  6811  6812  6813  6814  6836  6837  6838  6839  6840  6841  6842  6845  6847  6849  6851
      6855  6870  6871  6872  6874  6875  6877  6878  6879  6880  6881  6882  6883  6884  6885  6886  6887  6888  6889  6891
      6943  6944  6945  6946  6947  6948  6949  6950  6951  6952  6953  6954  6955  6956  6957  6958  6959  6960  6961  6962
      6963  6964  6965  6966  6967  6968  6969  6970  6971  6972  6973  6974  7054  7055  7056  7057  7058  7059  7060  7061
      7062  7063  7096  7097  7098  7099  7100  7852  7880  7881  7883  7884  7885  7886  7887  7888  7889  7890  7891  7892
      7893  7894  7895  7896  7897  7898  7899  7900  7901  7902  7991  8259  8260  8261  8262  8263  8264  8265  8266  8267
      8268  8269  8454  8463  8464  8465  8466  8467  8468  8469  8470  8471  8472  8473  8474  8475  8476  8477  8478  8479
      8480  8481  8482  8483  8484  8485  8486  8487  8488  8489  8490  8491  8492  8493  8494  9187  9292  9293  9365  9366
      9609  9668  9669  9670  9671  9672  9673  9698  9699  9763  9900  9901  9902  9903  9904  9905  9906  9908  9909  9910
      9911  9912  9913  9918  9919  9920  9921  9923  9924  9925  9926  9927  9939  9941  9942  9943  9944  9945  9946  9947
      9949  9950  9951  9952  9954  9955  9956  9957  9958  9959  9960  9961  9962  9963  9964  9965  9966  9967  9968  9969
      9970  9971  9972  9973  9974  9975  9976  9977  9978  9979  9980  9981  9982  9983  9984  9985  9986  9987  9988  9989
      9990  9991  9992  9993  9994  9995  9999 10000 10019 10036 10037 10038 10039 10040 10041 10042 10043 10044 10045 10046
     10047 10048 10049 10050 10051 10052 10053 10054 10055 10056 10057 10058 10059 10060 10061 10070 10071 10075 10076 10077
     10078 10112 10113 10114 10116 10137 10163 10164 10165 10166 10167 10168 10169 10170 10171 10172 10173 10174 10175 10176
     10177 10178 10179 10180 10181 10182 10183 10184 10185 10186 10187 10188 10189 10190 10191 10192 10193 10194 10279 10280
     10282 10283 10284 10286 10287 10288 10289 10290 10291 10292 10293 10347 10348 10349 10350 10351 10352 10353 10354 10355
     10557 10560 11021 11022 11023 11352 11353 11354 11379 11416 11417 11418 11419 11420 11421 11422 11423 11424 11425 11426
     11427 11428 11429 11430 11431 11432 11433 11434 11435 11436 11437 11438 11439 11440 11441 11442 11443 11444 11445 11446
     11615 11623 11624 11628 11629 11630 11631 11632 11633 11968 11969 12124 12148 12149 12710 12711 12712 12713 12714 12715
     12716 12717 12718 12719 12720 12721 12722 12723 12724 12725 12726 12727 12728 12729 12730 12731 12732 12733 12734 12735
     12736 12737 12738 12739 12740 12741 13800 13955 13956 13957 13958 13959 13960 13961 13962 13963 13964 13965 13966 13967
     13968 13969 13970 13971 13972 13973 13974 13975 13976 13977 13978 13979 13980 13981 13982 13983 13984 13985 13986 13987
     13988 14032 14033 14034 14537 14538 14539 14540 14631 14632 14633 14775 14776 14777 15264 15265 15266 15267 15268 15269
     15270 15271 15272 15273 15274 15275 15276 15277 15278 15279 15280 15281 15282 15283 15284 15285 15286 15287 15288 15289
     15290 15291 15292 15293 15294 15295 15341 15342 15343 15611 15753 15754 15755 15846 15847 15851 15852 15964 15965 15966
     15967 15968 15969 15970 15971 15972 15973 15974 15975 15976 15994 15995 15996 15997 15998 15999 16000 16001 16002 16003
     16004 16005 16006 16007 16095 16096 16097 16134 16135 16136 16137 16138 16139 16140 16141 16142 16143 16144 16145 16146
     16147 16148 16149 16150 16151 16152 16153 16154 16155 16156 16157 16158 16159 16160 16161 16162 16163 16307 16485 16486
     16487 16488 16489 16490 16491 16492 16493 16494 16495 16496 16497 16498 16499 16500 16501 16502 16503 16504 16505 16506
     16507 16508 16509 16510 16511 16512 16513 16514 16515 16670 16671 16672 16673 16674 16675 16676 16679 16680 16681 16682
     16683 16685 16686 16687 16691 16692 16693 16695 16703 16704 16705 16706 16707 16708 16709 16710 16711 16712 16713 16714
     16715 16716 16718 16719 16720 16721 16722 16723 16724 16725 16726 16727 16728 16729 17037 17362 17363 17365 17580 17581
     17582 17613 17620 17621 17622 17687 17688 17689 17690 17691 17692 17693 17694 17695 17696 17697 17698 17699 17700 17701
     17702 17703 17704 17705 17706 17707 17708 17709 17710 17711 17712 17713 17714 17715 17716 17717 17917 18342 18344 18345
     18346 18347 18348 18349 18350 18351 18352 18689 18756 18887 18892 18893 18894 18895 18896 18897 18898 18899 18900 18901
     18902 18903 18904 18905 18906 18907 18908 18909 18910 18911 18912 18913 18914 18915 18916 18917 18918 18919 18920 18921
     18922 18923 18924 18925 18926 18927 18928 18929 18930 18931 18932 18933 18934 18935 18936 18937 18938 18939 18940 18941
     18942 18943 18944 18945 18946 18947 18948 18949 18950 18951 18953 18954 18955 18956 18957 18958 18959 18960 18961 18963
     18964 18965 18966 18967 18968 18969 19088 19152 19153 19154 19155 19160 19161 19307 19308 19309 19310 19311 19313 19314
     19315 19316 19317 19318 19319 19320 19321 19322 19327 19328 19336 19337 19417 19418 19420 19421 19422 19423 19424 19425
     19426 19427 19428 19429 19431 19432 19433 19434 19586 19587 19588 19589 20141 20142 20143 20144 20145 20146 20147 20148
     20149 20150 20151 20152 20153 20154 20155 20156 20157 20158 20159 20160 20161 20162 20163 20164 20165 20166 20167 20168
     20169 20170 20171 20172 20173 20174 20233 20234 20529 20530 20531 20532 20533 20723 20724 20725 20726 20727 21180 21181
     21182 21183 21184 21185 21186 21187 21188 21189 21190 21191 21192 21193 21194 21195 21196 21197 21198 21199 21200 21201
     21202 21203 21204 21205 21206 21207 21208 21209 21210 21211 21212 21213 21214 21362 21363 21364 21365 21366 21367 21368
     21369 21370 21371 21372 21373 21374 22126 22127 22129 22130 22131 22132 22133 22134 22135 22136 22137 22138 22139 22140
     22143 22144 22145 22155 22165 22166 22167 22168 22169 22170 22171 22172 22173 22174 22175 22176 22177 22178 22179 22180
     22181 22182 22183 22184 22185 22186 22187 22188 22189 22190 22191 22192 22193 22194 22195 22196 22230 22241 22242 22243
     22338 22397 22441 22442 22443 22444 22445 22446 22447 22448 22449 22450 22451 22452 22453 22454 22455 22456 22457 22458
     22504 22505 22506 22507 22508 22509 22510 22511 22512 22513 22514 22515 22516 22517 22518 22519 22520 22521 22522 22523
     22524 22525 22526 22528 22529 22530 22531 22532 22533 22534 22535 22536 22537 22538 22539 22540 22541 22542 22543 22544
     22545 22546 22547 22548 22549 22550 22551 22638 22639 22640 22646 22647 22648 22649 22650 22651 22652 22714 22715 22716
     22717 22718 22719 22720 22721 22722 22723 23116 23117 23118 23119 23120 23121 23122 23123 23124 23125 23126 23127 23128
     23129 23130 23131 23132 23133 23134 23135 23136 23137 23138 23139 23140 23141 23142 23143 23144 23145 23146 23230 23882
     23883 23894 23895 23896 23897 24023 24024 24025 24026 24027 24028 24029 24030 24031 24032 24033 24034 24035 24036 24037
     24038 24039 24040 24041 24042 24043 24044 24045 24046 24047 24048 24049 24050 24051 24052 24053 24054 24055 24065 24066
     24087 24088 24089 24090 24091 24092 24397 24398 24399 24639 24640 24641 24642 24643 24644 24645 24646 24647 24648 24649
     24650 24651 24652 24653 24654 24655 24656 24657 24658 24659 24660 24661 24662 24663 24664 24665 24666 24667 24668 24669
     24670 24671 24673 24674 24675 24676 24677 24678 24679 24680 24681 24682 24683 24684 24686 24687 24688 24689 24690 24691
     24692 24708 24709 24887 24888 24889 24890 24891 24892 24895 24896 24898 24899 24901 24902 24903 24904 24906 24907 24909
     24910 24911 24912 24913 24914 24915 24916 24917 24918 24919 24920 24921 24922 24923 24924 24925 24926 24927 24928 24929
     24930 24931 24932 24933 24934 24935 24936 24937 24938 24939 24940 24941 24942 24943 24944 24945 24946 24947 24948 24949
     24950 24953 24954 24955 24956 24958 24959 24960 24961 24962 24963 24964 24966 24967 24968 24969 24971 24972 24973 24974
     24975 24976 24977 24978 24980 24981 24982 24983 24984 24985 24986 24987 24988 24989 24990 24991 24992 24993 24994 25013
     25014 25015 25017 25018 25019 25069 25070 25071 25072 25175 25176 25183 25184 25185 25186 25187 25188 25189 25190 25191
     25192 25197 25243 25244 25245 25246 25362 25370 25371 25372 25388 25589 25676 25677 25751 25752 25753 25754 25871 25872
     25873 25874 25875 25876 25877 25878 25879 25880 25882 25883 25884 25885 25886 25887 25888 25889 25890 25891 25892 25893
     25894 25895 25896 25897 25898 25899 25900 25991 25994 26048 26049 26050 26051 26052 26118 26119 26120 26121 26122 26123
     26124 26125 26126 26127 26128 26129 26130 26131 26132 26133 26134 26135 26136 26137 26138 26139 26140 26180 26340 26341
     26342 26343 26344 26345 26346 26347 26348 26349 26350 26351 26352 26353 26354 26355 26356 26357 26358 26359 26360 26361
     26362 26363 26364 26365 26366 26367 26368 26369 26370 26371 26372 26373 26374 26375 26376 26377 26378 26379 26380 26381
     26382 26383 26384 26385 26386 26387 26388 26389 26390 26391 26392 26393 26394 26395 26396 26397 26398 26399 26400 26401
     26402 26403 26404 26405 26406 26407 26408 26409 26410 26411 26412 26413 26414 26415 26416 26417 26418 26419 26420 26421
     26422 26423 26424 26425 26426 26427 26428 26429 26430 26431 26432 26433 26434 26435 26436 26437 26438 26439 26440 26441
     26442 26443 26444 26445 26446 26447 26448 26449 26450 26451 26452 26453 26454 26455 26456 26457 26458 26459 26460 26461
     26462 26463 26464 26465 26466 26467 26468 26469 26470 26471 26472 26473 26474 26475 26476 26477 26478 26479 26480 26481
     26482 26483 26484 26485 26486 26487 26488 26489 26490 26491 26492 26493 26494 26495 26496 26497 26498 26499 26500 26501
     26502 26503 26504 26505 26506 26507 26508 26509 26510 26511 26512 26513 26514 26515 26516 26517 26518 26519 26520 26521
     26522 26523 26524 26525 26526 26527 26528 26529 26530 26531 26532 26533 26534 26535 26536 26537 26538 26539 26540 26541
     26542 26543 26544 26545 26546 26547 26548 26549 26550 26551 26552 26553 26554 26555 26556 26557 26558 26559 26560 26561
     26562 26563 26564 26565 26566 26567 26568 26569 26570 26571 26572 26573 26574 26575 26576 26577 26578 26579 26580 26581
     26582 26583 26584 26585 26586 26587 26588 26589 26590 26591 26592 26593 26594 26595 26596 26597 26598 26599 26600 26601
     26602 26603 26604 26605 26606 26607 26608 26609 26610 26611 26612 26613 26614 26615 26616 26617 26618 26619 26620 26621
     26622 26623 26624 26625 26626 26627 26628 26629 26630 26631 26632 26633 26634 26635 26636 26637 26638 26639 26640 26641
     26642 26643 26644 26645 26646 26647 26648 26649 26650 26651 26652 26653 26654 26655 26656 26657 26658 26659 26660 26661
     26662 26663 26664 26665 26666 26667 26668 26669 26670 26671 26672 26673 26674 26675 26676 26677 26678 26679 26680 26681
     26682 26683 26684 26685 26686 26687 26688 26689 26690 26691 26692 26693 26694 26695 26696 26697 26698 26699 26700 26701
     26702 26703 26704 26705 26706 26707 26708 26709 26710 26711 26712 26713 26714 26715 26716 26717 26718 26719 26720 26721
     26722 26723 26724 26725 26726 26727 26728 26729 26730 26731 26732 26733 26734 26735 26736 26737 26738 26739 26740 26741
     26742 26743 26744 26745 26746 26747 26748 26749 26750 26751 26752 26753 26754 26755 26756 26757 26758 26759 26760 26761
     26762 26763 26764 26765 26766 26767 26768 26769 26770 26771 26772 26773 26774 26775 26776 26777 26778 26779 26780 26781
     26782 26783 26784 26785 26786 26787 26788 26789 26790 26791 26792 26793 26794 26795 26796 26797 26798 26799 26800 26801
     26802 26803 26804 26805 26806 26807 26808 26809 26810 26811 26812 26813 26814 26815 26816 26817 26818 26819 26820 26821
     26822 26823 26824 26825 26826 26827 26828 26829 26830 26831 26832 26833 26834 26835 26836 26837 26838 26839 26840 26841
     26842 26843 26844 26845 26846 26847 26848 26849 26850 26851 26852 26853 26854 26855 26856 26857 26858 26859 26860 26861
     26862 26863 26864 26865 26866 26867 26868 26869 26870 26871 26872 26873 26874 26875 26876 26877 26878 26879 26880 26881
     26882 26883 26884 26885 26886 26887 26888 26889 26890 26891 26892 26893 26894 26895 26896 26897 26898 26899 26900 26901
     26902 26903 26904 26905 26906 26907 26908 26909 26910 26911 26912 26913 26914 26915 26916 26917 26918 26919 26920 26921
     26922 26923 26924 26925 26926 26927 26928 26929 26930 26931 26932 26933 26934 26935 26936 26937 26938 26939 26940 26941
     26942 26943 26944 26945 26946 26947 26948 26949 26950 26951 26952 26953 26954 26955 26956 26957 26958 26959 26960 26961
     26962 26963 26964 26965 26966 26967 26968 26969 26970 26971 26972 26973 26974 26975 26976 26977 26978 26979 26980 26981
     26982 26983 26984 26985 26986 26987 26988 26989 26990 26991 26992 26993 26994 26995 26996 26997 26998 26999 27000 27001
     27002 27003 27004 27005 27006 27007 27008 27009 27010 27011 27012 27013 27014 27015 27016 27017 27018 27019 27020 27021
     27022 27023 27024 27025 27026 27027 27028 27029 27030 27031 27032 27033 27034 27035 27036 27037 27038 27039 27040 27041
     27042 27043 27044 27045 27046 27047 27048 27049 27050 27051 27052 27053 27054 27055 27056 27057 27058 27059 27060 27061
     27062 27063 27064 27065 27066 27067 27068 27069 27070 27071 27072 27073 27074 27075 27076 27077 27078 27079 27080 27081
     27082 27083 27084 27085 27086 27087 27088 27089 27090 27091 27092 27093 27094 27095 27096 27097 27098 27099 27100 27101
     27102 27103 27104 27105 27106 27107 27108 27109 27110 27111 27112 27113 27114 27115 27116 27117 27118 27119 27120 27121
     27122 27123 27124 27125 27126 27127 27128 27129 27130 27131 27132 27133 27134 27135 27136 27137 27138 27139 27140 27141
     27142 27143 27144 27145 27146 27147 27148 27149 27150 27151 27152 27153 27154 27155 27156 27157 27158 27159 27160 27161
     27162 27163 27164 27165 27166 27167 27168 27169 27170 27171 27172 27173 27174 27175 27176 27177 27178 27179 27180 27181
     27182 27183 27184 27185 27186 27187 27188 27189 27190 27191 27192 27193 27194 27195 27196 27197 27198 27199 27200 27201
     27202 27203 27204 27205 27206 27207 27208 27209 27210 27211 27212 27213 27214 27215 27216 27217 27218 27219 27220 27221
     27222 27223 27224 27225 27226 27227 27228 27229 27230 27231 27232 27233 27234 27235 27236 27237 27238 27239 27240 27241
     27242 27243 27244 27245 27246 27247 27248 27249 27250 27251 27252 27253 27254 27255 27256 27257 27260 27261 27262 27263
     27264 27265 27266 27267 27268


.. ansi-block::

    Found 2665 of 27269 columns with poor signal
    iterative correction
      - copying matrix
      - computing biases
    rescaling to factor 1
      - getting the sum of the matrix
        => 25100.692
      - rescaling biases
     - mouse_PSC
       * 1000000
    
      (Matrix size 2738x2738)                                                      [2020-02-06 15:41:12]
    
      - Parsing BAM (117 chunks)                                                   [2020-02-06 15:41:14]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    
      - Getting matrices                                                           [2020-02-06 15:42:22]
         .......... .......... .......... .......... ..........     50/117
         .......... .......... .......... .......... ..........    100/117
         .......... .......                                        117/117
    


.. ansi-block::

    
    WARNING: Using twice min_count as the matrix was symmetricized and contains twice as many interactions as the original
    
    WARNING: removing columns having less than 20 counts:
         1     2     3   197   198   199   380   381   382   540   541   542   543   698   699   700   850   851   852  1000
      1001  1002  1146  1147  1148  1276  1277  1278  1401  1402  1403  1532  1533  1534  1654  1655  1656  1657  1776  1777
      1778  1897  1898  1899  2022  2023  2024  2126  2127  2128  2129  2226  2227  2228  2321  2322  2323  2412  2413  2414
      2474  2475  2476  2645  2694  2695  2697  2698  2708  2709  2711  2712  2737
    
    WARNING: removing columns having less than (6950.013+0j) counts:
         1     2     3    67    68    69   197   198   199   372   373   379   380   381   382   540   541   542   543   601
       602   670   671   689   690   691   698   699   700   792   850   851   852   994   996   997   999  1000  1001  1002
      1009  1020  1021  1022  1032  1146  1147  1148  1276  1277  1278  1401  1402  1403  1532  1533  1534  1602  1605  1619
      1620  1621  1654  1655  1656  1657  1677  1775  1776  1777  1778  1897  1898  1899  1900  1950  2022  2023  2024  2126
      2127  2128  2129  2145  2226  2227  2228  2254  2260  2261  2262  2263  2321  2322  2323  2412  2413  2414  2474  2475
      2476  2477  2478  2501  2502  2503  2504  2506  2507  2508  2597  2598  2599  2622  2623  2644  2645  2649  2653  2654
      2656  2657  2658  2659  2660  2661  2662  2663  2664  2665  2666  2667  2668  2669  2670  2671  2672  2673  2674  2675
      2676  2677  2678  2679  2680  2681  2682  2683  2684  2685  2686  2687  2688  2689  2690  2691  2692  2693  2694  2695
      2696  2697  2698  2699  2700  2701  2702  2703  2704  2705  2706  2707  2708  2709  2710  2711  2712  2713  2714  2715
      2716  2717  2718  2719  2720  2721  2722  2723  2724  2725  2726  2727  2728  2729  2730  2731  2732  2733  2734  2735
      2736  2737


.. ansi-block::

    Found 202 of 2738 columns with poor signal
    iterative correction
      - copying matrix
      - computing biases
    rescaling to factor 1
      - getting the sum of the matrix
        => 2808.308
      - rescaling biases
       * 200000
    
      (Matrix size 13641x13641)                                                    [2020-02-06 15:45:25]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 15:45:26]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 15:46:31]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    


.. ansi-block::

    
    WARNING: Using twice min_count as the matrix was symmetricized and contains twice as many interactions as the original
    
    WARNING: removing columns having less than 20 counts:
         1     2     3     4     5     6     7     8     9    10    11    12    13    14    15   112   113   114   960   978
       979   980   981   982   983   984   985   986   987   988   989   990   991   992   993  1855  1856  1857  1859  1861
      1890  1891  1892  1893  1894  1895  1896  1897  1898  1899  1900  1901  1902  1903  1904  2093  2437  2690  2691  2692
      2693  2694  2695  2696  2697  2698  2699  2700  2701  2702  2703  2704  2705  2901  2902  2903  2904  2996  2997  3190
      3299  3300  3302  3344  3345  3347  3401  3402  3403  3422  3473  3474  3475  3476  3477  3478  3479  3480  3481  3482
      3483  3484  3485  3486  3487  3488  3550  3551  3949  4131  4132  4133  4134  4135  4233  4234  4235  4236  4237  4238
      4239  4240  4241  4242  4243  4244  4245  4246  4247  4248  4851  4953  4954  4977  4980  4982  4983  4984  4985  4986
      4987  4988  4989  4990  4991  4992  4993  4994  4995  4996  4997  4998  5030  5031  5091  5092  5093  5147  5678  5710
      5711  5712  5713  5714  5715  5716  5717  5718  5719  5720  5721  5722  5723  5724  5725  6358  6359  6360  6361  6362
      6363  6364  6365  6366  6367  6368  6369  6370  6371  6372  6373  6982  6983  6984  6985  6986  6987  6988  6989  6990
      6991  6992  6993  6994  6995  6996  7019  7272  7319  7635  7636  7637  7638  7639  7640  7641  7642  7643  7644  7645
      7646  7647  7648  7649  7650  7674  7987  7988  7989  8001  8004  8005  8006  8076  8246  8247  8248  8249  8250  8251
      8252  8253  8254  8255  8256  8257  8258  8259  8260  8261  8794  8848  8849  8850  8851  8852  8853  8854  8855  8856
      8857  8858  8859  8860  8861  8862  9450  9451  9452  9453  9454  9455  9456  9457  9458  9459  9460  9461  9462  9463
      9464  9465  9581  9798 10076 10077 10078 10079 10080 10081 10082 10083 10084 10085 10086 10087 10088 10089 10090 10269
     10270 10366 10367 10596 10597 10598 10599 10600 10601 10602 10603 10604 10605 10606 10607 10608 10609 10610 10611 10686
     10687 10688 10689 11074 11077 11088 11089 11090 11091 11092 11093 11094 11095 11096 11097 11098 11099 11100 11101 11102
     11103 11126 11226 11227 11228 11258 11259 11260 11261 11262 11263 11264 11273 11275 11276 11277 11278 11279 11280 11325
     11329 11330 11363 11364 11365 11366 11564 11565 11566 11567 11568 11569 11570 11571 11572 11573 11574 11575 11576 11577
     11578 11953 12018 12019 12020 12021 12022 12023 12024 12025 12026 12027 12028 12029 12030 12031 12032 12049 12050 12051
     12325 12326 12327 12328 12329 12330 12331 12332 12333 12334 12335 12336 12337 12338 12339 12340 12360 12461 12463 12471
     12472 12479 12500 12541 12628 12882 12948 12949 12950 12952 12953 12954 13030 13031 13177 13178 13181 13184 13197 13199
     13220 13236 13238 13246 13247 13248 13250 13251 13253 13254 13255 13256 13258 13259 13261 13262 13266 13267 13270 13271
     13272 13274 13275 13276 13277 13278 13279 13281 13303 13305 13306 13307 13308 13309 13312 13314 13315 13316 13317 13318
     13319 13320 13339 13366 13367 13368 13408 13411 13412 13413 13414 13416 13420 13421 13422 13423 13424 13425 13426 13427
     13428 13429 13430 13431 13432 13433 13434 13435 13436 13437 13438 13439 13440 13441 13442 13443 13444 13445 13446 13447
     13448 13449 13450 13452 13453 13455 13458 13459 13460 13461 13465 13466 13467 13468 13472 13484 13485 13486 13487 13488
     13489 13490 13491 13492 13493 13494 13495 13496 13497 13498 13499 13500 13501 13502 13503 13505 13506 13507 13508 13509
     13510 13511 13512 13513 13514 13515 13516 13517 13520 13529 13530 13531 13532 13551 13552 13568 13569 13581 13637 13638
     13639 13640
    
    WARNING: removing columns having less than (1599.084+0j) counts:
         1     2     3     4     5     6     7     8     9    10    11    12    13    14    15   112   113   114   318   329
       330   331   332   333   334   335   336   337   338   339   340   341   342   343   344   345   346   870   960   978
       979   980   981   982   983   984   985   986   987   988   989   990   991   992   993  1494  1495  1855  1856  1857
      1858  1859  1860  1861  1863  1889  1890  1891  1892  1893  1894  1895  1896  1897  1898  1899  1900  1901  1902  1903
      1904  1968  2074  2075  2076  2093  2393  2409  2436  2437  2438  2690  2691  2692  2693  2694  2695  2696  2697  2698
      2699  2700  2701  2702  2703  2704  2705  2900  2901  2902  2903  2904  2991  2992  2993  2994  2995  2996  2997  2998
      2999  3000  3001  3190  3252  3253  3255  3256  3257  3299  3300  3301  3302  3333  3334  3335  3336  3337  3338  3339
      3340  3341  3342  3343  3344  3345  3347  3348  3401  3402  3403  3408  3420  3421  3422  3430  3431  3432  3433  3434
      3435  3436  3437  3438  3439  3440  3441  3442  3443  3444  3445  3446  3473  3474  3475  3476  3477  3478  3479  3480
      3481  3482  3483  3484  3485  3486  3487  3488  3531  3532  3550  3551  3943  3944  3945  3946  3947  3948  3949  3950
      3951  3952  4131  4132  4133  4134  4135  4233  4234  4235  4236  4237  4238  4239  4240  4241  4242  4243  4244  4245
      4246  4247  4248  4648  4836  4837  4838  4851  4883  4885  4886  4952  4953  4954  4956  4957  4958  4959  4960  4961
      4962  4964  4965  4966  4967  4968  4969  4970  4971  4972  4973  4974  4975  4977  4979  4980  4982  4983  4984  4985
      4986  4987  4988  4989  4990  4991  4992  4993  4994  4995  4996  4997  4998  4999  5021  5022  5023  5026  5027  5028
      5029  5030  5031  5084  5085  5086  5087  5088  5089  5090  5091  5092  5093  5094  5095  5096  5097  5098  5113  5144
      5145  5146  5147  5148  5280  5513  5678  5710  5711  5712  5713  5714  5715  5716  5717  5718  5719  5720  5721  5722
      5723  5724  5725  5814  5818  6358  6359  6360  6361  6362  6363  6364  6365  6366  6367  6368  6369  6370  6371  6372
      6373  6980  6981  6982  6983  6984  6985  6986  6987  6988  6989  6990  6991  6992  6993  6994  6995  6996  7019  7272
      7319  7391  7635  7636  7637  7638  7639  7640  7641  7642  7643  7644  7645  7646  7647  7648  7649  7650  7674  7986
      7987  7988  7989  7990  7991  8001  8002  8003  8004  8005  8006  8051  8071  8072  8073  8074  8075  8076  8077  8078
      8079  8080  8081  8082  8083  8084  8246  8247  8248  8249  8250  8251  8252  8253  8254  8255  8256  8257  8258  8259
      8260  8261  8339  8358  8359  8360  8361  8364  8366  8367  8685  8794  8813  8814  8815  8816  8817  8818  8848  8849
      8850  8851  8852  8853  8854  8855  8856  8857  8858  8859  8860  8861  8862  9157  9450  9451  9452  9453  9454  9455
      9456  9457  9458  9459  9460  9461  9462  9463  9464  9465  9467  9468  9469  9470  9473  9474  9475  9476  9477  9478
      9479  9482  9484  9488  9581  9660  9662  9664  9665  9713  9714  9715  9716  9717  9718  9719  9720  9721  9798 10075
     10076 10077 10078 10079 10080 10081 10082 10083 10084 10085 10086 10087 10088 10089 10090 10091 10269 10270 10366 10367
     10596 10597 10598 10599 10600 10601 10602 10603 10604 10605 10606 10607 10608 10609 10610 10611 10686 10687 10688 10689
     10690 10691 11068 11069 11070 11071 11072 11073 11074 11077 11088 11089 11090 11091 11092 11093 11094 11095 11096 11097
     11098 11099 11100 11101 11102 11103 11126 11226 11227 11228 11229 11230 11231 11234 11258 11259 11260 11261 11262 11263
     11264 11265 11266 11267 11268 11270 11271 11272 11273 11274 11275 11276 11277 11278 11279 11280 11289 11325 11329 11330
     11331 11363 11364 11365 11366 11564 11565 11566 11567 11568 11569 11570 11571 11572 11573 11574 11575 11576 11577 11578
     11953 12017 12018 12019 12020 12021 12022 12023 12024 12025 12026 12027 12028 12029 12030 12031 12032 12049 12050 12051
     12204 12325 12326 12327 12328 12329 12330 12331 12332 12333 12334 12335 12336 12337 12338 12339 12340 12341 12343 12344
     12345 12346 12347 12349 12350 12351 12360 12450 12451 12452 12457 12460 12461 12462 12463 12464 12465 12466 12467 12468
     12469 12470 12471 12472 12473 12474 12475 12476 12477 12478 12479 12480 12483 12485 12486 12487 12488 12489 12490 12492
     12493 12494 12495 12496 12497 12498 12499 12500 12501 12502 12515 12541 12597 12598 12599 12600 12601 12628 12691 12844
     12882 12942 12943 12944 12945 12947 12948 12949 12950 12951 12952 12953 12954 12955 13030 13031 13065 13066 13067 13068
     13069 13071 13072 13073 13176 13177 13178 13179 13180 13181 13182 13183 13184 13195 13196 13197 13198 13199 13200 13205
     13210 13211 13212 13217 13218 13219 13220 13221 13222 13223 13224 13225 13226 13227 13228 13232 13233 13236 13237 13238
     13239 13240 13241 13242 13243 13244 13245 13246 13247 13248 13249 13250 13251 13252 13253 13254 13255 13256 13257 13258
     13259 13260 13261 13262 13263 13264 13265 13266 13267 13268 13269 13270 13271 13272 13273 13274 13275 13276 13277 13278
     13279 13280 13281 13282 13283 13284 13285 13286 13287 13288 13289 13290 13291 13292 13293 13294 13295 13296 13297 13298
     13299 13300 13301 13302 13303 13304 13305 13306 13307 13308 13309 13310 13311 13312 13313 13314 13315 13316 13317 13318
     13319 13320 13321 13322 13323 13324 13325 13326 13327 13328 13329 13330 13331 13332 13333 13334 13335 13336 13337 13338
     13339 13340 13341 13342 13343 13344 13345 13346 13347 13348 13349 13350 13351 13352 13353 13354 13355 13356 13357 13358
     13359 13360 13361 13362 13363 13364 13365 13366 13367 13368 13369 13370 13371 13372 13373 13374 13375 13376 13377 13378
     13379 13380 13381 13382 13383 13384 13385 13386 13387 13388 13389 13390 13391 13392 13393 13394 13395 13396 13397 13398
     13399 13400 13401 13402 13403 13404 13405 13406 13407 13408 13409 13410 13411 13412 13413 13414 13415 13416 13417 13418
     13419 13420 13421 13422 13423 13424 13425 13426 13427 13428 13429 13430 13431 13432 13433 13434 13435 13436 13437 13438
     13439 13440 13441 13442 13443 13444 13445 13446 13447 13448 13449 13450 13451 13452 13453 13454 13455 13456 13457 13458
     13459 13460 13461 13462 13463 13464 13465 13466 13467 13468 13469 13470 13471 13472 13473 13474 13475 13476 13477 13478
     13479 13480 13481 13482 13483 13484 13485 13486 13487 13488 13489 13490 13491 13492 13493 13494 13495 13496 13497 13498
     13499 13500 13501 13502 13503 13504 13505 13506 13507 13508 13509 13510 13511 13512 13513 13514 13515 13516 13517 13518
     13519 13520 13521 13522 13523 13524 13525 13526 13527 13528 13529 13530 13531 13532 13533 13534 13535 13536 13537 13538
     13539 13540 13541 13542 13543 13544 13545 13546 13547 13548 13549 13550 13551 13552 13553 13554 13555 13556 13557 13558
     13559 13560 13561 13562 13563 13564 13565 13566 13567 13568 13569 13570 13571 13572 13573 13574 13575 13576 13577 13578
     13579 13580 13581 13582 13583 13584 13585 13586 13587 13588 13589 13590 13591 13592 13593 13594 13595 13596 13597 13598
     13599 13600 13601 13602 13603 13604 13605 13606 13607 13608 13609 13610 13611 13612 13613 13614 13615 13616 13617 13618
     13619 13620 13621 13622 13623 13624 13625 13626 13627 13628 13629 13630 13631 13632 13633 13634 13637 13638 13639 13640
    


.. ansi-block::

    Found 1180 of 13641 columns with poor signal
    iterative correction
      - copying matrix
      - computing biases
    rescaling to factor 1
      - getting the sum of the matrix
        => 13587.267
      - rescaling biases
       * 100000
    
      (Matrix size 27269x27269)                                                    [2020-02-06 15:50:54]
    
      - Parsing BAM (122 chunks)                                                   [2020-02-06 15:50:55]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    
      - Getting matrices                                                           [2020-02-06 15:52:03]
         .......... .......... .......... .......... ..........     50/122
         .......... .......... .......... .......... ..........    100/122
         .......... .......... ..                                  122/122
    


.. ansi-block::

    
    WARNING: Using twice min_count as the matrix was symmetricized and contains twice as many interactions as the original
    
    WARNING: removing columns having less than 20 counts:
         1     2     3     4     5     6     7     8     9    10    11    12    13    14    15    16    17    18    19    20
        21    22    23    24    25    26    27    28    29    30   223   224   225   226   227   228   635  1783  1919  1920
      1921  1955  1956  1957  1958  1959  1960  1961  1962  1963  1964  1965  1966  1967  1968  1969  1970  1971  1972  1973
      1974  1975  1976  1977  1978  1979  1980  1981  1982  1983  1984  1985  2865  3182  3708  3709  3710  3711  3712  3713
      3715  3716  3717  3719  3720  3721  3725  3777  3778  3779  3780  3781  3782  3783  3784  3785  3786  3787  3788  3789
      3790  3791  3792  3793  3794  3795  3796  3797  3798  3799  3800  3801  3802  3803  3804  3805  3806  3807  3935  3936
      4148  4149  4152  4184  4185  4186  4285  4286  4870  4871  4872  4873  4875  4912  5091  5092  5378  5379  5380  5381
      5382  5383  5384  5385  5386  5387  5388  5389  5390  5391  5392  5393  5394  5395  5396  5397  5398  5399  5400  5401
      5402  5403  5404  5405  5406  5407  5408  5798  5799  5800  5801  5802  5803  5804  5805  5806  5978  5982  5988  5989
      5990  5991  5992  5993  6376  6377  6378  6594  6595  6596  6597  6598  6601  6602  6680  6681  6683  6685  6686  6687
      6688  6690  6691  6692  6693  6694  6730  6799  6800  6801  6802  6803  6804  6812  6813  6838  6840  6841  6842  6943
      6944  6945  6946  6947  6948  6949  6950  6951  6952  6953  6954  6955  6956  6957  6958  6959  6960  6961  6962  6963
      6964  6965  6966  6967  6968  6969  6970  6971  6972  6973  6974  7096  7097  7098  7099  7100  7889  7891  7892  7894
      7895  7896  7902  8259  8260  8261  8262  8263  8264  8265  8266  8267  8268  8269  8454  8455  8463  8464  8465  8466
      8467  8468  8469  8470  8471  8472  8473  8474  8475  8476  8477  8478  8479  8480  8481  8482  8483  8484  8485  8486
      8487  8488  8489  8490  8491  8492  8493  9187  9293  9365  9668  9671  9673  9698  9699  9901  9902  9903  9904  9905
      9906  9939  9946  9949  9950  9951  9955  9956  9957  9958  9960  9961  9962  9963  9964  9965  9966  9967  9968  9969
      9970  9971  9972  9973  9974  9975  9976  9977  9978  9979  9980  9981  9982  9983  9984  9985  9986  9987  9988  9989
      9990  9991  9992  9993  9995 10019 10056 10057 10058 10059 10164 10165 10168 10177 10178 10179 10180 10181 10182 10183
     10184 10185 10187 10188 10191 10192 10289 10290 10291 10352 11352 11353 11354 11416 11417 11418 11419 11420 11421 11422
     11423 11424 11425 11426 11427 11428 11429 11430 11431 11432 11433 11434 11435 11436 11437 11438 11439 11440 11441 11442
     11443 11444 11445 11446 11969 12124 12711 12712 12713 12714 12715 12716 12717 12718 12719 12720 12721 12722 12723 12724
     12725 12726 12727 12728 12729 12730 12731 12732 12733 12734 12735 12736 12737 12738 12739 12740 12741 13800 13955 13957
     13958 13959 13960 13961 13962 13963 13964 13965 13966 13967 13968 13969 13970 13971 13972 13973 13974 13975 13976 13977
     13978 13979 13980 13981 13982 13983 13984 13985 13986 13987 13988 14032 14033 14034 14537 14538 14539 14540 14631 14632
     14633 15264 15265 15266 15267 15268 15269 15270 15271 15272 15273 15274 15275 15276 15277 15278 15279 15280 15281 15282
     15283 15284 15285 15286 15287 15288 15289 15290 15291 15292 15293 15294 15295 15341 15342 15964 15967 15968 15969 15970
     15971 15972 15995 15996 15997 15998 16000 16001 16002 16003 16004 16005 16006 16007 16095 16134 16135 16136 16137 16142
     16145 16146 16147 16151 16485 16486 16487 16488 16489 16490 16491 16492 16493 16494 16495 16496 16497 16498 16499 16500
     16501 16502 16503 16504 16505 16506 16507 16508 16509 16510 16511 16512 16513 16514 16515 17037 17580 17581 17687 17688
     17689 17690 17691 17692 17693 17694 17695 17696 17697 17698 17699 17700 17701 17702 17703 17704 17705 17706 17707 17708
     17709 17710 17711 17712 17713 17714 17715 17716 17717 18892 18893 18894 18895 18896 18897 18898 18899 18900 18901 18902
     18903 18904 18905 18906 18907 18908 18909 18910 18911 18912 18913 18914 18915 18916 18917 18918 18919 18920 18921 18922
     18927 18929 19152 19153 19154 19160 19161 19422 19425 19586 19587 19588 20142 20143 20144 20145 20146 20147 20148 20149
     20150 20151 20152 20153 20154 20155 20156 20157 20158 20159 20160 20161 20162 20163 20164 20165 20166 20167 20168 20169
     20170 20171 20172 20173 20234 20529 20530 20531 20532 20533 20723 20724 20725 20726 20727 21180 21181 21183 21184 21185
     21186 21187 21188 21189 21190 21191 21192 21193 21194 21195 21196 21197 21198 21199 21200 21201 21202 21203 21204 21205
     21206 21207 21208 21209 21210 21211 21212 21213 21362 21363 21364 21365 21366 21367 21368 21369 21372 21373 22136 22137
     22138 22139 22140 22143 22144 22145 22155 22166 22167 22168 22169 22170 22171 22172 22173 22174 22175 22176 22177 22178
     22179 22180 22181 22182 22183 22184 22185 22186 22187 22188 22189 22190 22191 22192 22193 22194 22195 22196 22241 22242
     22441 22442 22443 22444 22445 22446 22505 22506 22507 22508 22509 22510 22511 22512 22513 22514 22515 22516 22517 22518
     22519 22521 22522 22532 22533 22535 22536 22538 22539 22540 22541 22542 22543 22544 22545 22546 22547 22548 22549 22550
     22551 22638 22639 22640 22647 22648 22649 22650 22651 22714 22715 22716 22717 22718 22719 22720 22721 22722 22723 23116
     23117 23118 23119 23120 23121 23122 23123 23124 23125 23126 23127 23128 23129 23130 23131 23132 23133 23134 23135 23136
     23137 23138 23139 23140 23141 23142 23143 23144 23145 23146 23230 23882 23883 23894 23895 23896 23897 24024 24025 24026
     24027 24028 24029 24030 24031 24032 24033 24034 24035 24036 24037 24038 24039 24040 24041 24042 24043 24044 24045 24046
     24047 24048 24049 24050 24051 24052 24053 24054 24066 24087 24088 24089 24090 24091 24092 24398 24639 24640 24641 24642
     24643 24644 24645 24646 24647 24648 24649 24650 24651 24652 24653 24654 24655 24656 24657 24658 24659 24660 24661 24662
     24663 24664 24665 24666 24667 24668 24669 24687 24708 24709 24889 24891 24910 24911 24913 24914 24915 24916 24924 24925
     24930 24931 24932 24933 24934 24945 24946 24947 24948 24950 24954 24958 24987 24988 24989 25013 25017 25018 25069 25070
     25071 25175 25183 25186 25189 25190 25243 25244 25245 25246 25752 25753 25754 25883 25884 25885 25886 25887 25888 25889
     25890 25892 25893 25894 25895 25896 25897 25898 26048 26049 26050 26051 26052 26341 26342 26343 26344 26345 26349 26350
     26351 26355 26356 26366 26367 26380 26381 26382 26383 26385 26386 26387 26422 26423 26424 26425 26426 26427 26428 26429
     26459 26460 26461 26463 26464 26465 26466 26467 26468 26470 26471 26472 26473 26474 26475 26476 26477 26479 26480 26481
     26482 26483 26484 26486 26487 26488 26489 26490 26493 26494 26495 26496 26497 26498 26499 26500 26501 26502 26503 26504
     26505 26506 26509 26510 26511 26512 26513 26514 26515 26516 26518 26519 26520 26521 26522 26523 26526 26527 26528 26529
     26530 26531 26532 26533 26534 26535 26536 26537 26538 26539 26540 26541 26542 26543 26544 26545 26546 26547 26549 26550
     26551 26574 26576 26591 26592 26593 26594 26595 26596 26597 26598 26599 26600 26601 26602 26603 26604 26605 26606 26607
     26608 26609 26610 26611 26612 26614 26615 26616 26617 26618 26619 26620 26621 26622 26623 26624 26625 26626 26627 26628
     26629 26631 26635 26650 26651 26653 26654 26657 26658 26659 26660 26661 26662 26664 26665 26666 26667 26668 26673 26679
     26680 26683 26685 26716 26717 26718 26719 26720 26721 26722 26723 26724 26746 26778 26780 26782 26799 26800 26801 26802
     26803 26804 26805 26806 26807 26809 26810 26811 26812 26813 26814 26815 26816 26817 26819 26820 26821 26825 26827 26828
     26829 26830 26831 26832 26833 26834 26835 26836 26837 26838 26839 26840 26841 26842 26843 26844 26845 26846 26847 26848
     26849 26850 26851 26852 26853 26854 26855 26856 26857 26858 26859 26860 26861 26862 26863 26864 26865 26866 26867 26868
     26869 26870 26871 26872 26873 26874 26875 26876 26877 26878 26879 26880 26881 26882 26883 26884 26885 26886 26887 26888
     26889 26890 26891 26892 26893 26894 26895 26897 26898 26899 26901 26902 26903 26904 26905 26906 26907 26908 26909 26910
     26911 26912 26913 26914 26916 26917 26918 26919 26920 26921 26922 26923 26924 26925 26927 26930 26931 26932 26933 26935
     26936 26937 26939 26941 26942 26946 26949 26953 26954 26955 26956 26957 26958 26959 26960 26961 26962 26963 26964 26965
     26966 26967 26968 26969 26970 26971 26972 26973 26974 26975 26976 26977 26978 26979 26980 26981 26982 26983 26984 26985
     26986 26987 26988 26989 26990 26991 26992 26993 26994 26995 26997 26998 26999 27000 27001 27002 27003 27004 27005 27006
     27007 27008 27009 27010 27011 27012 27013 27014 27015 27016 27017 27018 27019 27020 27021 27022 27023 27024 27025 27026
     27027 27028 27033 27034 27036 27037 27038 27040 27041 27042 27045 27046 27047 27048 27049 27050 27051 27052 27053 27054
     27055 27057 27059 27062 27063 27067 27069 27070 27071 27073 27074 27075 27076 27079 27080 27088 27089 27090 27091 27092
     27115 27116 27117 27118 27119 27120 27121 27122 27123 27124 27125 27126 27127 27128 27148 27149 27150 27152 27156 27158
     27204 27205 27206 27207 27208 27260 27261 27262 27263 27264 27265 27266 27267 27268
    
    WARNING: removing columns having less than (1863.967+0j) counts:
         1     2     3     4     5     6     7     8     9    10    11    12    13    14    15    16    17    18    19    20
        21    22    23    24    25    26    27    28    29    30   223   224   225   226   227   228   608   610   611   618
       619   635   636   657   658   659   660   661   662   663   664   665   666   667   668   669   670   671   672   673
       674   675   676   677   678   679   680   681   682   683   684   685   686   687   688   689   690   691   692   785
       786   787   854   855  1306  1334  1335  1738  1739  1740  1783  1919  1920  1921  1955  1956  1957  1958  1959  1960
      1961  1962  1963  1964  1965  1966  1967  1968  1969  1970  1971  1972  1973  1974  1975  1976  1977  1978  1979  1980
      1981  1982  1983  1984  1985  2859  2865  2961  2962  2986  2987  2988  2989  3182  3708  3709  3710  3711  3712  3713
      3714  3715  3716  3717  3718  3719  3720  3721  3722  3723  3724  3725  3732  3733  3776  3777  3778  3779  3780  3781
      3782  3783  3784  3785  3786  3787  3788  3789  3790  3791  3792  3793  3794  3795  3796  3797  3798  3799  3800  3801
      3802  3803  3804  3805  3806  3807  3934  3935  3936  4146  4147  4148  4149  4150  4151  4152  4183  4184  4185  4186
      4285  4286  4714  4717  4782  4783  4784  4785  4788  4789  4790  4814  4815  4816  4817  4870  4871  4872  4873  4874
      4875  4911  4912  5091  5092  5378  5379  5380  5381  5382  5383  5384  5385  5386  5387  5388  5389  5390  5391  5392
      5393  5394  5395  5396  5397  5398  5399  5400  5401  5402  5403  5404  5405  5406  5407  5408  5797  5798  5799  5800
      5801  5802  5803  5804  5805  5806  5978  5979  5980  5981  5982  5983  5984  5985  5986  5987  5988  5989  5990  5991
      5992  5993  5994  5995  5996  5997  5998  5999  6000  6050  6376  6377  6378  6500  6501  6502  6503  6504  6507  6508
      6509  6510  6511  6512  6514  6515  6594  6595  6596  6597  6598  6599  6600  6601  6602  6603  6663  6664  6665  6666
      6667  6668  6669  6670  6671  6672  6673  6674  6675  6676  6677  6678  6679  6680  6681  6682  6683  6684  6685  6686
      6687  6688  6690  6691  6692  6693  6694  6695  6730  6787  6799  6800  6801  6802  6803  6804  6805  6811  6812  6813
      6814  6821  6836  6837  6838  6839  6840  6841  6842  6845  6847  6848  6851  6852  6855  6857  6858  6859  6860  6861
      6862  6863  6864  6865  6866  6867  6868  6869  6870  6871  6872  6873  6874  6875  6876  6877  6878  6879  6880  6881
      6882  6883  6884  6885  6886  6887  6888  6889  6890  6891  6943  6944  6945  6946  6947  6948  6949  6950  6951  6952
      6953  6954  6955  6956  6957  6958  6959  6960  6961  6962  6963  6964  6965  6966  6967  6968  6969  6970  6971  6972
      6973  6974  7054  7055  7057  7058  7059  7060  7061  7062  7063  7096  7097  7098  7099  7100  7852  7880  7881  7883
      7884  7885  7886  7887  7888  7889  7890  7891  7892  7893  7894  7895  7896  7897  7898  7899  7900  7901  7902  8259
      8260  8261  8262  8263  8264  8265  8266  8267  8268  8269  8454  8455  8463  8464  8465  8466  8467  8468  8469  8470
      8471  8472  8473  8474  8475  8476  8477  8478  8479  8480  8481  8482  8483  8484  8485  8486  8487  8488  8489  8490
      8491  8492  8493  8494  8972  9187  9292  9293  9365  9366  9609  9668  9669  9670  9671  9672  9673  9698  9699  9761
      9762  9763  9764  9765  9766  9767  9768  9769  9900  9901  9902  9903  9904  9905  9906  9908  9909  9910  9911  9912
      9913  9914  9915  9916  9917  9918  9919  9920  9921  9923  9924  9925  9926  9927  9928  9929  9930  9931  9932  9933
      9934  9935  9936  9937  9938  9939  9940  9941  9942  9943  9944  9945  9946  9947  9949  9950  9951  9954  9955  9956
      9957  9958  9959  9960  9961  9962  9963  9964  9965  9966  9967  9968  9969  9970  9971  9972  9973  9974  9975  9976
      9977  9978  9979  9980  9981  9982  9983  9984  9985  9986  9987  9988  9989  9990  9991  9992  9993  9994  9995  9999
     10000 10019 10037 10038 10039 10040 10041 10042 10043 10044 10047 10048 10049 10050 10051 10052 10053 10054 10055 10056
     10057 10058 10059 10060 10061 10071 10075 10077 10078 10113 10114 10116 10136 10137 10163 10164 10165 10166 10167 10168
     10169 10170 10171 10172 10173 10174 10175 10176 10177 10178 10179 10180 10181 10182 10183 10184 10185 10186 10187 10188
     10189 10190 10191 10192 10193 10194 10222 10223 10279 10280 10281 10282 10283 10284 10285 10286 10287 10288 10289 10290
     10291 10292 10293 10348 10351 10352 10355 10436 10437 10438 10440 10556 10557 10559 10560 10564 10567 10568 11021 11022
     11023 11352 11353 11354 11416 11417 11418 11419 11420 11421 11422 11423 11424 11425 11426 11427 11428 11429 11430 11431
     11432 11433 11434 11435 11436 11437 11438 11439 11440 11441 11442 11443 11444 11445 11446 11615 11623 11624 11628 11629
     11630 11631 11632 11633 11830 11831 11968 11969 12124 12148 12149 12710 12711 12712 12713 12714 12715 12716 12717 12718
     12719 12720 12721 12722 12723 12724 12725 12726 12727 12728 12729 12730 12731 12732 12733 12734 12735 12736 12737 12738
     12739 12740 12741 13800 13954 13955 13956 13957 13958 13959 13960 13961 13962 13963 13964 13965 13966 13967 13968 13969
     13970 13971 13972 13973 13974 13975 13976 13977 13978 13979 13980 13981 13982 13983 13984 13985 13986 13987 13988 14032
     14033 14034 14537 14538 14539 14540 14631 14632 14633 14775 14776 14777 15264 15265 15266 15267 15268 15269 15270 15271
     15272 15273 15274 15275 15276 15277 15278 15279 15280 15281 15282 15283 15284 15285 15286 15287 15288 15289 15290 15291
     15292 15293 15294 15295 15341 15342 15754 15755 15847 15851 15852 15964 15965 15966 15967 15968 15969 15970 15971 15972
     15973 15974 15975 15976 15977 15994 15995 15996 15997 15998 15999 16000 16001 16002 16003 16004 16005 16006 16007 16095
     16096 16134 16135 16136 16137 16138 16139 16140 16141 16142 16143 16144 16145 16146 16147 16148 16149 16150 16151 16152
     16153 16154 16155 16156 16157 16158 16159 16160 16161 16162 16163 16307 16485 16486 16487 16488 16489 16490 16491 16492
     16493 16494 16495 16496 16497 16498 16499 16500 16501 16502 16503 16504 16505 16506 16507 16508 16509 16510 16511 16512
     16513 16514 16515 16670 16671 16672 16675 16679 16680 16681 16682 16683 16685 16686 16687 16691 16703 16705 16707 16708
     16709 16710 16711 16712 16713 16714 16715 16716 16718 16719 16720 16721 16722 16723 16724 16725 16726 16727 16728 17037
     17362 17363 17580 17581 17582 17613 17617 17618 17619 17620 17621 17622 17623 17624 17625 17626 17627 17628 17629 17687
     17688 17689 17690 17691 17692 17693 17694 17695 17696 17697 17698 17699 17700 17701 17702 17703 17704 17705 17706 17707
     17708 17709 17710 17711 17712 17713 17714 17715 17716 17717 18305 18306 18307 18342 18345 18346 18348 18349 18351 18353
     18689 18756 18883 18888 18892 18893 18894 18895 18896 18897 18898 18899 18900 18901 18902 18903 18904 18905 18906 18907
     18908 18909 18910 18911 18912 18913 18914 18915 18916 18917 18918 18919 18920 18921 18922 18923 18924 18925 18926 18927
     18928 18929 18930 18931 18932 18933 18934 18935 18936 18937 18938 18939 18940 18941 18942 18943 18944 18945 18946 18947
     18948 18949 18950 18951 18953 18954 18955 18956 18958 18959 18960 18961 18963 18964 18965 18966 18967 18968 18969 19088
     19152 19153 19154 19155 19160 19161 19307 19308 19309 19310 19311 19312 19313 19314 19315 19316 19317 19318 19319 19320
     19321 19322 19323 19327 19417 19418 19419 19420 19421 19422 19423 19424 19425 19426 19427 19428 19429 19430 19431 19432
     19433 19434 19586 19587 19588 19589 20141 20142 20143 20144 20145 20146 20147 20148 20149 20150 20151 20152 20153 20154
     20155 20156 20157 20158 20159 20160 20161 20162 20163 20164 20165 20166 20167 20168 20169 20170 20171 20172 20173 20174
     20234 20529 20530 20531 20532 20533 20723 20724 20725 20726 20727 20942 21180 21181 21183 21184 21185 21186 21187 21188
     21189 21190 21191 21192 21193 21194 21195 21196 21197 21198 21199 21200 21201 21202 21203 21204 21205 21206 21207 21208
     21209 21210 21211 21212 21213 21214 21362 21363 21364 21365 21366 21367 21368 21369 21370 21371 21372 21373 22125 22126
     22127 22128 22129 22130 22131 22132 22133 22134 22135 22136 22137 22138 22139 22140 22143 22144 22145 22155 22165 22166
     22167 22168 22169 22170 22171 22172 22173 22174 22175 22176 22177 22178 22179 22180 22181 22182 22183 22184 22185 22186
     22187 22188 22189 22190 22191 22192 22193 22194 22195 22196 22230 22241 22242 22243 22247 22397 22441 22442 22443 22444
     22445 22446 22447 22448 22449 22450 22451 22452 22453 22454 22455 22456 22457 22458 22504 22505 22506 22507 22508 22509
     22510 22511 22512 22513 22514 22515 22516 22517 22518 22519 22520 22521 22522 22523 22524 22525 22526 22528 22529 22530
     22531 22532 22533 22534 22535 22536 22537 22538 22539 22540 22541 22542 22543 22544 22545 22546 22547 22548 22549 22550
     22551 22566 22567 22568 22638 22639 22640 22647 22648 22649 22650 22651 22652 22714 22715 22716 22717 22718 22719 22720
     22721 22722 22723 23116 23117 23118 23119 23120 23121 23122 23123 23124 23125 23126 23127 23128 23129 23130 23131 23132
     23133 23134 23135 23136 23137 23138 23139 23140 23141 23142 23143 23144 23145 23146 23230 23882 23883 23894 23895 23896
     23897 24023 24024 24025 24026 24027 24028 24029 24030 24031 24032 24033 24034 24035 24036 24037 24038 24039 24040 24041
     24042 24043 24044 24045 24046 24047 24048 24049 24050 24051 24052 24053 24054 24055 24066 24087 24088 24089 24090 24091
     24092 24397 24398 24399 24639 24640 24641 24642 24643 24644 24645 24646 24647 24648 24649 24650 24651 24652 24653 24654
     24655 24656 24657 24658 24659 24660 24661 24662 24663 24664 24665 24666 24667 24668 24669 24670 24671 24673 24674 24675
     24676 24677 24678 24679 24680 24681 24682 24683 24684 24685 24686 24687 24688 24689 24690 24691 24692 24693 24708 24709
     24884 24885 24886 24887 24888 24889 24890 24891 24892 24893 24894 24895 24896 24897 24898 24899 24900 24901 24902 24903
     24904 24905 24906 24907 24908 24909 24910 24911 24912 24913 24914 24915 24916 24917 24918 24919 24920 24921 24922 24923
     24924 24925 24926 24927 24928 24929 24930 24931 24932 24933 24934 24935 24936 24937 24938 24939 24940 24941 24942 24943
     24944 24945 24946 24947 24948 24949 24950 24951 24953 24954 24955 24956 24958 24959 24960 24961 24962 24963 24964 24965
     24966 24967 24968 24969 24970 24971 24972 24973 24974 24975 24976 24977 24978 24979 24980 24981 24982 24983 24984 24985
     24986 24987 24988 24989 24990 24991 24992 24993 24994 25013 25014 25015 25016 25017 25018 25019 25069 25070 25071 25072
     25175 25176 25182 25183 25184 25185 25186 25187 25188 25189 25190 25191 25192 25193 25194 25197 25243 25244 25245 25246
     25362 25363 25370 25371 25372 25388 25589 25676 25677 25751 25752 25753 25754 25871 25872 25873 25874 25875 25876 25877
     25878 25879 25880 25882 25883 25884 25885 25886 25887 25888 25889 25890 25891 25892 25893 25894 25895 25896 25897 25898
     25899 25900 25907 25991 25993 25994 26003 26048 26049 26050 26051 26052 26118 26119 26120 26121 26122 26123 26124 26125
     26126 26127 26128 26129 26130 26131 26132 26133 26134 26135 26136 26137 26138 26139 26140 26180 26340 26341 26342 26343
     26344 26345 26346 26347 26348 26349 26350 26351 26352 26353 26354 26355 26356 26357 26366 26367 26376 26377 26378 26379
     26380 26381 26382 26383 26384 26385 26386 26387 26388 26391 26392 26395 26396 26397 26398 26399 26400 26401 26402 26403
     26405 26406 26407 26408 26409 26410 26411 26412 26413 26414 26415 26416 26417 26418 26419 26420 26421 26422 26423 26424
     26425 26426 26427 26428 26429 26430 26431 26432 26433 26434 26435 26436 26437 26438 26439 26440 26441 26442 26443 26444
     26445 26446 26447 26448 26449 26450 26451 26452 26453 26454 26455 26456 26457 26458 26459 26460 26461 26462 26463 26464
     26465 26466 26467 26468 26469 26470 26471 26472 26473 26474 26475 26476 26477 26478 26479 26480 26481 26482 26483 26484
     26485 26486 26487 26488 26489 26490 26491 26492 26493 26494 26495 26496 26497 26498 26499 26500 26501 26502 26503 26504
     26505 26506 26507 26508 26509 26510 26511 26512 26513 26514 26515 26516 26517 26518 26519 26520 26521 26522 26523 26524
     26525 26526 26527 26528 26529 26530 26531 26532 26533 26534 26535 26536 26537 26538 26539 26540 26541 26542 26543 26544
     26545 26546 26547 26548 26549 26550 26551 26552 26553 26554 26555 26556 26557 26558 26559 26560 26561 26562 26563 26564
     26565 26566 26567 26568 26569 26570 26571 26572 26573 26574 26575 26576 26577 26578 26579 26580 26581 26582 26583 26584
     26585 26586 26587 26588 26589 26590 26591 26592 26593 26594 26595 26596 26597 26598 26599 26600 26601 26602 26603 26604
     26605 26606 26607 26608 26609 26610 26611 26612 26613 26614 26615 26616 26617 26618 26619 26620 26621 26622 26623 26624
     26625 26626 26627 26628 26629 26630 26631 26632 26633 26634 26635 26636 26637 26638 26639 26640 26641 26642 26643 26644
     26645 26646 26647 26648 26649 26650 26651 26652 26653 26654 26655 26656 26657 26658 26659 26660 26661 26662 26663 26664
     26665 26666 26667 26668 26669 26670 26671 26672 26673 26674 26675 26676 26677 26678 26679 26680 26681 26682 26683 26684
     26685 26686 26687 26688 26689 26690 26691 26692 26693 26694 26695 26696 26697 26698 26699 26700 26701 26702 26703 26704
     26705 26706 26707 26708 26709 26710 26711 26712 26713 26714 26715 26716 26717 26718 26719 26720 26721 26722 26723 26724
     26725 26726 26727 26728 26729 26730 26731 26732 26733 26734 26735 26736 26737 26738 26739 26740 26741 26742 26743 26744
     26745 26746 26747 26748 26749 26750 26751 26752 26753 26754 26755 26756 26757 26758 26759 26760 26761 26762 26763 26764
     26765 26766 26767 26768 26769 26770 26771 26772 26773 26774 26775 26776 26777 26778 26779 26780 26781 26782 26783 26784
     26785 26786 26787 26788 26789 26790 26791 26792 26793 26794 26795 26796 26797 26798 26799 26800 26801 26802 26803 26804
     26805 26806 26807 26808 26809 26810 26811 26812 26813 26814 26815 26816 26817 26818 26819 26820 26821 26822 26823 26824
     26825 26826 26827 26828 26829 26830 26831 26832 26833 26834 26835 26836 26837 26838 26839 26840 26841 26842 26843 26844
     26845 26846 26847 26848 26849 26850 26851 26852 26853 26854 26855 26856 26857 26858 26859 26860 26861 26862 26863 26864
     26865 26866 26867 26868 26869 26870 26871 26872 26873 26874 26875 26876 26877 26878 26879 26880 26881 26882 26883 26884
     26885 26886 26887 26888 26889 26890 26891 26892 26893 26894 26895 26896 26897 26898 26899 26900 26901 26902 26903 26904
     26905 26906 26907 26908 26909 26910 26911 26912 26913 26914 26915 26916 26917 26918 26919 26920 26921 26922 26923 26924
     26925 26926 26927 26928 26929 26930 26931 26932 26933 26934 26935 26936 26937 26938 26939 26940 26941 26942 26943 26944
     26945 26946 26947 26948 26949 26950 26951 26952 26953 26954 26955 26956 26957 26958 26959 26960 26961 26962 26963 26964
     26965 26966 26967 26968 26969 26970 26971 26972 26973 26974 26975 26976 26977 26978 26979 26980 26981 26982 26983 26984
     26985 26986 26987 26988 26989 26990 26991 26992 26993 26994 26995 26996 26997 26998 26999 27000 27001 27002 27003 27004
     27005 27006 27007 27008 27009 27010 27011 27012 27013 27014 27015 27016 27017 27018 27019 27020 27021 27022 27023 27024
     27025 27026 27027 27028 27029 27030 27031 27032 27033 27034 27035 27036 27037 27038 27039 27040 27041 27042 27043 27044
     27045 27046 27047 27048 27049 27050 27051 27052 27053 27054 27055 27056 27057 27058 27059 27060 27061 27062 27063 27064
     27065 27066 27067 27068 27069 27070 27071 27072 27073 27074 27075 27076 27077 27078 27079 27080 27081 27082 27083 27084
     27085 27086 27087 27088 27089 27090 27091 27092 27093 27094 27095 27096 27097 27098 27099 27100 27101 27102 27103 27104
     27105 27106 27107 27108 27109 27110 27111 27112 27113 27114 27115 27116 27117 27118 27119 27120 27121 27122 27123 27124
     27125 27126 27127 27128 27129 27130 27131 27132 27133 27134 27135 27136 27137 27138 27139 27140 27141 27142 27143 27144
     27145 27146 27147 27148 27149 27150 27151 27152 27153 27154 27155 27156 27157 27158 27159 27160 27161 27162 27163 27164
     27165 27166 27167 27168 27169 27170 27171 27172 27173 27174 27175 27176 27177 27178 27179 27180 27181 27182 27183 27184
     27185 27186 27187 27188 27189 27190 27191 27192 27193 27194 27195 27196 27197 27198 27199 27200 27201 27202 27203 27204
     27205 27206 27207 27208 27209 27210 27211 27212 27213 27214 27215 27216 27217 27218 27219 27220 27221 27222 27223 27224
     27225 27226 27227 27228 27229 27230 27231 27232 27233 27234 27235 27236 27237 27238 27239 27240 27241 27242 27243 27244
     27245 27246 27247 27248 27249 27250 27251 27252 27253 27254 27255 27256 27257 27260 27261 27262 27263 27264 27265 27266
     27267 27268


.. ansi-block::

    Found 2722 of 27269 columns with poor signal
    iterative correction
      - copying matrix
      - computing biases
    rescaling to factor 1
      - getting the sum of the matrix
        => 26205.477
      - rescaling biases


