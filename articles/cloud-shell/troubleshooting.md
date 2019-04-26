---
title: Azure Cloud Shell-felsökning | Microsoft Docs
description: Felsöka Azure Cloudshell
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
ms.openlocfilehash: eb7deacc068661ca9a4f473ee2d36b7d4464c81c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199473"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Felsökning av & begränsningar i Azure Cloud Shell

Kända lösningar för att felsöka problem i Azure Cloud Shell omfattar:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Allmän felsökning

### <a name="early-timeouts-in-firefox"></a>Tidig tidsgränser i FireFox

- **Information om**: Cloudshell använder en öppen websocket att skicka indata/utdata till din webbläsare. FireFox har förinställda principer som kan stänga websocket tidigt orsakar tidig tidsgränser i Cloud Shell.
- **Upplösning**: Öppna FireFox och navigera till ”om: config” i dialogrutan. Sök efter ”network.websocket.timeout.ping.request” och ändra värdet från 0 till 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Inaktivera Cloud Shell i ett låst nätverk miljö

- **Information om**: Administratörer kan inaktivera åtkomst till Cloud Shell för sina användare. Cloudshell använder åtkomst till den `ux.console.azure.com` domäner som kan nekas, stoppas alla åtkomst till Cloud Shell entrypoints inklusive portal.azure.com, shell.azure.com, Visual Studio Code Azure-kontotillägg och docs.microsoft.com.
- **Upplösning**: Begränsa åtkomsten till `ux.console.azure.com` via nätverksinställningar för din miljö. Cloud Shell-ikonen finns kvar i portal.azure.com, men ansluta inte till tjänsten.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Storage-dialogruta - fel: 403 RequestDisallowedByPolicy

- **Information om**: När du skapar ett lagringskonto via Cloud Shell, är det misslyckades på grund av en Azure policy placeras av din administratör. Felmeddelandet innehåller: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Upplösning**: Kontakta Azure-administratören om du vill ta bort eller uppdatera Azure policy nekar skapa lager.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Storage-dialogruta - fel: 400 DisallowedOperation

- **Information om**: När du använder Azure Active Directory-prenumeration kan skapa du inte lagring.
- **Upplösning**: Använda en Azure-prenumeration kan skapa lagringsresurser. Azure AD-prenumerationer kan inte skapa Azure-resurser.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal utdata - fel: Det gick inte att ansluta terminal: Det går inte att upprätta websocket. Tryck på `Enter` återansluta.
- **Information om**: Cloudshell kräver möjligheten att upprätta en websocket-anslutning till Cloud Shell-infrastruktur.
- **Upplösning**: Kontrollera att du har konfigurerat dina nätverksinställningar för att aktivera skicka https-begäranden och websocket-förfrågningar till domäner på *. console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Ange din Cloud Shell-anslutning till stöd för att använda TLS 1.2
 - **Information om**: För att definiera TLS-version för din anslutning till Cloud Shell, måste du ange specifika inställningar för webbläsaren.
 - **Upplösning**: Gå till säkerhetsinställningarna i webbläsaren och markera kryssrutan bredvid ”Använd TLS 1.2”.

## <a name="bash-troubleshooting"></a>Bash-felsökning

### <a name="cannot-run-the-docker-daemon"></a>Det går inte att köra docker-daemon

