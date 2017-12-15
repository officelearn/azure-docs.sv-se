---
title: "Använda MySQL-databaser som PaaS på Azure-stacken | Microsoft Docs"
description: "Lär dig hur du kan distribuera MySQL Resource Provider och ange MySQL-databaser som en tjänst på Azure-stacken"
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
ms.date: 12/14/2017
ms.author: JeffGo
ms.openlocfilehash: 37fc6a737bd1cfb09caf69ea2c6d81ea0b7d8693
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Använda MySQL-databaser på Microsoft Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan distribuera en MySQL-resursprovidern på Azure-stacken. När du har distribuerat resursprovidern du skapa MySQL-servrar och databaser via Azure Resource Manager-mallar för distribution och ger MySQL-databaser som en tjänst. MySQL-databaser som är vanliga på webbplatser, stöd för flera olika plattformar webbplats. Exempelvis när du har distribuerat resursprovidern, kan du skapa WordPress webbplatser från Azure Web Apps-plattform som en tjänst (PaaS)-tillägg för Azure-stacken.

Om du vill distribuera MySQL-providern på ett system som inte har tillgång till internet, kan du kopiera filen [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) till en lokal resurs. Ange att resursnamnet när du tillfrågas. Du måste också installera Azure och Azure-stacken PowerShell-moduler.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL Server Resource Provider Adapter-arkitektur

Resursprovidern består av tre komponenter:

- **MySQL resource provider kortet VM**, vilket är en Windows-dator som kör provider-tjänster.
- **Resursprovidern själva**, som bearbetar begäranden om etablering och visar databasen resurser.
- **Servrar som är värd för MySQL servern**, som ger kapacitet för databaser, kallas värd för servrar.

Den här versionen skapar inte längre en MySQL-instans. Du måste skapa dem och/eller ge åtkomst till externa SQL-instanser. Besök den [Azure Stack Snabbstartsgalleriet](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) för en exempelmall kan:
- Skapa en MySQL-server du
- Hämta och distribuera en MySQL-Server från Marketplace.

> [!NOTE]
> Värd för servrar som installerats på ett flernodigt Azure stacken måste skapas från en klientprenumeration. De kan inte skapas från prenumerationen Standard Provider. Med andra ord måste de skapas från klientportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar avgiftsbelagda virtuella datorer och måste ha rätt licenser för. Tjänstadministratören kan vara ägare till den prenumerationen.

### <a name="required-privileges"></a>Behörigheter som krävs
System-kontot måste ha följande behörigheter:

1.  Databas: Skapa, ta bort
2.  Inloggning: Skapa, ställa in, drop, bevilja, återkalla

## <a name="deploy-the-resource-provider"></a>Distribuera resursprovidern

