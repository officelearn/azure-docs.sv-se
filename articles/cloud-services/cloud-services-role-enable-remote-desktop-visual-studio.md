---
title: Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services
description: Hur du konfigurerar ditt tjänstprogram för Azure-molnet för att tillåta anslutningar till fjärrskrivbord
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: 703e969fe31def329be60037cceba27864063b4e
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304072"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Aktivera anslutning till fjärrskrivbordet för en roll i Azure Cloud Services med hjälp av Visual Studio

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Fjärrskrivbord kan du komma åt skrivbordet för en roll som körs i Azure. Du kan använda en anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med ditt program när den körs.

Publiceringsguiden med Visual Studio för molntjänster innehåller ett alternativ för att aktivera Fjärrskrivbord under publiceringsprocessen, med hjälp av autentiseringsuppgifter som du anger. Det är lämpligt att använda det här alternativet när du använder Visual Studio 2017 version 15.4 och tidigare.

Med Visual Studio 2017 version 15.5 och senare, men rekommenderar vi att du inte aktivera fjärrskrivbord via Publiceringsguiden såvida inte du arbetar endast som en enskild utvecklare. För alla situationer som projektet som kan öppnas av andra utvecklare aktivera du i stället fjärrskrivbord via Azure-portalen, via PowerShell eller från en releasepipeline i ett arbetsflöde för kontinuerlig distribution. Den här rekommendationen är på grund av en ändring i hur Visual Studio kommunicerar med fjärrskrivbord i Molntjänsten VM, enligt beskrivningen i den här artikeln.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurera fjärrskrivbord via Visual Studio 2017 version 15.4 och tidigare

När du använder Visual Studio 2017 version 15.4 och tidigare, kan du använda den **aktivera Fjärrskrivbord för alla roller** alternativet i Publiceringsguiden. Du kan fortfarande använda guiden med Visual Studio 2017 version 15.5 och senare, men Använd inte alternativet för fjärrskrivbord.

1. I Visual Studio kan du starta Publiceringsguiden genom att högerklicka på din cloud service-projekt i Solution Explorer och välja **publicera**.

2. Logga in på din Azure-prenumeration om det behövs och välj **nästa**.

3. På den **inställningar** väljer **aktivera Fjärrskrivbord för alla roller**och välj sedan den **inställningar...**  länk för att öppna den **konfiguration av fjärrskrivbord** dialogrutan.

4. Längst ned i dialogrutan Välj **fler alternativ**. Det här kommandot visar en nedrullningsbar lista där du skapar eller väljer ett certifikat så att du kan kryptera autentiseringsuppgifter när du ansluter via fjärrskrivbord.

   > [!Note]
   > De certifikat som du behöver för en fjärrskrivbordsanslutning skiljer sig från de certifikat som du använder för andra Azure-åtgärder. Fjärråtkomst-certifikatet måste ha en privat nyckel.

5. Välj ett certifikat i listan eller välj  **&lt;skapa... &gt;**. Om du skapar ett nytt certifikat, ange ett eget namn för det nya certifikatet när du uppmanas och välj **OK**. Det nya certifikatet visas i rutan nedrullningsbara listan.

6. Ange ett användarnamn och ett lösenord. Du kan inte använda ett befintligt konto. Använd inte ”administratör” som användarnamn för det nya kontot.

7. Välj ett datum som kontot upphör att gälla, samt efter vilka fjärrskrivbordsanslutningar kommer att blockeras.

8. När du har angett all nödvändig information, Välj **OK**. Visual Studio lägger du till inställningar för fjärrskrivbord i projektets `.cscfg` och `.csdef` filer, inklusive det lösenord som har krypterats med det valda certifikatet.

9. Slutföra alla återstående steg med hjälp av den **nästa** knappen och välj **publicera** när du är redo att publicera din molntjänst. Om du inte är redo att publicera, väljer **Avbryt** och svar **Ja** när du uppmanas att spara ändringarna. Du kan publicera din molntjänst senare med de här inställningarna.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurera Fjärrskrivbord när du använder Visual Studio 2017 version 15.5 och senare

