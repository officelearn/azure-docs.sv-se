---
title: Aktivera anslutning till fjärrskrivbord för en roll i Azure-molntjänster
description: Hur du konfigurerar ditt tjänstprogram för Azure-molnet för att tillåta anslutningar till fjärrskrivbord
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.workload: azure
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: fe8b2b59616246743b38aa3b7a7972c092529b5d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31788474"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med hjälp av Visual Studio

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Fjärrskrivbord kan du få åtkomst till skrivbordet på en roll som körs i Azure. Du kan använda anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med programmet när den körs.

Guiden Publicera som Visual Studio tillhandahåller för molntjänster innehåller ett alternativ för att aktivera Fjärrskrivbord under publiceringsprocessen, med hjälp av autentiseringsuppgifter som du anger. Det här alternativet är lämpligt när du använder Visual Studio 2017 version 15,4 och tidigare.

Med Visual Studio 2017 version 15,5 och senare, men rekommenderas det att du inte aktivera fjärrskrivbord via guiden Publicera om du arbetar med endast som en enda utvecklare. Alla situationen projektet som kan öppnas av andra utvecklare aktivera du i stället fjärrskrivbord genom Azure-portalen via PowerShell, eller från en version definition i ett arbetsflöde för kontinuerlig distribution. Denna rekommendation beror på en ändring i hur Visual Studio kommunicerar med fjärrskrivbord på molntjänst för virtuell dator, som beskrivs i den här artikeln.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurera fjärrskrivbord via Visual Studio 2017 version 15,4 och tidigare

När du använder Visual Studio 2017 version 15,4 och tidigare, kan du använda den **aktivera Fjärrskrivbord för alla roller** i guiden Publicera. Du kan använda guiden i Visual Studio 2017 version 15,5 och senare, men Använd inte alternativet för fjärrskrivbord.

1. I Visual Studio kan du starta guiden Publicera genom att högerklicka på ditt molntjänstprojekt i Solution Explorer och välja **publicera**.

2. Logga in på din Azure-prenumeration om det behövs och välj **nästa**.

3. På den **inställningar** väljer **aktivera Fjärrskrivbord för alla roller**och välj den **inställningar...**  länk för att öppna den **konfiguration av fjärrskrivbord** dialogrutan.

4. Längst ned i dialogrutan Välj **fler alternativ**. Detta kommando visar en lista som du kan skapa eller välj ett certifikat så att du kan kryptera autentiseringsuppgifter information när du ansluter via fjärrskrivbord.

   > [!Note]
   > De certifikat som du behöver för en anslutning till fjärrskrivbord skiljer sig från de certifikat som du använder för andra åtgärder i Azure. Fjärråtkomst-certifikatet måste ha en privat nyckel.

5. Välj ett certifikat från listan eller välj  **&lt;skapa... &gt;**. Om du skapar ett nytt certifikat, ange ett eget namn för det nya certifikatet när du uppmanas och väljer **OK**. Det nya certifikatet visas i den nedrullningsbara listrutan.

6. Ange ett användarnamn och ett lösenord. Du kan inte använda ett befintligt konto. Använd inte ”administratör” som användarnamn för det nya kontot.

7. Välj ett datum som kontot upphör att gälla och efter vilken fjärrskrivbordsanslutningar kommer att blockeras.

8. När du har angett informationen som krävs, Välj **OK**. Visual Studio lägger till inställningar för fjärrskrivbord till ditt projekt `.cscfg` och `.csdef` filer, inklusive det lösenord som har krypterats med det valda certifikatet.

9. Slutför eventuella återstående steg med den **nästa** knappen och markera **publicera** när du är redo att publicera din tjänst i molnet. Om du inte är redo att publicera väljer **Avbryt** och svaret **Ja** när du uppmanas att spara ändringarna. Du kan publicera din molntjänst senare med de här inställningarna.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurera Fjärrskrivbord när du använder Visual Studio 2017 version 15,5 och senare