1. Om du inte redan har gjort det, registrera dina development kit och ladda ned Windows Server 2016 Datacenter Core avbildningen nedladdningsbara via Marketplace-hantering. Du måste använda en avbildning för Windows Server 2016 Core. Du kan också använda ett skript för att skapa en [Windows Server 2016 bild](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -måste du markera alternativet core. .NET 3.5-körningsmiljön inte längre behövs.


2. Logga in på en värd som har åtkomst till Privilegierade Endpoint-VM.

    a. Logga in på den fysiska värden på Azure Stack Development Kit (ASDK)-installationer.

    b. På datorer med flera noder, måste värden vara ett system som kan komma åt den privilegierade slutpunkten.
    
    >[!NOTE]
    > System där skriptet körs *måste* vara en Windows 10 eller Windows Server 2016 system med den senaste versionen av .NET-körningsmiljön installerad. Annars misslyckas installationen. ASDK värden uppfyller kriterierna.
    

3. Hämta binära MySQL resursprovidern och köra Self-Extractor extrahera innehållet till en tillfällig katalog.

    >[!NOTE] 
    > Resource provider build motsvarar Azure Stack-versioner. Du måste ladda ned rätt binärfil för versionen av Azure-stacken som körs.

    | Azure-stacken Build | MySQL RP installer |
    | --- | --- |
    | 1.0.171122.1 | [MySQL RP version 1.1.12.0](https://aka.ms/azurestackmysqlrp) |
    | 1.0.171028.1 | [MySQL RP version 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |
    | 1.0.170928.3 | [MySQL RP version 1.1.3.0](https://aka.ms/azurestackmysqlrp1709) |

4.  Azure-stacken rotcertifikatet hämtas från Privilegierade slutpunkten. För ASDK skapas ett självsignerat certifikat som en del av den här processen. Du måste ange ett lämpligt certifikat för flera noder.

    Om du måste ange ditt eget certifikat, måste en PFX-fil placeras i den **DependencyFilesLocalPath** (se nedan) enligt följande:

    - Antingen ett jokerteckencertifikat för \*.dbadapter.\< region\>.\< externa fqdn\> eller en enda platscertifikat med ett eget namn för mysqladapter.dbadapter.\< region\>.\< externa fqdn\>
    - Det här certifikatet måste vara betrott, utfärdats som av en certifikatutfärdare. Det vill säga måste förtroendekedja för finnas utan mellanliggande certifikat.
    - En enda certifikatfil finns i DependencyFilesLocalPath.
    - Filnamnet får inte innehålla specialtecken.



5. Öppna en **nya** utökade (administratör) PowerShell-konsolen och ändra till katalogen där du extraherade filerna. Använd ett nytt fönster för att undvika problem som kan uppstå i felaktigt PowerShell-moduler som redan har lästs in i systemet.

6. [Installera Azure PowerShell version 1.2.11](azure-stack-powershell-install.md).

7. Kör skriptet DeploySqlProvider.ps1.

Skriptet utför de här stegen:

* Hämta MySQL connector binärfilen (Detta kan ges offline).
* Överför certifikat och andra artefakter till ett lagringskonto på Azure-stacken.
* Publicera gallery-paket så att du kan distribuera SQL-databaser via galleriet.
* Publicera ett gallery-paket för distribution av servrar som är värd för
* Distribuera en virtuell dator med hjälp av Windows Server 2016-avbildning som skapades i steg 1 och installera resursprovidern.
* Registrera en lokal DNS-post som mappar till dina VM-resursprovidern.
* Registrera din resursprovidern med lokala Azure Resource Manager (klient och Admin).


Du kan:
- Ange minst de obligatoriska parametrarna på kommandoraden
- eller, om du kör utan parametrar kan du ange dem när du uppmanas.

Här är ett exempel som du kan köra från PowerShell fråga (men ändra kontoinformation och lösenord om det behövs):


```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parametrar
Du kan ange dessa parametrar på kommandoraden. Om du inte vill eller någon parameter valideringen misslyckas, uppmanas du att ange de nödvändiga.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt Privleged slutpunkten. | _krävs_ |
| **AzCredential** | Ange autentiseringsuppgifter för Azure-administratörskonto Stack-tjänsten. Använda samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken). | _krävs_ |
| **VMLocalCredential** | Definiera autentiseringsuppgifter för det lokala administratörskontot för resursprovidern MySQL VM. | _krävs_ |
| **PrivilegedEndpoint** | Ange IP-adress eller DNS-namnet på den privilegierade slutpunkten. |  _krävs_ |
| **DependencyFilesLocalPath** | Sökvägen till en lokal resurs som innehåller [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi). Om du har angett en placeras certifikatfilen i den här katalogen samt. | _valfria_ (_obligatoriska_ för flera noder) |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikat | _krävs_ |
| **MaxRetryCount** | Ange hur många gånger som du vill försöka utföra varje åtgärd om det finns ett fel.| 2 |
| **RetryDuration** | Definiera tidsgräns mellan försök i sekunder. | 120 |
| **Avinstallera** | Ta bort resursprovidern och alla associerade resurser (se nedan) | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel | Nej |
| **AcceptLicense** | Hoppar över frågan om du vill acceptera licensvillkoren GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


Beroende på systemets prestanda och hämta hastigheter, kan installationen ta så lite som 20 minuter eller så länge som flera timmar. Uppdatera administrationsportal om bladet MySQLAdapter inte är tillgänglig.

> [!NOTE]
> Om installationen tar mer än 90 minuter, kanske den inte och visas ett felmeddelande på skärmen och i loggfilen. Distributionen försöks från steg som misslyckades. Datorer som inte uppfyller de rekommenderade specifikationerna för minne och core kan inte distribuera MySQL RP.



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen

> [!NOTE]
>  När installationen är klar, behöver du uppdatera portalen om du vill se admin-bladet.


1. Logga in på administrationsportal som tjänstadministratör.

2. Kontrollera att distributionen har slutförts. Bläddra efter **resursgrupper** &gt;, klicka på den **system.\< plats\>.mysqladapter** resurs gruppen och kontrollera att alla fyra distributioner lyckades.

      ![Kontrollera distributionen av MySQL RP](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Ange kapacitet genom att ansluta till en värd MySQL-server

1. Logga in på Azure Stack-portalen som en tjänstadministratör

2. Bläddra till **administrativa resurser** &gt; **MySQL som värd för servrar** &gt; **+ Lägg till**.

    Den **MySQL värd servrar** bladet är där du kan ansluta MySQL Server Resource Provider för faktiska instanser av MySQL-Server som fungerar som den resursprovidern backend.

    ![Värdservrar](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Fyll i formuläret med anslutningsinformationen för MySQL-Server-instansen. Ange det fullständigt kvalificerade domännamnet (FQDN) eller en giltig IPv4-adress och inte det korta namnet VM. Den här installationen inte längre innehåller en standardinstans MySQL. Storleken som hjälper resursprovidern hantera databaskapacitet. Det bör vara nära den fysiska kapaciteten för databasservern.

    > [!NOTE]
    > Så länge MySQL-instans kan användas av klienten och Azure Resource Manager-administratören, kan den placeras under kontroll av resursprovidern. MySQL-instans __måste__ tilldelas enbart RP.

4. När du lägger till servrar, måste du tilldela dem till en ny eller befintlig SKU att differentiering av Tjänsterbjudanden.
  Du kan till exempel har en enterprise-instans tillhandahåller:
    - databaskapacitet
    - Automatisk säkerhetskopiering
    - reservera högpresterande servrar för olika avdelningar
 

SKU-namnet bör avspegla egenskaper så att klienter kan placera sina databaser på lämpligt sätt. Alla värdservrar i en SKU ska ha samma funktioner.

![Skapa en MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKU: er kan ta upp till en timme att vara synliga i portalen. Du kan inte skapa en databas förrän SKU: N har skapats.


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>Testa distributionen genom att skapa din första MySQL-databas


1. Logga in på Azure Stack-portalen som tjänstadministratör

2. Klicka på den **+ ny** knappen &gt; **Data + lagring** &gt; **MySQL-databas**.

3. Fyll i formuläret med databasinformationen.

    ![Skapa en test MySQL-databas](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Välj en SKU.

    ![Välj en SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Skapa en inställning för inloggning. Inställningen för inloggning kan återanvändas eller skapa en ny. Den här inställningen innehåller användarnamn och lösenord för databasen.

    ![Skapa en ny databasinloggning](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    Anslutningssträngen innehåller det verkliga Databasservernamnet. Kopiera den från portalen.

    ![Hämta anslutningssträngen för MySQL-databas](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> Längden på användarnamn får inte överstiga 32 tecken med MySQL 5.7 eller 16 tecken i tidigare versioner.


## <a name="add-capacity"></a>Lägga till kapacitet

Lägga till kapacitet genom att lägga till ytterligare servrar för MySQL på Azure Stack-portalen. Ytterligare servrar kan läggas till en ny eller befintlig SKU. Kontrollera server-egenskaper är samma.


## <a name="make-mysql-databases-available-to-tenants"></a>Göra MySQL-databaser som är tillgängliga för klienter
Skapa planer och erbjudanden om du vill göra MySQL-databaser som är tillgängliga för klienter. Lägg till Microsoft.MySqlAdapter-tjänst, Lägg till en kvot osv.

![Skapa planer och erbjudanden om du vill inkludera databaser](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Uppdatera lösenordet för administratörer
Du kan ändra lösenordet genom att först ändra MySQL-serverinstansen. Bläddra till **ADMINISTRATIONSRESURSER** &gt; **MySQL värd servrar** &gt; och klicka på värdservern. Klicka på panelen inställningar på lösenord.

![Uppdatera administratörslösenordet](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="remove-the-mysql-resource-provider-adapter"></a>Ta bort MySQL Resource Provider-kort

Om du vill ta bort resursprovidern, är det viktigt att du först ta bort eventuella beroenden.

1. Kontrollera att du har de ursprungliga distributionspaket som du hämtade för den här versionen av Resursprovidern.

2. Alla klient-databaser måste tas bort från resursprovidern (detta inte ta bort data). Det här ska utföras av hyresgäster sig själva.

3. Klienter måste avregistrera från namnområdet.

4. Administratören måste ta bort värdservrar från MySQL-kort

5. Administratören måste ta bort de scheman som refererar till MySQL-kort.

6. Administratören måste ta bort kvoter som är kopplade till MySQL-kort.

7. Kör skriptet för distribution med-avinstallera parametern, Azure Resource Manager slutpunkter, DirectoryTenantID och autentiseringsuppgifter för administratörskontot för tjänsten.




## <a name="next-steps"></a>Nästa steg

Försök med andra [PaaS services](azure-stack-tools-paas-services.md) som den [SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md) och [Apptjänster resursprovidern](azure-stack-app-service-overview.md).
