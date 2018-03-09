---
title: "Med hjälp av SQL-databaser på Azure-stacken | Microsoft Docs"
description: "Lär dig hur du kan distribuera SQL-databaser som en tjänst på Azure-stacken och snabba steg för att distribuera SQL Server resource provider nätverkskort."
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 805e39dfdee3a23d4ddc196085be59788cee912a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Använda SQL-databaser på Microsoft Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använda SQL Server resource provider kort för att exponera SQL-databaser som en tjänst av [Azure Stack](azure-stack-poc.md). När du installerar resursprovidern och ansluta till en eller flera instanser av SQL Server kan du och användarna skapa:
- Databaser för moln-ursprungliga appar.
- Webbplatser som är baserade på SQL.
- Arbetsbelastningar som är baserade på SQL.
Du behöver inte etablera en virtuell dator (VM) som är värd för SQL Server varje gång.

Resursprovidern stöder inte alla databasen hanteringsfunktioner i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Till exempel elastiska databaspooler och möjligheten att reglera databasprestanda uppåt och nedåt automatiskt inte tillgänglig. Dock resource provider har stöd liknande skapa, läsa, uppdatera och ta bort CRUD-åtgärder. API: et är inte kompatibel med SQL-databas.

## <a name="sql-resource-provider-adapter-architecture"></a>Adapter-arkitektur för SQL-provider
Resursprovidern består av tre komponenter:

- **SQL resource provider kortet VM**, vilket är en Windows-dator som kör provider-tjänster.
- **Resursprovidern själva**, som bearbetar begäranden om etablering och visar databasen resurser.
- **Servrar som värdar för SQL Server**, som ger kapacitet för databaser kallas värdservrar.

Du måste skapa en (eller flera) instanser av SQL Server och/eller ge åtkomst till externa SQL Server-instanser.

> [!NOTE]
> Värd för servrar som är installerade på Azure-stacken måste integrerade system skapas från en klientprenumeration. De kan inte skapas från standard providern prenumerationen. De måste skapas från klientportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar avgiftsbelagda virtuella datorer och måste ha rätt licenser för. Tjänstadministratören kan vara klient Prenumerationens ägare.

## <a name="deploy-the-resource-provider"></a>Distribuera resursprovidern

1. Om du inte redan har gjort det, registrera dina development kit och ladda ned Windows Server 2016 Datacenter Core avbildningen nedladdningsbara via Marketplace-hantering. Du måste använda en avbildning för Windows Server 2016 Core. Du kan också använda ett skript för att skapa en [Windows Server 2016 avbildningen](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Måste du markera alternativet core.)

2. Logga in på en värd som kan komma åt den privilegierade slutpunkten VM.

    - Logga in på den fysiska värden på Azure-stacken Development Kit-installationer.

    - På datorer med flera noder, måste värden vara ett system som kan komma åt den privilegierade slutpunkten.
    
    >[!NOTE]
    > System där skriptet körs *måste* vara en Windows 10 eller Windows Server 2016 system med den senaste versionen av .NET-körningsmiljön installerad. Annars misslyckas installationen. Azure SDK för stacken värden uppfyller det här kriteriet.


