---
title: Med hjälp av MySQL-databaser på Azure-stacken | Microsoft Docs
description: Lär dig hur du kan distribuera MySQL-databaser som en tjänst på Azure-stacken och snabba steg för att distribuera MySQL Server resource provider-adapter.
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938125"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Distribuera resursprovidern MySQL på Azure-stacken

Använda resursprovidern MySQL-Server för att visa MySQL-databaser som en tjänst i Azure-stacken. MySQL-resursprovidern körs som en tjänst på en Server Core för Windows Server 2016 virtuell dator (VM).

## <a name="prerequisites"></a>Förutsättningar

Det finns flera förutsättningar som måste vara uppfyllda innan du kan distribuera Azure Stack MySQL-resursprovidern. Slutför stegen i den här artikeln på en dator som kan komma åt den privilegierade slutpunkten VM för att uppfylla kraven.

* Om du inte redan gjort det, [registrera Azure Stack](.\azure-stack-registration.md) med Azure så att du kan ladda ned Azure marketplace-objekt.
* Lägg till nödvändiga Windows Server core VM på Azure-stacken Marketplace genom att hämta den **Windows Server 2016 Datacenter - Server Core** bild. Du kan också använda ett skript för att skapa en [Windows Server 2016 avbildningen](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Kontrollera att du väljer alternativet core när du kör skriptet.

  >[!NOTE]
  >Du kan placera en enda om du behöver installera en uppdatering. MSU-paketet i den lokala beroende sökvägen. Om mer än en. MSU-fil hittas, MySQL resource providerinstallationen att misslyckas.

* Hämta binära MySQL resursprovidern och kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog.

  >[!NOTE]
  >Om du vill distribuera MySQL-providern på ett system som inte har tillgång till Internet, kopiera den [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) filen till en lokal resurs. Ange namnet på när du uppmanas för den. Du måste installera Azure och Azure-stacken PowerShell-moduler.

* Resursprovidern har en minsta motsvarande Azure-Stack skapa. Kontrollera att du har hämtat rätt binärfil för versionen av Azure-stacken som du kör.

    | Azure Stack-versionen | MySQL RP-version|
    | --- | --- |
    | Version 1804 (1.0.180513.1)|[MySQL RP version 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    | Version 1802 (1.0.180302.1) | [MySQL RP version 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | Version 1712 (1.0.180102.3 eller 1.0.180106.1 (integrerad system)) | [MySQL RP version 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>Certifikat

För ASDK skapas ett självsignerat certifikat som en del av den här processen. Du måste ange ett lämpligt certifikat för ett Azure-stacken integrerat system. Om du måste ange ditt eget certifikat, placera en .pfx-fil i den **DependencyFilesLocalPath** som uppfyller följande kriterier:

* Antingen ett jokerteckencertifikat för \*.dbadapter.\< region\>.\< externa fqdn\> eller en enda platscertifikat med ett eget namn för mysqladapter.dbadapter.\< region\>.\< externa fqdn\>.
* Certifikatet måste vara betrodd. Förtroendekedja för måste finnas utan mellanliggande certifikat.
* En enda certifikatfil finns i DependencyFilesLocalPath.
* Filnamnet kan inte ha några specialtecken eller blanksteg.

## <a name="deploy-the-resource-provider"></a>Distribuera resursprovidern

När du har alla nödvändiga komponenter installeras, kör du den **DeployMySqlProvider.ps1** skript för att distribuera MYSQL-resursprovidern. Skriptet DeployMySqlProvider.ps1 hämtas som en del av MySQL resource provider binärfilen som du hämtade för din version av Azure-stacken.

> [!IMPORTANT]
> Systemet som du kör skriptet på måste vara en Windows 10 eller Windows Server 2016 system med den senaste versionen av .NET-körningsmiljön installerad.

Distribuera resursprovidern MySQL, öppna ett nytt upphöjd PowerShell-konsolfönster och ändra till den katalog där du extraherade de binära filerna för MySQL resource provider. Vi rekommenderar att du använder ett nytt PowerShell-fönster för att undvika eventuella problem som orsakas av PowerShell-moduler som har redan lästs in.

Kör den **DeployMySqlProvider.ps1** skript som utför följande aktiviteter:

* Överför certifikat och andra artefakter till ett lagringskonto på Azure-stacken.
* Publicerar gallery-paket så att du kan distribuera MySQL-databaser med galleriet.
* Publicerar ett gallery-paket för distribution av värdservrar.
* Distribuerar en virtuell dator med Windows Server 2016 core bild du ned och installerar sedan MySQL-resursprovidern.
* Registrerar en lokal DNS-post som mappar till dina VM-resursprovidern.
* Registrerar din resursprovidern med den lokala Azure Resource Manager för operatorn och användarkonton.
* Du kan också installerar en enskild uppdatering i Windows Server under installationen av resurs-providern.

> [!NOTE]
> När MySQL resource provider distributionen startar, **system.local.mysqladapter** resursgruppen har skapats. Det kan ta upp till 75 minuter att slutföra de fyra distributioner som krävs för att den här resursgruppen.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 parametrar

Du kan ange parametrarna från kommandoraden. Om du inte, eller om någon parameter valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ |
| **AzCredential** | Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken. | _Krävs_ |
| **VMLocalCredential** | Autentiseringsuppgifterna för det lokala administratörskontot för resursprovidern MySQL VM. | _Krävs_ |
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ |
| **DependencyFilesLocalPath** | Sökvägen till en lokal resurs som innehåller [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Om du anger ett av dessa sökvägar måste certifikatfilen placeras i den här katalogen samt. | _Valfria_ för enskild nod _obligatoriska_ för flera noder. |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikatet. | _Krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** | Timeoutintervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser (se nedan). | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |
| **AcceptLicense** | Hoppar över frågan om du vill acceptera licensvillkoren GPL.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> SKU: er kan ta upp till en timme att vara synliga i portalen. Du kan inte skapa en databas tills SKU: N är installerade och körs.

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Distribuera MySQL-resursprovidern med hjälp av ett anpassat skript

Du kan anpassa följande skript för att ta bort alla manuella konfigurationer när du distribuerar resursprovidern. Ändra standard kontoinformation och lösenord som behövs för din Azure Stack-distribution.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

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
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Uppdatera webbläsaren för att kontrollera att du kan se de senaste uppdateringarna när installationsskriptet för resurs-providern har slutförts.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen

1. Logga in på administrationsportal som tjänstadministratör.
2. Välj **resursgrupper**
3. Välj den **system.\< plats\>.mysqladapter** resursgruppen.
4. På sidan Sammanfattning för resursen gruppera översikt, meddelande under **distributioner** ska vara **3 lyckades**.
5. Du kan få mer detaljerad information om resource provider distribution under **inställningar**. Välj **distributioner** att hämta information som: STATUS, TIDSSTÄMPEL och varaktighet för varje distribution.

## <a name="next-steps"></a>Nästa steg

[Lägg till värdservrar](azure-stack-mysql-resource-provider-hosting-servers.md)
