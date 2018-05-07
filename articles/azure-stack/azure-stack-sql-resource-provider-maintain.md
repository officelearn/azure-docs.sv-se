---
title: Med hjälp av SQL-databaser på Azure-stacken | Microsoft Docs
description: Lär dig hur du kan distribuera SQL-databaser som en tjänst på Azure-stacken och snabba steg för att distribuera SQL Server resource provider nätverkskort.
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 53436d131672622ae1a72a1bb84d5aa83fdbdc0c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
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

## <a name="update-the-virtual-machine-operating-system"></a>Uppdatera operativsystemet för virtuell dator
Det finns flera sätt att uppdatera Windows Server-VM:
* Installera den senaste providern resurspaket med en för närvarande korrigeringsfil Windows Server 2016 Core-bild
* Installera ett paket för Windows Update under installationen eller uppdatering av RP

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Uppdatera definitioner för virtuella Windows Defender
Följ dessa steg om du vill uppdatera definitioner för Defender:

1. Hämta Windows Defender-definitioner uppdatera från [Windows Defender Definition](https://www.microsoft.com/en-us/wdsi/definitions)

    På sidan, under ”hämta och installera manuellt definitionerna” hämta ”Windows Defender Antivirus för Windows 10 och Windows 8.1” 64-bitars filsystem. 
    
    Direktlänk: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Skapa en PowerShell-session till den SQL RP kortet virtuella datorns Underhåll slutpunkten
3. Kopiera filen update definitioner till DB kortet datorn med Underhåll endpoint-session
4. Underhåll PowerShell session anropa den _uppdatering DBAdapterWindowsDefenderDefinitions_ kommando
5. Efter installationen bör du ta bort uppdateringsfilen använda definitioner. Det kan tas bort på Underhåll session med den _ta bort ItemOnUserDrive)_ kommando.


Här är ett exempelskript för att uppdatera definitionerna Defender (Ersätt adressen eller namnet på den virtuella datorn med det faktiska värdet):

```powershell
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="collect-diagnostic-logs"></a>Samla in diagnostikloggar
SQL-resursprovidern är en låst av den virtuella datorn. Om det blir nödvändigt att samla in loggar från den virtuella datorn, en PowerShell bara tillräckligt Administration JEA ()-slutpunkt _DBAdapterDiagnostics_ har angetts för detta ändamål. Det finns två kommandon som är tillgängliga via den här slutpunkten:

* Get-AzsDBAdapterLog - förbereder ett zip-paket som innehåller RP diagnostik loggar och placeras på enheten i sessionen. Kommandot kan anropas utan parametrar och samlar in de sista fyra timmarna loggar.
* Ta bort AzsDBAdapterLog - rensar den befintliga loggen paket på resursprovidern VM

Ett användarkonto kallas _dbadapterdiag_ skapas under RP distribution eller uppdatering för att ansluta till slutpunkten diagnostik för att extrahera RP loggar. Lösenordet för det här kontot är detsamma som det lösenord som angetts för det lokala administratörskontot under distribution/uppdatera.

Om du vill använda dessa kommandon behöver du skapa PowerShell-fjärrsession till resource provider virtuella datorn och kör kommandot. Alternativt kan du ange parametrarna FromDate och ToDate. Om du inte anger något eller båda av dessa, FromDate kommer att fyra timmar före aktuell tid och ToDate blir den aktuella tiden.

Det här exempelskriptet demonstrerar användningen av dessa kommandon:

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
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
