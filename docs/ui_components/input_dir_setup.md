---
template: overrides/main.html
---

# Input Directory Setup

This section outlines how the data should be organized prior to running the EDF conversion software. The data files should be in EDF/EDF+ format following the [specifications](https://www.edfplus.info/specs/edf.html) provided by the EDF developers. There are two ways to organize the directory structure  prior to running the software. The root directory should contain a sub-directory for each of the  subjects you want to have converted. Note that at this moment the names of the EDF/EDF+ files are not yet BIDS compliant, but they do contain some metadata in the filename that will be used later.

## Setup a working directory

It is recommended that you establish a working directory to ensure your data remains organized. The optimal setup would be one with 4 directories:

* **unorganized**: this directory holds unorganized subject data ready to be organized for **edf2bids**
* **organized**: this directory holds organized subject data ready to be organized for **edf2bids** (according to either option below)
* **input**: this directory holds subject data ready to be converted with **edf2bids**
* **output**: this directory holds the output converted data from **edf2bids**

```sh

working_dir/
├── unorganized/
├── organized/
├── input/
└── output/

```

## Option 01: Do not specify visit/session number

If you do not need to specify the visit or session number for each EDF file for the subjects, then this option will assign session numbers based on the **Date** timestamp within the EDF files. So the earliest EDF file will be given **ses-001** while the latest EDF file will be given **ses-###** (### will be equal to the number of EDF files for that subject).

### Example

```sh

input/
├── <sub_num>/                                          # Individual subject directory
│   ├── X~X_432a35cf-adg25-462-24aa-325db4e5e2d3.edf    # Individual EDF files
│   └── X~Xe_7d22151a-ac455-3adc312b-426aae3251ac.edf
│
└── <sub_num>/                                          # Individual subject directory
    ├── X_ X_35a1ed7a-7764-4cb0-8571-51026e3dbef4.edf   # Individual EDF files
    └── X_X_e515c5ac-6301-4acd-8a69-fb208d5fd097.edf

```

!!! warning
    When beginning a conversion with **edf2bids** ensure the **output** directory is empty and the **input** directory only contains the subject folders you wish to convert.

## Option 02: Specify visit/session number

In some instances you may want to manually assign the specific visit or session numbers for the EDF files. In this scenario you would need to place each EDF file into a directory with the following naming scheme:

```sh
<sub_num>_<visit_num>_<ses_num>_<type>_<task>_[RET]
```

  * **sub_num:** specific subject number
  * **visit_num:** each stay within the hospital (2 digits)
  * **ses_num:** each day spent in hospital during the visit (2 digits starting with SE, ex. SE01)
  * **type:** type of data collected (should be _IEEG/_EEG)
  * **task:** format of the edf data (should be _CLIP/_FULL/_CS)
  * **RET (optional):** indicates the study is retrospective

!!! note "Note on Visit Numbers"
    * If any retrospective studies exist for a subject, they should be assigned the first visit number 01
    * All following admissions to the hospital would be given incremental visit numbers (i.e. visit 2: 02, visit 3: 03)
    * Two separate admissions to the hospital should not have the same visit number
    * Scalp and Intracranial recordings should have distinct visit numbers (ex. If 01 is used for the participant’s first stay in the EMU for scalp EEG, 02 should be used if they come back for intracranial EEG)

!!! note "Note on Session Numbers"
	If a day in sequence of sessions is missing/not present this session number should still be accounted for
	e.g. 
	```sh
	ses-001 (data present), ses-002 (data missing), ses-003 (data present)
	```
	The directory naming would look like:
	```sh
	sub-003_01_SE01_IEEG_FULL_RET
	sub-003_01_SE03_IEEG_FULL_RET
	```
	Notice that no folder is specified for the missing ses-002 but the number is still accounted for by skipping it.
	
### Example

```sh
sub-003_02_SE01_IEEG_FULL_RET
```

A folder with the above naming scheme would indicate this is subject 3's second visit and first session. The data collected was a full IEEG recording that was retrospective (recorded prior to the subject consent).

Each day of recording should be in a separate folder within the subject directory:

```sh

input/
├── <sub_num>/                                                # Individual subject directory
│   │
│   ├── <sub_num>_<visit_num>_<ses_num>_<type>_<task>_[RET]/  # Specify visit, session, type, and task
│   │   │
│   │   ├── X~X_432a35cf-adg25-462-24aa-325db4e5e2d3.edf      # Individual EDF files
│   │   └── X~Xe_7d22151a-ac455-3adc312b-426aae3251ac.edf     # Individual EDF files
│   │
│   └── <sub_num>_<visit_num>_<ses_num>_<type>_<task>_[RET]/  # Specify visit, session, type, and task
│       │     
│       └── X~X_432a35cf-adg25-462-24aa-325db4e5e2d3.edf      # Individual EDF files
│
└── <sub_num>/                                                # Individual subject directory
    │
    └── <sub_num>_<visit_num>_<ses_num>_<type>_<task>_[RET]/  # Specify visit, session, type, and task
        │
        ├── X~X_432a35cf-adg25-462-24aa-325db4e5e2d3.edf      # Individual EDF files
        └── LastName~ FirstName_7d22151a-ac455-3adc312b-426aae3251ac.edf     # You can include the subject first/last name to be used when de-identifying the data

```


<br><br>