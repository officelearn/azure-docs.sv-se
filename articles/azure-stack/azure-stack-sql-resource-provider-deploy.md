---
title: "Med hjälp av SQL-databaser på Azure-stacken | Microsoft Docs"
description: "Lär dig hur du kan distribuera SQL-databaser som en tjänst på Azure-stacken och snabba steg för att distribuera SQL Server resource provider nätverkskort"
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
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 6e65af68dcd2306aabda65efdf8fe056c0d9b4a4
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Använda SQL-databaser på Microsoft Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använda SQL Server resource provider kort för att exponera SQL-databaser som en tjänst av [Azure Stack](azure-stack-poc.md). När du installerar resursprovidern och ansluta till en eller flera instanser av SQL Server kan du och användarna skapa:
- databaser för moln-ursprungliga appar
- webbplatser som är baserade på SQL
- arbetsbelastningar som är baserade på SQL du inte att etablera en virtuell dator (VM) som är värd för SQL Server varje gång.

Resursprovidern stöder inte alla databasen hanteringsfunktioner i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Till exempel elastiska databaspooler och möjligheten att reglera databasprestanda uppåt och nedåt automatiskt inte tillgänglig. Dock resource provider har stöd liknande skapa, läsa, uppdatera och ta bort CRUD-åtgärder. API: et är inte kompatibel med SQL-databas.

## <a name="sql-server-resource-provider-adapter-architecture"></a>SQL Server Resource Provider Adapter-arkitektur
Resursprovidern består av tre komponenter:

- **SQL resource provider kortet VM**, vilket är en Windows-dator som kör provider-tjänster.
- **Resursprovidern själva**, som bearbetar begäranden om etablering och visar databasen resurser.
- **Servrar som värdar för SQL Server**, som ger kapacitet för databaser, kallas värd för servrar.

Du måste skapa en (eller flera) SQL-servrar och/eller ge åtkomst till externa SQL-instanser.

## <a name="deploy-the-resource-provider"></a>Distribuera resursprovidern

