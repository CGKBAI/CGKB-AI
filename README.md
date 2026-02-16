Procedure
=====
For questions or collaboration, feel free to contact
Huo Tingjun: tingjunhou@zju.edu.cn; Feng Zhiwei: fengzhiwei@suat-sz.edu.cn.
To power up the platform you’ll need to configure the runtime stack below. Follow the steps once and you’re ready to serve from your own machine.
### 1.Setting Up the MCCS Toolchain
#### Download and Install Docker

To install Docker, follow the official instructions available at (https://docs.docker.com/get-started/get-docker/).

*On Windows, additional setup may be required, including enabling Hyper-V and WSL2. If WSL is already installed, upgrade it using the following command in the command line or PowerShell:
```powershell
wsl --set-version distro_name 2
```
To check the current version and distribution name, use:
```powershell
wsl -l -v
```
*A system restart may be necessary before using Docker.

*On Windows/macOS, launch Docker Desktop after installation.

Timing 1 min - 3 min
#### Obtain the MCCS Toolchain
*On Windows    
>*Open PowerShell or Command Prompt via the Start menu or by using the Win+R hotkey.
>*In the command line window, run:
  ```powershell
docker pull stcmz/mccs
```
*On macOS
>*Open Terminal via Spotlight Search or the Applications folder.
>*In the command line window, run:
 ```powershell
docker pull stcmz/mccs
```
Timing 10 sec 
#### Launch the MCCS Container
*On Windows
>*Navigate to the folder containing your data (PDB files, download lists, etc.).
>*Open PowerShell or Command Prompt in the folder by holding Shift, right-clicking, and selecting “Open PowerShell window here” or “Open command line here”.
>*Run the following command to start the MCCS container:
 ```powershell
docker run --rm -it -v "$(pwd):/data" stcmz/mccs
```
>*You should now be successfully switched into the MCCS container.
*On macOS
>*Open Terminal.
>*Navigate to the folder containing your data using the cd command.
>*Run the following command to start the MCCS container:
 ```powershell
docker run --rm -it -v "$(pwd):/data" stcmz/mccs
```
>*You should now be successfully switched into the MCCS container.

Timing 5 sec 
### 2.Preparing the Download Lists
To prepare download lists for protein structures and sequences, follow these steps: First, create a plain text file for each download list. You can have multiple lists for different sets of data. Entries from both PDB (Protein Data Bank) and UniProt databases should be separated by line breaks or white spaces within each list. To organize downloads, add labels by naming them at the beginning of a line followed by a single colon. Each label applies to all entries on that line, allowing multiple lines to share the same label for organizing PDB files into specific folders. For example, in a file like Pain_relieving.txt：

5HT1B: P28222

5HT2A: 6A93 6A94

5HT2B: P41595 4IB4 4NC3 5TVN 6DRX 6DRY 6DRZ 6DS0

Timing 3 d– 7 d

The entry "5HT1B: P28222" assigns the label 5HT1B to the UniProt entry P28222, while "5HT2A: 6A93 6A94" groups the PDB entries 6A93 and 6A94 under the label 5HT2A. Similarly, "5HT2B: P41595 4IB4 4NC3 5TVN 6DRX 6DRY 6DRZ 6DS0" organizes multiple PDB and UniProt entries under 5HT2B. 

CRITICAL STEP If you collect entries in an Excel worksheet, use the protein name as a label and copy the corresponding PDB entries into the text file list. This structured approach ensures efficient management and retrieval of protein data for research and analysis.
### 3.Downloading and Splitting PDB Files
To download and organize PDB files efficiently, run the following command from MCCS toolchain to download all PDB files:
 ```powershell
pdbget -s -l Pain_relieving.txt -o Pain_relieving
```

Timing 5 min – 10 min

!CAUTION If an error occurs during downloading, you can restart by running the same command, and the tool will automatically skip already downloaded files while retrieving only the missing ones.
### 4. Further Cleaning
This step serves as an additional validation of the data. During this process, redundant PDB files should be deleted, keeping only the PDB files related to the target protein and small molecules (if applicable). Low-quality structures should also be removed to prevent any negative impact on docking calculations. It is essential to maintain the folder structure and ensure that no more than three to four PDB entries are retained for the same protein. The cleaned files should be properly named, with protein files saved as AminoAcids.pdb and small molecule files as Ligand.pdb.
!CAUTION When parsing PDB files, extra information such as ions, water molecules, and lipids may be included along with the protein target and small molecules. Since these components are unnecessary for subsequent calculations, manually reviewing and removing irrelevant files ensures data accuracy.
bash

Timing 30 min – 60 min 

### 5.Generating Pocket Files and Pocket Images
In this step, you need to identify binding pockets within each protein and generate an MOL2-format pocket placeholder using Sybyl or other software. These pocket placeholders typically consist of a cluster of carbon atoms representing the binding pocket within the protein. The generated MOL2 file should be named Pocket.mol2. For specific Sybyl operations, refer to relevant manuals. Next, open the protein structure and pocket placeholder in PyMOL, adjust rotation and zoom to obtain an optimal view, and configure the color scheme in a single command run:
 ```powershell
load Pocket.mol2; zoom; show surface, Pocket; set antialias, 2; spectrum; spectrum count, magenta_magenta, Pocket;
```

Timing 10 sec 
This command loads the pocket placeholder, adjusts the view, displays the pocket surface, sets anti-aliasing to level 2, applies a color spectrum to the protein, and ensures the pocket is displayed in magenta. Then, manually adjust the perspective by zooming and rotating for a suitable view. Finally, render and save the image in PNG format with a resolution of 3000x3000 using the following command:
 ```powershell
ray 3000,3000; png .\Rendering.png
```

Timing 50 sec 
This step ensures high-quality visualization of the binding pocket, where the protein appears in a rainbow color scheme and the pocket placeholder in magenta.
### 6.Preparing Receptor and Ligand Using Scripts from MCCS Toolchain Preparation of the receptor(s)
Use the sidechain fixing script for UCSF Chimera to scan and fix the receptors. Run the script below at a command line.
 ```powershell
chimera --nogui --script incompleteSideChains.py receptor.pdb
```

Timing 5 sec – 3 min, average 10 sec
CRITICAL STEP The script reads in the molecule and replaces each truncated residue with a complete one in the same kind using the Dunbrack rotamer library. The script always saves the fixed structure in the file named “fixed.pdb”. Rename it to a more meaningful one once succeeded. The script is single-threaded and therefore multiple receptors can be fixed simultaneously on a multi-core CPU to boost the overall speed.

!CAUTION The script does not always result in a valid structure. Due to the low resolution of 3D structure or scanning error in crystallography, the resulting structures may sometimes have steric clashes for the missing sidechains. In such cases, with the original atom coordinates being honored, the script unavoidably generates a fixed structure with overlapped sidechains. Use molecular modeling software to refine the problematic sidechains or simply skip the fixing, otherwise the following steps may fail. Then, use VEGA to convert the receptor PDB into a PDBQT. (http://autodock.scripps.edu/faqs-help/faq/what-is-the-format-of-a-pdbqt-file). Run the script below at a command line.
 ```powershell
vega receptor.pdb -o receptor.pdbqt -f VINA -c Gasteiger -p VINA -l GEN -r APOLAR -w
```

Timing < 2 sec
The script emits the receptor structure in the Vina PDBQT format after applying the Gasteiger charge template (the -c argument) and the Vina force field (the -p argument), adding hydrogens as in generic organic molecule (the -l argument), and removing apolar hydrogens (the -r argument) and water molecules (the -w argument).
#### Preparation of the ligand(s)
Use VEGA to convert the ligand PDB into a PDBQT. (http://autodock.scripps.edu/faqs-help/faq/what-is-the-format-of-a-pdbqt-file). Run the script below at a command line.
```powershell
vega ligand.pdb -o ligand.pdbqt -f VINA -c Gasteiger -p VINA -l GEN -r APOLAR -j FLEX -w
```

Timing 1 sec
The script emits the ligand structure in the Vina PDBQT format after defining the flexible torsions (the -w argument), applying the Gasteiger charge template (the -c argument) and the Vina force field (the -p argument), adding hydrogens as in generic organic molecule (the -l argument), and removing apolar hydrogens (the -r argument) and water molecules (the -w argument). Then, use PROPKA to predict the pKa values for the input ligand. Run the script below at a command line.
```powershell
propka3 ligand.pdb
```

Timing 1 sec 
PROPKA is a Python based script. It accepts PDB and PDBQT formats and the output is a file describing the predicted pKa values. The file is named after the input PDB but with a “.pka” extension.
### 7.Generating the Workload Using in-house Scripts
To prepare the workload for the GenCompPlat platform, several scripts are executed in sequence. The WF0_FetchProteinData script downloads metadata for each protein and its structure, generating the corresponding layout. The WF1_DownloadChemblBioactivityData script retrieves ChEMBL bioactivity data and formats the output into a specific layout. Next, the WF2_ProcessChemblActivityData script extracts bioactivity data for each protein from the ChEMBL database and structures the output in a predefined format. The WF3_ProvisionCompoundsForDocking script processes bioactivity data by converting it into a simple active/inactive classification and generating the corresponding layout. The WF4_BuildFingerprintDatabase script calculates molecular fingerprints (FP2) for ligands in the bioactivity database and stores them in a global fingerprint database. The WF5_PrecomputeFingerprints script then collects molecular fingerprints (FP2) from the global database for ligands in the bioactivity dataset and generates the final layout. Finally, use the following script (prepared_GCP.sh) to generate additional data for GenCompPlat:
```powershell
for i in */*/*; do 
    cd $i || continue
    chimera --nogui --script ~/incompleteSideChains.py AminoAcids.pdb
    mv fixed.pdb AminoAcids_fixed.pdb
    propka3 Ligand.pdb
    vega Ligand.pdb -o Ligand.pdbqt -f VINA -c Gasteiger -p VINA -l GEN -r APOLAR -w -j FLEX
    vega AminoAcids_fixed.pdb -o AminoAcids_fixed.pdbqt -f VINA -c Gasteiger -p VINA -l GEN -r APOLAR -w
    obabel AminoAcids.pdb -O AminoAcids.mol2
    pdbm -CL -l15 Pocket.mol2 > Pocket.conf
    cd -
done
pdbqtf $(grep --include=*.pdbqt -rl '?') -a 
```

Timing 1 min- 3 min
This script automates the processing of protein and ligand structures, ensuring proper formatting for docking and analysis on the GenCompPlat platform. 
### 8.Compiling and Deploying the Platform
To compile and deploy the database platform, first, open Visual Studio and build the solution by opening CytoscapeOnline.sln, navigating to the Build menu, selecting Build Solution, and ensuring no errors occur during the build process. Next, open Task Runner Explorer by selecting View > Other Windows > Task Runner Explorer from the Visual Studio menu. Then, open the integrated terminal in Visual Studio by pressing “Ctrl + ~”, navigate to the project directory using the command:
```powershell
cd C:\Git\GenericComputationPlatform\GenericComputationPlatform
```
Press Enter to switch to the project directory and install dependencies by running:
```powershell
npm ci
```
This command installs all project dependencies based on package-lock.json. Refresh Task Runner Explorer by clicking the refresh button to ensure updated tasks are visible. Then, execute the build tasks sequentially in Task Runner Explorer by double-clicking Build 1, Build 2, and Build 3, waiting for each to complete before proceeding to the next.
Next, set “DockingApiService” as the startup project by right-clicking it in Solution Explorer, selecting “Set as StartUp Project”, and launching it in non-debug mode by navigating to “Debug > Start Without Debugging”, which starts the backend service. Similarly, set “GenericComputationPlatform” as the startup project by right-clicking it in Solution Explorer, selecting “Set as StartUp Project”, and launching it in non-debug mode via “Debug > Start Without Debugging”, which starts the frontend application.

Timing 5 min- 10 min

If you need the package, please download the latest release from the [Releases](https://github.com/CGKBAI/CGKB-AI/releases) page.

For data-privacy reasons, the protein sequences are **not** included in this repository.  
To run the software you must:

1. Create your own database named `Computation.db`.  
2. Place the complete dataset under the `Workspace/` directory.

Once both steps are complete, the program will be ready to use.

For questions or collaboration, feel free to contact  
[fengzhiwei@suat-sz.edu.cn](mailto:fengzhiwei@suat-sz.edu.cn).
```
