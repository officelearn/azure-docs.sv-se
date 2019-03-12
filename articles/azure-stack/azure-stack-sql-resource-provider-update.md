---
title: Uppdaterar Azure Stack SQL-resursprovider | Microsoft Docs
description: Lär dig hur du kan uppdatera Azure Stack SQL-resursprovider.
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
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: c4a00d62cc9ab049ed3c647e9d1e2ed1e5a28329
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760013"
---
# <a name="update-the-sql-resource-provider"></a>Uppdatera SQL-resursprovider

*Gäller för: Integrerade Azure Stack-system.*

En ny SQL-resursprovider kan släppas när Azure Stack uppdateras till en ny version. Även om befintliga resursprovidern fortsätter att fungera, rekommenderar vi uppdaterar till den senaste versionen så snart som möjligt. 

Från och med SQL resource provider version 1.1.33.0, uppdateringar är kumulativa och behöver inte installeras i den ordning som de släpptes; Så länge som startar från version 1.1.24.0 eller senare. Till exempel om du kör version 1.1.24.0 av SQL-resursprovider, kan du uppgradera till version 1.1.33.0 eller senare utan att behöva installera version 1.1.30.0. Om du vill granska tillgängliga resursgrupper providerversioner och versionen av Azure Stack som de kan användas för att referera till listan över versioner i [distribuera resource provider krav](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Uppdatera resursprovidern med den *UpdateSQLProvider.ps1* skript. Det här skriptet ingår i nedladdningen av den nya SQL-resursprovidern. Uppdateringen är ungefär på samma sätt som används för att [distribuerar resursprovidern](./azure-stack-sql-resource-provider-deploy.md). Uppdateringsskriptet använder samma argument som skriptet som DeploySqlProvider.ps1 och du måste ange information om certifikat.

 > [!IMPORTANT]
 > Innan du uppgraderar resursprovidern Läs den viktiga informationen för att lära dig om nya funktioner och korrigeringar kända problem som kan påverka din distribution.

## <a name="update-script-processes"></a>Uppdatera skript processer

Den *UpdateSQLProvider.ps1* skriptet skapar en ny virtuell dator (VM) med den senaste resource provider koden.

> [!NOTE]
> Vi rekommenderar att du hämtar den senaste Windows Server 2016 Core-avbildningen från Marketplace-hantering. Om du vill installera en uppdatering kan du placera en **enda** MSU-paketet i lokala beroendesökvägen. Skriptet misslyckas om det finns fler än en MSU-fil i den här platsen.

Efter den *UpdateSQLProvider.ps1* skriptet skapar en ny virtuell dator, skriptet migreras följande inställningar från den gamla VM-providern:

* databasinformation
* som är värd för serverinformation
* DNS-post som krävs

## <a name="update-script-parameters"></a>Uppdatera Skriptparametrar

Du kan ange följande parametrar från kommandoraden när du kör den **UpdateSQLProvider.ps1** PowerShell-skript. Om du inte, eller om någon parameter-valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ |
| **AzCredential** | Autentiseringsuppgifter för administratörskontot för Azure Stack-tjänsten. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure Stack. | _Krävs_ |
| **VMLocalCredential** | Autentiseringsuppgifterna för det lokala administratörskontot för SQL-resursprovider VM. | _Krävs_ |
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ |
| **AzureEnvironment** | Azure-miljön för admin kontot som du använde för att distribuera Azure Stack. Krävs endast för Azure AD-distributioner. Miljö som stöds är **AzureCloud**, **azureusgovernment eller**, eller om du använder en Kina Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | I den här katalogen måste du också placera din .pfx-certifikatfil. | _Valfritt för enskild nod, men obligatoriskt för flera noder_ |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikat. | _Krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** |Timeout-intervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser. | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |

## <a name="update-script-powershell-example"></a>Uppdatera exempel på PowerShell-skript
Följande är ett exempel på hur du använder den *UpdateSQLProvider.ps1* skript som du kan köra från en upphöjd PowerShell-konsol. Tänk på att ändra Variabelinformation och lösenord efter behov:  

> [!NOTE]
> Den här uppdateringen gäller endast för integrerade Azure Stack-systemen.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
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
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert 

 ```

## <a name="next-steps"></a>Nästa steg

[Underhålla SQL-resursprovider](azure-stack-sql-resource-provider-maintain.md)
