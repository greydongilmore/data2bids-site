---
template: overrides/main.html
title: Imaging Directory Setup
---

## Imaging Data

**data2bids** will anonymize imaging DICOM files if they are present within the input directory. The DICOMs should be within a directory named **imaging**, which is at the root of the subject directory. Within the **imaging** directory, there should be a sub-directory for all imaging sessions for the subject. The session sub-directories should contain the DICOM files either compressed or uncompressed. The accepted compression formats include: .tar, .tgz, .tar.gz, .tar.bz2, .zip. The DICOM filenames can be anything, you could also place all the DICOM files for all imaging sequences into one directory.

The imaging session folders should be in the format **`EPL31_LHS_001_<ses_num>`** , where **`ses_num`** is the imaging session number for the imaging data. If the subject only has one session, you still need to include the DICOM files within a session sub-directory.

For details on the DICOM header tags that are anonymized see the section below.

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

## Anonymization 

Certain DICOM viewers and analysis tools require specific DICOM header tags to be present. Complete removal of these tags may render the DICOMS unreadable by these tools. Instead, the DICOM header tags that are deemed an identifier are overwritten by random data. The tool used to perform this action is <a href="https://github.com/blairconrad/dicognito" target="_blank">dicognito</a>.

??? note "Anonymized DICOM tags"
    | Attribute     | Description         |
    |---------------|---------------------|
    | AccessionNumber (0008,0050) | A RIS generated number that identifies the order for the Study. |
    | FillerOrderNumberImagingServiceRequest (0040,2017) | The order number assigned to the Imaging Service Request by the party filling the order. |
    | InstitutionName (0008,0080) | Institution where the equipment that produced the Composite Instances is located. |
    | InstitutionAddress (0008,0081) | Mailing address of the institution where the equipment that produced the Composite Instances is located. |
    | InstitutionalDepartmentName (0008,1040) | Department in the institution where the equipment that produced the Composite Instances is located. |
    | OtherPatientNames (0010,1001) | Other names used to identify the Patient. |
    | PatientID (0010,0020) | Primary hospital identification number or code for the patient. |
    | PerformedProcedureStepID (0040,0253) | User or equipment generated identifier of that part of a Procedure that has been carried out within this step. |
    | PlacerOrderNumberImagingServiceRequest (0040,2016) | The order number assigned to the Imaging Service Request by the party placing the order. |
    | RequestedProcedureID (0040,1001) | Identifier that identifies the Requested Procedure in the Imaging Service Request. |
    | ScheduledProcedureStepID (0040,0009) | Identifier that identifies the Scheduled Procedure Step. |
    | StationName (0008,1010) | User defined name identifying the machine that produced the Composite Instances. |
    | StudyID (0020,0010) | User or equipment generated Study identifier. |

??? note "Removed DICOM tags"
    | Attribute/Tag | Description         |
    |---------------|---------------------|
    | CountryOfResidence (0010,2150) | Country in which patient currently resides. |
    | Occupation (0010,2180) | Occupation of the Patient. |
    | PatientAddress (0010,1040) | Legal address of the named patient. |
    | RegionOfResidence (0010,2152) | Region within patient's country of residence. |
    

<br>
<br>
<br>

