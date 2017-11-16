---
title: "Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med hjälp av PowerShell"
description: "Hur du konfigurerar ditt tjänstprogram för azure-molnet som använder PowerShell för att tillåta anslutningar till fjärrskrivbord"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: ab99eaa10d232e244b17325188e83128c651caf6
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Fjärrskrivbord kan du få åtkomst till skrivbordet på en roll som körs i Azure. Du kan använda anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med programmet när den körs.

Den här artikeln beskriver hur du aktiverar fjärrskrivbord på dina Molntjänstroller med hjälp av PowerShell. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för kraven för den här artikeln. PowerShell använder Remote Desktop-tillägget så att du kan aktivera Fjärrskrivbord när programmet distribueras.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurera fjärrskrivbord från PowerShell
Den [Set AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet kan du aktivera Fjärrskrivbord på angivna roller eller alla roller i distributionen cloud service. Cmdleten kan du ange användarnamnet och lösenordet för användaren av fjärrskrivbord via den *autentiseringsuppgifter* parameter som accepterar ett PSCredential-objekt.

Om du använder PowerShell interaktivt kan du enkelt skapa PSCredential-objekt genom att anropa den [Get-autentiseringsuppgifter](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

```
$remoteusercredentials = Get-Credential
```

Detta kommando visar en dialogruta där du kan ange användarnamn och lösenord för användaren på ett säkert sätt.

Eftersom PowerShell hjälper i automatiseringsscenarier, kan du också ställa in den **PSCredential** objekt på ett sätt som inte kräver användaråtgärder. Du måste först skapa ett säkert lösenord. Du börjar med att ange ett lösenord i oformaterad text konvertera den till en säker sträng med hjälp av [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Därefter måste du konvertera den här säker sträng till en krypterad sträng som standard med hjälp av [ConvertFrom SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nu kan du spara den här krypterad sträng som standard till en fil med hjälp av [Set-innehåll](https://technet.microsoft.com/library/ee176959.aspx).

Du kan också skapa en fil säkert lösenord så att du inte behöver skriva in lösenordet varje gång. En fil säkert lösenord är också bättre än oformaterad text. Använd följande PowerShell för att skapa en fil säkert lösenord:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> När du ställer in lösenordet kontrollerar du att du uppfyller de [krav på komplexitet](https://technet.microsoft.com/library/cc786468.aspx).
>
>

För att skapa autentiseringsobjektet från filen säkert lösenord, måste du läsa innehållet i filen och omvandla dem till en säker sträng med hjälp av [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

Den [Set AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet accepterar också en *giltighetstid* som anger en **DateTime** vid som användarkontot upphör att gälla. Du kan till exempel ange kontot som ska gälla några dagar från det aktuella datum och tid.

Det här PowerShell-exemplet visar hur du ställer Remote Desktop-tillägget på en tjänst i molnet:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Du kan också ange distributionsplatsen och roller som du vill aktivera Fjärrskrivbord på. Om dessa parametrar anges, cmdlet aktiverar fjärrskrivbord på alla roller i den **produktion** distributionsplatsen.

Tillägget för fjärrskrivbord är associerade med en distribution. Om du skapar en ny distribution för tjänsten som du behöver aktivera Fjärrskrivbord på distributionen. Om du alltid vill ha fjärrskrivbord aktiverat bör du överväga integrering av PowerShell-skript i ditt arbetsflöde för distribution.

## <a name="remote-desktop-into-a-role-instance"></a>Fjärrskrivbord till en rollinstans
Den [Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet är används för att remote desktop i en viss rollinstans av Molntjänsten. Du kan använda den *LocalPath* parametern för att ladda ned RDP-filen lokalt. Eller så kan du använda den *starta* parametern för att starta dialogrutan anslutning till fjärrskrivbord för att få åtkomst till molnet rollen tjänstinstansen direkt.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Kontrollera om tillägget för fjärrskrivbord är aktiverat på en tjänst
Den [Get-AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdleten visas som fjärrskrivbord är aktiverat eller inaktiverat på en tjänstdistribution. Cmdleten returnerar användarnamnet för användaren av fjärrskrivbord och de roller som remote desktop tillägget har aktiverats för. Som standard det som händer på distributionsplatsen och du kan välja att använda mellanlagringsplatsen i stället.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Ta bort tillägget för fjärrskrivbord från en tjänst
Om du redan har aktiverat remote desktop tillägg på en distribution och uppdatera inställningarna för fjärrskrivbordet, först ta bort tillägget. Och aktivera det igen med de nya inställningarna. Till exempel om du vill ange ett nytt lösenord för användarkontot eller kontot har upphört att gälla. Detta krävs på befintliga distributioner som har filnamnstillägget remote desktop aktiverad. För nya distributioner kan du bara använda tillägget direkt.

Du kan använda för att ta bort tillägget för remote desktop från distributionen av [ta bort AzureServiceRemoteDesktopExtension](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet. Du kan också ange distributionsplatsen och roll som du vill ta bort tillägget för remote desktop.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Om du vill ta bort tilläggets konfiguration, bör du anropar den *ta bort* med den **UninstallConfiguration** parameter.
>
> Den **UninstallConfiguration** parametern avinstallerar tillägget konfiguration som tillämpas på tjänsten. Konfiguration för varje är associerad med konfigurationen av tjänsten. Anropar den *ta bort* cmdlet utan **UninstallConfiguration** tas den <mark>distribution</mark> tilläggets konfiguration, vilket effektivt tar bort tillägget. Tilläggets konfiguration förblir dock kopplat till tjänsten.
>
>

## <a name="additional-resources"></a>Ytterligare resurser

[Hur du konfigurerar molntjänster](cloud-services-how-to-configure-portal.md)
[Cloud services vanliga frågor och svar - fjärrskrivbord](cloud-services-faq.md)
