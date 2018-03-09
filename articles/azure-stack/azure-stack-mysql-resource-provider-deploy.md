---
title: "Använda MySQL-databaser som PaaS på Azure-stacken | Microsoft Docs"
description: "Lär dig hur du kan distribuera MySQL Resource Provider och ange MySQL-databaser som en tjänst på Azure-stacken."
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
ms.openlocfilehash: 067e478548ba840ece14737cdf3e6d5d4da28be0
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Använda MySQL-databaser på Microsoft Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan distribuera en MySQL-resursprovidern på Azure-stacken. När du har distribuerat resursprovidern kan du skapa MySQL-servrar och databaser via Azure Resource Manager-mallar för distribution. Du kan också ge MySQL-databaser som en tjänst. 

MySQL-databaser som är vanliga på webbplatser, stöd för flera olika plattformar webbplats. Till exempel när du har distribuerat resursprovidern kan du skapa WordPress webbplats från Web Apps-plattform som en tjänst (PaaS)-tillägg för Azure-stacken.

Om du vill distribuera MySQL-providern på ett system som inte har tillgång till Internet, kopiera filen [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) till en lokal resurs. Ange att resursnamnet när du tillfrågas om den. Du måste installera Azure och Azure-stacken PowerShell-moduler.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Adapter-arkitektur för MySQL-Server-provider

Resursprovidern består av tre komponenter:

- **MySQL resource provider kortet VM**, vilket är en Windows-dator som kör provider-tjänster.

- **Resursprovidern själva**, som bearbetar begäranden om etablering och visar databasen resurser.

- **Servrar som är värd för MySQL servern**, som ger kapacitet för databaser som kallas värd för servrar.

Den här versionen skapar inte längre MySQL-instanser. Det innebär att du behöver skapa dem själv och/eller ge åtkomst till externa SQL-instanser. Besök den [Azure Stack Snabbstartsgalleriet](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) för en exempelmall kan:
- Skapa en MySQL-server.
- Hämta och distribuera en MySQL-Server från Azure Marketplace.

> [!NOTE]
> Värd för servrar som är installerade på Azure-stacken måste integrerade system skapas från en klientprenumeration. De kan inte skapas från standard providern prenumerationen. De måste skapas från klientportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar avgiftsbelagda virtuella datorer och måste ha rätt licenser för. Tjänstadministratören kan vara klient Prenumerationens ägare.

### <a name="required-privileges"></a>Behörigheter som krävs
System-kontot måste ha följande behörigheter:

1.  Databas: Skapa, ta bort
2.  Inloggning: Skapa, ställa in, drop, bevilja, återkalla

## <a name="deploy-the-resource-provider"></a>Distribuera resursprovidern

1. Om du inte redan har gjort det, registrera dina development kit och ladda ned Windows Server 2016 Datacenter Core avbildningen nedladdningsbara via Marketplace-hantering. Du måste använda en avbildning för Windows Server 2016 Core. Du kan också använda ett skript för att skapa en [Windows Server 2016 avbildningen](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Måste du markera alternativet core.)


2. Logga in på en värd som kan komma åt den privilegierade slutpunkten VM.

    - Logga in på den fysiska värden på Azure SDK-installationer. 
    - På datorer med flera noder, måste värden vara ett system som kan komma åt den privilegierade slutpunkten.
    
    >[!NOTE]
    > System där skriptet körs *måste* vara en Windows 10 eller Windows Server 2016 system med den senaste versionen av .NET-körningsmiljön installerad. Annars misslyckas installationen. Azure SDK för stacken värden uppfyller det här kriteriet.
    

