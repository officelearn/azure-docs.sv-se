---
title: Azure Cloud Shell fel sökning | Microsoft Docs
description: Felsöka Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 1d244d7b62fcfefeec6f628f473274ae982bf4d8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394293"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Felsöka & begränsningar i Azure Cloud Shell

Kända lösningar för fel söknings problem i Azure Cloud Shell är:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Allmän fel sökning

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Fel vid körning av AzureAD-cmdletar i PowerShell

- **Information**: när du kör AzureAD-cmdletar som `Get-AzureADUser` i Cloud Shell kan ett fel meddelande visas: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`. 
- **Lösning**: kör `Connect-AzureAD`-cmdleten. Tidigare körde Cloud Shell cmdleten automatiskt under PowerShell-starten. För att påskynda start tiden körs cmdleten inte längre automatiskt. Du kan välja att återställa föregående beteende genom att lägga till `Connect-AzureAD` i $PROFILE-filen i PowerShell.

### <a name="early-timeouts-in-firefox"></a>Tidiga tids gränser i FireFox

- **Information**: Cloud Shell använder en öppen WebSocket för att skicka in-/utdata till din webbläsare. FireFox har förinställda principer som kan stänga WebSocket för att orsaka tidig tids gränser i Cloud Shell.
- **Lösning**: Öppna Firefox och gå till "about: config" i URL-rutan. Sök efter "Network. WebSocket. timeout. ping. Request" och ändra värdet från 0 till 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Inaktivera Cloud Shell i en låst nätverks miljö

- **Information**: administratörer kan vilja inaktivera åtkomst till Cloud Shell för sina användare. Cloud Shell använder åtkomst till `ux.console.azure.com`s domän, som kan nekas, vilket hindrar all åtkomst till Cloud Shell entrypoints, inklusive portal.azure.com, shell.azure.com, Visual Studio Code Azure Account extension och docs.microsoft.com. I det amerikanska regerings molnet är start punkten `ux.console.azure.us`; Det finns ingen motsvarande shell.azure.us.
- **Lösning**: begränsa åtkomsten till `ux.console.azure.com` eller `ux.console.azure.us` via nätverks inställningar till din miljö. Cloud Shell ikonen finns kvar i Azure Portal, men kommer inte att kunna ansluta till tjänsten.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialog rutan lagring – fel: 403 RequestDisallowedByPolicy

- **Information**: när du skapar ett lagrings konto via Cloud Shell, Miss lyckas det på grund av en Azure-princip som har lagts av administratören. Fel meddelandet kommer att innehålla: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Lösning**: kontakta Azure-administratören om du vill ta bort eller uppdatera Azure policy som nekar lagrings skapande.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialog rutan lagring – fel: 400 DisallowedOperation

- **Information**: när du använder en Azure Active Directory-prenumeration kan du inte skapa lagring.
- **Lösning**: Använd en Azure-prenumeration som kan skapa lagrings resurser. Azure AD-prenumerationer kan inte skapa Azure-resurser.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal-utdata-fel: det gick inte att ansluta till terminalen: det går inte att upprätta WebSocket. Tryck på `Enter` för att ansluta igen.
- **Information**: Cloud Shell kräver möjlighet att upprätta en WebSocket-anslutning till Cloud Shell-infrastruktur.
- **Lösning**: kontrol lera att du har konfigurerat dina nätverks inställningar så att du kan skicka https-förfrågningar och WebSocket-begäranden till domäner på *. Console.Azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Ange Cloud Shell anslutning som stöd för TLS 1,2
 - **Information**: om du vill definiera versionen av TLS för anslutningen till Cloud Shell måste du ange webbläsaraktiverade inställningar.
 - **Lösning**: navigera till webbläsarens säkerhets inställningar och markera kryss rutan bredvid "använd TLS 1,2".

## <a name="bash-troubleshooting"></a>Bash-felsökning

### <a name="cannot-run-the-docker-daemon"></a>Det går inte att köra Docker daemon

- **Information**: Cloud Shell använder en behållare som värd för din gränssnitts miljö, eftersom det inte är tillåtet att köra daemonen.
- **Lösning**: Använd [Docker-Machine](https://docs.docker.com/machine/overview/), som installeras som standard, för att hantera Docker-behållare från en fjärran sluten värd.

## <a name="powershell-troubleshooting"></a>PowerShell-felsökning

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

- **Information**: om en användare startar ett GUI-program returnerar inte prompten. Till exempel, när en klon en privat GitHub-lagrings platsen som har två Factor Authentication aktive rad, visas en dialog ruta för att slutföra den två Factor Authentication.
- **Lösning**: Stäng och öppna gränssnittet igen.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Felsöka fjärrhantering av virtuella Azure-datorer
> [!NOTE]
> Virtuella Azure-datorer måste ha en offentlig IP-adress.

- **Information**: på grund av standard inställningarna för Windows-brandväggen för WinRM kan användaren se följande fel: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Lösning**: kör `Enable-AzVMPSRemoting` för att aktivera alla aspekter av PowerShell-fjärrkommunikation på mål datorn.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` uppdaterar inte resultatet i Azure Drive

- **Information**: som standard för att optimera användar upplevelsen cachelagras resultatet av `dir` i Azure Drive.
- **Lösning**: när du har skapat, uppdaterat eller tagit bort en Azure-resurs kör du `dir -force` för att uppdatera resultatet i Azure-enheten.