- **Information om**: Cloudshell använder en behållare för att vara värd för miljön shell, som ett resultat som kör daemon är inte tillåtet.
- **Upplösning**: Använda [docker-dator](https://docs.docker.com/machine/overview/), som installeras som standard för att hantera docker-behållare från en fjärransluten Docker-värd.

## <a name="powershell-troubleshooting"></a>Felsökning av PowerShell

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

- **Information om**: Om en användare startar ett GUI-program, returnerar prompten inte. När en klona en privat GitHub-lagringsplats som har tvåfaktorautentisering aktiverat, exempelvis visas en dialogruta för att slutföra tvåfaktorautentisering.
- **Upplösning**: Stäng och öppna gränssnittet.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Felsökning av fjärrhantering av virtuella Azure-datorer
> [!NOTE]
> Virtuella Azure-datorer måste ha en offentlig IP-adress som riktas mot.

- **Information om**: På grund av Windows-brandväggen standardinställningarna för WinRM kan användaren se följande fel: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Upplösning**:  Kör `Enable-AzVMPSRemoting` att aktivera alla aspekter av PowerShell-fjärrkommunikation på måldatorn.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` uppdaterar inte resultatet i Azure-enheten

- **Information om**: Som standard att optimera användarupplevelsen, resultatet av `dir` cachelagras i Azure-enheten.
- **Upplösning**: När du skapar, uppdatera eller ta bort en Azure-resurs, kör du `dir -force` att uppdatera resultatet i Azure-enheten.

## <a name="general-limitations"></a>Allmänna begränsningar

Azure Cloud Shell har följande kända begränsningar:

### <a name="system-state-and-persistence"></a>Systemtillstånd och persistence

Den dator som ger Cloud Shell-sessionen är tillfälligt och den återanvänds när sessionen är inaktiv i 20 minuter. Cloudshell kräver en Azure-filresurs som ska monteras. Prenumerationen måste därför att kunna konfigurera lagringsresurser för att komma åt Cloud Shell. Annat att tänka på är:

- Med monterade lagringen, endast ändringar i den `clouddrive` directory sparas. I Bash, din `$HOME` katalogen bevaras också.
- Azure-filresurser kan monteras endast från din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  - Kör i Bash, `env` att hitta din region som `ACC_LOCATION`.
- Azure Files stöder endast lokalt redundant lagring och ra-GRS-konton.

### <a name="browser-support"></a>Webbläsarstöd

Cloudshell har stöd för de senaste versionerna av följande webbläsare:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Användningsgränser

Cloudshell är avsedd för interaktiva användningsfall. Därför kan avslutas alla tidskrävande icke-interaktiva sessioner utan varning.

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheter har angetts som vanliga användare utan sudo-åtkomst. Valfri installation utanför din `$Home` directory behålls inte.

## <a name="bash-limitations"></a>Bash-begränsningar

### <a name="editing-bashrc"></a>Redigera .bashrc

Vara försiktig när du redigerar .bashrc, detta kan orsaka oväntade fel i Cloud Shell.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="preview-version-of-azuread-module"></a>Preview-versionen av AzureAD-modulen

För närvarande `AzureAD.Standard.Preview`, en förhandsversion av .NET Standard-baserade, modulen är tillgänglig. Den här modulen innehåller samma funktioner som `AzureAD`.

### <a name="sqlserver-module-functionality"></a>`SqlServer` modulfunktioner

Den `SqlServer` modulen som ingår i Cloud Shell har endast förhandsversioner stöd för PowerShell Core. I synnerhet `Invoke-SqlCmd` är inte tillgänglig än.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standardplatsen för när de skapas från Azure-enheten

Med hjälp av PowerShell-cmdletar, kan inte användare skapa filer under Azure-enheten. När användarna skapar nya filer med andra verktyg, till exempel vim eller nano filerna sparas i den `$HOME` som standard.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Tabbifyllning kan utlösa PSReadline undantag

Om användarens PSReadline EditMode har angetts till Emacs användaren försöker att visa alla möjligheter via tabbifyllning, och fönsterstorleken är för liten för att visa alla möjligheter, PSReadline genereras ett ohanterat undantag.

### <a name="large-gap-after-displaying-progress-bar"></a>Stort mellanrum efter visar förloppsindikator

Om ett kommando eller en användare visar en förloppsindikator, en sådan flik Slutför när i den `Azure:` enhet, så är det möjligt att markören inte är korrekt en lucka visas där förloppsindikatorn var tidigare.

### <a name="random-characters-appear-inline"></a>Slumpmässiga tecken visas infogade

Markörens position sekvensen kodar `5;13R`, kan visas i användardata. Tecknen kan tas bort manuellt.

## <a name="personal-data-in-cloud-shell"></a>Personliga data i Cloud Shell

Personliga data tar allvarligt i Azure Cloud Shell, data inhämtas och lagras av Azure Cloud Shell-tjänsten används för att ange standardvärden för din upplevelse som de senast använda shell, önskad storlek, önskade teckensnitt och filen dela information som molnbaserad tillbaka enheten. Om du vill exportera eller ta bort data, Använd följande anvisningar.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportera
För att **exportera** användarinställningarna Cloud Shell gör du till exempel önskad shell, storlek och teckensnitt som kör följande kommandon.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloudshell")](https://shell.azure.com)
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
För att **ta bort** användarinställningarna Cloud Shell gör du till exempel önskad shell, storlek och teckensnitt som kör följande kommandon. Nästa gång du startar Cloud Shell blir du ombedd att publicera en filresurs igen. 

>[!Note]
> Om du tar bort dina användarinställningar tas faktiska Azure Files-resurs inte bort. Gå till Azure Files att slutföra åtgärden.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloudshell")](https://shell.azure.com)
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