1. Om du inte redan har gjort det, registrera dina development kit och ladda ned Windows Server 2016 Datacenter Core avbildningen nedladdningsbara via Marketplace-hantering. Du måste använda en avbildning för Windows Server 2016 Core. Du kan också använda ett skript för att skapa en [Windows Server 2016 bild](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -måste du markera alternativet core. .NET 3.5-körningsmiljön inte längre behövs.

2. Logga in på en värd som har åtkomst till Privilegierade Endpoint-VM.

    a. Logga in på den fysiska värden på Azure Stack Development Kit (ASDK)-installationer.

    b. På datorer med flera noder, måste värden vara ett system som kan komma åt den privilegierade slutpunkten.

3. [Hämta SQL-providern binärfiler resursfilen](https://aka.ms/azurestacksqlrp) och köra Self-Extractor extrahera innehållet till en tillfällig katalog.

4. Azure-stacken rotcertifikatet hämtas från Privilegierade slutpunkten. För ASDK skapas ett självsignerat certifikat som en del av den här processen. Du måste ange ett lämpligt certifikat för flera noder.

    Om du måste ange ditt eget certifikat, måste följande certifikat:

    Ett jokerteckencertifikat för \*.dbadapter.\< region\>.\< externa fqdn\>. Det här certifikatet måste vara betrott, utfärdats som av en certifikatutfärdare. Det vill säga måste förtroendekedja för finnas utan mellanliggande certifikat. En enda platscertifikat kan användas med explicit VM namn [sqladapter] används under installationen.


5. Öppna en **nya** utökade (administratör) PowerShell-konsolen och ändra till katalogen där du extraherade filerna. Använd ett nytt fönster för att undvika problem som kan uppstå i felaktigt PowerShell-moduler som redan har lästs in i systemet.

6. [Installera Azure PowerShell version 1.2.11](azure-stack-powershell-install.md).

7. Kör skriptet DeploySqlProvider.ps1 med de parametrar som anges nedan.

Skriptet utför de här stegen:

- Överför certifikat och andra artefakter till ett lagringskonto på Azure-stacken.
- Publicera gallery-paket så att du kan distribuera SQL-databaser via galleriet.
- Publicera ett gallery-paket för distribution av servrar som är värd för
- Distribuera en virtuell dator med hjälp av Windows Server 2016-avbildning som skapades i steg 1 och installera resursprovidern.
- Registrera en lokal DNS-post som mappar till dina VM-resursprovidern.
- Registrera din resursprovidern med lokala Azure Resource Manager (användar- och admin).

> [!NOTE]
> Om installationen tar mer än 90 minuter, kanske den inte och du ser ett felmeddelande på skärmen och loggfilen, men distributionen försöks från steg som misslyckades. Datorer som inte uppfyller de rekommenderade specifikationerna för minne och vCPU kan inte distribuera SQL RP.
>

Här är ett exempel som du kan köra från PowerShell fråga (men ändra kontoinformation och lösenord om det behövs):

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = "AzureStack"
# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
.$tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -CloudAdminCredential $cloudAdminCreds -PrivilegedEndpoint '10.10.10.10' -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parametrar
Du kan ange dessa parametrar på kommandoraden. Om du inte vill eller någon parameter valideringen misslyckas, uppmanas du att ange de nödvändiga.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _krävs_ |
| **AzCredential** | Ange autentiseringsuppgifter för Azure-administratörskonto Stack-tjänsten. Använda samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken). | _krävs_ |
| **VMLocalCredential** | Definiera autentiseringsuppgifter för det lokala administratörskontot för SQL-resursprovidern VM. | _krävs_ |
| **PrivilegedEndpoint** | Ange IP-adress eller DNS-namnet på slutpunkten Privleged. |  _krävs_ |
| **DependencyFilesLocalPath** | Certifikatets PFX-fil måste placeras i den här katalogen samt. | _valfria_ (_obligatoriska_ för flera noder) |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikat | _krävs_ |
| **MaxRetryCount** | Ange hur många gånger som du vill försöka utföra varje åtgärd om det finns ett fel.| 2 |
| **RetryDuration** | Definiera tidsgräns mellan försök i sekunder. | 120 |
| **Avinstallera** | Ta bort resursprovidern och alla associerade resurser (se nedan) | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel | Nej |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen

> [!NOTE]
>  När installationen är klar, behöver du uppdatera portalen om du vill se admin-bladet.


1. Logga in på administrationsportal som tjänstadministratör.

2. Kontrollera att distributionen har slutförts. Bläddra efter **resursgrupper** &gt;, klicka på den **system.\< plats\>.sqladapter** resurs gruppen och kontrollera att alla fyra distributioner lyckades.

      ![Kontrollera distributionen av SQL-RP](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)





## <a name="removing-the-sql-adapter-resource-provider"></a>Ta bort Resource Provider för SQL-kort

Det är viktigt att du först ta bort eventuella beroenden för att ta bort resursprovidern.

1. Kontrollera att du har de ursprungliga distributionspaket som du hämtade för den här versionen av Resursprovidern.

2. Alla användardatabaser som måste tas bort från resursprovidern (detta inte ta bort data). Det här ska utföras av användarna själva.

3. Administratören måste ta bort värdservrar från SQL-kort

4. Administratören måste ta bort de scheman som refererar till SQL-kort.

5. Administratören måste ta bort alla SKU: er och kvoter som är kopplade till SQL-kort.

6. Kör skriptet för distribution med-avinstallera parametern, Azure Resource Manager slutpunkter, DirectoryTenantID och autentiseringsuppgifter för administratörskontot för tjänsten.


## <a name="next-steps"></a>Nästa steg


Försök med andra [PaaS services](azure-stack-tools-paas-services.md) som den [MySQL Server resursprovidern](azure-stack-mysql-resource-provider-deploy.md) och [Apptjänster resursprovidern](azure-stack-app-service-overview.md).
