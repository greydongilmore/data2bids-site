---
template: main.html
title: 
---

## ieegProc output

The output files from _ieegProc_ conform to the <a href="https://bids-specification.readthedocs.io/en/stable/05-derivatives/01-introduction.html" target="_blank">BIDS Derivatives specification</a>. In general, there are two classes of data outputs from _ieegProc_:

1. **Visual QA (quality assessment):** summary figures are generated throughout the pipeline so the user can confirm accuracy.

2. **Derivative (preprocessed):** processed data that is ready for analysis after various preparation procedures have been applied (i.e. INU-corrected versions of the T1-weighted image, the brain mask, electrode contact coordinate files etc). All data is aligned into the same-subject’s T1w space.


The _ieegProc_ output directory has the following structure:

```
derivatives/
	├── atlasreg/
	│		├── dataset_description.json
	│		└── sub-<subject_label>/
	│				├── qc/...
	│				└── <processed_files>...
	│
	└── seeg_coordinates/
			├── dataset_description.json
			└── sub-P001/
					└── <processed_files>...
```

For each participant in the dataset, a subject specific directory (`sub-<subject_label>`) will be generated in two locations:

1. `atlasreg`: this directory contains processed data files from the imaging analysis pipelines.
2. `seeg_coordinates`: this directory will store the electrode contact coordinates in several coordinate spaces (see ). 

The `dataset_description.json` is a metadata file in which ieegProc records metadata required by the BIDS standard.


## Visual QA

*ieegProc* generates summary figures which are stored in a directory labelled `qc` within each patient directory under `atlasreg`. The figures provide a snapshot of the results from various parts of the pipeline.

```java
derivatives/
	└── atlasreg/
		└── sub-<subject_label>/
				└── qc/...
```

## atlasreg derivatives

### Step 1: input image volumes

**ieegProc** begins by copying the relevant imaging volumes from the `bids` directory to the `atlasreg` directory. **ieegProc** will search for four image volumes as input. At minimum, **ieegProc** requires a T1w volume and a post-op CT to run.

- **T1w** with gadolinium
- **T1w** without gadolinium
- post-op **CT** containing electrodes/grids/strips
- **PET**

After copying, each volume is renamed accordinly:

- `sub-<subject_label>_acq-contrast_T1w.nii.gz`: **T1w** gad-enhanced
- `sub-<subject_label>_acq-noncontrast_T1w.nii.gz`: **T1w** non-enhanced
- `sub-<subject_label>_ct.nii.gz`: post-op **CT** containing electrodes/grids/strips
- `sub-<subject_label>_pet.nii.gz`: **PET**

Execpt for the filename, these volumes will remain an identical copy to the ones found in the `bids` directory.

```py
derivatives/
	└── atlasreg/
		└── sub-<subject_label>/
				├── qc/...
				├── sub-<subject_label>_acq-contrast_T1w.nii.gz
				├── sub-<subject_label>_acq-noncontrast_T1w.nii.gz
				├── sub-<subject_label>_ct.nii.gz
				└── sub-<subject_label>_pet.nii.gz
```

### Step 2: rigid registration

In sterotaxy, gadolinium contrast provides simultaneous enhancement of vessels and gray/white matter contrast within a single T1w volume. Preoperative trajectory planning is done on the enhanced T1w volume. Thus, this volume is chosen first as the fixed image during registration. If an enhanced T1w volume doesn't exists, then the non-enhanced will be used as the fixed image.

Currently, two registration algorithms are included:

