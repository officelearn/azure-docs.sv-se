---
title: Underhåll av SQL-resursprovider i Azure Stack | Microsoft Docs
description: Lär dig hur du kan underhålla SQL resource provider-tjänsten på Azure Stack.
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
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "36300918"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Underhåll-åtgärder för SQL-resursprovider

SQL-resursprovider körs på en låst virtuell dator. Om du vill aktivera underhållsåtgärder som du behöver uppdatera den virtuella datorns säkerhet. Om du vill göra detta med hjälp av principen om lägsta behörighet, kan du använda [PowerShell tillräckligt Administration JEA ()](https://docs.microsoft.com/en-us/powershell/jea/overview) endpoint *DBAdapterMaintenance*. Installationspaketet resource provider innehåller ett skript för den här åtgärden.

## <a name="patching-and-updating"></a>Uppdatering

SQL-resursprovider underhålls inte som en del av Azure Stack eftersom det är en tilläggskomponent som. Microsoft tillhandahåller uppdateringar till SQL-resursprovider som det behövs. När ett uppdaterade SQL-kort har släppts tillhandahålls ett skript för att installera uppdateringen. Det här skriptet skapar en ny resursprovider VM, migrera tillståndet för den gamla VM-providern till den nya virtuella datorn. Mer information finns i [uppdatera SQL-resursprovider](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Providern virtuell dator

Eftersom resursprovidern körs på en *användaren* virtuell dator som du ska använda de uppdateringar som krävs och uppdateringarna när de ges ut. Du kan använda Windows update-paket som tillhandahålls som en del av korrigeringen och uppdateringscykeln för att tillämpa uppdateringar för den virtuella datorn.

## <a name="backuprestoredisaster-recovery"></a>Säkerhetskopiering och återställning/Haveriberedskap

 Eftersom det är en tilläggskomponent som, säkerhetskopieras SQL-resursprovider inte som en del av en process för Azure Stack Business Continuity Disaster Recovery (BCDR). Skript kommer att tillhandahållas för följande åtgärder:

- Säkerhetskopiering av information om tillstånd (lagras i ett Azure Stack-lagringskonto).
- Återställer resursprovidern om en fullständig stack återställning krävs.

>[!NOTE]
>Om du behöver göra en återställning måste databasservrar återställas innan resursprovidern har återställts.

## <a name="updating-sql-credentials"></a>Uppdatering av SQL-autentiseringsuppgifter

Du är ansvarig för att skapa och underhålla sysadmin konton på SQL-servrar. Resursprovidern måste ha ett konto med dessa privilegier för att hantera databaser för användare, men det behöver inte åtkomst till användarnas data. Om du behöver uppdatera lösenorden sysadmin på SQL-servrar kan du kan använda provider för nätverksresurser administratörsgränssnittet för att ändra ett lagrade lösenord. Lösenorden lagras i Key Vault på Azure Stack-instansen.

Om du vill ändra inställningarna, Välj **Bläddra** &gt; **ADMINISTRATIONSRESURSER** &gt; **som är värd för SQL-servrar** &gt; **SQL-inloggningar** och välj ett användarnamn. Ändringen måste göras på SQL-instansen först (och alla repliker, om det behövs.) Under **inställningar**väljer **lösenord**.

![Uppdatera administratörslösenordet](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Hemligheter rotation

*Dessa anvisningar gäller endast för Azure Stack integrerade system Version 1804 och senare. Försök inte att rotera hemligheter i förväg 1804 Azure Stack-versioner.*

När du använder resursprovidrar SQL- och MySQL med Azure Stack integrerade system, kan du rotera hemligheterna för följande infrastruktur (distribution):

- Externt SSL-certifikat [angav under distributionen](azure-stack-pki-certs.md).
- Resource provider VM lokala administratörslösenordet anges under distributionen.
- Resource provider diagnostik (dbadapterdiag) lösenord.

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

**Ändra lösenordet för VM-lokala administratörskontot.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Ändra lösenord för SSL-certifikat.**

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
|AzCredential|Autentiseringsuppgifter för Azure Stack-tjänstadministratör-konto.|
|CloudAdminCredential|Azure Stack molnet admin inloggningsuppgift för domänkontot.|
|PrivilegedEndpoint|Privilegierad slutpunkt för åtkomst till Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostik lösenord.|
|VMLocalCredential|Konto för lokal administratör på MySQLAdapter VM.|
|DefaultSSLCertificatePassword|Standard SSL-certifikat (* pfx) lösenord.|
|DependencyFilesLocalPath|Beroende filer lokal sökväg.|
|     |     |

### <a name="known-issues"></a>Kända problem

**Problemet**: hemligheter rotation loggar.<br>
Loggar för hemligheter rotation inte samlas in automatiskt om det anpassade skriptet hemliga rotation misslyckas när den körs.

**Lösning**:<br>
Använd cmdleten Get-AzsDBAdapterLogs för att samla in alla loggar för resource provider, inklusive AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, sparas i C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Uppdatera operativsystemet för virtuell dator

Använd någon av följande metoder för att uppdatera operativsystemet för virtuell dator.

- Installera det senaste providern resurspaketet med en för närvarande uppdaterad avbildning för Windows Server 2016 Core.
- Installera en uppdatering för Windows-paketet under installationen av eller uppdatera till resursprovidern.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Uppdatera Windows Defender-definitioner för virtuell dator

Att uppdatera Windows Defender-definitioner:

1. Ladda ned Windows Defender-definitioner uppdatera från [Windows Defender Definition](https://www.microsoft.com/en-us/wdsi/definitions).

   Uppdatera sidan och bläddra ned till ”manuellt hämta och installera definitionerna” av definitionerna. Ladda ned filen ”Windows Defender Antivirus för Windows 10 och Windows 8.1” 64-bitars.

   Du kan också använda [den här Direktlänk](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) download/kör fpam fe.exe-filen.

2. Skapa en PowerShell-session till den SQL resource provider nätverkskort virtuella datorns Underhåll slutpunkt.

3. Kopiera filen definitioner uppdatering till den virtuella datorn med Underhåll endpoint sessionen.

4. På Underhåll PowerShell-session och kör den *uppdatering DBAdapterWindowsDefenderDefinitions* kommando.

5. När du har installerat definitionerna rekommenderar vi att du tar bort uppdateringsfilen definitioner genom att använda den *Remove-ItemOnUserDrive* kommando.

**PowerShell-Skriptexemplet för att uppdatera definitioner.**

Du kan redigera och kör följande skript för att uppdatera Defender-definitioner. Ersätt värdena i skriptet med värden från din miljö.

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

Du kan använda PowerShell tillräckligt Administration JEA ()-slutpunkten för att samla in loggar från den virtuella datorn som låst *DBAdapterDiagnostics*. Den här slutpunkten tillhandahåller följande kommandon:

- **Get-AzsDBAdapterLog**. Det här kommandot skapar ett zip-paket med resource provider-diagnostikloggar och sparar filen på den sessionen enheten. Du kan köra det här kommandot utan några parametrar och de sista fyra timmarna loggar samlas in.
- **Ta bort AzsDBAdapterLog**. Det här kommandot tar bort den befintliga log paket på resursprovidern VM.

### <a name="endpoint-requirements-and-process"></a>Slutpunkten kraven och processen

När en resursprovider installerades eller uppdaterades, den **dbadapterdiag** användarkonto har skapats. Du använder det här kontot för att samla in diagnostikloggar.

>[!NOTE]
>Lösenordet för dbadapterdiag är samma som det lösenord som används för lokal administratör på den virtuella datorn som skapas under en provider-distribution eller uppdatering.

Du använder den *DBAdapterDiagnostics* kommandon, skapa en PowerShell-fjärrsession till resource provider virtuella datorn och kör den **Get-AzsDBAdapterLog** kommando.

Du ställer in tidsintervallet för loggar in med hjälp av den **FromDate** och **ToDate** parametrar. Om du inte anger någon eller båda av dessa parametrar, används följande standardinställningar:

- FromDate är fyra timmar före aktuell tid.
- ToDate är den aktuella tiden.

**PowerShell-Skriptexemplet för insamling av loggar.**

Följande skript visar hur du samlar in diagnostiska loggar från resursprovidern VM.

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

[Lägg till SQL Server som är värd för servrar](azure-stack-sql-resource-provider-hosting-servers.md)
