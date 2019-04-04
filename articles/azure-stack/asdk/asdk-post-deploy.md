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
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: 7699c9279138aedfdcfe63fb42e65ad102ac92c9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652475"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Publicera konfigurationsuppgifter för ASDK installation

Efter [installerar Azure Stack Development Kit (ASDK)](asdk-install.md), bör du se några ändringar i konfigurationen för rekommenderade efter installationen när du är inloggad som AzureStack\AzureStackAdmin på värddatorn ASDK. 

## <a name="install-azure-stack-powershell"></a>Installera Azure Stack PowerShell

Azure Stack-kompatibla Azure PowerShell-moduler som krävs för att arbeta med Azure Stack.

PowerShell-kommandon för Azure Stack installeras via PowerShell-galleriet. Att registrera PSGallery databasen, öppna en upphöjd PowerShell-session och kör följande kommando:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Du kan använda API-versionsprofiler för att ange Azure Stack-kompatibla AzureRM-moduler.  API-versionsprofiler är ett sätt att hantera skillnaderna mellan Azure och Azure Stack. En profil för API-versionen är en uppsättning AzureRM PowerShell-moduler med specifika API-versioner. Den **AzureRM.Bootstrapper** modulen som är tillgänglig via PowerShell-galleriet innehåller PowerShell-cmdlets som krävs för att arbeta med API-versionsprofiler.

Du kan installera den senaste Azure Stack PowerShell-modulen med eller utan Internet-anslutning till värddatorn ASDK:

> [!IMPORTANT]
> Innan du installerar versionen som krävs, se till att du [avinstallera alla befintliga Azure PowerShell-moduler](../azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Med en Internetanslutning** från värddatorn ASDK. Kör följande PowerShell-skript för att installera dessa moduler på din development kit-installation:

- Azure Stack 1901 eller senare:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Azure Stack-Modulversion 1.7.1 är en viktig ändring. Migrera från Azure Stack 1.6.0 eller senare finns i den [Migreringsguide](https://aka.ms/azspshmigration171).

  - Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  - Azure Stack 1810 eller tidigare:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.5.0.
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

  Om installationen lyckas visas AzureRM- och AzureStack-moduler i utdata.

- **Utan Internetanslutning** från värddatorn ASDK. I ett scenario med frånkopplade måste du först hämta PowerShell-moduler på en dator som är ansluten till internet med hjälp av följande PowerShell-kommandon:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Sedan kopiera de hämta paketen till ASDK-dator och registrera platsen som standard-databasen och installerar AzureRM- och AzureStack-moduler från den här lagringsplatsen:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Ladda ned Azure Stack-verktyg

[Verktyg för AzureStack](https://github.com/Azure/AzureStack-Tools) är en GitHub-lagringsplats som är värd för PowerShell-moduler för att hantera och distribuera resurser till Azure Stack. Klona GitHub-databasen för att få dessa verktyg eller ladda ned mappen AzureStack verktyg genom att köra följande skript:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force

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

## <a name="reset-the-password-expiration-policy"></a>Återställ förfalloprincipen för lösenord 

Följ dessa steg för att säkerställa att lösenordet för development kit värden inte går ut innan din utvärderingsversion har upphört att gälla, när du har distribuerat ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Ändra förfalloprincipen för lösenord från Powershell

Kör kommandot från en upphöjd Powershell-konsol:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Ändra förfalloprincipen för lösenord manuellt

1. Öppna på development kit värden **Group Policy Management** (GPMC. MMC) och gå till **Group Policy Management** – **skog: azurestack.local** – **domäner** – **azurestack.local**.
2. Högerklicka på **Standarddomänprincip** och klicka på **redigera**.
3. I den Redigeraren för Grupprinciphantering, navigera till **Datorkonfiguration** – **principer** – **Windows-inställningar** – **säkerhetsinställningar**– **Kontoprinciper** – **lösenordsprincip**.
4. I den högra rutan, dubbelklickar du på **högsta ålder för lösenord**.
5. I den **högsta ålder för lösenord egenskaper** i dialogrutan den **lösenord upphör att gälla i** värde att **180**, och klicka sedan på **OK**.

![Konsolen Grupprinciphantering](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>Aktivera flera innehavare

För distributioner med hjälp av Azure AD, måste du [aktivera flera innehavare](../azure-stack-enable-multitenancy.md#enable-multi-tenancy) för ASDK installationen.

> [!NOTE]
> När administratören eller användaren konton från andra domäner än den som används för att registrera Azure Stack som används för att logga in på Azure Stack-portalen, domännamn som används för att registrera Azure Stack måste läggas till på portalen URL: en. Till exempel om Azure Stack har registrerats med fabrikam.onmicrosoft.com och det användarkonto som har loggat in är admin@contoso.com, URL: en du använder för att logga in på användarportalen: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Nästa steg

[Registrera ASDK med Azure](asdk-register.md)
