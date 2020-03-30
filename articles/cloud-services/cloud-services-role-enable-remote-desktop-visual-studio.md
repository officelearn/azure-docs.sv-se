---
title: Aktivera Fjärrskrivbord för en roll med Visual Studio (Azure Cloud Services)
description: Konfigurera ditt Azure-molntjänstprogram så att fjärrskrivbordsanslutningar tillåts
services: cloud-services
author: ghogen
manager: jillfra
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: f4622e44c795182ee68c617f335c9e1651d3adcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294392"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med Visual Studio

> [!div class="op_single_selector"]
> * [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Med Fjärrskrivbord kan du komma åt skrivbordet för en roll som körs i Azure. Du kan använda en anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med programmet medan det körs.

Publiceringsguiden som Visual Studio tillhandahåller för molntjänster innehåller ett alternativ för att aktivera Fjärrskrivbord under publiceringsprocessen med hjälp av autentiseringsuppgifter som du tillhandahåller. Att använda det här alternativet är lämpligt när du använder Visual Studio 2017 version 15.4 och tidigare.

Med Visual Studio 2017 version 15.5 och senare rekommenderar vi dock att du undviker att aktivera Fjärrskrivbord via publiceringsguiden om du inte bara arbetar som en enda utvecklare. För alla situationer där projektet kan öppnas av andra utvecklare aktiverar du i stället Fjärrskrivbord via Azure-portalen, via PowerShell eller från en versionspipeline i ett arbetsflöde för kontinuerlig distribution. Den här rekommendationen beror på en förändring i hur Visual Studio kommunicerar med Fjärrskrivbord på den virtuella datorn för molntjänsten, vilket förklaras i den här artikeln.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurera Fjärrskrivbord via Visual Studio 2017 version 15.4 och tidigare

När du använder Visual Studio 2017 version 15.4 och tidigare kan du använda alternativet **Aktivera fjärrskrivbord för alla roller** i publiceringsguiden. Du kan fortfarande använda guiden med Visual Studio 2017 version 15.5 och senare, men använd inte alternativet Fjärrskrivbord.

1. Starta publiceringsguiden i Visual Studio genom att högerklicka på molntjänstprojektet i Solution Explorer och välja **Publicera**.

2. Logga in på din Azure-prenumeration om det behövs och välj **Nästa**.

3. På sidan **Inställningar** väljer du **Aktivera fjärrskrivbord för alla roller**och väljer sedan länken **Inställningar...** för att öppna dialogrutan **Konfiguration av fjärrskrivbord.**

4. Längst ned i dialogrutan väljer du **Fler alternativ**. Det här kommandot visar en listruta där du skapar eller väljer ett certifikat så att du kan kryptera autentiseringsuppgifter när du ansluter via fjärrskrivbord.

   > [!Note]
   > De certifikat som du behöver för en fjärrskrivbordsanslutning skiljer sig från de certifikat som du använder för andra Azure-åtgärder. Fjärråtkomstcertifikatet måste ha en privat nyckel.

5. Välj ett certifikat i listan eller välj ** &lt;Skapa... &gt;**. Om du skapar ett nytt certifikat anger du ett eget namn för det nya certifikatet när du uppmanas att göra det och väljer **OK**. Det nya certifikatet visas i listrutan.

6. Ange ett användarnamn och ett lösenord. Du kan inte använda ett befintligt konto. Använd inte "Administratör" som användarnamn för det nya kontot.

7. Välj ett datum då kontot upphör att gälla och därefter blockeras fjärrskrivbordsanslutningar.

8. När du har angett all nödvändig information väljer du **OK**. Visual Studio lägger till inställningarna `.cscfg` för `.csdef` fjärrskrivbord i projektets och filerna, inklusive lösenordet som krypteras med det valda certifikatet.

9. Slutför eventuella återstående steg med knappen **Nästa** och välj sedan **Publicera** när du är redo att publicera molntjänsten. Om du inte är redo att publicera väljer du **Avbryt** och svarar **Ja** när du uppmanas att spara ändringarna. Du kan publicera din molntjänst senare med dessa inställningar.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurera Fjärrskrivbord när du använder Visual Studio 2017 version 15.5 och senare

Med Visual Studio 2017 version 15.5 och senare kan du fortfarande använda publiceringsguiden med ett molntjänstprojekt. Du kan också använda alternativet **Aktivera fjärrskrivbord för alla roller** om du bara arbetar som en enda utvecklare.

Om du arbetar som en del av ett team bör du i stället aktivera fjärrskrivbord på Azure-molntjänsten med hjälp av [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md) eller [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Den här rekommendationen beror på en förändring i hur Visual Studio 2017 version 15.5 och senare kommunicerar med molntjänsten VM. När du aktiverar Fjärrskrivbord via publiceringsguiden kommunicerar tidigare versioner av Visual Studio med den virtuella datorn via det som kallas "RDP-plugin". Visual Studio 2017 version 15.5 och senare kommunicerar istället med hjälp av "RDP-tillägget" som är säkrare och mer flexibelt. Den här ändringen stämmer också överens med det faktum att Azure-portalen och PowerShell-metoderna för att aktivera Fjärrskrivbord också använder RDP-tillägget.

När Visual Studio kommunicerar med RDP-tillägget överför den ett lösenord för oformaterad text via TLS. Projektets konfigurationsfiler lagrar dock bara ett krypterat lösenord, som kan dekrypteras till oformaterad text endast med det lokala certifikat som ursprungligen användes för att kryptera det.

Om du distribuerar molntjänstprojektet från samma utvecklingsdator varje gång är det lokala certifikatet tillgängligt. I det här fallet kan du fortfarande använda alternativet **Aktivera fjärrskrivbord för alla roller** i publiceringsguiden.

Om du eller andra utvecklare vill distribuera molntjänstprojektet från olika datorer har dock inte de andra datorerna det certifikat som krävs för att dekryptera lösenordet. Därför visas följande felmeddelande:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Du kan ändra lösenordet varje gång du distribuerar molntjänsten, men den åtgärden blir obekväm för alla som behöver använda Fjärrskrivbord.

Om du delar projektet med ett team är det bäst att avmarkera alternativet i publiceringsguiden och i stället aktivera Fjärrskrivbord direkt via [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md) eller med [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Distribuera från en byggserver med Visual Studio 2017 version 15.5 och senare

Du kan distribuera ett molntjänstprojekt från en byggserver (till exempel med Azure DevOps Services) där Visual Studio 2017 version 15.5 eller senare är installerad i byggagenten. Med det här arrangemanget sker distributionen från samma dator där krypteringscertifikatet är tillgängligt.

Om du vill använda RDP-tillägget från Azure DevOps Services inkluderar du följande information i din build pipeline:

1. Inkludera `/p:ForceRDPExtensionOverPlugin=true` i dina MSBuild-argument för att se till att distributionen fungerar med RDP-tillägget i stället för RDP-insticksprogrammet. Ett exempel:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. När du har skapat steg lägger du till Azure **Cloud Service Deployment-steget** och anger dess egenskaper.

1. Efter distributionssteget lägger du till ett **Azure Powershell-steg,** anger **egenskapen Visningsnamn** till "Azure Deployment: Enable RDP Extension" (eller ett annat lämpligt namn) och väljer lämplig Azure-prenumeration.

1. Ange **skripttyp** till "Infogad" och klistra in koden nedan i fältet **Infogat skript.** (Du kan också `.ps1` skapa en fil i projektet med det här skriptet, ange **skripttyp** till "Script File Path" och ange **skriptsökväg** så att den pekar på filen.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Ansluta till en Azure-roll med fjärrskrivbord

När du har publicerat molntjänsten på Azure och har aktiverat Fjärrskrivbord kan du använda Visual Studio Server Explorer för att logga in på den virtuella molntjänsten VM:

1. Expandera **Azure-noden** i Server Explorer och expandera noden för en molntjänst och en av dess roller för att visa en lista över instanser.

2. Högerklicka på en instansnod och välj **Anslut med fjärrskrivbord**.

3. Ange användarnamn och lösenord som du skapade tidigare. Du är nu inloggad på din fjärrsession.

## <a name="additional-resources"></a>Ytterligare resurser

[Konfigurera molntjänster](cloud-services-how-to-configure-portal.md)
