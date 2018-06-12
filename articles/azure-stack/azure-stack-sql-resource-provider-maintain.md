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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e7ddbe1235b3957a1e0cb7693ee728bfdbf9db6b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295667"
---
# <a name="maintenance-operations"></a>Underhåll 
SQL-resursprovidern är en låst av den virtuella datorn. Uppdaterar den resource provider virtuella datorns säkerhet kan göras via PowerShell bara tillräckligt Administration JEA ()-slutpunkten _DBAdapterMaintenance_. Ett skript som medföljer den RP installationspaket för att underlätta dessa åtgärder.

## <a name="patching-and-updating"></a>Uppdateringsverktyg och uppdatera
SQL-resursprovidern servas inte som en del av Azure-stacken eftersom det är en tilläggskomponent. Microsoft tillhandahåller uppdateringar till SQL-resursprovidern vid behov. SQL-resursprovidern instansieras på en _användaren_ virtuell dator i prenumerationen Standard Provider. Därför är det nödvändigt att ange Windows korrigeringsfiler, antivirus signaturer osv. Windows uppdateringspaket som tillhandahålls som en del av korrigeringsfil och uppdateringscykeln kan användas för att installera uppdateringar för Windows-VM. När en uppdaterad adapter släpps, tillhandahålls ett skript för att installera uppdateringen. Det här skriptet skapar en ny RP VM och migrera några tillstånd som du redan har.

 ## <a name="backuprestoredisaster-recovery"></a>Säkerhetskopiering/återställning/Disaster Recovery
 SQL-resursprovidern säkerhetskopieras inte som en del av Azure Stack BC-DR-processen, eftersom det är en tilläggskomponent. Skript kommer att tillhandahållas för att underlätta:
- Säkerhetskopiering av nödvändiga statusinformation (som lagras i ett lagringskonto i Azure-Stack)
- Återställer RP återställning av en fullständig stack blir nödvändigt.
Databasservrar måste återställas först (om det behövs), innan resursen providern har återställts.

## <a name="updating-sql-credentials"></a>Uppdatera autentiseringsuppgifterna för SQL
Du ansvarar för att skapa och underhålla system administratörskonton på SQL-servrar. Resursprovidern måste ha ett konto med dessa behörigheter för att hantera databaser åt användare - behöver inte åtkomst till data i dessa databaser. Om du behöver uppdatera sa-lösenord på SQL-servrar använda du update möjligheterna för resurs-providerns administratörsgränssnittet för att ändra det lagrade lösenordet som används av resursprovidern. Lösenord lagras i ett Nyckelvalv på Azure Stack-instansen.

Om du vill ändra inställningarna klickar du på **Bläddra** &gt; **ADMINISTRATIONSRESURSER** &gt; **värd för SQL-servrar** &gt; **SQL-inloggningar** och välj ett inloggningsnamn. Den måste ändras för SQL-instans först (och alla repliker, om det behövs). I den **inställningar** panelen, klickar du på **lösenord**.

![Uppdatera administratörslösenordet](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Hemligheter rotation 
*Dessa anvisningar gäller bara för Azure-stacken integrerad System Version 1804 och senare. Försök inte hemliga rotation i pre-1804 Azure Stack versioner.*

När med Azure-stacken resursproviders SQL och MySQL integrerat system, kan du rotera följande infrastruktur (distribution) hemligheter:
- Externa SSL-certifikat [anges under distributionen](azure-stack-pki-certs.md).
- Resource provider VM lokala administratörslösenordet anges under distributionen.
- Resource provider diagnostiska användare (dbadapterdiag) lösenord.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-exempel för att rotera hemligheter

**Ändra alla hemligheter på samma gång**
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

**Ändra diagnostiska användarlösenord**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd 
```

**Ändra lösenordet för Virtuella lokala administratörskontot**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Ändra SSL-certifikat**
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
|DiagnosticsUserPassword|Diagnostik användarens lösenord.|
|VMLocalCredential|Det lokala administratörskontot för MySQLAdapter VM.|
|DefaultSSLCertificatePassword|Standard SSL-certifikat (* pfx) lösenord.|
|DependencyFilesLocalPath|Beroende filer lokal sökväg.|
|     |     |

### <a name="known-issues"></a>Kända problem
**Problemet**: loggarna för hemligheter rotation samlas inte in automatiskt om hemliga rotation anpassat skript misslyckas när den körs.

**Lösning**: Använd cmdlet Get-AzsDBAdapterLogs för att samla in alla loggar för resource provider, inklusive AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log under C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Uppdatera operativsystemet för virtuell dator
Det finns flera sätt att uppdatera Windows Server-VM:
- Installera den senaste providern resurspaket med en för närvarande korrigeringsfil Windows Server 2016 Core-bild
- Installera ett paket för Windows Update under installationen eller uppdatering av RP

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Uppdatera definitioner för virtuella Windows Defender
Följ dessa steg om du vill uppdatera definitioner för Defender:

1. Hämta Windows Defender-definitioner uppdatera från [Windows Defender Definition](https://www.microsoft.com/en-us/wdsi/definitions).

    På sidan, under ”hämta och installera manuellt definitionerna” hämta ”Windows Defender Antivirus för Windows 10 och Windows 8.1” 64-bitars filsystem. 
    
    Direktlänk: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64.

2. Skapa en PowerShell-session till den SQL RP kortet virtuella datorns Underhåll slutpunkten
3. Kopiera filen update definitioner till DB kortet datorn med Underhåll endpoint-session
4. Underhåll PowerShell session anropa den _uppdatering DBAdapterWindowsDefenderDefinitions_ kommando
5. Efter installationen bör du ta bort uppdateringsfilen använda definitioner. Det kan tas bort på Underhåll session med den _ta bort ItemOnUserDrive)_ kommando.


Här är ett exempelskript för att uppdatera definitionerna Defender (Ersätt adressen eller namnet på den virtuella datorn med det faktiska värdet):

```powershell
# Set credentials for the RP VM local admin user
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession 
```


## <a name="collect-diagnostic-logs"></a>Samla in diagnostikloggar
SQL-resursprovidern är en låst av den virtuella datorn. Om det blir nödvändigt att samla in loggar från den virtuella datorn, en PowerShell bara tillräckligt Administration JEA ()-slutpunkt _DBAdapterDiagnostics_ har angetts för detta ändamål. Det finns två kommandon som är tillgängliga via den här slutpunkten:

- **Get-AzsDBAdapterLog**. Förbereder ett zip-paket som innehåller RP diagnostik loggar och placeras på enheten i sessionen. Kommandot kan anropas utan parametrar och samlar in de sista fyra timmarna loggar.
- **Ta bort AzsDBAdapterLog**. Rensar upp den befintliga loggen paket på resursprovidern VM

Ett användarkonto kallas **dbadapterdiag** skapas under RP distribution eller uppdatering för att ansluta till slutpunkten diagnostik för att extrahera RP loggar. Lösenordet för det här kontot är detsamma som det lösenord som angetts för det lokala administratörskontot under distribution/uppdatera.

Om du vill använda dessa kommandon behöver du skapa PowerShell-fjärrsession till resource provider virtuella datorn och kör kommandot. Alternativt kan du ange parametrarna FromDate och ToDate. Om du inte anger något eller båda av dessa, FromDate kommer att fyra timmar före aktuell tid och ToDate blir den aktuella tiden.

Det här exempelskriptet demonstrerar användningen av dessa kommandon:

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>Nästa steg
[Lägg till SQL Server som värd för servrar](azure-stack-sql-resource-provider-hosting-servers.md)
