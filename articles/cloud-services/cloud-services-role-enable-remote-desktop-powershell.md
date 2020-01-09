---
title: Använd PowerShell för att aktivera fjärr skrivbord för en roll
titleSuffix: Azure Cloud Services
description: Så här konfigurerar du Azure Cloud Service-programmet med PowerShell för att tillåta fjärr skrivbords anslutningar
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75386127"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Aktivera Anslutning till fjärrskrivbord för en roll i Azure Cloud Services med PowerShell

> [!div class="op_single_selector"]
> * [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Med fjärr skrivbord kan du komma åt Skriv bordet för en roll som körs i Azure. Du kan använda en anslutning till fjärr skrivbord för att felsöka och diagnostisera problem med programmet medan det körs.

I den här artikeln beskrivs hur du aktiverar fjärr skrivbord i moln tjänst roller med hjälp av PowerShell. Se [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för de krav som krävs för den här artikeln. PowerShell använder fjärr skrivbords tillägget så att du kan aktivera fjärr skrivbord när programmet har distribuerats.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurera fjärr skrivbord från PowerShell
Med cmdleten [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) kan du aktivera fjärr skrivbord på angivna roller eller alla roller i moln tjänst distributionen. Med cmdleten kan du ange användar namn och lösen ord för fjärr skrivbords användaren via den *Credential* -parameter som accepterar ett PSCredential-objekt.

Om du använder PowerShell interaktivt kan du enkelt ange PSCredential-objektet genom att anropa cmdleten [Get-credentials](https://technet.microsoft.com/library/hh849815.aspx) .

```powershell
$remoteusercredentials = Get-Credential
```

Det här kommandot visar en dialog ruta där du kan ange användar namn och lösen ord för fjärran vändaren på ett säkert sätt.

Eftersom PowerShell bidrar till automatiserings scenarier kan du också konfigurera **PSCredential** -objektet på ett sätt som inte kräver användar interaktion. Först måste du konfigurera ett säkert lösen ord. Du börjar med att ange ett lösen ord för oformaterad text för att konvertera det till en säker sträng med hjälp av [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Därefter måste du konvertera den här säkra strängen till en krypterad standard sträng med [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nu kan du spara den här krypterade standard strängen i en fil med hjälp av [set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Du kan också skapa en säker lösen ords fil så att du inte behöver skriva in lösen ordet varje gång. Dessutom är en säker lösen ords fil bättre än en fil med oformaterad text. Använd följande PowerShell för att skapa en säker lösen ords fil:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> När du anger lösen ordet kontrollerar du att du uppfyller [komplexitets kraven](https://technet.microsoft.com/library/cc786468.aspx).

Om du vill skapa Credential-objektet från den säkra lösen ords filen måste du läsa filens innehåll och konvertera tillbaka dem till en säker sträng med [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

Cmdlet: en [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) accepterar också en *förfallo* parameter som anger ett **datum/tid** -värde då användar kontot upphör att gälla. Du kan till exempel ange att kontot ska förfalla några dagar från aktuellt datum och aktuell tid.

Det här PowerShell-exemplet visar hur du ställer in fjärr skrivbords tillägget på en moln tjänst:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Du kan också ange den distributions plats och de roller som du vill aktivera fjärr skrivbord på. Om dessa parametrar inte anges aktiverar cmdleten fjärr skrivbord på alla roller på **produktions** distributions platsen.

Fjärr skrivbords tillägget är associerat med en distribution. Om du skapar en ny distribution för tjänsten måste du aktivera fjärr skrivbord för distributionen. Om du alltid vill att fjärr skrivbord ska vara aktiverat, bör du överväga att integrera PowerShell-skripten i distributions arbets flödet.

## <a name="remote-desktop-into-a-role-instance"></a>Fjärr skrivbord till en roll instans

Cmdlet: en [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) används för fjärr skrivbord i en speciell roll instans av moln tjänsten. Du kan använda parametern *localPath* för att ladda ned RDP-filen lokalt. Du kan också använda *Start* parametern för att direkt starta dialog rutan anslutning till fjärrskrivbord för att få åtkomst till moln tjänst Rolls instansen.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Kontrol lera om fjärr skrivbords tillägget är aktiverat på en tjänst

Cmdleten [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) visar att fjärr skrivbord är aktiverat eller inaktiverat i en tjänst distribution. Cmdleten returnerar användar namnet för fjärr skrivbords användaren och de roller som fjärr skrivbords tillägget är aktiverat för. Detta sker som standard på distributions platsen och du kan välja att använda mellanlagringsplatsen i stället.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Ta bort fjärr skrivbords tillägget från en tjänst

Om du redan har aktiverat tillägget för fjärr skrivbord på en distribution och behöver uppdatera inställningarna för fjärr skrivbord tar du först bort tillägget. Och aktiverar det igen med de nya inställningarna. Om du till exempel vill ange ett nytt lösen ord för fjärranvändarkontot eller om kontot har upphört att gälla. Detta krävs för befintliga distributioner där fjärr skrivbords tillägget är aktiverat. För nya distributioner kan du helt enkelt tillämpa tillägget direkt.

Om du vill ta bort fjärr skrivbords tillägget från distributionen kan du använda cmdleten [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) . Du kan också ange den distributions plats och den roll som du vill ta bort fjärr skrivbords tillägget från.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Om du vill ta bort tilläggs konfigurationen fullständigt bör du anropa *Remove* -cmdleten med parametern **UninstallConfiguration** .
>
> **UninstallConfiguration** -parametern avinstallerar all tilläggs konfiguration som tillämpas på tjänsten. Varje tilläggs konfiguration är associerad med tjänst konfigurationen. Om du anropar *Remove* -cmdleten utan **UninstallConfiguration** avassocieras <mark>distributionen</mark> från tilläggs konfigurationen, och därför tas tillägget bort effektivt. Tilläggs konfigurationen förblir dock associerad med tjänsten.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)


