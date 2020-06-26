---
template: overrides/base.html
---

# Input Directory Setup

This section outlines how the data should be organized prior to running the EDF conversion software. The data files should be in EDF/EDF+ format following the [specifications](https://www.edfplus.info/specs/edf.html) provided by the EDF developers. There are two ways to organize the directory structure  prior to running the software. The root directory should contain a sub-directory for each of the  subjects you want to have converted. Note that at this moment the names of the EDF/EDF+ files are not yet BIDS compliant, but they do contain some metadata in the filename that will be used later.

## Option 01: Specify visit/session number

If you do not need to specify the visit or session number for each EDF file for the subjects, then this option will assign session numbers based on the Date timestamp within the EDF files. So the earliest EDF file will be given `ses-001` while the latest EDF file will be given `ses-###` (will be equal to the amount of EDF files for that subject).

```sh
.
├─ <sub_num>/                                           # Individual subject directory
   ├─ X~X_432a35cf-adg25-462-24aa-325db4e5e2d3.edf      # Individual EDF files
   ├─ X~Xe_ 7d22151a-ac455-3adc312b-426aae3251ac.edf

```

## Option 02: Do not specify visit/session number

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

Each day of recording should be in a separate folder within the subject directory:

```sh
.
├─ <sub_num>/                                           
   |
   |─ <sub_num>_<visit_num>_<ses_num>_<type>_<task>_[RET]/      
   |   |
   |   └─ LastName~ FirstName_432a35cf-adg25-462-24aa-325db4e5e2d3.edf
   |
   └─ <sub_num>_<visit_num>_<ses_num>_<type>_<task>_[RET]/
       |
       └─ LastName~ FirstName_ 7d22151a-ac455-3adc312b-426aae3251ac.edf

```

!!! note
    * If any retrospective studies exist for a subject, they should be assigned the first visit number 01
    * All following admissions to the hospital would be given incremental visit numbers (i.e. visit 2: 02, visit 3: 03)
    * Two separate admissions to the hospital should not have the same visit number
    * Scalp and Intracranial recordings should have distinct visit numbers (ex. If 01 is used for the participant’s first stay in the EMU for scalp EEG, 02 should be used if they come back for intracranial EEG)

<br><br>