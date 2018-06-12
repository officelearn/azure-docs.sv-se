---
title: Uppdatera Azure Stack SQL resursprovidern | Microsoft Docs
description: Lär dig hur du kan uppdatera Azure Stack SQL-resursprovidern.
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 3a7656e54181c8e8e7b6b1bd39f80ce8ed01c807
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294868"
---
# <a name="update-the-sql-resource-provider"></a>Uppdatera SQL-resursprovidern
En ny SQL-resursprovidern kan frigöras när Azure Stack-versioner har uppdaterats. Medan det befintliga kortet fortsätter att fungera, rekommenderar vi uppdatera till den senaste versionen så snart som möjligt. Uppdateringar måste installeras i ordning: du kan inte hoppa över versioner (se listan över versioner i [distribuera resource provider krav](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Uppdatering av resursprovidern som du använder den *UpdateSQLProvider.ps1* skript. Processen påminner om den process som används för att installera en resursleverantör, enligt beskrivningen i den [distribuera resursprovidern](.\azure-stack-sql-resource-provider-deploy.md) artikel. Skriptet ingår i hämtningen av resursprovidern.

Den *UpdateSQLProvider.ps1* skriptet skapar en ny virtuell dator med senaste resource provider kod och migrerar inställningarna från den gamla virtuella datorn till den nya virtuella datorn. Inställningar som migreras inkluderar databasen och värd-serverinformation och nödvändiga DNS-posten.

Skriptet kräver användning av samma argument som beskrivs för skriptet DeploySqlProvider.ps1. Ange certifikat här samt. 

Vi rekommenderar att du hämtar senaste Windows Server 2016 Core-avbildning från Marketplace-hantering. Du kan placera en enda om du behöver installera en uppdatering. MSU-paketet i den lokala beroende sökvägen. Om mer än en. MSU-fil hittas, skriptet misslyckas.

Följande är ett exempel på den *UpdateSQLProvider.ps1* skript som du kan köra från PowerShell-Kommandotolken. Glöm inte att ändra kontoinformation och lösenord som behövs: 

> [!NOTE]
> Uppdateringen gäller bara för integrerade system.

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

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parametrar
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


## <a name="next-steps"></a>Nästa steg

[Underhåll SQL-resursprovidern](azure-stack-sql-resource-provider-maintain.md)
