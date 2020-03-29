---
title: Använda PowerShell för att aktivera fjärrskrivbord för en roll
titleSuffix: Azure Cloud Services
description: Konfigurera ditt azure cloud service-program med PowerShell för att tillåta fjärrskrivbordsanslutningar
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386127"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med PowerShell

> [!div class="op_single_selector"]
> * [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Med Fjärrskrivbord kan du komma åt skrivbordet för en roll som körs i Azure. Du kan använda en anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med programmet medan det körs.

I den här artikeln beskrivs hur du aktiverar fjärrskrivbord på molntjänstrollerna med PowerShell. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för de förutsättningar som krävs för den här artikeln. PowerShell använder tillägget för fjärrskrivbord så att du kan aktivera Fjärrskrivbord när programmet har distribuerats.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurera fjärrskrivbord från PowerShell
[Med cmdleten Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) kan du aktivera Fjärrskrivbord på angivna roller eller alla roller i molntjänstdistributionen. Med cmdlet kan du ange användarnamn och lösenord för fjärrskrivbordsanvändaren via parametern *Autentiseringsuppgifter* som accepterar ett PSCredential-objekt.

Om du använder PowerShell interaktivt kan du enkelt ställa in PSCredential-objektet genom att anropa cmdleten [Get-Credentials.](https://technet.microsoft.com/library/hh849815.aspx)

```powershell
$remoteusercredentials = Get-Credential
```

Det här kommandot visar en dialogruta där du kan ange användarnamn och lösenord för fjärranvändaren på ett säkert sätt.

Eftersom PowerShell hjälper till i automatiseringsscenarier kan du också konfigurera **PSCredential-objektet** på ett sätt som inte kräver användarinteraktion. Först måste du ställa in ett säkert lösenord. Du börjar med att ange ett oformaterat textlösenord konvertera det till en säker sträng med [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Därefter måste du konvertera den här säkra strängen till en krypterad standardsträng med [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nu kan du spara den krypterade standardsträngen i en fil med [Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Du kan också skapa en säker lösenordsfil så att du inte behöver skriva in lösenordet varje gång. Dessutom är en säker lösenordsfil bättre än en oformaterad textfil. Använd följande PowerShell för att skapa en säker lösenordsfil:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> När du ställer in lösenordet ska du se till att du uppfyller [komplexitetskraven](https://technet.microsoft.com/library/cc786468.aspx).

Om du vill skapa autentiseringsuppgifterna från den säkra lösenordsfilen måste du läsa filinnehållet och konvertera tillbaka dem till en säker sträng med [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

[Cmdleten Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) accepterar också en *parameter för förfallodatum,* som anger en **DateTime** då användarkontot upphör att gälla. Du kan till exempel ange att kontot ska upphöra att gälla några dagar från aktuellt datum och aktuell tid.

Det här PowerShell-exemplet visar hur du ställer in fjärrskrivbordstillägget på en molntjänst:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Du kan också ange distributionsplatsen och rollerna som du vill aktivera fjärrskrivbord på. Om dessa parametrar inte anges aktiverar cmdlet fjärrskrivbord på alla roller i **distributionsplatsen För produktion.**

Tillägget För fjärrskrivbord är associerat med en distribution. Om du skapar en ny distribution för tjänsten måste du aktivera fjärrskrivbord på den distributionen. Om du alltid vill ha fjärrskrivbord aktiverat bör du överväga att integrera PowerShell-skripten i distributionsarbetsflödet.

## <a name="remote-desktop-into-a-role-instance"></a>Fjärrskrivbord till en rollinstans

[Cmdlet get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) används för att fjärrskrivbordas till en specifik rollinstans av molntjänsten. Du kan använda parametern *LocalPath* för att hämta RDP-filen lokalt. Du kan också använda parametern *Launch* för att direkt starta dialogrutan Anslutning till fjärrskrivbord för att komma åt molntjänstrollinstansen.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Kontrollera om tillägget Fjärrskrivbord är aktiverat på en tjänst

[Cmdleten Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) visar att fjärrskrivbord är aktiverat eller inaktiverat vid en tjänstdistribution. Cmdlet returnerar användarnamnet för fjärrskrivbordsanvändaren och de roller som fjärrskrivbordstillägget är aktiverat för. Som standard sker detta på distributionsplatsen och du kan välja att använda mellanlagringsplatsen i stället.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Ta bort fjärrskrivbordstillägg från en tjänst

Om du redan har aktiverat fjärrskrivbordstillägget på en distribution och behöver uppdatera fjärrskrivbordsinställningarna tar du först bort tillägget. Och aktivera det igen med de nya inställningarna. Om du till exempel vill ange ett nytt lösenord för fjärranvändarkontot eller om kontot har upphört att gälla. Detta krävs på befintliga distributioner som har tillägget för fjärrskrivbord aktiverat. För nya distributioner kan du helt enkelt använda tillägget direkt.

Om du vill ta bort fjärrskrivbordstillägget från distributionen kan du använda cmdleten [Remove-AzureServiceRemoteDesktopExtension.](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) Du kan också ange distributionsplatsen och rollen som du vill ta bort fjärrskrivbordstillägget från.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Om du vill ta bort tilläggskonfigurationen helt bör du anropa *ta bort* cmdlet med parametern **UninstallConfiguration.**
>
> Parametern **UninstallConfiguration** avinstallerar alla tilläggskonfigurationer som tillämpas på tjänsten. Varje tilläggskonfiguration är associerad med tjänstkonfigurationen. Anropar *den bortkopplade* cmdleten utan **UninstallKonfigurering** disassociates <mark>distributionen</mark> från tilläggskonfigurationen, vilket effektivt tar bort tillägget. Tilläggskonfigurationen förblir dock associerad med tjänsten.

## <a name="additional-resources"></a>Ytterligare resurser

[Konfigurera molntjänster](cloud-services-how-to-configure-portal.md)


