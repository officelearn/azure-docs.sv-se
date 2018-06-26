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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938340"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Distribuera SQL Server-resursprovidern på Azure-stacken

Använda Azure Stack SQL Server-resursprovidern för att visa SQL-databaser som en tjänst i Azure-stacken. SQL-resursprovidern körs som en tjänst på en Server Core för Windows Server 2016 virtuell dator (VM).

## <a name="prerequisites"></a>Förutsättningar

Det finns flera förutsättningar som måste vara på plats innan du kan distribuera Azure Stack SQL-resursprovidern. Utför följande steg på en dator som kan komma åt den privilegierade slutpunkten VM för att uppfylla dessa krav:

- Om du inte redan gjort det, [registrera Azure Stack](.\azure-stack-registration.md) med Azure så att du kan ladda ned Azure marketplace-objekt.
- Lägg till nödvändiga Windows Server core VM på Azure-stacken Marketplace genom att hämta den **Windows Server 2016 Datacenter - Server Core** bild. Du kan också använda ett skript för att skapa en [Windows Server 2016 avbildningen](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Kontrollera att du väljer alternativet core när du kör skriptet.

  >[!NOTE]
  >Om du behöver installera en uppdatering kan du placera ett enda MSU-paket i den lokala beroende sökvägen. Om mer än en MSU-fil hittas att SQL resource providerinstallationen misslyckas.

- Hämta SQL resursprovidern binära och kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog. Resursprovidern har en minsta motsvarande Azure-Stack skapa. Kontrollera att du har hämtat rätt binärfil för versionen av Azure-stacken som du kör.

    |Azure Stack-versionen|SQL RP-version|
    |-----|-----|
    |Version 1804 (1.0.180513.1)|[SQL RP version 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Version 1802 (1.0.180302.1)|[SQL RP version 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |Version 1712 (1.0.180102.3, 1.0.180103.2 eller 1.0.180106.1 (integrerad system))|[SQL RP version 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>Certifikat

För integrerade system-installationer. Du måste ange den SQL PaaS PKI-certifikat som beskrivs i de valfria PaaS certifikat i [Azure Stack PKI distributionskrav](.\azure-stack-pki-certs.md#optional-paas-certificates). Placera den .pfx-fil på den plats som den **DependencyFilesLocalPath** parameter.

## <a name="deploy-the-sql-resource-provider"></a>Distribuera SQL-resursprovidern

När du har alla nödvändiga komponenter installeras, kör du den **DeploySqlProvider.ps1** skript för att distribuera SQL-resursprovidern. Skriptet DeploySqlProvider.ps1 hämtas som en del av SQL resource provider binärfilen som du hämtade för din version av Azure-stacken.

> [!IMPORTANT]
> Systemet som du kör skriptet på måste vara en Windows 10 eller Windows Server 2016 system med den senaste versionen av .NET-körningsmiljön installerad.

Distribuera SQL-resursprovidern genom att öppna en **nya** upphöjd PowerShell konsolfönstret och ändra till den katalog där du extraherade de binära filerna för SQL resource provider. Vi rekommenderar att du använder ett nytt PowerShell-fönster för att undvika eventuella problem som orsakas av PowerShell-moduler som har redan lästs in.

Kör skriptet DeploySqlProvider.ps1 som utför följande uppgifter:

- Överför certifikat och andra artefakter till ett lagringskonto på Azure-stacken.
- Publicerar gallery-paket så att du kan distribuera SQL-databaser med galleriet.
- Publicerar ett gallery-paket för distribution av värdservrar.
- Distribuerar en virtuell dator med Windows Server 2016 core bild du ned och installerar sedan SQL-resursprovidern.
- Registrerar en lokal DNS-post som mappar till dina VM-resursprovidern.
- Registrerar din resursprovidern med den lokala Azure Resource Manager för operatorn och användarkonton.
- Du kan också installerar en enskild uppdatering i Windows Server under installationen av resurs-providern.

> [!NOTE]
> När SQL resource provider distributionen startar, **system.local.sqladapter** resursgruppen har skapats. Det kan ta upp till 75 minuter fyra nödvändiga distributioner till den här resursgruppen.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parametrar

Du kan ange följande parametrar från kommandoraden. Om du inte, eller om någon parameter valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ |
| **AzCredential** | Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken. | _Krävs_ |
| **VMLocalCredential** | Autentiseringsuppgifterna för det lokala administratörskontot för SQL-resursprovidern VM. | _Krävs_ |
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ |
| **DependencyFilesLocalPath** | Ditt certifikat PFX-fil måste placeras i den här katalogen samt. | _Valfria_ (_obligatoriska_ för integrerade system) |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikatet. | _Krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** | Timeoutintervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser (se nedan). | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |

>[!NOTE]
> SKU: er kan ta upp till en timme att vara synliga i portalen. Du kan inte skapa en databas tills SKU: N är installerade och körs.

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Distribuera SQL-resursprovidern med hjälp av ett anpassat skript

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
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Uppdatera webbläsaren för att kontrollera att du kan se de senaste uppdateringarna när installationsskriptet för resurs-providern har slutförts.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen

Du kan använda följande steg verifierar att SQL-resursprovidern har distribuerats.

1. Logga in på administrationsportal som tjänstadministratör.
2. Välj **resursgrupper**.
3. Välj den **system.\< plats\>.sqladapter** resursgruppen.
4. Meddelandet under **distributioner**, visas i nästa skärmdump, bör vara **4 lyckades**.

      ![Kontrollera distributionen av SQL-resursprovidern](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Du kan få mer detaljerad information om resource provider distribution under **inställningar**. Välj **distributioner** att hämta information som: STATUS, TIDSSTÄMPEL och varaktighet för varje distribution.

## <a name="next-steps"></a>Nästa steg

[Lägg till värdservrar](azure-stack-sql-resource-provider-hosting-servers.md)