1. `reg_aladin`: command-line function from NiftyReg, developed by Marc Modat ([NiftyReg](https://sourceforge.net/projects/niftyreg/){target=_blank})
2. `greedy`: command-line function for fast image registration, developed by Paul Yushkevich ([Greedy](https://sites.google.com/view/greedyreg/about){target=_blank})

For each floating volume (`<volume_type>`), the rigid registration step will generate the following files:

* `sub-<subject_label>_space-T1w_desc-rigid_<volume_type>.nii.gz`: floating volume with the rigid transform applied without interpolation.
* `sub-<subject_label>_space-T1w_desc-rigidInterp_<volume_type>.nii.gz`: floating volume with the rigid transform applied with linear interpolation.
* `sub-<subject_label>_desc-rigid_from-<volume_type>_to-T1w_type-ras_xfm.txt`: rigid transform matrix from floating to fixed image space in RAS `.txt` format.
* `sub-<subject_label>_desc-rigid_to-<volume_type>_from-T1w_type-ras_xfm.txt`: rigid transform matrix to floating from fixed image space in RAS `.txt` format.
* `sub-<subject_label>_desc-rigid_from-<volume_type>_to-T1w_type-ras_xfm.tfm`: rigid transform matrix from floating to fixed image space in 3D Slicer `.tfm` format.


!!! Note
	Since there can be up to two T1w volumes, *ieegProc* will copy one of the volumes to a new file labelled `sub-<subject_label>_T1w.nii.gz`. From this point onward, any step requiring a T1w volume as input will link to this new volume. If only one T1w volume exists, then `sub-<subject_label>_T1w.nii.gz` will be an identical copy. If both gad-enhanced and non-enhanced T1w volumes exist then the non-enhanced volume will be copied to `sub-<subject_label>_T1w.nii.gz`.

### Step 3: affine/non-linear registration

*ieegProc* will perform template space normalization using the newly renamed T1w volume (see note above) as the floating image. For the affine registration step, either `reg_aladin` or `greedy` can be used. For the non-linear registration step, either `ANTS` or `greedy` can be used. These options can be configured in the `config.yml` file.

After both registration steps, the following files will be generated:

* `sub-<subject_label>_desc-n4_T1w.nii.gz`: T1w volume with `N4BiasFieldCorrection` performed.
* `sub-<subject_label>_space-<template_space>_desc-affine_T1w.nii.gz`: T1w volume with affine transform applied to template space with linear interpolation.
* `sub-<subject_label>_desc-affine_from-subject_to-<template_space>_type-ras_xfm.txt`: affine transform matrix from subject space to template space in RAS `.txt` format.
* `sub-<subject_label>_desc-affine_to-subject_from-<template_space>_type-ras_xfm.txt`: affine transform matrix from template space to subject space in RAS `.txt` format.
* `sub-<subject_label>_desc-affine_from-subject_to-<template_space>_type-itk_xfm.txt`: affine transform matrix from subject space to template space in ITK `.txt` format.
* `sub-<subject_label>_desc-affine_from-subject_to-<template_space>_type-itk_xfm.tfm`: affine transform matrix from subject space to template space in 3D Slicer `.tfm` format.

### Step 4: tissue segmentation

*ieegProc* will perform image segmentation of three structures within the T1w volume: white matter, gray matter, and CSF. To improve segmenation outcomes, the chosen template space brain mask and WM/GM/CSF probabilistic tissue segmentations are warped to T1w volume space. This helps to highlight the areas that are brain and not skull/dura/etc.

* `sub-<subject_label>_label-brain_desc-affine_from-<template_space>_mask.nii.gz`: template space brain mask with the affine transform applied to T1w volume space.
* `sub-<subject_label>_label-<CSF/WM/GM>_desc-affine_from-<template_space>_probseg.nii.gz`: template space probabilistic segmentations with the affine transform applied to T1w volume space.

After the segmentation step, the following files will be generated:

* `sub-<subject_label>_desc-atropos3seg_mapping.json`: 
* `sub-P033_desc-atroposKseg_dseg.nii.gz`: 
* `sub-P033_desc-atroposKseg_probseg.nii.gz`:




```java
derivatives/
	└── atlasreg/
		└── sub-<subject_label>/
				├── qc/...
				├── sub-<subject_label>_acq-contrast_T1w.nii.gz
				├── sub-<subject_label>_acq-noncontrast_T1w.nii.gz
				├── sub-<subject_label>_ct.nii.gz
				├── sub-<subject_label>_acq-noncontrast_space-T1w_desc-affine_T1w.nii.gz
				├── sub-<subject_label>_T1w.nii.gz
				├── sub-<subject_label>_space-MNI152NLin2009cSym_desc-affine_T1w.nii.gz
				├── sub-<subject_label>_desc-brain_from-MNI152NLin2009cSym_reg-affine_mask.nii.gz
				├── sub-<subject_label>_label-CSF_from-MNI152NLin2009cSym_reg-affine_probseg.nii.gz
				├── sub-<subject_label>_label-GM_from-MNI152NLin2009cSym_reg-affine_probseg.nii.gz
				├── sub-<subject_label>_label-WM_from-MNI152NLin2009cSym_reg-affine_probseg.nii.gz
				├── sub-<subject_label>_space-T1w_desc-affine_ct.nii.gz
				├── sub-<subject_label>_space-ct_desc-mask_contacts.nii.gz
				├── sub-<subject_label>_desc-n4_T1w.nii.gz
				├── sub-<subject_label>_desc-atroposKseg_dseg.nii.gz
				├── sub-<subject_label>_desc-atroposKseg_probseg.nii.gz
				├── sub-<subject_label>_label-CSF_desc-atropos3seg_probseg.nii.gz
				├── sub-<subject_label>_label-GM_desc-atropos3seg_probseg.nii.gz
				├── sub-<subject_label>_label-WM_desc-atropos3seg_probseg.nii.gz
				├── sub-<subject_label>_desc-atropos3seg_probseg.nii.gz
				├── sub-<subject_label>_desc-brain_from-atropos3seg_mask.nii.gz
				├── sub-<subject_label>_desc-masked_from-atropos3seg_T1w.nii.gz
				├── sub-<subject_label>_space-MNI152NLin2009cSym_desc-SyN_T1w.nii.gz
				├── sub-<subject_label>_atlas-CerebrA_from-MNI152NLin2009cSym_reg-SyN_dseg.nii.gz
				└── sub-<subject_label>_desc-dilated_atlas-CerebrA_from-MNI152NLin2009cSym_reg-SyN_dseg.nii.gz
```

Spatially-standardized derivatives are denoted with a `space-` label. Within patient volumes are registered to the volumetric T1w so the space is denoated `space-T1w` for these volumes. Other derivatives will have been coregistered to a template space, such as `MNI152NLin2009cSym`.

The registration transform matricies are stored in `.txt` and `.tfm` files to allow for easy loading into 3D Slicer.

```java
derivatives/
	└── atlasreg/
		└── sub-<subject_label>/
				├── qc/...
				├── sub-<subject_label>_acq-noncontrast_desc-affine_from-T1w_to-T1w_type-ras_xfm.txt
				├── sub-<subject_label>_desc-affine_from-subject_to-MNI152NLin2009cSym_type-itk_xfm.txt
				├── sub-<subject_label>_desc-affine_from-subject_to-MNI152NLin2009cSym_type-ras_xfm.txt
				├── sub-<subject_label>_desc-affine_from-ct_to-T1w_type-ras_xfm.txt
				├── sub-<subject_label>_from-subject_to-MNI152NLin2009cSym_Composite.h5
				└── sub-<subject_label>_from-subject_to-MNI152NLin2009cSym_InverseComposite.h5
```

The remaining files provide information about the pipeline.

```java
derivatives/
	└── atlasreg/
		└── sub-<subject_label>/
				├── qc/...
				├── sub-<subject_label>_desc-atropos3seg_mapping.json
				├── sub-<subject_label>_desc-dilated_atlas-CerebrA_from-MNI152NLin2009cSym_electrodes.tsv
				└── sub-<subject_label>_desc-dilated_atlas-CerebrA_from-MNI152NLin2009cSym_electrodes.xlsx
```

The files within the `qc` derivatives directory are:

```
derivatives/
	└── atlasreg/
		└── sub-<subject_label>/
				└── qc/
					├── sub-<subject_label>_acq-contrast_from-T1w_to-T1w_regqc.png
					├── sub-<subject_label>_desc-masked_from-ct_to-T1w_regqc.png
					├── sub-<subject_label>_desc-affine_from-subject_to-MNI152NLin2009cSym_regqc.html
					├── sub-<subject_label>_desc-affine_from-subject_to-MNI152NLin2009cSym_regqc.png
					├── sub-<subject_label>_desc-atropos3seg_probseg.png
					├── sub-<subject_label>_atlas-CerebrA_from-MNI152NLin2009cSym_dseg.html
					├── sub-<subject_label>_atlas-CerebrA_from-MNI152NLin2009cSym_dseg.png
					├── sub-<subject_label>_desc-dilated_atlas-CerebrA_from-MNI152NLin2009cSym_dseg.png
					├── sub-<subject_label>_space-ct_desc-mask_contacts.html
					├── sub-<subject_label>_space-MNI152NLin2009cSym_desc-affine_electrodes.html
					└── sub-<subject_label>_space-MNI152NLin2009cSym_desc-affine_electrodevis.png
```


### seeg_coordinates derivatives

The files within the `seeg_coordinates` derivatives directory are:

```
derivatives/
	└── seeg_coordinates/
		└── sub-<subject_label>/
				├── sub-<subject_label>_mapping.tsv
				├── sub-<subject_label>_space-T1w_mcp.tfm
				├── sub-<subject_label>_space-acpc_actual.fcsv
				├── sub-<subject_label>_space-acpc_actual.tsv
				├── sub-<subject_label>_space-acpc_planned.fcsv
				├── sub-<subject_label>_space-acpc_planned.tsv
				├── sub-<subject_label>_space-acpc_SEEGA.fcsv
				├── sub-<subject_label>_space-acpc_SEEGA.tsv
				├── sub-<subject_label>_space-native_actual.fcsv
				├── sub-<subject_label>_space-native_actual.tsv
				├── sub-<subject_label>_space-native_planned.fcsv
				├── sub-<subject_label>_space-native_planned.tsv
				├── sub-<subject_label>_space-native_SEEGA.fcsv
				└── sub-<subject_label>_space-native_SEEGA.tsv
```

## Sample QA figures

### Coregistration of T1w without contrast to T1w+gadolinum

<center><img src="imgs/sub-P078_desc-masked_from-noncontrast_to-contrast_regqc.svg" alt="drawing"/></center>
<br><br>

### Coregistration of post-op CT to T1w+gadolinum

<center><img src="imgs/sub-P078_desc-masked_from-ct_to-T1w_regqc.svg" alt="drawing"/></center>
<br><br>

### Electrode contact positions in 2D and 3D

<center><img src="imgs/sub-P078_space-MNI152NLin2009cSym_desc-affine_electrodevis.png" alt="drawing"/></center>



<center><embed type="text/html" src="imgs/sub-P078_space-MNI152NLin2009cSym_desc-affine_electrodes.html" width="900" height="700"/></center>