## <a name="general-limitations"></a>Allmänna begränsningar

Azure Cloud Shell har följande kända begränsningar:

### <a name="quota-limitations"></a>Kvot begränsningar

Azure Cloud Shell har en gräns på 20 samtidiga användare per klient organisation per region. Om du försöker öppna fler samtidiga sessioner än gränsen visas fel meddelandet "klient användare över kvot". Om du har ett legitimt behov av att fler sessioner ska vara öppna än detta (till exempel för utbildning) kan du kontakta supporten i förväg för att begära en kvot ökning.

Cloud Shell tillhandahålls som en kostnads fri tjänst och är avsedd att användas för att konfigurera din Azure-miljö, inte som en allmän plattform för data bearbetning. Onödig automatiserad användning kan anses vara i strid med Azures användar villkor och kan leda till att Cloud Shell åtkomst blockeras.

### <a name="system-state-and-persistence"></a>System tillstånd och persistence

Datorn som tillhandahåller Cloud Shell-sessionen är temporär och återvinns när sessionen är inaktiv i 20 minuter. Cloud Shell kräver att en Azure-filresurs monteras. Därför måste prenumerationen kunna konfigurera lagrings resurser för att få åtkomst till Cloud Shell. Andra överväganden är:

- Med monterad lagring behålls bara ändringar i `clouddrive` katalogen. I bash sparas även din `$HOME`s katalog.
- Azure-filresurser kan bara monteras inifrån din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  - Kör `env` i bash för att hitta din regions uppsättning som `ACC_LOCATION`.
- Azure Files stöder endast lokalt redundant lagring och geo-redundanta lagrings konton.

### <a name="browser-support"></a>Webb läsar stöd

Cloud Shell stöder de senaste versionerna av följande webbläsare:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Användnings gränser

Cloud Shell är avsedd för interaktiva användnings fall. Det innebär att alla tids krävande sessioner som inte är interaktiva avslutas utan varning.

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheter anges som vanliga användare utan sudo-åtkomst. Alla installationer utanför din `$Home`s katalog sparas inte.

## <a name="bash-limitations"></a>Bash-begränsningar

### <a name="editing-bashrc"></a>Redigerar. bashrc

Var försiktig när du redigerar. bashrc. Detta kan orsaka oväntade fel i Cloud Shell.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="preview-version-of-azuread-module"></a>För hands version av AzureAD-modul

För närvarande är en för hands version av .NET standard-baserad, `AzureAD.Standard.Preview`. Den här modulen ger samma funktioner som `AzureAD`.

### <a name="sqlserver-module-functionality"></a>funktioner i `SqlServer` modul

Den `SqlServer`-modul som ingår i Cloud Shell har bara för hands versions stöd för PowerShell Core. I synnerhet är `Invoke-SqlCmd` inte tillgängligt ännu.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standard fil plats när du skapar från Azure Drive

Med hjälp av PowerShell-cmdlets kan användarna inte skapa filer under Azure-enheten. När användare skapar nya filer med andra verktyg, till exempel vim eller nano, sparas filerna som standard i `$HOME`.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Ifyllning av flikar kan utlösa PSReadline-undantag

Om användarens PSReadline-EditMode är inställt på emacs, försöker användaren Visa alla möjligheter via att slutföra flikarna, och fönstret är för litet för att visa alla möjligheter, PSReadline ger upphov till ohanterat undantag.

### <a name="large-gap-after-displaying-progress-bar"></a>Stort mellanrum när förlopps indikatorn visas

Om ett kommando eller en användar åtgärd visar en förlopps indikator, en flik som slutförs när du befinner dig i `Azure:`-enheten, är det möjligt att markören inte är korrekt inställd och att ett mellanrum visas där förlopps indikatorn tidigare användes.

### <a name="random-characters-appear-inline"></a>Slumpmässiga tecken visas infogade

Markör positionens sekvens koder, till exempel `5;13R`, kan visas i användarindata. Tecknen kan tas bort manuellt.

## <a name="personal-data-in-cloud-shell"></a>Person uppgifter i Cloud Shell

Azure Cloud Shell tar dina personliga data på allvar, används de data som samlas in och lagras av Azure Cloud Shells tjänsten för att tillhandahålla standardvärden för din upplevelse, till exempel ditt senast använda gränssnitt, önskad tecken storlek, önskad teckensnitts typ och information om fil resurs den tillbaka moln enheten. Använd följande instruktioner om du vill exportera eller ta bort dessa data.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportera
För att kunna **Exportera** användar inställningarna Cloud Shell sparar till dig, till exempel prioriterat gränssnitt, tecken storlek och teckensnitts typ, kör följande kommandon.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Kör följande kommandon i bash eller PowerShell:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Ta bort
För att **ta bort** dina användar inställningar Cloud Shell sparar för dig som prioriterat gränssnitt, tecken storlek och teckensnitts typ kör följande kommandon. Nästa gång du startar Cloud Shell uppmanas du att publicera en fil resurs igen. 

>[!Note]
> Om du tar bort användar inställningarna tas inte den faktiska Azure Filess resursen bort. Gå till Azure Files för att slutföra åtgärden.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Kör följande kommandon i bash eller PowerShell:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Azure Government begränsningar
Azure Cloud Shell i Azure Government är endast tillgängligt via Azure Portal.
