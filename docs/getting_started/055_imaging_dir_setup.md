---
template: overrides/main.html
title: Imaging Directory Setup
---

## Imaging Data

**edf2bids** will anonymize imaging DICOM files if they are present within the input directory. The DICOMs should be within a directory named **imaging**, which is at the root of the subject directory. Within the **imaging** directory, there should be a sub-directory for all imaging sessions for the subject. The session sub-directories should contain the DICOM files either compressed or uncompressed. The accepted compression formats include: .tar, .tgz, .tar.gz, .tar.bz2, .zip. The DICOM filenames can be anything, you could also place all the DICOM files for all imaging sequences into one directory.

The imaging session folders should be in the format **`EPL31_LHS_001_<ses_num>`** , where **`ses_num`** is the imaging session number for the imaging data. If the subject only has one session, you still need to include the DICOM files within a session sub-directory.

EPL31_LHS_001_01
For details on the DICOM header tags that are anonymized [**see the explanation here**](07_run_conversion.html#imaging-anonymization).


### Example

#### Static

```sh

input/
├── EPL31_LHS_0001/                           # Individual subject directory
|     └── imaging/                            # Imaging directory for dicoms
|           ├── EPL31_LHS_0001_01/            # Imaging sub-directory for each session
|           |     ├── T1w_scan/               
|           |     |       ├── t1w_file01.dcm
|           |     |       ├── t1w_file02.dcm  # optional DICOM directories for each sequence, can be given any name
|           |     |       └── t1w_file03.dcm
|           |     |
|           |     ├── T2w_scan/
|           |     |       ├── t2w_file01.dcm
|           |     |       ├── t2w_file02.dcm
|           |     |       └── t2w_file03.dcm
|           |     |
|           |     └── dwi_scan/
|           |           └── dwi_scan.tar.gz   # the DICOM files can be stored in a compressed format (i.e. tar.gz)
|           |
|           └── EPL31_LHS_0001_02/
|                 ├── t1w_scan.tar.gz
|                 └── dwi_scan.tar.gz
|
└── EPL31_LHS_0002/                           
      └── imaging/                            
            └── EPL31_LHS_0002_01/            
                 ├── t1w_scan_01.dcm
                 ├── t1w_scan_02.dcm
                 ├── t1w_scan_03.dcm          # you can combine all the DICOM files into one directory
                 ├── dwi_scan_01.dcm
                 ├── dwi_scan_02.dcm
                 └── dwi_scan_03.dcm

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

