---
title: Felsökning av Azure Cloud Shell | Microsoft-dokument
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
ms.openlocfilehash: 0b1b22095c77344ed71762d3d51b12f19d9f1811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458060"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Felsökning & begränsningar i Azure Cloud Shell

Kända lösningar för felsökningsproblem i Azure Cloud Shell inkluderar:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Allmän felsökning

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Fel vid körning av AzureAD-cmdlets i PowerShell

- **Information**: När du kör AzureAD-cmdlets som `Get-AzureADUser` i `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`Cloud Shell kan du se ett fel: . 
- **Upplösning**: `Connect-AzureAD` Kör cmdleten. Tidigare körde Cloud Shell den här cmdleten automatiskt under PowerShell-start. För att påskynda starttiden körs inte längre cmdleten automatiskt. Du kan välja att återställa `Connect-AzureAD` det tidigare beteendet genom att lägga till i $PROFILE-filen i PowerShell.

### <a name="early-timeouts-in-firefox"></a>Tidiga timeout i Firefox

- **Detaljer:** Cloud Shell använder en öppen websocket för att skicka indata / utdata till din webbläsare. Firefox har förinställda principer som kan stänga websocket i förtid orsakar tidiga timeout i Cloud Shell.
- **Upplösning:** Öppna Firefox och navigera till "about:config" i url-rutan. Sök efter "network.websocket.timeout.ping.request" och ändra värdet från 0 till 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Inaktivera Cloud Shell i en låst nätverksmiljö

- **Information**: Administratörer kanske vill inaktivera åtkomst till Cloud Shell för sina användare. Cloud Shell använder åtkomst `ux.console.azure.com` till domänen, som kan nekas, vilket stoppar all åtkomst till Cloud Shells entrypoints, inklusive portal.azure.com, shell.azure.com, Visual Studio Code Azure-kontotillägg och docs.microsoft.com. I det amerikanska regeringsmolnet `ux.console.azure.us`är ingångspunkten ; det inte finns någon motsvarande shell.azure.us.
- **Lösning**: Begränsa `ux.console.azure.com` `ux.console.azure.us` åtkomsten till eller via nätverksinställningar till din miljö. Cloud Shell-ikonen finns fortfarande i Azure-portalen, men kommer inte att ansluta till tjänsten.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialogrutan Lagring - Fel: 403 RequestDisallowedByPolicy

- **Information**: När du skapar ett lagringskonto via Cloud Shell misslyckas det på grund av en Azure-princip som placerats av administratören. Felmeddelandet kommer att innehålla:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Lösning**: Kontakta din Azure-administratör för att ta bort eller uppdatera Azure-principen neka lagringsgenerering.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialogrutan Lagring - Fel: 400 Otillåten Operation

- **Information**: När du använder en Azure Active Directory-prenumeration kan du inte skapa lagring.
- **Lösning**: Använd en Azure-prenumeration som kan skapa lagringsresurser. Azure AD-prenumerationer kan inte skapa Azure-resurser.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminalutdata - Fel: Det gick inte att ansluta terminalen: websocket kan inte upprättas. Tryck `Enter` för att återansluta.
- **Detaljer:** Cloud Shell kräver möjligheten att upprätta en websocketanslutning till Cloud Shell-infrastruktur.
- **Lösning**: Kontrollera att du har konfigurerat nätverksinställningarna för att möjliggöra att skicka https-begäranden och websocket-begäranden till domäner på *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Ställa in din Cloud Shell-anslutning så att den får stöd med TLS 1.2
 - **Information**: Om du vill definiera versionen av TLS för din anslutning till Cloud Shell måste du ange webbläsarspecifika inställningar.
 - **Upplösning**: Navigera till webbläsarens säkerhetsinställningar och markera kryssrutan bredvid "Använd TLS 1.2".

## <a name="bash-troubleshooting"></a>Felsökning av bash

### <a name="cannot-run-the-docker-daemon"></a>Det går inte att köra dockerdemonen

