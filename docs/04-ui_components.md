---
title: UI Components
template: overrides/base.html
---
This section describes steps taken within the EDF conversion software developed by Greydon Gilmore. Ensure the [**input data directory is organized**](03-input_dir_setup.md#organize-input-directory) prior to running the software.

## Adjusting metadata settings

1. In the software, select **File** then **Settings**.

	<p align="center"><img src="/img/metadata_01.png" alt="drawing" width="80%"/></p>

2. A settings panel will appear with two tabs: Json Metadata and Electrode Info.

	* **Json metadata** contains general information about the dataset.
  	  * **Lab:** what is the name of the lab collecting this data
  	  * **Experimenter:** list all individuals involved in this dataset
  	  * **Dataset Name:** overall name for the dataset.
  	  * **Institution Name/Address:** name and address of the institution at which  this dataset was collected

	<p align="center"><img src="/img/metadata_02.png" alt="drawing" width="50%"/></p>

	* **Electrode info** contains information about the specific electrodes used at the center:
  	  * **Manufacturer:** who produces the electrodes.
  	  * **Type:** what type of electrode is it (i.e. depth, scalp etc.)
  	  * **Material:** what material are the electrodes made from (i.e. platinum)
  	  * **Diameter:** what is the diameter of the electrode

	<p align="center"><img src="/img/metadata_03.png" alt="drawing" width="50%"/></p>

3. Once you have modified the information click Save and the information will be stored and used for every subsequent dataset processed in the Conversion software. You will not need to re-define these values, unless they change at your site.

## Main window settings panel

1. The settings panel is located at the bottom left of the main window.

  <p align="center"><img src="/img/main_settings_panel.png" alt="drawing" width="20%"/></p>

  * **De-identify input directory**: if this is selected the input EDF files will be de-identified first prior to being copied. If left unchecked, then only the output directory EDF files will be de-identified. Default is unchecked.
  * **Offset dates**: if selected all dates in the EDF files will be offset by a random number of days (~1000 days). The offset value can be determined but is securely stored.
  * **Test conversion**: this should only be selected if testing the software on new data. This option will not copy the EDF files or read the annotations. This means it will quickly run through the data and output a BIDS structure without the EDF file.

## Input directory selection

The input directory selected here should be organized according to the first section in this documentation. Prior to loading the data, make sure all the settings in the settings panel have been configured to your specification (found at the bottom left of the main window).

1. In the software, select **Input Directory** and choose the directory that contains the data to be converted (select the main/root directory that contains all subject directories). Click **Select Folder** in the window and the data will load into the **Input Directory** window.

    <p align="center"><img src="/img/input_dir_01.png" alt="drawing" width="100%"/></p>

2. You will now be able to review the information that was detected about the input files. Each subject is expandable by clicking the box beside the subject name. The columns displayed are:

    * **Name:** this is the name of the subdirectory within the patient folder that contains the EDF file. If the EDF files are all in the same directory, then the name will be the name of the EDF files.
    * **Date:** the date the data was recorded.
    * **Time:** the time the data was recorded.
    * **Size:** the size of the EDF file in gigabytes.
    * **Frequency (adjustable):** the frequency the data was recorded at. This is automatically calculated based on information extracted from the EDF file. However, if there is an error then the user can double click on the frequency box and manually change it.
    * **Duration:** the total duration of the EDF file recording, which is automatically calculated. 
    * **EDF Type:** type of EDF file (EDF+D or EDF+C). This field should always show EDF+C as outlined in the Natus EDF batch export document.
    * **Type (selectable):** the type of EEG data collected (Intracranial or Scalp). This value is automatically detected by the flags `_EEG/_IEEG` used in the directory name. This value can be changed by the user if there is an error.
    * **Task (selectable):** the condition of the recorded file: Full, clip or cortical stimulation (CS). This value is automatically detected by the flags `_CLIP/_FULL/_CS` used in the directory name. This can be changed by the user if there is an error.
    * **Ret/Pro (selectable):** whether the data file is retrospective or prospective. This value is automatically detected based on the RET flag used in the directory name. This value can be changed by the user if there is an error.
    * **Channel File:** this indicates if a channel_labels.txt file was found. If you notice the labels changed in a specific session you can include a channel_label.txt file within each session folder. Yes indicates a channel label file was found, No otherwise.


3. Once you have confirmed the input data is correct, click **Output Directory** and select the directory you want the BIDS dataset to appear. The **Output Directory** window will now present the final output file information (prior to conversion). This is the final check to ensure that any changes have been updated.

    <p align="center"><img src="/img/output_dir_01.png" alt="drawing" width="80%"/></p>


    !!! note 
        EDF files in the Input Directory will be **COPIED** to the new location and will be renamed to be BIDS compliant. Thus, you will have two copies of the EDF files. This is a safety measure in case an error occurs in the conversion, the source data will remain intact.


4. If you are converting data for a participant that already has recordings in the output folder, the checkboxes for those recordings will appear checked in the **Output Directory** window.

5. Once you have confirmed the Output Directory file information is correct press **Convert**.

6. During the conversion process you can cancel the conversion at any time by pressing the **Cancel** button. However, if you cancel the conversion you will need to delete the contents of the output directory and start over.

7. You will receive updates in the **Conversion Status** window. The final notice, once the conversion is complete, will show **Your data has been BIDsified!**. 

  <p align="center"><img src="/img/final_message.png" alt="drawing" width="90%"/></p>

## BIDs output folder structure

The output folder will look like the following:

  <p align="center"><img src="/img/dir_structure_01.png" alt="drawing" width="60%"/></p>

  * Each patient will have their own folder
  * **code:** this folder will contain the code used to convert the EDF data for each EDF file for the subjects.
  * **dataset_description.json:** this file will contain the Json Metadata you previously defined. This is overall information about the dataset
  * **participants.tsv:** this file contains a list of all the subjects in the dataset and their general demographics (i.e. age, sex etc.). This file can also be used to store additional subject information that is not variable over the different visits and sessions. 

Within each subject directory there will be a different session folder for each EDF file.

  <p align="center"><img src="/img/sub_dir_structure_01.png" alt="drawing" width="60%"/></p>

  * **\*_scans.tsv:** this file contains general information about each EDF file for the subject (i.e. filename, recording duration, EDF type etc.).

Within each session folder there will be a modality folder (either eeg or ieeg), inside that will be five files, containing different information associated with the specific EDF file. This file can also be used to store additional metadata information that varies from one recording to the next.

  <p align="center"><img src="/img/ses_dir_structure_01.png" alt="drawing" width="60%"/></p>

  * **\*_electrodes.tsv:** contains information associated with the electrodes used to collect the data.
  * **\*_annotations.tsv:** contains all annotations present in the EDF file, identifiers have been scrubbed.
  * **\*_channels.tsv:** contains information about each channel that was used in the recording.
  * **\*_eeg.json/** **or** **\*_ieeg.json:** contains metadata information about the EDF file.
  * **\*_eeg.edf/** **or** **\*_ieeg.edf:** contains the data in the de-identified EDF file.

<br><br>