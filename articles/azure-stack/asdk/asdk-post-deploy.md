---
title: "Bokför distributionskonfigurationer för Azure Stack Development Kit (ASDK) | Microsoft Docs"
description: "Beskriver de rekommenderade konfigurationsändringarna som gör när du har installerat Azure Stack Development Kit (ASDK)."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 2183576e87aa2fb31f8be8f676a5aee7d52f68df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="post-asdk-installation-configuration-tasks"></a>Publicera ASDK installation konfigurationsuppgifter
Efter [installerar ASDK](asdk-install.md), det finns några rekommenderade efter installationen konfigurationen ändras. 

## <a name="install-azure-stack-powershell"></a>Installera Azure Stack PowerShell 
Azure Stack kompatibla Azure PowerShell-moduler som krävs för att arbeta med Azure-stacken.

PowerShell-kommandon för Azure-stacken installeras via PowerShell-galleriet. Öppna en upphöjd PowerShell-session för att registrera PSGallery databasen och kör följande kommando:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Azure Stack kompatibel AzureRM moduler installeras via API-version profiler. 2017-03-09-profil API version profil, som är tillgänglig genom att installera modulen AzureRM.Bootstrapper krävs för Azure-stacken. 
 
 Du kan installera PowerShell för Azure-stacken med eller utan internet-anslutning till värddatorn ASDK:

- **Med en Internetanslutning** från värddatorn ASDK. Kör följande PowerShell-skript för att installera dessa moduler på development kit installationen:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```
  Om installationen lyckas visas AzureRM och AzureStack-moduler i utdata.

- **Utan Internetanslutning** från värddatorn ASDK. I ett frånkopplat scenario måste du först hämta PowerShell-moduler för en dator som är ansluten till internet med hjälp av följande PowerShell-kommandon:

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
    -RequiredVersion 1.2.11
  ```
  Därefter kopiera de hämta paketen till ASDK datorn och registrera platsen till standarddatabas och installera modulerna AzureRM och AzureStack från den här lagringsplatsen:

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

## <a name="download-the-azure-stack-tools"></a>Hämta Azure Stack-verktyg
[AzureStack verktyg](https://github.com/Azure/AzureStack-Tools) är en GitHub-lagringsplats som är värd för PowerShell-moduler för att hantera och distribuera resurser till Azure-stacken. Om du vill hämta verktygen klona GitHub-lagringsplatsen eller hämta mappen AzureStack verktyg genom att köra följande skript:

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
Du kan använda cmdleten Test-AzureStack genom att följa dessa steg för att säkerställa att distributionen ASDK lyckades:

1. Logga in som AzureStack\CloudAdmin på värddatorn ASDK.
2. Öppna PowerShell som administratör (inte PowerShell ISE).
3. Kör: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Kör: `Test-AzureStack`

Testerna ta några minuter att slutföra. Om installationen har lyckats resultatet ser ut ungefär så:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Om ett fel uppstod, följ felsökningsstegen för att få hjälp.

## <a name="activate-the-administrator-and-tenant-portals"></a>Aktivera portaler administratör och klient
Du måste aktivera både Azure Stack-administratör och klienten portaler när distributioner som använder Azure AD. Den här aktiveringen samtycker till att ge Azure-Stack-portalen och Azure Resource Manager rätt behörigheter (som visas på sidan medgivande) för alla användare av katalogen.

- För administratörsportalen, gå till https://adminportal.local.azurestack.external/guest/signup, Läs informationen och klicka sedan på **acceptera**. När du accepterar bör du lägga till tjänstadministratörer som inte är också directory innehavaradministratörer.

- För klientportal, gå till https://portal.local.azurestack.external/guest/signup, Läs informationen och klicka sedan på **acceptera**. När du accepterar, kan användare i katalogen logga in på klientportalen. 

> [!NOTE] 
> Om portalerna inte har aktiverat directory administratören logga in och använda portalerna. Om en annan användare loggar in, visas ett fel som talar om att administratören inte har behörighet till andra användare. När administratören inte internt hör till den katalog som Azure-stacken är registrerad på måste Azure-stacken katalogen läggas till Aktiverings-URL. Till exempel om Azure-stacken är registrerad på fabrikam.onmicrosoft.com och Administratörsanvändare är admin@contoso.com, navigera till https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com att aktivera portalen. 

## <a name="reset-the-password-expiration-policy"></a>Återställ förfalloprincipen för lösenord 
Följ dessa steg om du vill kontrollera att lösenordet för development kit värden inte upphör att gälla innan din utvärdering avslutas, när du har distribuerat ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Ändra förfalloprincipen för lösenord från Powershell:
Kör kommandot från en upphöjd Powershell-konsolen:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Ändra förfalloprincipen för lösenord manuellt:
1. Öppna på development kit värden **Grupprinciphantering** (GPMC. MMC) och gå till **Grupprinciphantering** – **skog: azurestack.local** – **domäner** – **azurestack.local**.
2. Högerklicka på **Standarddomänprincip** och på **redigera**.
3. I den Redigeraren för Grupprinciphantering, navigera till **Datorkonfiguration** – **principer** – **Windowsinställningar** – **säkerhetsinställningar**– **Konto principer** – **lösenordsprincip**.
4. I den högra rutan, dubbelklickar du på **högsta ålder för lösenord**.
5. I den **högsta ålder för lösenord egenskaper** i dialogrutan den **lösenord upphör att gälla om** värde till **180**, och klicka sedan på **OK**.

![Konsolen Grupprinciphantering](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Nästa steg
[Registrera ASDK med Azure](asdk-register.md)
