---
template: overrides/main.html
title: Input Directory Setup
---

This section outlines how the data should be organized prior to running the **edf2bids** software. The data files should be in EDF/EDF+ format following the <a href="https://www.edfplus.info/specs/edf.html" target="_blank">specifications</a> provided by the EDF format developers. 

[Setup a working directory](05_input_dir_setup.html#setup-a-working-directory) is a recommended way to organize your working directory of data (this is optional but highly recommended).

!!! note 
    At this moment the names of the EDF/EDF+ files are not yet BIDS compliant, but they do contain some metadata in the filename that will be used later.

## Setup a working directory

It is recommended that you establish a working directory to ensure your data remains organized. The optimal setup would be one with the following five directories:

* **unorganized**: this directory holds unorganized subject data ready to be organized for **edf2bids**
* **organized**: this directory holds organized subject data ready to be organized for **edf2bids** (according to either option below)
* **input**: this directory holds subject data ready to be converted with **edf2bids**
* **output**: this directory holds the output converted data from **edf2bids**
* **complete**: this directory holds the fully converted data from the output directory (which needs to be empty every time a new conversion is occurring)

!!! warning
    When beginning a conversion with **edf2bids** ensure the **output** directory is empty and the **input** directory only contains the subject folders you wish to convert.

### Example

#### Static

```sh

working_dir/
├── unorganized/
├── organized/
├── input/
├── output/
└── complete/

```

#### Interactive

<div id="tree"></div>
<script>
    $(document).ready(function() {
      $.ajax({
          url : "../../assets/working_dir.json",
          dataType: "text",
          success : function (tree) {
              $('#tree').bstreeview({ data: tree });
          }
      });
});
</script>

## Input directory

If you do not require a specific naming convention for visits and sessions then [Option 01: Do not specify visit/session number](05_input_dir_setup.html#option-01-do-not-specify-visitsession-number) should be followed. In this option, the visit and session number are not supplied to the **edf2bids** software and instead are automatically determined based on the date/time of the EDF/EDF+ acquisition.

For the **EpLink study**, you should follow [Option 02: Specify visit/session number](05_input_dir_setup.html#option-02-specify-visitsession-number).

### Option 01: Do not specify visit/session number

If you do not need to specify the visit or session number for each EDF file for the subjects, then this option will assign session numbers based on the **Date** timestamp within the EDF files. So the earliest EDF file will be given **ses-001** while the latest EDF file will be given **ses-###** (### will be equal to the number of EDF files for that subject).

!!! important "Input directory"
    The input directory (`input`) should contain a sub-directory for each of the subjects you want to have converted (i.e. `input\sub-001`, `input\sub-002` etc.). Within the **edf2bids** software you select `input` as the input directory and not the individual subject directories

!!! note "Definition of terms"
    for a complete list of terms see the [definitions page](02_definitions.html#filename-terms)

#### Static example

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

#### Interactive example

<div id="tree2"></div>
<script>
    $(document).ready(function() {
      $.ajax({
          url : "../../assets/no_ses_visit.json",
          dataType: "text",
          success : function (tree) {
              $('#tree2').bstreeview({ data: tree });
          }
      });
});
</script>

### Option 02: Specify visit/session number

In some instances you may want to manually assign the specific visit or session numbers for the EDF files. In this scenario you would need to place each EDF file into a directory with the following naming scheme:

```sh
<sub_num>_<visit_num>_<ses_num>_<type>_<task>_[RET]
```

!!! note "Definition of terms"
    for a complete list of terms see the [definitions page](02_definitions.html#filename-terms)

```sh
sub-003_02_SE01_IEEG_FULL_RET
```

A folder with the above naming scheme would indicate this is subject 3's second visit and first session. The data collected was a full IEEG recording that was retrospective (recorded prior to the subject consent).

!!! important "Input directory"
    The input directory (`input`) should contain a sub-directory for each of the subjects you want to have converted (i.e. `input\sub-001`, `input\sub-002` etc.). Within the **edf2bids** software you select `input` as the input directory and not the individual subject directories
    
Each day of recording should be in a separate folder within the subject directory:

#### Static example

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

#### Interactive example

<div id="tree3"></div>
<script>
    $(document).ready(function() {
      $.ajax({
          url : "../../assets/specify_ses_visit.json",
          dataType: "text",
          success : function (tree) {
              $('#tree3').bstreeview({ data: tree });
          }
      });
});
</script>

## Channel labels

For recordings where patient specific channel labels are defined, a channel labels text file should be created that dictates how the labels should appear within the EDF file. For instance, EEG recordings commonly make use of the 10-20 setup, which means the channel labels do not change between patients. However, in iEEG recordings, channels labels are unique to the patient depending on where the depth electrodes were implanted. To ensure the EDF file has the correct channel labels, you should create a text file called "\*_channel_labels.txt", where __\*__ indicates any string can be placed there but the file name needs to end with **channel_labels.txt** (i.e. sub-001_channel_labels.txt, EPL31_LHS_0001_channel_labels.txt).

When creating the channel labels file you do not need to include default NeuroWorks channels, such as:

* Patient Event
* TRIG
* OSAT
* PR
* Pleth
* EDF Annotations

You only need to provide channels with unique names for the specific patient, for instance:

* LAHc1
* RAm1

The implanted depth electrodes each have several contacts on them to record from. So the contacts on the electrode are numbered, such as:

* **10 contact electrode LAHc:** LAHc1, LAHc2, LAHc3, LAHc4, LAHc5, LAHc6, LAHc7, LAHc8, LAHc9, LAHc10 

When creating the channel labels file, it is easiest to use Excel or Libreoffice. The first column should contain C1 to Cn, where n is the total number of iEEG channels used in the recording. Next to each channel number, place the corresponding electrode contacts name.

??? note "Example _channel_labels.txt file"
    |[]()|       |
    |----|-------|
    |C1  | LOFr1 |
    |C2  | LOFr2 |
    |C3  | LOFr3 |
    |C4  | LOFr4 |
    |C5  | LOFr5 |
    |C6  | LOFr6 |
    |C7  | LOFr7 |
    |C8  | LOFr8 |
    |C9  | LOFr9 |
    |C10 | LOFr10 |
    |C11 | LCg1 |
    |C12 | LCg2 |
    |C13 | LCg3 |
    |C14 | LCg4 |
    |C15 | LCg5 |
    |C16 | LCg6 |
    |C17 | LCg7 |
    |C18 | LCg8 |
    |C19 | LCg9 |
    |C20 | LCg10 |
    |C21 | LAm1 |
    |C22 | LAm2 |
    |C23 | LAm3 |
    |C24 | LAm6 |
    |C25 | LAm7 |
    |C26 | LAm8 |
    |C27 | LAm9 |
    |C28 | LAm10 |
    |C29 | LAHc1 |
    |C30 | LAHc2 |
    |C31 | LAHc3 |
    |C32 | LAHc6 |
    |C33 | LAHc7 |
    |C34 | LAHc8 |
    |C35 | LAHc9 |
    |C36 | LAHc10 |

## Imaging Data

**edf2bids** will anonymize imaging DICOM files if they are present within the input directory. The DICOMs should be within a directory named **imaging**, which is at the root of the subjects directory. Within the **imaging** directory should be another directory with the desired output name for the zipped directory (containing all the anonymized DICOMs). The directories containing the actual DICOM files can be given any name, generally they are named after the specific sequence acquired for the DICOMs inside.

### Example

#### Static

```sh

input/
└── <sub_num>/                           # Individual subject directory
      └── <imaging>/                       # Imaging directory for dicoms
            ├── T1w_scan/<*.dcm files>     # DICOM directories, can be given any name
            ├── T2w_scan/<*.dcm files>
            └── dwi/<*.dcm files>

```

#### Interactive

<div id="tree4"></div>
<script>
    $(document).ready(function() {
      $.ajax({
          url : "../../assets/imaging_data.json",
          dataType: "text",
          success : function (tree) {
              $('#tree4').bstreeview({ data: tree });
          }
      });
});
</script>

<br>
<br>
<br>