- **Detaljer:** Cloud Shell använder en behållare för att vara värd för din skalmiljö, vilket innebär att körningen av demonen inte tillåts.
- **Upplösning**: Använd [docker-maskin](https://docs.docker.com/machine/overview/), som installeras som standard, för att hantera docker-behållare från en fjärradatorvärd.

## <a name="powershell-troubleshooting"></a>PowerShell-felsökning

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

- **Information**: Om en användare startar ett GUI-program returneras inte uppmaningen. Till exempel när en klona en privat GitHub repo som har två faktorautentisering aktiverat, visas en dialogruta för att slutföra tvåfaktorsautentisering.
- **Upplösning**: Stäng och öppna skalet igen.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Felsöka fjärrhantering av virtuella Azure-datorer
> [!NOTE]
> Virtuella Azure-datorer måste ha en offentlig riktad IP-adress.

- **Information**: På grund av standardinställningarna för Windows-brandväggen för WinRM kan användaren se följande fel:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Lösning**: `Enable-AzVMPSRemoting` Kör för att aktivera alla aspekter av PowerShell-ommotning på måldatorn.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir`uppdaterar inte resultatet i Azure-enheten

- **Information**: Som standard, för att optimera `dir` för användarupplevelse, är resultaten av cachelagrade i Azure-enhet.
- **Lösning:** När du har skapat, uppdaterat `dir -force` eller tagit bort en Azure-resurs körs du för att uppdatera resultaten i Azure-enheten.

## <a name="general-limitations"></a>Allmänna begränsningar

Azure Cloud Shell har följande kända begränsningar:

### <a name="quota-limitations"></a>Kvotbegränsningar

Azure Cloud Shell har en gräns på 20 samtidiga användare per klient per region. Om du försöker öppna fler samtidiga sessioner än gränsen visas felet "Klientanvändare över kvot". Om du har ett legitimt behov av att ha fler sessioner öppna än så här (till exempel för träningspass), kontakta supporten före din förväntade användning för att begära en kvotökning.

Cloud Shell tillhandahålls som en kostnadsfri tjänst och är utformad för att användas för att konfigurera din Azure-miljö, inte som en allmän datorplattform. Överdriven automatiserad användning kan anses bryta mot Azures användarvillkor och kan leda till att Cloud Shell-åtkomst blockeras.

### <a name="system-state-and-persistence"></a>Systemtillstånd och uthållighet

Datorn som tillhandahåller din Cloud Shell-session är tillfällig och återvinns efter att sessionen är inaktiv i 20 minuter. Cloud Shell kräver att en Azure-filresurs monteras. Därför måste din prenumeration kunna konfigurera lagringsresurser för att komma åt Cloud Shell. Andra överväganden är:

- Med monterad lagring sparas `clouddrive` endast ändringar i katalogen. I Bash `$HOME` är katalogen också kvar.
- Azure-filresurser kan endast monteras inifrån din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  - I Bash, `env` kör för att `ACC_LOCATION`hitta din region som .
- Azure Files stöder endast lokalt redundanta lagrings- och geoupptundära lagringskonton.

### <a name="browser-support"></a>Stöd för webbläsare

Cloud Shell stöder de senaste versionerna av följande webbläsare:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Användningsgränser

Cloud Shell är avsett för interaktiva användningsfall. Därför avslutas alla långvariga icke-interaktiva sessioner utan förvarning.

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheter anges som vanliga användare utan sudo-åtkomst. Någon installation `$Home` utanför katalogen sparas inte.

## <a name="bash-limitations"></a>Bash begränsningar

### <a name="editing-bashrc"></a>Redigera .bashrc

Var försiktig när du redigerar .bashrc, om du gör det kan det orsaka oväntade fel i Cloud Shell.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="preview-version-of-azuread-module"></a>Förhandsversion av AzureAD-modulen

`AzureAD.Standard.Preview`För närvarande finns en förhandsversion av .NET Standard-baserad modul tillgänglig. Den här modulen innehåller `AzureAD`samma funktioner som .

### <a name="sqlserver-module-functionality"></a>`SqlServer`modulfunktionalitet

Modulen `SqlServer` som ingår i Cloud Shell har endast stöd för förhandsversioner för PowerShell Core. I synnerhet `Invoke-SqlCmd` är inte tillgänglig ännu.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standardfilplats när den skapas från Azure-enhet

Med PowerShell-cmdletar kan användare inte skapa filer under Azure-enheten. När användare skapar nya filer med andra verktyg, till exempel vim eller nano, sparas filerna till `$HOME` som standard.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Flikkomplettering kan kasta PSReadline-undantag

Om användarens PSReadline EditMode är inställt på Emacs, försöker användaren visa alla möjligheter via flikkomplettering och fönsterstorleken är för liten för att visa alla möjligheter, kommer PSReadline att skapa ohanterat undantag.

### <a name="large-gap-after-displaying-progress-bar"></a>Stort mellanrum efter att ha visat förloppsindikator

Om ett kommando eller en användaråtgärd visar ett förloppsfält, en sådan flik som slutförs i `Azure:` enheten, är det möjligt att markören inte är korrekt inställd och ett mellanrum visas där förloppsindikatorn tidigare.

### <a name="random-characters-appear-inline"></a>Slumpmässiga tecken visas infogade

Markörpositionssekvenskoderna `5;13R`kan till exempel visas i användarindata. Tecknen kan tas bort manuellt.

## <a name="personal-data-in-cloud-shell"></a>Personuppgifter i Cloud Shell

Azure Cloud Shell tar dina personuppgifter på allvar, de data som samlas in och lagras av Azure Cloud Shell-tjänsten används för att tillhandahålla standardvärden för din upplevelse, till exempel ditt senast använda skal, önskad teckenstorlek, önskad teckensnittstyp och information om fildelning som tillbaka molnenhet. Om du vill exportera eller ta bort dessa data använder du följande instruktioner.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportera
För att **exportera** användarinställningarna cloud shell sparar för dig som önskat skal, teckenstorlek och teckensnittstyp köra följande kommandon.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Kör följande kommandon i Bash eller PowerShell:

Bash:

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
För att **ta bort** dina användarinställningar Cloud Shell sparar för dig som önskat skal, teckenstorlek och teckensnittstyp köra följande kommandon. Nästa gång du startar Cloud Shell blir du ombedd att gå in på en filresurs igen. 

>[!Note]
> Om du tar bort dina användarinställningar tas inte den faktiska Azure-filresursen bort. Gå till dina Azure-filer för att slutföra den åtgärden.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Kör följande kommandon i Bash eller PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Begränsningar för Azure Government
Azure Cloud Shell i Azure Government är endast tillgängligt via Azure-portalen.

>[!Note]
> Det går inte att ansluta till GCC-High eller Government DoD Clouds for Exchange Online.
