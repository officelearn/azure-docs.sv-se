---
title: Aktivera anslutning till fjärrskrivbordet för en roll i Azure Cloud Services med hjälp av PowerShell
description: Hur du konfigurerar ditt tjänstprogram för azure-molnet som använder PowerShell för att tillåta anslutningar till fjärrskrivbord
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: c5b70d40ed43cfc5d1c7a826c639d00d394733fb
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060868"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Aktivera anslutning till fjärrskrivbordet för en roll i Azure Cloud Services med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Fjärrskrivbord kan du komma åt skrivbordet för en roll som körs i Azure. Du kan använda en anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med ditt program när den körs.

Den här artikeln beskriver hur du aktiverar fjärrskrivbord på dina Molntjänstroller med hjälp av PowerShell. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för kraven för den här artikeln. PowerShell använder Remote Desktop-tillägget så att du kan aktivera Fjärrskrivbord när programmet har distribuerats.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurera fjärrskrivbord från PowerShell

Den [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet kan du aktivera Fjärrskrivbord på angivna roller eller alla roller i molndistributionen för tjänsten. Cmdlet: en kan du ange användarnamnet och lösenordet för fjärrskrivbordsanvändare via den *Credential* parameter som accepterar ett PSCredential-objekt.

Om du använder PowerShell interaktivt kan du enkelt skapa PSCredential-objekt genom att anropa den [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

```
$remoteusercredentials = Get-Credential
```

Det här kommandot visar en dialogruta så att du kan ange användarnamn och lösenord för användaren på ett säkert sätt.

Eftersom PowerShell hjälper i automatiseringsscenarier, du kan också ställa in den **PSCredential** objekt på ett sätt som inte kräver interaktion från användaren. Först behöver du ställa in ett säkert lösenord. Du börjar med att ange ett lösenord i oformaterad text konvertera den till en säker sträng med hjälp av [ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx). Sedan måste du konvertera den här säker sträng till en krypterad sträng som standard med hjälp av [ConvertFrom SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nu kan du spara den här krypterade vanlig sträng i en fil med hjälp av [Set-innehåll](https://technet.microsoft.com/library/ee176959.aspx).

Du kan också skapa ett säkert lösenord-fil så att du inte anger du lösenordet varje gång. En fil för säkert lösenord är dessutom bättre än en oformaterad textfil. Använd följande PowerShell för att skapa en fil för säkert lösenord:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> När du anger lösenordet, se till att du uppfyller de [komplexitetskrav](https://technet.microsoft.com/library/cc786468.aspx).

För att skapa autentiseringsobjektet från filen säkert lösenord, måste du läsa igenom filen och konvertera dem till en säker sträng med hjälp av [ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx).

Den [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) -cmdlet accepterar också en *upphör att gälla* parametern, som anger en **DateTime** vid som användarkontot upphör att gälla. Du kan till exempel ange kontot som ska upphöra att gälla några få dagar från aktuellt datum och tid.

Det här PowerShell-exemplet visar hur du anger Remote Desktop-tillägget för en tjänst i molnet:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Du kan även ange den distributionsplats och roller som du vill aktivera Fjärrskrivbord på. Om dessa parametrar inte anges kommer cmdleten gör att Fjärrskrivbord på alla roller i den **produktion** distributionsplats.

Remote Desktop-tillägget är associerad med en distribution. Om du skapar en ny distribution för tjänsten kan behöva du aktivera Fjärrskrivbord på distributionen. Om du alltid vill ha fjärrskrivbord aktiverat bör du överväga integrera PowerShell-skript i ditt arbetsflöde för distribution.

## <a name="remote-desktop-into-a-role-instance"></a>Fjärrskrivbord till en rollinstans

Den [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet är van vid remote desktop till en viss roll-instans för din molntjänst. Du kan använda den *LocalPath* parametern för att ladda ned RDP-filen lokalt. Du kan också använda den *starta* parametern för att starta dialogrutan anslutning till fjärrskrivbord för att komma åt instansen av molntjänstroll direkt.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Kontrollera om tillägget för fjärrskrivbord är aktiverat på en tjänst

Den [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet visar som fjärrskrivbord är aktiverat eller inaktiverat på en tjänstdistribution. Cmdleten returnerar användarnamnet för användaren av fjärrskrivbord och de roller som remote desktop tillägget har aktiverats för. Som standard det händer på distributionsplatsen och du kan välja att använda mellanlagringsplatsen i stället.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Ta bort tillägget för fjärrskrivbord från en tjänst

Om du redan har aktiverat tillägget för remote desktop på en distribution och uppdatera inställningarna för fjärrskrivbordet, du först ta bort tillägget. Och aktivera det igen med de nya inställningarna. Exempel: Om du vill ange ett nytt lösenord för användarkontot eller kontot har upphört att gälla. Detta krävs på befintliga distributioner som har tillägget remote desktop aktiverat. För nya distributioner lägga du helt enkelt till tillägget direkt.

Om du vill ta bort tillägget för remote desktop från distributionen, kan du använda den [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet. Du kan även ange den distributionsplats och roll som du vill ta bort tillägget för remote desktop.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Om du vill ta bort tilläggets konfiguration, bör du anropa den *ta bort* cmdlet med den **UninstallConfiguration** parametern.
>
> Den **UninstallConfiguration** parametern avinstallerar alla tilläggskonfigurationen som tillämpas på tjänsten. Varje tilläggskonfigurationen är associerad med tjänstens konfiguration. Anropa den *ta bort* cmdleten utan **UninstallConfiguration** tas den <mark>distribution</mark> tilläggskonfigurationen, vilket effektivt tar bort den tillägg. Tilläggskonfigurationen är dock fortfarande kopplad till tjänsten.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)