Du kan fortfarande använda Publiceringsguiden med ett molntjänstprojekt med Visual Studio 2017 version 15.5 och senare. Du kan också använda den **aktivera Fjärrskrivbord för alla roller** alternativet om du använder endast som en enskild utvecklare.

Om du arbetar som en del av ett team, bör du istället aktivera Fjärrskrivbord i Azure-molntjänst genom att använda antingen den [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md) eller [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Den här rekommendationen är på grund av en ändring i hur Visual Studio 2017 version 15.5 och senare kommunicerar med VM-Molntjänsten. När du aktiverar Remote Desktop via Publiceringsguiden kommunicera tidigare versioner av Visual Studio med den virtuella datorn via vad kallas ”RDP plugin-programmet”. Visual Studio 2017 version 15.5 och senare kommunicerar i stället använder ”RDP-tillägget” det är säkrare och mer flexibel. Den här ändringen också överensstämmer med det faktum att Azure-portalen och PowerShell-metoder för att aktivera fjärrskrivbord även använda RDP-tillägget.

När Visual Studio kommunicerar med RDP-tillägget kan överföra ett lösenord i klartext via SSL. Projektets konfigurationsfiler lagra dock endast ett krypterat lösenord, som kan dekrypteras i klartext endast med det lokala certifikatarkivet som användes för att kryptera den.

Om du distribuerar molntjänstprojektet från samma utvecklingsdatorn varje gång, finns det lokala certifikatarkivet. I det här fallet, du kan fortfarande använda den **aktivera Fjärrskrivbord för alla roller** alternativet i Publiceringsguiden.

Om du eller andra utvecklare distribuerar molntjänstprojektet från olika datorer, men har sedan de andra datorerna inte certifikatet som krävs för att dekryptera lösenordet. Därför kan se du följande felmeddelande visas:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Du kan ändra lösenordet varje gång du distribuerar Molntjänsten, men åtgärden inte praktiskt för alla som behöver använda Fjärrskrivbord.

Om du delar projektet med ett team, är det bäst att avmarkerar du alternativet i Publiceringsguiden och i stället aktivera fjärrskrivbord direkt via den [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md) eller genom att använda [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Distribuera från en build-server med Visual Studio 2017 version 15.5 och senare

Du kan distribuera ett molntjänstprojekt från en build-servern (till exempel med Azure DevOps-tjänster) som Visual Studio 2017 version 15.5 eller senare är installerad i build-agenten. Med den här ordningen sker distribueringen från samma dator där krypteringscertifikatet är tillgänglig.

Om du vill använda RDP-tillägget från Azure DevOps-tjänster med följande information i din pipeline för build:

1. Inkludera `/p:ForceRDPExtensionOverPlugin=true` i MSBuild-argument för att kontrollera att distributionen fungerar med RDP-tillägget i stället för RDP-plugin-programmet. Exempel:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Efter din byggsteg, lägga till den **Azure Molntjänstdistribution** steg och Ställ in dess egenskaper.

1. Efter distributionen, lägger du till en **Azure Powershell** steg genom att ange dess **visningsnamn** egenskap till ”Azure-distribution: aktivera RDP-tillägget” (eller en annan lämplig namn) och välj lämplig Azure prenumeration.

1. Ange **Skripttyp** till ”intern” och klistra in koden nedan i den **infogat skript** fält. (Du kan också skapa en `.ps1` filen i ditt projekt med det här skriptet genom att ange **Skripttyp** till ”skript filsökväg” och ange **skriptets sökväg** så att den pekar till filen.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Ansluta till en Azure-roll med hjälp av fjärrskrivbord

När du publicerar din molntjänst i Azure och har aktiverat Fjärrskrivbord kan använda du Visual Studio Server Explorer för att logga in på Molntjänsten VM:

1. I Server Explorer expanderar du den **Azure** noden och sedan expanderar du noden för en molntjänst och en av dess roller att visa en lista över instanser.

2. Högerklicka på en instans-nod och välj **ansluta med hjälp av fjärrskrivbord**.

3. Ange det användarnamn och lösenord som du skapade tidigare. Du är nu inloggad på ditt fjärrsessionen.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)
