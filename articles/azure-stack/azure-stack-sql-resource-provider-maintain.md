---
title: Underhålla resursprovidern SQL Azure-stacken | Microsoft Docs
description: Lär dig hur du kan upprätthålla tjänsten SQL resource provider på Azure-stacken.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ad899739dab1dc51d64368d2136ab87f73f6f3a0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300918"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Underhållsåtgärder för SQL resource provider

SQL-resursprovidern körs på virtuella låst. Om du vill aktivera underhållsåtgärder som du behöver uppdatera den virtuella datorns säkerhet. Om du vill göra detta med hjälp av principen om minsta behörighet, kan du använda [PowerShell bara tillräckligt Administration JEA ()](https://docs.microsoft.com/en-us/powershell/jea/overview) endpoint *DBAdapterMaintenance*. Installationspaketet för resource provider innehåller ett skript för den här åtgärden.

## <a name="patching-and-updating"></a>Uppdateringsverktyg och uppdatera

SQL-resursprovidern är inte underhålls som en del av Azure-stacken eftersom det är en tilläggskomponent. Microsoft tillhandahåller uppdateringar till SQL-resursprovidern vid behov. När ett uppdaterats SQL-kort släpps, tillhandahålls ett skript för att installera uppdateringen. Det här skriptet skapar en ny resurs provider VM, migrera tillståndet för den gamla providern VM till den nya virtuella datorn. Mer information finns i [uppdatera resursprovidern SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Providern virtuell dator

Eftersom resursprovidern körs på en *användaren* virtuell dator, måste du använda de nödvändiga korrigeringarna och programvaruuppdateringarna när de ges ut. Du kan använda uppdateringspaket för Windows som tillhandahålls som en del av korrigeringsfil och uppdateringscykeln för att installera uppdateringar för den virtuella datorn.

## <a name="backuprestoredisaster-recovery"></a>Säkerhetskopiering/återställning/Disaster Recovery

 Eftersom det är en tilläggskomponent säkerhetskopieras resursprovidern SQL inte som en del av en Azure Stack Business Continuity Disaster Recovery BCDR-process. Skript kommer att tillhandahållas för följande åtgärder:

- Säkerhetskopiera statusinformation (som lagras i ett Azure-stacken storage-konto.)
- Återställer resursprovidern om återställning av en fullständig stack krävs.

>[!NOTE]
>Om du behöver göra en återställning måste databasservrar återställas innan resursprovidern återställs.

## <a name="updating-sql-credentials"></a>Uppdatera autentiseringsuppgifterna för SQL

Du är ansvarig för att skapa och hantera konton sysadmin på SQL-servrar. Resursprovidern måste ha ett konto med dessa behörigheter för att hantera databaser för användare, men behöver inte åtkomst till användarnas data. Om du behöver uppdatera lösenorden sysadmin på SQL-servrar kan du använda gränssnittet för resurs-providerns administrator för att ändra ett lagrade lösenord. Lösenord lagras i ett Nyckelvalv på Azure Stack-instansen.

Om du vill ändra inställningarna, Välj **Bläddra** &gt; **ADMINISTRATIONSRESURSER** &gt; **värd för SQL-servrar** &gt; **SQL-inloggningar** och välj ett användarnamn. Den måste ändras för SQL-instans först (och alla repliker, om det behövs.) Under **inställningar**väljer **lösenord**.

![Uppdatera administratörslösenordet](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Hemligheter rotation

*Dessa anvisningar gäller endast för Azure-stacken integrerad System Version 1804 och senare. Försök inte att rotera hemligheter i pre-1804 Azure Stack versioner.*

När med Azure-stacken resursproviders SQL och MySQL integrerat system, kan du rotera följande infrastruktur (distribution) hemligheter:

- Externa SSL-certifikat [anges under distributionen](azure-stack-pki-certs.md).
- Resource provider VM lokala administratörslösenordet anges under distributionen.
- Resource provider diagnostiska användare (dbadapterdiag) lösenord.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-exempel för att rotera hemligheter

**Ändra alla hemligheter på samma gång.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Ändra diagnostiska användarens lösenord.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**Ändra lösenordet för Virtuella lokala administratörskontot.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Ändra lösenordet för SSL-certifikat.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 parametrar

|Parameter|Beskrivning|
|-----|-----|
|AzCredential|Azure-stacken tjänstadministratör kontoautentisering.|
|CloudAdminCredential|Azure Stack molnet admin domän autentiseringsuppgifter för tjänstekontot.|
|PrivilegedEndpoint|Privilegierade slutpunkter för åtkomst till Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostik för lösenord.|
|VMLocalCredential|Lokala administratörskontot på MySQLAdapter VM.|
|DefaultSSLCertificatePassword|Standard SSL-certifikat (* pfx) lösenord.|
|DependencyFilesLocalPath|Beroende filer lokal sökväg.|
|     |     |

### <a name="known-issues"></a>Kända problem

**Problemet**: hemligheter rotation loggar.<br>
Loggar för hemligheter rotation inte samlas in automatiskt om hemliga rotation anpassat skript misslyckas när den körs.

**Lösning**:<br>
Använd cmdleten Get-AzsDBAdapterLogs för att samla in alla loggar för resource provider, inklusive AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, sparas i C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Uppdatera operativsystemet för virtuell dator

Använd någon av följande metoder för att uppdatera operativsystemet för virtuell dator.

- Installera den senaste providern resurspaket med en för närvarande korrigeringsfil Windows Server 2016 Core-bild.
- Installera ett paket för Windows Update under installationen av eller uppdatera till resursprovidern.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Uppdatera definitioner för virtuella Windows Defender

Uppdatera definitioner för Windows Defender:

1. Hämta Windows Defender-definitioner uppdatera från [Windows Defender Definition](https://www.microsoft.com/en-us/wdsi/definitions).

   Uppdatera sidan och rulla ned till ”hämta och installera manuellt definitionerna” av definitionerna. Hämta filen ”Windows Defender Antivirus för Windows 10 och Windows 8.1” 64-bitars.

   Du kan också använda [denna Direktlänk](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) att hämta/köra filen fpam fe.exe.

2. Skapa en PowerShell-session till den SQL resource provider kortet virtuella datorns Underhåll slutpunkten.

3. Kopiera filen definitioner uppdateringen till den virtuella datorn med Underhåll endpoint sessionen.

4. Kör på Underhåll PowerShell-session på *uppdatering DBAdapterWindowsDefenderDefinitions* kommando.

5. När du har installerat definitioner, rekommenderar vi att du tar bort uppdateringsfilen definitioner genom att använda den *ta bort ItemOnUserDrive* kommando.

**Exempel PowerShell-skript för uppdatering av definitioner.**

Du kan redigera och kör följande skript för att uppdatera definitioner för Defender. Ersätt värdena i skriptet med värden från din miljö.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
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

## <a name="collect-diagnostic-logs"></a>Samla in diagnostikloggar

Du kan använda PowerShell bara tillräckligt Administration JEA ()-slutpunkten för att samla in loggar från låst virtuella *DBAdapterDiagnostics*. Den här slutpunkten innehåller följande kommandon:

- **Get-AzsDBAdapterLog**. Det här kommandot skapar en zip-paketet resource provider diagnostik loggar och sparar filen på den sessionen enheten. Du kan köra det här kommandot utan några parametrar och de sista fyra timmarna loggar samlas in.
- **Ta bort AzsDBAdapterLog**. Detta kommando tar bort den befintliga loggen paket på resursprovidern VM.

### <a name="endpoint-requirements-and-process"></a>Slutpunkten kraven och processen

När en resursleverantör installerades eller uppdaterades, den **dbadapterdiag** användarkontot skapas. Det här kontot ska du använda för att samla in diagnostikloggar.

>[!NOTE]
>Dbadapterdiag kontolösenord är detsamma som det lösenord som används för lokal administratör på den virtuella datorn som skapas under en provider-distribution eller uppdatering.

Att använda den *DBAdapterDiagnostics* kommandon, skapa PowerShell-fjärrsession till resource provider virtuella datorn och kör den **Get-AzsDBAdapterLog** kommando.

Du ställer in tidsintervallet för Logginsamling med den **FromDate** och **ToDate** parametrar. Om du inte anger något eller båda av dessa parametrar, används följande standardinställningar:

- FromDate är fyra timmar före aktuell tid.
- ToDate är den aktuella tiden.

**Exempel PowerShell-skript för insamling av loggar.**

Följande skript visar hur du samlar in diagnostikloggar från resursprovidern VM.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
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

[Lägg till SQL Server som värd för servrar](azure-stack-sql-resource-provider-hosting-servers.md)