3. Hämta SQL resursprovidern binär. Kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog.

    >[!NOTE] 
    > Resursprovidern har en minsta motsvarande Azure-Stack skapa. Se till att ladda ned rätt binärfil för versionen av Azure-stacken som körs.

    | Azure Stack-version | Installationsprogrammet för providern av SQL-resurs |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP version 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 eller 1.0.180106.1 (med flera noder) | [SQL RP version 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP version 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP version 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Azure-stacken rotcertifikatet hämtas från Privilegierade slutpunkten. För Azure-stacken SDK skapas ett självsignerat certifikat som en del av den här processen. Du måste ange ett lämpligt certifikat för integrerade system.

   För att ge ditt eget certifikat, placera en .pfx-fil i den **DependencyFilesLocalPath** på följande sätt:

    - Antingen ett jokerteckencertifikat för \*.dbadapter.\< region\>.\< externa fqdn\> eller en enda platscertifikat med ett eget namn för sqladapter.dbadapter.\< region\>.\< externa fqdn\>.

    - Det här certifikatet måste vara betrodd. Det vill säga måste förtroendekedja för finnas utan mellanliggande certifikat.

    - En enda certifikatfil kan finnas i katalogen som anges av parametern DependencyFilesLocalPath.

    - Filnamnet får inte innehålla specialtecken.


5. Öppna en **nya** utökade (administratör) PowerShell-konsolen och ändra till katalogen där du extraherade filerna. Använd ett nytt fönster för att undvika problem som kan uppstå från felaktig PowerShell-moduler som har redan lästs in i systemet.

6. [Installera Azure PowerShell version 1.2.11](azure-stack-powershell-install.md).

7. Kör skriptet DeploySqlProvider.ps1 som utför de här stegen:

    - Överför certifikat och andra artefakter till ett lagringskonto på Azure-stacken.
    - Publicerar gallery-paket så att du kan distribuera SQL-databaser via galleriet.
    - Publicerar ett gallery-paket för distribution av värdservrar.
    - Distribuerar en virtuell dator med hjälp av Windows Server 2016-avbildningen som skapades i steg 1 och installerar sedan resursprovidern.
    - Registrerar en lokal DNS-post som mappar till dina VM-resursprovidern.
    - Registrerar din resursprovidern med lokala Azure Resource Manager (användar- och admin).
    - Du installerar en enskild uppdatering i Windows under installationen av RP

8. Vi rekommenderar att du hämtar senaste Windows Server 2016 Core-avbildning från Marketplace-hantering. Du kan placera en enda om du behöver installera en uppdatering. MSU-paketet i den lokala beroende sökvägen. Om mer än en. MSU-fil hittas, skriptet misslyckas.


Här är ett exempel som du kan köra från PowerShell-Kommandotolken. (Måste du ändra kontoinformation och lösenord som krävs.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parametrar
Du kan ange dessa parametrar på kommandoraden. Om du inte, eller om någon parameter valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ |
| **AzCredential** | Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken. | _Krävs_ |
| **VMLocalCredential** | Autentiseringsuppgifterna för det lokala administratörskontot för SQL-resursprovidern VM. | _Krävs_ |
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ |
| **DependencyFilesLocalPath** | Ditt certifikat PFX-fil måste placeras i den här katalogen samt. | _Valfria_ (_obligatoriska_ för flera noder) |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikatet. | _Krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** | Timeoutintervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser (se nedan). | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen

> [!NOTE]
>  När installationen är klar måste du uppdatera portalen om du vill se admin-bladet.


1. Logga in på administrationsportal som tjänstadministratör.

2. Kontrollera att distributionen har slutförts. Gå till **resursgrupper**. Välj sedan den **system.\< plats\>.sqladapter** resursgruppen. Kontrollera att alla fyra distributioner lyckades.

      ![Kontrollera distributionen av SQL-resursprovidern](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Uppdatera SQL resource provider nätverkskort (med flera noder bara versioner 1710 och senare)
Ett nytt SQL resource provider-kort kan frigöras när Azure Stack-versioner har uppdaterats. När det befintliga kortet fortsätter att fungera, rekommenderar vi uppdatera till den senaste versionen så snart som möjligt. Uppdateringar måste installeras i ordning: du kan inte hoppa över versioner (se tabellen ovan).

Uppdateringsprocessen liknar installationen som beskrivs ovan. Du kan skapa en ny virtuell dator med senaste resource provider kod. Dessutom kan migrera du inställningar till den här nya instansen inklusive databas och värd-serverinformation. Du kan också migrera nödvändiga DNS-posten.

Använda skriptet UpdateSQLProvider.ps1 med samma argument som det beskrivs ovan. Du måste ange det här certifikatet samt.

Vi rekommenderar att du hämtar senaste Windows Server 2016 Core-avbildning från Marketplace-hantering. Du kan placera en enda om du behöver installera en uppdatering. MSU-paketet i den lokala beroende sökvägen. Om mer än en. MSU-fil hittas, skriptet misslyckas.


> [!NOTE]
> Uppdateringen gäller bara för integrerade system.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
Du kan ange dessa parametrar på kommandoraden. Om du inte, eller om någon parameter valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ |
| **AzCredential** | Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken. | _Krävs_ |
| **VMLocalCredential** | Autentiseringsuppgifterna för det lokala administratörskontot för SQL-resursprovidern VM. | _Krävs_ |
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ |
| **DependencyFilesLocalPath** | Ditt certifikat PFX-fil måste placeras i den här katalogen samt. | _Valfria_ (_obligatoriska_ för flera noder) |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikatet. | _Krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** |Timeoutintervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser (se nedan). | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |


## <a name="collect-diagnostic-logs"></a>Samla in diagnostikloggar
SQL-resursprovidern är en låst av den virtuella datorn. Om det blir nödvändigt att samla in loggar från den virtuella datorn, en PowerShell bara tillräckligt Administration JEA ()-slutpunkt _DBAdapterDiagnostics_ har angetts för detta ändamål. Det finns två kommandon som är tillgängliga via den här slutpunkten:

* Get-AzsDBAdapterLog - förbereder ett zip-paket som innehåller RP diagnostik loggar och placeras på enheten i sessionen. Kommandot kan anropas utan parametrar och samlar in de sista fyra timmarna loggar.
* Ta bort AzsDBAdapterLog - rensar den befintliga loggen paket på resursprovidern VM

Ett användarkonto kallas _dbadapterdiag_ skapas under RP distribution eller uppdatering för att ansluta till slutpunkten diagnostik för att extrahera RP loggar. Lösenordet för det här kontot är detsamma som det lösenord som angetts för det lokala administratörskontot under distribution/uppdatera.

Om du vill använda dessa kommandon behöver du skapa PowerShell-fjärrsession till resource provider virtuella datorn och kör kommandot. Alternativt kan du ange parametrarna FromDate och ToDate. Om du inte anger något eller båda av dessa, FromDate kommer att fyra timmar före aktuell tid och ToDate blir den aktuella tiden.

Det här exempelskriptet demonstrerar användningen av dessa kommandon:

```
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

## <a name="maintenance-operations-integrated-systems"></a>Underhållsåtgärder (integrerad system)
SQL-resursprovidern är en låst av den virtuella datorn. Uppdaterar den resource provider virtuella datorns säkerhet kan göras via PowerShell bara tillräckligt Administration JEA ()-slutpunkten _DBAdapterMaintenance_.

Ett skript som medföljer den RP installationspaket för att underlätta dessa åtgärder.

### <a name="update-the-virtual-machine-operating-system"></a>Uppdatera operativsystemet för virtuell dator
Det finns flera sätt att uppdatera Windows Server-VM:
* Installera den senaste providern resurspaket med en för närvarande korrigeringsfil Windows Server 2016 Core-bild
* Installera ett paket för Windows Update under installationen eller uppdatering av RP


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Uppdatera definitioner för virtuella Windows Defender

Följ dessa steg om du vill uppdatera definitioner för Defender:

1. Hämta Windows Defender-definitioner uppdatera från [Windows Defender Definition](https://www.microsoft.com/en-us/wdsi/definitions)

    På sidan, under ”hämta och installera manuellt definitionerna” hämta ”Windows Defender Antivirus för Windows 10 och Windows 8.1” 64-bitars filsystem. 
    
    Direktlänk: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Skapa en PowerShell-session till den SQL RP kortet virtuella datorns Underhåll slutpunkten
3. Kopiera filen update definitioner till DB kortet datorn med Underhåll endpoint-session
4. Underhåll PowerShell session anropa den _uppdatering DBAdapterWindowsDefenderDefinitions_ kommando
5. Efter installationen bör du ta bort uppdateringsfilen använda definitioner. Det kan tas bort på Underhåll session med den _ta bort ItemOnUserDrive)_ kommando.


Här är ett exempelskript för att uppdatera definitionerna Defender (Ersätt adressen eller namnet på den virtuella datorn med det faktiska värdet):

```
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

## <a name="remove-the-sql-resource-provider-adapter"></a>Ta bort SQL resource provider-kort

Om du vill ta bort resursprovidern, är det viktigt att du först ta bort eventuella beroenden.

1. Se till att du har de ursprungliga distributionspaket som du hämtade för den här versionen av SQL resource provider nätverkskort.

2. Alla användardatabaser som måste tas bort från resursprovidern. (Databaserna inte bort data.) Den här uppgiften ska utföras av användarna själva.

3. Administratören måste ta bort värdservrar från SQL resource provider nätverkskort.

4. Administratören måste ta bort de scheman som refererar till SQL resource provider nätverkskort.

5. Administratören måste ta bort alla SKU: er och kvoter som är associerade med SQL resource provider-kort.

6. Kör skriptet för distribution med följande element:
    - -Parametern avinstallera
    - Azure Resource Manager-slutpunkter
    - DirectoryTenantID
    - Autentiseringsuppgifterna för tjänstkontot för administratör


## <a name="next-steps"></a>Nästa steg

[Lägg till värdservrar](azure-stack-sql-resource-provider-hosting-servers.md) och [skapa databaser](azure-stack-sql-resource-provider-databases.md).

Försök med andra [PaaS services](azure-stack-tools-paas-services.md) som den [MySQL Server resursprovidern](azure-stack-mysql-resource-provider-deploy.md) och [Apptjänster resursprovidern](azure-stack-app-service-overview.md).
