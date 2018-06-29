---
title: Underhålla resursprovidern MySQL på Azure-stacken | Microsoft Docs
description: Lär dig hur du kan upprätthålla tjänsten MySQL resource provider på Azure-stacken.
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: a1fadd0cfdb38452e86cfce643bebbfd746e8643
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085800"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Underhållsåtgärder för MySQL resource provider

MySQL-resursprovidern körs på virtuella låst. Om du vill aktivera underhållsåtgärder som du behöver uppdatera den virtuella datorns säkerhet. Du kan använda PowerShell bara tillräckligt Administration JEA ()-slutpunkten DBAdapterMaintenance om du vill göra detta med hjälp av principen om minsta behörighet. Installationspaketet för resource provider innehåller ett skript för den här åtgärden.

## <a name="update-the-virtual-machine-operating-system"></a>Uppdatera operativsystemet för virtuell dator

Eftersom resursprovidern körs på en *användaren* virtuell dator, måste du använda de nödvändiga korrigeringarna och programvaruuppdateringarna när de ges ut. Du kan använda uppdateringspaket för Windows som tillhandahålls som en del av korrigeringsfil och uppdateringscykeln för att installera uppdateringar för den virtuella datorn.

Uppdatera providern virtuella datorer som använder någon av följande metoder:

- Installera den senaste providern resurspaket med en för närvarande korrigeringsfil Windows Server 2016 Core-bild.
- Installera ett paket för Windows Update under installationen av eller uppdatera till resursprovidern.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Uppdatera definitioner för virtuella Windows Defender

Följ dessa steg om du vill uppdatera definitionerna Defender:

1. Hämta Windows Defender-definitioner uppdatera från [Windows Defender Definition](https://www.microsoft.com/en-us/wdsi/definitions).

    På sidan definitioner rulla ”hämta och installera manuellt definitionerna”. Hämta filen ”Windows Defender Antivirus för Windows 10 och Windows 8.1” 64-bitars.

    Du kan också använda [denna Direktlänk](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) att hämta/köra filen fpam fe.exe.

2. Öppna ett PowerShell-session till den MySQL resource provider kortet virtuella datorns Underhåll slutpunkten.

3. Kopiera filen definitioner uppdateringen till resource provider kortet VM som använder Underhåll endpoint sessionen.

4. Kör på Underhåll PowerShell-session på _uppdatering DBAdapterWindowsDefenderDefinitions_ kommando.

5. När du har installerat definitioner, rekommenderar vi att du tar bort uppdateringsfilen definitioner genom att använda den _ta bort ItemOnUserDrive)_ kommando.

**Exempel PowerShell-skript för uppdatering av definitioner.**

Du kan redigera och kör följande skript för att uppdatera definitioner för Defender. Ersätt värdena i skriptet med värden från din miljö.

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
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Hemligheter rotation

*Dessa anvisningar gäller endast för Azure-stacken integrerad System Version 1804 och senare. Försök inte att rotera hemligheter i pre-1804 versioner av Azure-stacken.*

När med Azure-stacken resursproviders SQL och MySQL integrerat system, kan du rotera följande infrastruktur (distribution) hemligheter:

- Externa SSL-certifikat [anges under distributionen](azure-stack-pki-certs.md).
- Resource provider VM lokala administratörslösenordet anges under distributionen.
- Resource provider diagnostiska användare (dbadapterdiag) lösenord.

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

**Ändra lösenordet för Virtuella lokala administratörskontot.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Ändra lösenordet för SSL-certifikat.**

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
|AzCredential|Azure-stacken tjänstadministratör kontoautentisering.|
|CloudAdminCredential|Azure Stack molnet admin domän autentiseringsuppgifter för tjänstekontot.|
|PrivilegedEndpoint|Privilegierade slutpunkter för åtkomst till Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostik för lösenord.|
|VMLocalCredential|Det lokala administratörskontot på MySQLAdapter VM.|
|DefaultSSLCertificatePassword|Standard SSL-certifikat (* pfx) lösenord.|
|DependencyFilesLocalPath|Beroende filer lokal sökväg.|
|     |     |

### <a name="known-issues"></a>Kända problem

**Problem:**<br>
Loggar för hemligheter rotation inte samlas in automatiskt om skriptet hemliga rotation misslyckas när den körs.

**Lösning:**<br>
Använd cmdleten Get-AzsDBAdapterLogs för att samla in alla resource provider loggar, inklusive AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, sparas i C:\Logs.

## <a name="collect-diagnostic-logs"></a>Samla in diagnostikloggar

Du kan använda PowerShell bara tillräckligt Administration JEA ()-slutpunkten DBAdapterDiagnostics för att samla in loggar från den virtuella datorn som låst. Den här slutpunkten innehåller följande kommandon:

- **Get-AzsDBAdapterLog**. Det här kommandot skapar en zip-paketet resource provider diagnostik loggar och sparar filen på den sessionen enheten. Du kan köra det här kommandot utan några parametrar och de sista fyra timmarna loggar samlas in.

- **Ta bort AzsDBAdapterLog**. Detta kommando tar bort den befintliga loggen paket på resursprovidern VM.

### <a name="endpoint-requirements-and-process"></a>Slutpunkten kraven och processen

När en resursleverantör installerades eller uppdaterades, skapas dbadapterdiag användarkontot. Det här kontot ska du använda för att samla in diagnostikloggar.

>[!NOTE]
>Dbadapterdiag kontolösenord är detsamma som det lösenord som används för lokal administratör på den virtuella datorn som skapas under en provider-distribution eller uppdatering.

Att använda den _DBAdapterDiagnostics_ kommandon, skapa PowerShell-fjärrsession till resource provider virtuella datorn och kör den **Get-AzsDBAdapterLog** kommando.

Du ställer in tidsintervallet för Logginsamling med den **FromDate** och **ToDate** parametrar. Om du inte anger något eller båda av dessa parametrar, används följande standardinställningar:

* FromDate är fyra timmar före aktuell tid.
* ToDate är den aktuella tiden.

**Exempel PowerShell-skript för insamling av loggar.**

Följande skript visar hur du samlar in diagnostikloggar från resursprovidern VM.

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
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Nästa steg

[Ta bort MySQL-resursprovidern](azure-stack-mysql-resource-provider-remove.md)
