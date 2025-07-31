CGKB-AI
=====
To power up the platform you’ll need to configure the runtime stack below. Follow the steps once and you’re ready to serve from your own machine.
### 1. Install Visual Studio 2022 Enterprise
Navigate to the official Visual Studio release history page:
```powershell
https://learn.microsoft.com/en-us/visualstudio/releases/2022/release-history#release-dates-and-build-numbers
```
Download the installer for the Enterprise edition and follow the on-screen prompts to complete the installation.

### 2. Install Open Babel 2.4.0  
Visit the official Open Babel website.
Locate and download the version released on September 21, 2016 (Open Babel 2.4.0), then run the installer to install it.

### 3. Install jdock (Latest Version)
Go to the GitHub repository: stcmz/jdock.
Download the latest release and follow the repository’s installation instructions to set it up.

### 4. Python 3.11.9
Visit the official Python website and download the 3.11.9 version installer.
Run the installer, ensuring to check "Add Python to PATH" during setup for environment configuration.
After installation, open a command prompt or terminal and install the necessary libraries using pip:
bash
pip install scikit-learn numpy pandas  
```powershell
pip install scikit-learn numpy pandas  
```

### 5. Install Node.js
Open a command prompt or PowerShell (run as administrator).
Use the Winget package manager to install Node.js

### 6. Start the API
Open the project in Visual Studio.
In the Solution Explorer, right-click the DockingApiService project.
Select Set as StartUp Project, then start the application to launch the backend.

### 7. Launch the web app
Open the project in Visual Studio.
In the Solution Explorer, right-click the GenericComputationPlatform project.
Select Set as StartUp Project, then start the application. The website will now be accessible.Enjoy! 🎉

If you need the package, please download the latest release from the [Releases](https://github.com/CGKBAI/CGKB-AI/releases) page.

For data-privacy reasons, the protein sequences are **not** included in this repository.  
To run the software you must:

1. Create your own database named `Computation.db`.  
2. Place the complete dataset under the `Workspace/` directory.

Once both steps are complete, the program will be ready to use.

For questions or collaboration, feel free to contact  
[fengzhiwei@suat-sz.edu.cn](mailto:fengzhiwei@suat-sz.edu.cn).
```
