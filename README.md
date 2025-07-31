```markdown
# 🚀 Quick-Start Guide

> Copy-paste the steps below to get **JDock** running in minutes.

---

### 1. Visual Studio 2022 Enterprise
```powershell
winget install Microsoft.VisualStudio.2022.Enterprise
# select workloads: .NET desktop, Desktop development with C++
```

### 2. Open Babel 2.4.0  
Download and install from  
[http://openbabel.org/wiki/Category:Releases](http://openbabel.org/wiki/Category:Releases)

### 3. Clone the repo
```bash
git clone https://github.com/stcmz/jdock.git
cd jdock
```

### 4. Python 3.11.9
```powershell
winget install Python.Python.3.11 --version 3.11.9
pip install scikit-learn numpy pandas
```

### 5. Node.js LTS via Winget
```powershell
winget install OpenJS.NodeJS.LTS
```

### 6. Start the API
* In **Solution Explorer** → right-click **DockingApiService** → **Set as StartUp Project** → **F5**

### 7. Launch the web app
* Stop debugging  
* Right-click **GenericComputationPlatform** → **Set as StartUp Project** → **F5**

Enjoy! 🎉
```