Du kan fortfarande använda guiden Publicera med ett molntjänstprojekt med Visual Studio 2017 version 15,5 och senare. Du kan också använda den **aktivera Fjärrskrivbord för alla roller** alternativet om du arbetar endast som en enda utvecklare.

Om du arbetar som en del av ett team, bör du i stället aktivera Fjärrskrivbord på Azure-molntjänst genom att använda antingen den [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md) eller [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Denna rekommendation beror på en ändring i hur Visual Studio 2017 version 15,5 och senare kommunicerar med VM-Molntjänsten. När du aktiverar fjärrskrivbord via guiden Publicera kommunicera tidigare versioner av Visual Studio med den virtuella datorn via vad kallas ”RDP plugin-programmet”. Visual Studio 2017 version 15,5 och senare kommunicerar med hjälp av ”RDP tillägget” i stället som är säkrare och mer flexibelt. Den här ändringen justerar också med Azure portal och PowerShell-metoder för att aktivera Fjärrskrivbord också använda RDP-tillägget.

När Visual Studio kommunicerar med RDP-tillägget kan överföra ett lösenord i klartext via SSL. Projektets konfigurationsfiler lagra dock endast ett krypterat lösenord, som kan dekrypteras i oformaterad text med det lokala certifikat som ursprungligen användes för att kryptera den.

Om du distribuerar molntjänstprojektet från samma utvecklingsdatorn varje gång, finns det lokala certifikatarkivet. I så fall måste du fortfarande kan använda den **aktivera Fjärrskrivbord för alla roller** i guiden Publicera.

Om du eller andra utvecklare du vill distribuera molntjänstprojektet från olika datorer, men har sedan de andra datorerna inte certifikatet som krävs för att dekryptera lösenordet. Därför kan se du följande felmeddelande:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Du kan ändra lösenordet varje gång du distribuerar Molntjänsten, men åtgärden praktiskt för alla som behöver använda Fjärrskrivbord.

Om du delar projektet med ett team, är det bäst att avmarkera alternativet i guiden Publicera och i stället aktivera fjärrskrivbord direkt via den [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md) eller genom att använda [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Distribuera från en build-server med Visual Studio 2017 version 15,5 och senare

Du kan distribuera ett molntjänstprojekt från en build-server (till exempel med Visual Studio Team Services) vilka Visual Studio-2017 15,5 eller senare är installerat i build-agenten. Med den här ordningen sker distribution från samma dator där krypteringscertifikat är tillgänglig.

Om du vill använda RDP-tillägget från Visual Studio Team Services innehåller följande information i build-definition:

1. Inkludera `/p:ForceRDPExtensionOverPlugin=true` i MSBuild-argument för att kontrollera att distributionen fungerar med RDP-tillägget i stället för RDP-plugin-programmet. Exempel:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Efter din build-stegen lägga till den **Azure Cloud Service-distributionen** steg och ange dess egenskaper.

1. Efter steget distribution lägger du till en **Azure Powershell** steg genom att ange dess **visningsnamn** egenskapen till ”Azure-distribution: aktivera RDP tillägget” (eller en annan lämplig namn) och välj din lämplig Azure prenumeration.

1. Ange **skripttypen** till ”infogad” och klistra in koden nedan i den **infogat skript** fältet. (Du kan också skapa en `.ps1` filen i ditt projekt med det här skriptet kan du ange **skripttypen** till ”sökväg skriptfilen” och ange **skriptsökvägen** så att den pekar till filen.)

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

Du kan använda Visual Studio Server Explorer för att logga in på molntjänst för virtuell dator när du har publicerat din tjänst i molnet på Azure och har aktiverat Fjärrskrivbord:

1. I Server Explorer expanderar den **Azure** nod, och sedan expanderar du noden för en tjänst i molnet och en av dess roller för att visa en lista över instanser.

2. En instans-noden och välj **ansluta med hjälp av fjärrskrivbord**.

3. Ange användarnamn och lösenord som du skapade tidigare. Du är nu inloggad i fjärrsessionen.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du molntjänster](cloud-services-how-to-configure-portal.md)