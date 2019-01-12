---
title: Underhåll av MySQL-resursprovider i Azure Stack | Microsoft Docs
description: Lär dig hur du kan underhålla MySQL resource provider-tjänsten på Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: e0de6b26499b4a5166db411eccee543be43c8352
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245998"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Underhåll-åtgärder för MySQL-resursprovider

MySQL-resursprovider körs på en låst virtuell dator. Om du vill aktivera underhållsåtgärder som du behöver uppdatera den virtuella datorns säkerhet. Du kan använda PowerShell tillräckligt Administration JEA ()-slutpunkten DBAdapterMaintenance om du vill göra detta med hjälp av principen om lägsta behörighet. Installationspaketet resource provider innehåller ett skript för den här åtgärden.

## <a name="update-the-virtual-machine-operating-system"></a>Uppdatera operativsystemet för virtuell dator

Eftersom resursprovidern körs på en *användaren* virtuell dator som du ska använda de uppdateringar som krävs och uppdateringarna när de ges ut. Du kan använda Windows update-paket som tillhandahålls som en del av korrigeringen och uppdateringscykeln för att tillämpa uppdateringar för den virtuella datorn.

Uppdatera providern VM med någon av följande metoder:

- Installera det senaste providern resurspaketet med en för närvarande uppdaterad avbildning för Windows Server 2016 Core.
- Installera en uppdatering för Windows-paketet under installationen av eller uppdatera till resursprovidern.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Uppdatera Windows Defender-definitioner för virtuell dator

Följ dessa steg om du vill uppdatera Defender-definitioner:

1. Ladda ned Windows Defender-definitioner uppdatera från [Windows Defender Definition](https://www.microsoft.com/en-us/wdsi/definitions).

    Rulla ned till ”manuellt hämta och installera definitionerna” på sidan definitioner. Ladda ned filen ”Windows Defender Antivirus för Windows 10 och Windows 8.1” 64-bitars.

    Du kan också använda [den här Direktlänk](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) download/kör fpam fe.exe-filen.

2. Öppna en PowerShell-session till den MySQL resource provider nätverkskort virtuella datorns Underhåll slutpunkt.

3. Kopiera uppdateringsfilen definitioner till resursprovideradaptern virtuell dator med Underhåll endpoint sessionen.

4. På Underhåll PowerShell-session och kör den _uppdatering DBAdapterWindowsDefenderDefinitions_ kommando.

5. När du har installerat definitionerna rekommenderar vi att du tar bort uppdateringsfilen definitioner genom att använda den _Remove-ItemOnUserDrive)_ kommando.

**PowerShell-Skriptexemplet för att uppdatera definitioner.**

Du kan redigera och kör följande skript för att uppdatera Defender-definitioner. Ersätt värdena i skriptet med värden från din miljö.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Hemligheter rotation

*Dessa anvisningar gäller endast för integrerade Azure Stack-system.*

När du använder resursprovidrar SQL- och MySQL med Azure Stack integrerade system, ansvarar Azure Stack-operatör för att rotera följande resource provider infrastruktur hemligheter för att säkerställa att de inte upphör:

- Externt SSL-certifikat [angav under distributionen](azure-stack-pki-certs.md).
- Resource provider VM lokala administratörslösenordet anges under distributionen.
- Resource provider diagnostik (dbadapterdiag) lösenord.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-exempel för att rotera hemligheter

**Ändra alla hemligheter på samma gång.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**Ändra diagnostiska användarens lösenord.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**Ändra lösenordet för VM-lokala administratörskontot.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Ändra lösenord för SSL-certifikat.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 parametrar

|Parameter|Beskrivning|
|-----|-----|
|AzCredential|Autentiseringsuppgifter för Azure Stack-tjänstadministratör-konto.|
|CloudAdminCredential|Azure Stack molnet admin inloggningsuppgift för domänkontot.|
|PrivilegedEndpoint|Privilegierad slutpunkt för åtkomst till Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostik lösenord.|
|VMLocalCredential|Det lokala administratörskontot på MySQLAdapter VM.|
|DefaultSSLCertificatePassword|Standard SSL-certifikat (* pfx) lösenord.|
|DependencyFilesLocalPath|Beroende filer lokal sökväg.|
|     |     |

### <a name="known-issues"></a>Kända problem

**Problem:**<br>
Loggar för hemligheter rotation inte samlas in automatiskt om hemliga rotation skriptet misslyckas när den körs.

**Lösning:**<br>
Använd cmdleten Get-AzsDBAdapterLogs för att samla in alla resource provider loggar, inklusive AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, sparas i C:\Logs.

## <a name="collect-diagnostic-logs"></a>Samla in diagnostikloggar

Du kan använda PowerShell tillräckligt Administration JEA ()-slutpunkten DBAdapterDiagnostics för att samla in loggar från låst virtuell dator. Den här slutpunkten tillhandahåller följande kommandon:

- **Get-AzsDBAdapterLog**. Det här kommandot skapar ett zip-paket med resource provider-diagnostikloggar och sparar filen på den sessionen enheten. Du kan köra det här kommandot utan några parametrar och de sista fyra timmarna loggar samlas in.

- **Ta bort AzsDBAdapterLog**. Det här kommandot tar bort den befintliga log paket på resursprovidern VM.

### <a name="endpoint-requirements-and-process"></a>Slutpunkten kraven och processen

När en resursprovider installerades eller uppdaterades, skapas dbadapterdiag användarkontot. Du använder det här kontot för att samla in diagnostikloggar.

>[!NOTE]
>Lösenordet för dbadapterdiag är samma som det lösenord som används för lokal administratör på den virtuella datorn som skapas under en provider-distribution eller uppdatering.

Du använder den _DBAdapterDiagnostics_ kommandon, skapa en PowerShell-fjärrsession till resource provider virtuella datorn och kör den **Get-AzsDBAdapterLog** kommando.

Du ställer in tidsintervallet för loggar in med hjälp av den **FromDate** och **ToDate** parametrar. Om du inte anger någon eller båda av dessa parametrar, används följande standardinställningar:

* FromDate är fyra timmar före aktuell tid.
* ToDate är den aktuella tiden.

**PowerShell-Skriptexemplet för insamling av loggar.**

Följande skript visar hur du samlar in diagnostiska loggar från resursprovidern VM.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Nästa steg

[Ta bort MySQL-resursprovider](azure-stack-mysql-resource-provider-remove.md)
