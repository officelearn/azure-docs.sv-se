---
title: Publicera distributionskonfigurationer för Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Beskriver rekommenderade konfigurationsändringarna ska göra när du har installerat Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e057d7a649397083240e9f67080808a3057c7f50
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055001"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Publicera konfigurationsuppgifter för ASDK installation

Efter [installerar Azure Stack Development Kit (ASDK)](asdk-install.md), behöver du ändra en vissa rekommenderade konfiguration efter installationen.

## <a name="install-azure-stack-powershell"></a>Installera Azure Stack PowerShell

Azure Stack-kompatibla Azure PowerShell-moduler som krävs för att arbeta med Azure Stack.

PowerShell-kommandon för Azure Stack installeras via PowerShell-galleriet. Att registrera PSGallery databasen, öppna en upphöjd PowerShell-session och kör följande kommando:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

Du kan använda API-versionsprofiler för att ange Azure Stack-kompatibla AzureRM-moduler.  API-versionsprofiler är ett sätt att hantera skillnaderna mellan Azure och Azure Stack. En profil för API-versionen är en uppsättning AzureRM PowerShell-moduler med specifika API-versioner. Den **AzureRM.Bootstrapper** modulen som är tillgänglig via PowerShell-galleriet innehåller PowerShell-cmdlets som krävs för att arbeta med API-versionsprofiler.

Du kan installera den senaste Azure Stack PowerShell-modulen med eller utan Internet-anslutning till värddatorn ASDK:

> [!IMPORTANT]
> Innan du installerar versionen som krävs, se till att du [avinstallera alla befintliga Azure PowerShell-moduler](.\.\azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Med en Internetanslutning** från värddatorn ASDK. Kör följande PowerShell-skript för att installera dessa moduler på din development kit-installation:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  # Install Azure Stack Module Version 1.4.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.
  Install-Module -Name AzureStack -RequiredVersion 1.4.0 

  ```

  Om installationen lyckas visas AzureRM- och AzureStack-moduler i utdata.

- **Utan Internetanslutning** från värddatorn ASDK. I ett scenario med frånkopplade måste du först hämta PowerShell-moduler på en dator som är ansluten till internet med hjälp av följande PowerShell-kommandon:

  ```PowerShell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureRM `
    -Path $Path `
    -Force `
    -RequiredVersion 1.2.11

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureStack `
    -Path $Path `
    -Force `
  # Install Azure Stack Module Version 1.4.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.  
    -RequiredVersion 1.4.0
  ```

  Sedan kopiera de hämta paketen till ASDK-dator och registrera platsen som standard-databasen och installerar AzureRM- och AzureStack-moduler från den här lagringsplatsen:

    ```PowerShell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository `
      -Name $RepoName `
      -SourceLocation $SourceLocation `
      -InstallationPolicy Trusted

    Install-Module AzureRM `
      -Repository $RepoName

    Install-Module AzureStack `
      -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Ladda ned Azure Stack-verktyg

[Verktyg för AzureStack](https://github.com/Azure/AzureStack-Tools) är en GitHub-lagringsplats som är värd för PowerShell-moduler för att hantera och distribuera resurser till Azure Stack. Klona GitHub-databasen för att få dessa verktyg eller ladda ned mappen AzureStack verktyg genom att köra följande skript:

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Verifiera installationen ASDK
För att säkerställa att ASDK distributionen har lyckats, kan du använda cmdleten Test-AzureStack genom att följa dessa steg:

1. Logga in som AzureStack\AzureStackAdmin på värddatorn ASDK.
2. Öppna PowerShell som administratör (inte PowerShell ISE).
3. Kör: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Kör: `Test-AzureStack`

Testerna ta ett par minuter att slutföra. Om installationen slutfördes korrekt visas utdata ser ut ungefär så:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Om det uppstod ett fel, följer du anvisningarna för att få hjälp.

## <a name="activate-the-administrator-and-tenant-portals"></a>Aktivera administratör och klient-portaler
När du har distributioner som använder Azure AD, måste du aktivera båda Azure Stack-administratör och klient portaler. Den här aktiveringen godkänner ger Azure Stack-portalen och Azure Resource Manager rätt behörigheter (som visas på sidan medgivande) för alla användare av katalogen.

- Administratörsportalen går du till https://adminportal.local.azurestack.external/guest/signup, Läs informationen och klicka sedan på **acceptera**. Efter att du godkänt, kan du lägga till administratörer som inte är också directory-klientadministratörer.

- För klientportalen går du till https://portal.local.azurestack.external/guest/signup, Läs informationen och klicka sedan på **acceptera**. Efter att du godkänt, kan användare i katalogen logga in till klientportalen. 

> [!NOTE] 
> Om portalerna inte har aktiverat kan endast directory-administratör logga in och använda portalerna. Om en annan användare loggar in, visas ett fel som talar om att administratören inte har behörighet till andra användare. När administratören internt inte tillhör den katalog som Azure Stack är registrerad, måste Azure Stack-katalog läggas till Aktiverings-URL. Till exempel om Azure Stack är registrerad på fabrikam.onmicrosoft.com och administratören är admin@contoso.com, navigera till https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com att aktivera på portalen. 

## <a name="reset-the-password-expiration-policy"></a>Återställ förfalloprincipen för lösenord 
Följ dessa steg för att säkerställa att lösenordet för development kit värden inte går ut innan din utvärderingsversion har upphört att gälla, när du har distribuerat ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Ändra förfalloprincipen för lösenord från Powershell:
Kör kommandot från en upphöjd Powershell-konsol:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Ändra förfalloprincipen för lösenord manuellt:
1. Öppna på development kit värden **Group Policy Management** (GPMC. MMC) och gå till **Group Policy Management** – **skog: azurestack.local** – **domäner** – **azurestack.local**.
2. Högerklicka på **Standarddomänprincip** och klicka på **redigera**.
3. I den Redigeraren för Grupprinciphantering, navigera till **Datorkonfiguration** – **principer** – **Windows-inställningar** – **säkerhetsinställningar**– **Kontoprinciper** – **lösenordsprincip**.
4. I den högra rutan, dubbelklickar du på **högsta ålder för lösenord**.
5. I den **högsta ålder för lösenord egenskaper** i dialogrutan den **lösenord upphör att gälla i** värde att **180**, och klicka sedan på **OK**.

![Konsolen Grupprinciphantering](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Nästa steg
[Registrera ASDK med Azure](asdk-register.md)
