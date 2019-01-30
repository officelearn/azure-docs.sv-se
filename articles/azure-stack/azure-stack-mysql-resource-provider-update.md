---
title: Uppdaterar Azure Stack MySQL-resursprovider | Microsoft Docs
description: Lär dig hur du kan uppdatera Azure Stack MySQL-resursprovider.
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
ms.date: 01/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: f3129c27d123aa448f8293f7e1827dc3ebc5c82e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239085"
---
# <a name="update-the-mysql-resource-provider"></a>Uppdatera MySQL-resursprovider 

*Gäller för: Integrerade Azure Stack-system.*

En ny MySQL-resursprovideradaptern kan släppas när Azure Stack bygger uppdateras. Befintliga kortet fortsätter att fungera, rekommenderar vi uppdaterar till den senaste versionen så snart som möjligt. 

Från och med MySQL resource provider version 1.1.33.0, uppdateringar är kumulativa och behöver inte installeras i den ordning som de släpptes; Så länge som startar från version 1.1.24.0 eller senare. Till exempel om du kör version 1.1.24.0 av MySQL-resursprovider, kan du uppgradera till version 1.1.33.0 eller senare utan att behöva installera version 1.1.30.0. Om du vill granska tillgängliga resursgrupper providerversioner och versionen av Azure Stack som de kan användas för att referera till listan över versioner i [distribuera resource provider krav](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Uppdatering av resursprovidern som du använder den **UpdateMySQLProvider.ps1** skript. Processen påminner om en process som används för att installera en resursleverantör, enligt beskrivningen i den [distribuerar resursprovidern](#deploy-the-resource-provider) i den här artikeln. Skriptet ingår hämtning av resursprovidern. 

 > [!IMPORTANT]
 > Innan du uppgraderar resursprovidern Läs den viktiga informationen för att lära dig om nya funktioner och korrigeringar kända problem som kan påverka din distribution.

## <a name="update-script-processes"></a>Uppdatera skript processer

Den **UpdateMySQLProvider.ps1** skriptet skapar en ny virtuell dator med den senaste resource provider koden och migrerar inställningarna från den gamla virtuella datorn till den nya virtuella datorn. De inställningar som migreras inkluderar databasen och som är värd för serverinformation nödvändiga DNS-posten. 

>[!NOTE]
>Vi rekommenderar att du hämtar den senaste Windows Server 2016 Core-avbildningen från Marketplace-hantering. Om du vill installera en uppdatering kan du placera en **enda** MSU-paketet i lokala beroendesökvägen. Skriptet misslyckas om det finns fler än en MSU-fil i den här platsen.

Skriptet måste använda samma argument som beskrivs för DeployMySqlProvider.ps1-skriptet. Ange certifikat här också.  


## <a name="update-script-parameters"></a>Uppdatera Skriptparametrar 
Du kan ange följande parametrar från kommandoraden när du kör den **UpdateMySQLProvider.ps1** PowerShell-skript. Om du inte, eller om någon parameter-valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna. 

| Parameternamn | Beskrivning | Kommentar eller standardvärde | 
| --- | --- | --- | 
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ | 
| **AzCredential** | Autentiseringsuppgifter för Azure Stack-tjänstadministratörskonto. Använda samma autentiseringsuppgifter som du använde för att distribuera Azure Stack. | _Krävs_ | 
| **VMLocalCredential** |Autentiseringsuppgifterna för det lokala administratörskontot för SQL-resursprovider VM. | _Krävs_ | 
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ | 
| **AzureEnvironment** | Azure-miljön för admin kontot som du använde för att distribuera Azure Stack. Krävs endast för Azure AD-distributioner. Miljö som stöds är **AzureCloud**, **azureusgovernment eller**, eller om du använder en Kina Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Din .pfx-certifikatfil måste placeras i den här katalogen samt. | _Valfritt_ (_obligatoriska_ för flera noder) | 
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikat. | _Krävs_ | 
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 | 
| **RetryDuration** | Timeout-intervall mellan försök i sekunder. | 120 | 
| **Avinstallera** | Ta bort resursprovidern och alla associerade resurser (se nedan). | Nej | 
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej | 
| **AcceptLicense** | Hoppar över uppmaningen om att acceptera licensen GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Uppdatera exempel på skript
Följande är ett exempel på hur du använder den *UpdateMySQLProvider.ps1* skript som du kan köra från en upphöjd PowerShell-konsol. Tänk på att ändra Variabelinformation och lösenord efter behov:  

> [!NOTE] 
> Uppdateringen gäller endast för integrerade system. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

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
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

## <a name="next-steps"></a>Nästa steg
[Underhålla MySQL-resursprovider](azure-stack-mysql-resource-provider-maintain.md)
