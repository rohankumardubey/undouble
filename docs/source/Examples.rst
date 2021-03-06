.. _code_directive:

-------------------------------------

Flowers
''''''''''

Analyze for similar flowers in the example dataset.

.. code:: python

    # Import library
    from undouble import Undouble
    
    # Init with default settings
    model = Undouble(method='phash', hash_size=8)
    
    # Import example data
    targetdir = model.import_example(data='flowers')
    
    # Importing the files files from disk, cleaning and pre-processing
    model.import_data(targetdir)
    
    # Compute image-hash
    model.fit_transform()
    
    # Group images with image-hash <= threshold
    model.group(threshold=0)
    
    # Plot the images
    model.plot()


.. |flower_group1| image:: ../figs/flowers1.png
.. |flower_group2| image:: ../figs/flowers2.png
.. |flower_group3| image:: ../figs/flowers3.png

.. table:: Groupings
   :align: center

   +------------------+
   | |flower_group1|  |
   +------------------+
   | |flower_group2|  |
   +------------------+
   | |flower_group3|  |
   +------------------+



Get identical images
''''''''''''''''''''''''

.. code:: python

    # Import library
    for i, group in enumerate(model.results['select_pathnames']):
        print('----------------------------GROUP %s----------------------------' %i)
        print(group)


Move files
''''''''''''''''''''''''

The move function :func:`undouble.undouble.Undouble.move` will systematically move the images.
A threshold of 0 will group images with an identical image hash. However, the threshold of 10 showed the best results when undoubling my personal photo deck because photos, such as from bursts, were also grouped.
Before moving any of the images, the resolution and blurness of all images that are part of a group are checked.
The image in the group with the highest resolution will be copied, and all other images are moved to the **undouble** subdirectory.

.. code:: python

    model.move()
    
    # >Wait! Before you continue, you are at the point of physically moving files!
    # >[7] similar images are detected over [3] groups.
    # >[4] images will be moved to the [undouble] directory.
    # >[3] images will be copied to the [undouble] directory.
    # >Type <ok> to proceed.


101 objects dataset
''''''''''''''''''''

I utilized the Caltech 101 [1] dataset and saved it to my local disk. I will analyze the results with aHash, pHash, dHash, and Wavelet hash.
The Caltech dataset contains 9144 real-world images belonging to 101 categories. About 40 to 800 images per category.
The size of each image is roughly 300 x 200 pixels. For the input to undouble, we can simply provide the path location
where all images are stored, and all subdirectories will be recursively analyzed too.
Note that this dataset does not contain ground truth labels with identical images labels.

.. code:: python

    # Import library
    from undouble import Undouble
    # Initialize model
    model = Undouble(method='phash', hash_size=8)
    # Import example data
    targetdir = './101_ObjectCategories'
    # Importing the files files from disk, cleaning and pre-processing
    model.import_data(targetdir)
    # Compute image-hash
    model.fit_transform()
    
    # [undouble] >INFO> Extracting images from: [D://magweg/101_ObjectCategories]
    # [undouble] >INFO> [9144] files are collected recursively from path: [D://magweg/101_ObjectCategories]
    # [undouble] >INFO> [9144] images are extracted.
    # [undouble] >INFO> Reading and checking images.
    # [undouble] >INFO> Reading and checking images.
    # 100%|??????????????????????????????| 9144/9144 [00:29<00:00, 305.49it/s]
    # [undouble] >INFO> Extracting features using method: [ahash]
    # 100%|??????????????????????????????| 9144/9144 [00:01<00:00, 5826.17it/s]
    # [undouble] >INFO> Build adjacency matrix with ahash differences.
    
    # Group images that are identical in image-hash, i.e. those with a hash difference of 0.
    model.group(threshold=0)
    
    # print([*model.results.keys()])
    #   * img: Preprocessed images
    #   * pathnames: Absolute path location to image file
    #   * filenames: Filename
    #   * select_pathnames: Selected path locations that have image-hash score <= threshold
    #   * select_scores: Image-hash scores of the selected images
    
    # Plot the marked images
    model.plot()
    
    # Move the files
    model.move()
    

Average hash
--------------

The average hash function detected 135 groups that could be linked to 335 images with an identical hash (threshold=0). Despite identical images being detected, most of the groups showed collisions such as the top and bottom left, and/or near-identical images, such as the motorbikes.


.. |ahash_101objects| image:: ../figs/ahash_101objects.png

.. table:: Average hash
   :align: center

   +---------------------+
   | |ahash_101objects|  |
   +---------------------+


Differential hash
------------------

The differential hash function detected 28 images that could be linked to 31 images with an identical hash (threshold=0). A visual inspection showed no collisions but near-identical images (two motorbikes) were detected.


.. |dhash_101objects| image:: ../figs/dhash_101objects.png

.. table:: Differential hash
   :align: center

   +---------------------+
   | |dhash_101objects|  |
   +---------------------+


Perceptual hash
------------------

The perceptual hash function detected 38 groups that could be linked to 41 images with an identical hash (threshold=0). A visual inspection showed no collisions but near-identical images were detected, such as the motorbikes.


.. |phash_101objects| image:: ../figs/phash_101objects.png

.. table:: Perceptual hash
   :align: center

   +---------------------+
   | |phash_101objects|  |
   +---------------------+


Haar wavelet??hash
------------------

The wavelet hash function detected 141 groups that could be linked to 513 images with an identical hash (threshold=0). A visual inspection showed that almost all groups contained either collisions or near-identical images (Figure 12). Who had known that a strawberry could have a similar image-hash as the motorbike?


.. |wave_101objects| image:: ../figs/wave_101objects.png

.. table:: Haar wavelet??hash
   :align: center

   +---------------------+
   | |wave_101objects|   |
   +---------------------+



References
''''''''''''''''''''

    * [1] L. Fei-Fei, R. Fergus, and P. Perona. Learning generative visual models from few training examples: an incremental Bayesian approach tested on 101 object categories. IEEE. CVPR 2004, Workshop on Generative-Model Based Vision. 2004