---
template: overrides/base.html
---

# BIDS Output Folder Structure

The output folder will look like the following:
  
  <center><table style="width:100%"><tr><td>
     <img src="../img/dir_structure_01.png" alt="drawing"/>
  </td></tr></table></center>

  * Each patient will have their own folder
  * **code:** this folder will contain the code used to convert the EDF data for each EDF file for the subjects.
  * **dataset_description.json:** this file will contain the Json Metadata you previously defined. This is overall information about the dataset
  * **participants.tsv:** this file contains a list of all the subjects in the dataset and their general demographics (i.e. age, sex etc.). This file can also be used to store additional subject information that is not variable over the different visits and sessions. 

Within each subject directory there will be a different session folder for each EDF file.
  
  <center><table style="width:100%"><tr><td>
     <img src="../img/sub_dir_structure_01.png" alt="drawing"/>
  </td></tr></table></center>

  * **\*_scans.tsv:** this file contains general information about each EDF file for the subject (i.e. filename, recording duration, EDF type etc.).

Within each session folder there will be a modality folder (either eeg or ieeg), inside that will be five files, containing different information associated with the specific EDF file. This file can also be used to store additional metadata information that varies from one recording to the next.
  
  <center><table style="width:100%"><tr><td>
     <img src="../img/ses_dir_structure_01.png" alt="drawing"/>
  </td></tr></table></center>

  * **\*_electrodes.tsv:** contains information associated with the electrodes used to collect the data.
  * **\*_annotations.tsv:** contains all annotations present in the EDF file, identifiers have been scrubbed.
  * **\*_channels.tsv:** contains information about each channel that was used in the recording.
  * **\*_eeg.json/** **or** **\*_ieeg.json:** contains metadata information about the EDF file.
  * **\*_eeg.edf/** **or** **\*_ieeg.edf:** contains the data in the de-identified EDF file.