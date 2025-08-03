---
template: overrides/main.html
title: 
---

## Scene Generation

Copy the following files to a subject folder within a new derivatives folder named `slicer_scene`:

```java
derivatives/
	└── atlasreg/
		└── sub-<subject_label>/
				├── qc/...
				├── sub-<subject_label>_acq-contrast_T1w.nii.gz
				├── sub-<subject_label>_acq-noncontrast_space-T1w_desc-affine_T1w.nii.gz
				├── sub-<sub_label>_space-T1w_desc-rigid_ses-post_ct.nii.gz
				├── sub-<sub_label>_acq-nac_space-T1w_desc-rigid_pet.nii.gz
				├── sub-<sub_label>_acq-ac_space-T1w_desc-rigid_pet.nii.gz
				├── sub-<sub_label>_desc-rigid_from-noncontrast_to-contrast_type-ras_xfm.tfm
				├── sub-<sub_label>_desc-nonlin_atlas-CerebrAThomasMiddle_from-MNI152NLin2009cSym_dseg.seg.nrrd
```

Within 3D Slicer, create the following markups lists and save them in the `slicer_scene` subject folder:

```java
derivatives/
	└── slicer_scene/
		└── sub-<subject_label>/
				├── sub-<sub_label>_acpc.fcsv
				├── sub-<sub_label>_actual.fcsv
				├── sub-<sub_label>_planned.fcsv
				├── sub-<sub_label>_SEEGA.fcsv
				├── ...
```