3. Hämta binära MySQL resursprovidern. Kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog.

    >[!NOTE] 
    > Resursprovidern har en minsta motsvarande Azure-Stack skapa. Se till att ladda ned rätt binärfil för versionen av Azure-stacken som körs.

    | Azure Stack-version | MySQL RP installer |
    | --- | --- |
    | 1802: 1.0.180302.1 | [MySQL RP version 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 eller 1.0.180106.1 (med flera noder) | [MySQL RP version 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP version 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP version 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  Azure-stacken rotcertifikatet hämtas från Privilegierade slutpunkten. För Azure-SDK skapas ett självsignerat certifikat som en del av den här processen. Du måste ange ett lämpligt certifikat för flera noder.

    Om du måste ange ditt eget certifikat, placera en .pfx-fil i den **DependencyFilesLocalPath** som uppfyller följande kriterier:

    - Antingen ett jokerteckencertifikat för \*.dbadapter.\< region\>.\< externa fqdn\> eller en enda platscertifikat med ett eget namn för mysqladapter.dbadapter.\< region\>.\< externa fqdn\>.

    - Det här certifikatet måste vara betrodd. Det vill säga måste förtroendekedja för finnas utan mellanliggande certifikat.

    - En enda certifikatfil finns i DependencyFilesLocalPath.
    
    - Filnamnet får inte innehålla några specialtecken eller blanksteg.


5. Öppna en **nya** utökade (administratör) PowerShell-konsolen. Ändra till katalogen där du extraherade filerna. Använd ett nytt fönster för att undvika problem som kan uppstå från felaktig PowerShell-moduler som har redan lästs in i systemet.

6. [Installera Azure PowerShell version 1.2.11](azure-stack-powershell-install.md).

7. Kör skriptet `DeployMySqlProvider.ps1`.

Skriptet utför de här stegen:

* Hämtar MySQL connector binärfilen (Detta kan ges offline).
* Överför certifikat och andra artefakter till ett lagringskonto på Azure-stacken.
* Publicerar gallery-paket så att du kan distribuera SQL-databaser via galleriet.
* Publicerar ett gallery-paket för distribution av värdservrar.
* Distribuerar en virtuell dator med hjälp av Windows Server 2016-avbildningen som skapades i steg 1. Den installeras också resursprovidern.
* Registrerar en lokal DNS-post som mappar till dina VM-resursprovidern.
* Registrerar din resursprovidern med lokala Azure Resource Manager (klient och admin).


Du kan:
- Ange nödvändiga parametrar på kommandoraden.
- Kör utan några parametrar och ange dem när du blir tillfrågad.

Här är ett exempel som du kan köra från PowerShell-Kommandotolken. Glöm inte att ändra kontoinformation och lösenord som behövs:


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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parametrar
Du kan ange dessa parametrar på kommandoraden. Om du inte, eller om någon parameter valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ |
| **AzCredential** | Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken. | _Krävs_ |
| **VMLocalCredential** | Autentiseringsuppgifterna för det lokala administratörskontot för resursprovidern MySQL VM. | _Krävs_ |
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ |
| **DependencyFilesLocalPath** | Sökvägen till en lokal resurs som innehåller [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Om du anger ett av dessa sökvägar måste certifikatfilen placeras i den här katalogen samt. | _Valfria_ (_obligatoriska_ för flera noder) |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikatet. | _Krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** | Timeoutintervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser (se nedan). | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |
| **AcceptLicense** | Hoppar över frågan om du vill acceptera licensvillkoren GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen

> [!NOTE]
>  När installationen är klar måste du uppdatera portalen om du vill se admin-bladet.


1. Logga in på administrationsportal som tjänstadministratör.

2. Kontrollera att distributionen har slutförts. Gå till **resursgrupper**, och välj sedan den **system.\< plats\>.mysqladapter** resursgruppen. Kontrollera att alla fyra distributioner lyckades.

      ![Kontrollera distributionen av MySQL RP](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Ange kapacitet genom att ansluta till en värd MySQL-server

1. Logga in på Azure Stack-portalen som en tjänstadministratör

2. Välj **administrativa resurser** > **MySQL som värd för servrar** > **+ Lägg till**.

    På den **MySQL värd servrar** bladet kan du ansluta resursprovidern MySQL-servern till verkliga förekomster av MySQL-Server som fungerar som den resursprovidern serverdel.

    ![Värdservrar](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Ange anslutningsinformation för MySQL-Server-instansen. Se till att ange det fullständigt kvalificerade domännamnet (FQDN) eller en giltig IPv4-adress och inte det korta namnet VM. Den här installationen inte längre innehåller en standardinstans MySQL. Den storlek som tillhandahålls hjälper resursprovidern hantera databaskapacitet. Det bör vara nära den fysiska kapaciteten för databasservern.

    > [!NOTE]
    >Om MySQL-instans kan användas av klienten och Azure Resource Manager-administratören, kan den placeras under kontroll av resursprovidern. MySQL-instans *måste* tilldelas enbart resursprovidern.

4. När du lägger till servrar, måste du tilldela dem till en ny eller befintlig SKU att differentiering av Tjänsterbjudanden.
  Du kan till exempel har en enterprise-instans tillhandahåller:
    - databaskapacitet
    - Automatisk säkerhetskopiering
    - reservera högpresterande servrar för olika avdelningar
 

SKU-namnet bör avspegla egenskaper så att klienter kan placera sina databaser på lämpligt sätt. Alla värdservrar i en SKU ska ha samma funktioner.

![Skapa en MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKU: er kan ta upp till en timme att vara synliga i portalen. Du kan inte skapa en databas förrän SKU: N har skapats.


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Testa distributionen genom att skapa din första MySQL-databas


1. Logga in på Azure Stack-portalen som en tjänstadministratör

2. Välj **+ ny** > **Data + lagring** > **MySQL-databas**.

3. Ange detaljer för databasen.

    ![Skapa en test MySQL-databas](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Välj en SKU.

    ![Välj en SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Skapa en inställning för inloggning. Du kan återanvända en befintlig inloggning inställning eller skapa en ny. Den här inställningen innehåller användarnamn och lösenord för databasen.

    ![Skapa en ny databasinloggning](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    Anslutningssträngen innehåller det verkliga Databasservernamnet. Kopiera den från portalen.

    ![Hämta anslutningssträngen för MySQL-databas](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> Längden på användarnamn får inte överstiga 32 tecken i MySQL 5.7. Det får inte överstiga 16 tecken i tidigare versioner.


## <a name="add-capacity"></a>Lägga till kapacitet

Lägga till kapacitet genom att lägga till ytterligare servrar för MySQL på Azure Stack-portalen. Ytterligare servrar kan läggas till en ny eller befintlig SKU. Kontrollera server-egenskaper är samma.


## <a name="make-mysql-databases-available-to-tenants"></a>Göra MySQL-databaser som är tillgängliga för klienter
Skapa planer och erbjudanden om du vill göra MySQL-databaser som är tillgängliga för klienter. Till exempel lägga till tjänsten Microsoft.MySqlAdapter, lägga till en kvot och så vidare.

![Skapa planer och erbjudanden om du vill inkludera databaser](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Uppdatera lösenordet för administratörer
Du kan ändra lösenordet genom att först ändra MySQL-serverinstansen. Välj **administrativa resurser** > **MySQL som värd för servrar**. Välj servern som värd. I den **inställningar** fönstret väljer **lösenord**.

![Uppdatera administratörslösenordet](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Uppdatera MySQL resource provider nätverkskort (med flera noder bara versioner 1710 och senare)
Ett nytt SQL resource provider-kort kan frigöras när Azure Stack-versioner har uppdaterats. Medan det befintliga kortet fortsätter att fungera, rekommenderar vi uppdatera till den senaste versionen så snart som möjligt. 

Uppdateringsprocessen liknar installationen som beskrivs ovan. Du kan skapa en ny virtuell dator med senaste resource provider kod. Sedan migrerar du inställningarna till den här nya instans, inklusive databas och värd-serverinformation. Du kan också migrera nödvändiga DNS-posten.

Använda skriptet UpdateMySQLProvider.ps1 med samma argument som beskrivs ovan. Ange certifikat här samt.

> [!NOTE]
> Uppdateringen gäller bara för integrerade system.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 parametrar
Du kan ange dessa parametrar på kommandoraden. Om du inte, eller om någon parameter valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ |
| **AzCredential** | Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken. | _Krävs_ |
| **VMLocalCredential** |Autentiseringsuppgifterna för det lokala administratörskontot för SQL-resursprovidern VM. | _Krävs_ |
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ |
| **DependencyFilesLocalPath** | Ditt certifikat PFX-fil måste placeras i den här katalogen samt. | _Valfria_ (_obligatoriska_ för flera noder) |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikat | _Krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** | Timeoutintervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Ta bort resursprovidern och alla associerade resurser (se nedan). | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |
| **AcceptLicense** | Hoppar över frågan om du vill acceptera licensvillkoren GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>Samla in diagnostikloggar
MySQL-resursprovidern är en låst av den virtuella datorn. Om det blir nödvändigt att samla in loggar från den virtuella datorn, en PowerShell bara tillräckligt Administration JEA ()-slutpunkt _DBAdapterDiagnostics_ har angetts för detta ändamål. Det finns två kommandon som är tillgängliga via den här slutpunkten:

* Get-AzsDBAdapterLog - förbereder ett zip-paket som innehåller RP diagnostik loggar och placeras på enheten i sessionen. Kommandot kan anropas utan parametrar och samlar in de sista fyra timmarna loggar.
* Ta bort AzsDBAdapterLog - rensar den befintliga loggen paket på resursprovidern VM

Ett användarkonto kallas _dbadapterdiag_ skapas under RP distribution eller uppdatering för att ansluta till slutpunkten diagnostik för att extrahera RP loggar. Lösenordet för det här kontot är detsamma som det lösenord som angetts för det lokala administratörskontot under distribution/uppdatera.

Om du vill använda dessa kommandon måste du skapa en PowerShell-fjärrsession till resource provider virtuell dator och anropa kommandot. Alternativt kan du ange parametrarna FromDate och ToDate. Om du inte anger något eller båda av dessa, FromDate kommer att fyra timmar före aktuell tid och ToDate blir den aktuella tiden.

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
MySQL-resursprovidern är en låst av den virtuella datorn. Uppdaterar den resource provider virtuella datorns säkerhet kan göras via PowerShell bara tillräckligt Administration JEA ()-slutpunkten _DBAdapterMaintenance_.

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

2. Skapa en PowerShell-session till den MySQL RP kortet virtuella datorns Underhåll slutpunkten
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


## <a name="remove-the-mysql-resource-provider-adapter"></a>Ta bort MySQL resource provider-kort

Om du vill ta bort resursprovidern, är det viktigt att du först ta bort eventuella beroenden.

1. Se till att du har de ursprungliga distributionspaket som du hämtade för den här versionen av resursprovidern.

2. Alla klient-databaser måste tas bort från resursprovidern. (Klient-databaserna inte bort data.) Den här uppgiften ska utföras av hyresgäster sig själva.

3. Klienter måste avregistrera från namnområdet.

4. Administratören måste ta bort värdservrar från MySQL-kort.

5. Administratören måste ta bort de scheman som refererar till MySQL-kort.

6. Administratören måste ta bort kvoter som är associerade med MySQL-kort.

7. Kör skriptet för distribution med följande element:
    - -Parametern avinstallera
    - Azure Resource Manager-slutpunkter
    - DirectoryTenantID
    - Autentiseringsuppgifterna för tjänstkontot för administratör
