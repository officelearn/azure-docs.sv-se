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
ms.openlocfilehash: 70b07cae9a1dc8b45e27f95e19fbc84f06a0b6d3
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Använda SQL-databaser på Microsoft Azure-stacken
Använda Azure Stack SQL Server-resursprovidern för att visa SQL-databaser som en tjänst i Azure-stacken. SQL resource provider-tjänsten körs på SQL-resursprovidern VM, som är en virtuell dator med Windows Server core.

## <a name="prerequisites"></a>Förutsättningar
Det finns flera förutsättningar som måste vara på plats innan du kan distribuera Azure Stack SQL-resursprovidern. Utför följande steg på en dator som kan komma åt den privilegierade slutpunkten VM:

- Om du inte redan har gjort det, [registrera Azure Stack](.\azure-stack-registration.md) med Azure så att du kan ladda ned Azure marketplace-objekt.
- Lägg till nödvändiga Windows Server core VM på Azure-stacken Marketplace genom att hämta den **Windows Server 2016 Server core** bild. Du kan placera en enda om du behöver installera en uppdatering. MSU-paketet i den lokala beroende sökvägen. Om mer än en. MSU-fil hittas, SQL resource provider-installationen misslyckas.
- Hämta SQL-resursprovidern binära och kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog. Resursprovidern har en minsta motsvarande Azure-Stack skapa. Glöm inte att hämta rätt binärfil för versionen av Azure-stacken som du använder:
    - Azure-stacken version 1802 (1.0.180302.1): [SQL RP version 1.1.18.0](https://aka.ms/azurestacksqlrp1802).
    - Azure-stacken version 1712 (1.0.180102.3, 1.0.180103.2 eller 1.0.180106.1 (integrerad system)): [SQL RP version 1.1.14.0](https://aka.ms/azurestacksqlrp1712).
- För integrerade system-installationer, du måste ange SQL PaaS PKI-certifikat enligt beskrivningen i avsnittet valfria PaaS certifikat i [Azure Stack PKI distributionskrav](.\azure-stack-pki-certs.md#optional-paas-certificates), genom att placera den .pfx-fil på plats anges av den **DependencyFilesLocalPath** parameter.
- Kontrollera att du har den [senaste versionen av Azure-stacken PowerShell](.\azure-stack-powershell-install.md) (v1.2.11) installerat. 

## <a name="deploy-the-sql-resource-provider"></a>Distribuera SQL-resursprovidern
När du har förberett att installera SQL-resursprovidern som uppfyller alla krav, kan du nu köra den **DeploySqlProvider.ps1** skript för att distribuera SQL-resursprovidern. Skriptet DeploySqlProvider.ps1 hämtas som en del av binär SQL resursprovidern som du hämtade för din Azure Stack-version. 

> [!IMPORTANT]
> System där skriptet körs måste vara en Windows 10 eller Windows Server 2016 system med den senaste versionen av .NET-körningsmiljön installerad.


Distribuera SQL-resursprovidern, öppna en ny upphöjd PowerShell-konsol (administratör) och ändra till den katalog där du extraherade de binära filerna för SQL resource provider.

> [!NOTE]
> Använd ett nytt PowerShell-konsolfönster för att undvika problem som kan uppstå från felaktig PowerShell-moduler som har redan lästs in i systemet.

Kör skriptet DeploySqlProvider.ps1 som utför följande steg:
- Överför certifikat och andra artefakter till ett lagringskonto på Azure-stacken.
- Publicerar gallery-paket så att du kan distribuera SQL-databaser via galleriet.
- Publicerar ett gallery-paket för distribution av värdservrar.
- Distribuerar en virtuell dator med hjälp av Windows Server 2016-avbildningen som skapades i steg 1 och installerar sedan resursprovidern.
- Registrerar en lokal DNS-post som mappar till dina VM-resursprovidern.
- Registrerar din resursprovidern med lokala Azure Resource Manager (användar- och admin).
- Du installerar en enskild uppdatering i Windows under RP-installationen.

Distribution av SQL resource provider börjar och skapar system.local.sqladapter resursgruppen. Det kan ta upp till 75 minuter att slutföra fyra nödvändiga distributioner till den här resursgruppen.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parametrar
Du kan ange dessa parametrar på kommandoraden. Om du inte, eller om någon parameter valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

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



## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Distribuera SQL-resursprovidern med hjälp av ett anpassat skript
Du kan anpassa följande exempel på skript genom att ändra standard kontoinformation och lösenord som behövs för att undvika att manuellt ange nödvändig information när DeploySqlProvider.ps1 skriptet körs:

```powershell
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

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen
Stegen i det här avsnittet kan användas för att se till att SQL-resursprovidern har distribuerats.

> [!NOTE]
>  Du måste uppdatera portalen om du vill se admin bladet och gallery objekt när installationen är klar.

1. Logga in på administrationsportal som tjänstadministratör.

2. Kontrollera att distributionen har slutförts. Gå till **resursgrupper**. Välj sedan den **system.\< plats\>.sqladapter** resursgruppen. Kontrollera att alla fyra distributioner lyckades.

      ![Kontrollera distributionen av SQL-resursprovidern](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>Nästa steg

[Lägg till värdservrar](azure-stack-sql-resource-provider-hosting-servers.md)
