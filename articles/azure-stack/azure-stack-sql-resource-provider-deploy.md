---
title: "Med hjälp av SQL-databaser på Azure-stacken | Microsoft Docs"
description: "Lär dig hur du kan distribuera SQL-databaser som en tjänst på Azure-stacken och snabba steg för att distribuera SQL Server resource provider nätverkskort."
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
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

Du måste skapa en (eller flera) intances av SQL Server och/eller ge åtkomst till externa SQL Server-instanser.

## <a name="deploy-the-resource-provider"></a>Distribuera resursprovidern

1. Om du inte redan har gjort det, registrera dina development kit och ladda ned Windows Server 2016 Datacenter Core avbildningen nedladdningsbara via Marketplace-hantering. Du måste använda en avbildning för Windows Server 2016 Core. Du kan också använda ett skript för att skapa en [Windows Server 2016 avbildningen](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Måste du markera alternativet core). .NET 3.5-körningsmiljön inte längre behövs.

2. Logga in på en värd som kan komma åt den privilegierade slutpunkten VM.

    - Logga in på den fysiska värden på Azure-stacken Development Kit-installationer.

    - På datorer med flera noder, måste värden vara ett system som kan komma åt den privilegierade slutpunkten.
    
    >[!NOTE]
    > System där skriptet körs *måste* vara en Windows 10 eller Windows Server 2016 system med den senaste versionen av .NET-körningsmiljön installerad. Annars misslyckas installationen. Azure SDK för stacken värden uppfyller det här kriteriet.


3. Hämta SQL resursprovidern binär. Kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog.

    >[!NOTE] 
    > Resource provider build motsvarar Azure Stack-versioner. Se till att ladda ned rätt binärfil för versionen av Azure-stacken som körs.

    | Azure Stack-version | Installationsprogrammet för providern av SQL-resurs |
    | --- | --- |
    |1.0.180102.3, 1.0.180103.2 eller 1.0.180106.1 (med flera noder) | [SQL RP version 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP version 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP version 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Azure-stacken rotcertifikatet hämtas från Privilegierade slutpunkten. För Azure-stacken SDK skapas ett självsignerat certifikat som en del av den här processen. Du måste ange ett lämpligt certifikat för flera noder.

   För att ge ditt eget certifikat, placera en .pfx-fil i den **DependencyFilesLocalPath** på följande sätt:

    - Antingen ett jokerteckencertifikat för \*.dbadapter.\< region\>.\< externa fqdn\> eller en enda platscertifikat med ett eget namn för sqladapter.dbadapter.\< region\>.\< externa fqdn\>.

    - Det här certifikatet måste vara betrodd. Det vill säga måste förtroendekedja för finnas utan mellanliggande certifikat.

    - En enda certifikatfil finns i DependencyFilesLocalPath.

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

> [!NOTE]
> Om installationen tar mer än 90 minuter kan misslyckas det. Om det misslyckas visas ett felmeddelande på skärmen och loggfilen, men distributionen försöks från steg som misslyckades. Datorer som inte uppfyller de rekommenderade specifikationerna för minne och vCPU kan inte distribuera SQL resoure provider.
>

Här är ett exempel som du kan köra från PowerShell-Kommandotolken. (Måste du ändra kontoinformation och lösenord som krävs.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
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
När Azure Stack build uppdateras publicerat ett nytt SQL resource provider-kort. Befintliga nätverkskortet kan fortsätta att fungera. Vi rekommenderar dock att uppdatera till den senaste versionen så snart som möjligt efter den Azure-stacken har uppdaterats. 

Uppdateringsprocessen liknar installationen som beskrivs ovan. Du kan skapa en ny virtuell dator med senaste resource provider kod. Dessutom kan migrera du inställningar till den här nya instansen inklusive databas och värd-serverinformation. Du kan också migrera nödvändiga DNS-posten.

Använda skriptet UpdateSQLProvider.ps1 med samma argument som det beskrivs ovan. Du måste ange det här certifikatet samt.

> [!NOTE]
> Den här uppdateringen stöds endast på datorer med flera noder.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

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
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikatet. | _krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** |Timeoutintervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser (se nedan). | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |



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
