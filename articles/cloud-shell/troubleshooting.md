---
title: Azure Cloud Shell-felsökning | Microsoft Docs
description: Felsöka Azure Cloudshell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 21bc0633a9cc607325b48998791cb12631ecd0d7
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856495"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Felsökning av & begränsningar i Azure Cloud Shell

Kända lösningar för att felsöka problem i Azure Cloud Shell omfattar:

## <a name="general-troubleshooting"></a>Allmän felsökning

### <a name="early-timeouts-in-firefox"></a>Tidig tidsgränser i FireFox
- **Information om**: Cloud Shell använder en öppen websocket att skicka indata/utdata till din webbläsare. FireFox har förinställda principer som kan stänga websocket tidigt orsakar tidig tidsgränser i Cloud Shell.
- **Lösning**: öppna FireFox och navigera till ”om: config” i dialogrutan. Sök efter ”network.websocket.timeout.ping.request” och ändra värdet från 0 till 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Storage-dialogruta - fel: 403 RequestDisallowedByPolicy
- **Information om**: när du skapar ett lagringskonto via Cloud Shell, är det misslyckades på grund av en Azure policy placeras av din administratör. Felmeddelandet innehåller: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Lösning**: Kontakta Azure-administratören om du vill ta bort eller uppdatera Azure policy nekar skapa lager.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Storage-dialogruta - fel: 400 DisallowedOperation
 - **Information om**: när du använder Azure Active Directory-prenumeration kan du kan inte skapa lagring.
 - **Lösning**: använda en Azure-prenumeration kan skapa lagringsresurser. Azure AD-prenumerationer kan inte skapa Azure-resurser.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal utdata - fel: Det gick inte att ansluta terminal: går inte att upprätta websocket. Tryck på `Enter` återansluta.
 - **Information om**: Cloud Shell kräver möjligheten att upprätta en websocket-anslutning till Cloud Shell-infrastruktur.
 - **Lösning**: Kontrollera att du har konfigurerat dina nätverksinställningar för att aktivera skicka https-begäranden och websocket-förfrågningar till domäner på *. console.azure.com.

## <a name="bash-troubleshooting"></a>Bash-felsökning

### <a name="cannot-run-the-docker-daemon"></a>Det går inte att köra docker-daemon

- **Information om**: Cloud Shell använder en behållare för att vara värd för miljön shell, som ett resultat som kör daemon är inte tillåtet.
- **Lösning**: använda [docker-dator](https://docs.docker.com/machine/overview/), som installeras som standard för att hantera docker-behållare från en fjärransluten Docker-värd.

## <a name="powershell-troubleshooting"></a>Felsökning av PowerShell

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

- **Information om**: om en användare startar en GUI-app, returnerar inte meddelandet. När en användare klonar en privat GitHub-lagringsplats som har tvåfaktorautentisering aktiverat, exempelvis visas en dialogruta för att slutföra autentisering med två faktorer.  
- **Lösning**: Stäng och öppna gränssnittet.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online inte öppnas hjälpsidan

- **Information om**: om en användare anger `Get-Help Find-Module -online`, något som ser ett felmeddelande visas: `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Lösning**: Kopiera webbadressen och öppna den manuellt i din webbläsare.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Felsökning av fjärrhantering av virtuella Azure-datorer

- **Information om**: på grund av Windows-brandväggen standardinställningarna för WinRM att användaren kan se följande fel: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Lösning**: kör `Enable-AzureRmVMPSRemoting` att aktivera alla aspekter av PowerShell-fjärrkommunikation på måldatorn.
 

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` cachelagrar resultatet i Azure-enheten

- **Information om**: resultatet av `dir` cachelagras i Azure-enheten.
- **Lösning**: när du skapar eller ta bort en resurs i vyn Azure-enheten kör `dir -force` att uppdatera.

## <a name="general-limitations"></a>Allmänna begränsningar
Azure Cloud Shell har följande kända begränsningar:

### <a name="system-state-and-persistence"></a>Systemtillstånd och persistence

Den dator som ger Cloud Shell-sessionen är tillfälligt och den återanvänds när sessionen är inaktiv i 20 minuter. Cloudshell kräver en Azure-filresurs som ska monteras. Prenumerationen måste därför att kunna konfigurera lagringsresurser för att komma åt Cloud Shell. Annat att tänka på är:

* Med monterade lagringen, endast ändringar i den `clouddrive` directory sparas. I Bash, din `$Home` katalogen bevaras också.
* Azure-filresurser kan monteras endast från din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Kör i Bash, `env` att hitta din region som `ACC_LOCATION`.
* Azure Files stöder endast lokalt redundant lagring och ra-GRS-konton.

### <a name="browser-support"></a>Webbläsarstöd

Cloudshell har stöd för de senaste versionerna av Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox och Apple Safari. Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>För en viss användare kan endast en shell vara aktiv

Användarna kan bara starta en typ av gränssnittet i taget, antingen **Bash** eller **PowerShell**. Du kan dock ha flera instanser av Bash eller PowerShell som körs på en gång. Växlar mellan Bash eller PowerShell orsaker Cloud Shell för att starta om, vilket avslutar befintliga sessioner.

### <a name="usage-limits"></a>Användningsgränser

Cloudshell är avsedd för interaktiva användningsfall. Därför kan avslutas alla tidskrävande icke-interaktiva sessioner utan varning.

## <a name="bash-limitations"></a>Bash-begränsningar

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheter har angetts som vanliga användare utan sudo-åtkomst. Valfri installation utanför din `$Home` directory behålls inte.

### <a name="editing-bashrc"></a>Redigera .bashrc

Vara försiktig när du redigerar .bashrc, detta kan orsaka oväntade fel i Cloud Shell.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="azuread-module-name"></a>`AzureAD` Modulnamn

Den `AzureAD` Modulnamn är för närvarande `AzureAD.Standard.Preview`, modulen fungerar på samma sätt.

### <a name="sqlserver-module-functionality"></a>`SqlServer` modulfunktioner

Den `SqlServer` modulen som ingår i Cloud Shell har endast förhandsversioner stöd för PowerShell Core. I synnerhet `Invoke-SqlCmd` är inte tillgänglig än.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standardplatsen för när de skapas från Azure-enheten:

Med hjälp av PowerShell-cmdletar, kan användare inte skapa filer under Azure-enheten. När användarna skapar nya filer med andra verktyg, till exempel vim eller nano filerna sparas i den `$HOME` som standard. 

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

Om du kör ett kommando som skapar en Windows-dialogruta som `Connect-AzureAD` eller `Connect-AzureRmAccount`, något som ser ett felmeddelande visas: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-crashes-psreadline"></a>Tabbifyllning kraschar PSReadline

Om användarens EditMode i PSReadline har angetts till Emacs användaren försöker att visa alla möjligheter via tabbifyllning, och fönsterstorleken är för liten för att visa alla möjligheter, PSReadline kraschar.

### <a name="large-gap-after-displaying-progress-bar"></a>Stort mellanrum efter visar förloppsindikator

Om användaren utför en åtgärd som visar en förloppsindikator, en sådan flik Slutför när i den `Azure:` enhet, så är det möjligt att markören inte är korrekt en lucka visas där förloppsindikatorn var tidigare.

### <a name="random-characters-appear-inline"></a>Slumpmässiga tecken visas infogade

Markörens position sekvensen kodar `5;13R`, kan visas i användardata.  Tecknen kan tas bort manuellt.

## <a name="personal-data-in-cloud-shell"></a>Personliga data i Cloud Shell

Personliga data tar allvarligt i Azure Cloud Shell, data inhämtas och lagras av Azure Cloud Shell-tjänsten används för att ange standardvärden för din upplevelse som de senast använda shell, önskad storlek, önskade teckensnitt och filen dela information som molnbaserad tillbaka enheten. Om du vill exportera eller ta bort data, har vi inkluderat följande instruktioner.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportera
För att **exportera** användarinställningarna Cloud Shell gör du till exempel önskad shell, storlek och teckensnitt som kör följande kommandon.

1. Starta Bash i Cloudshell
2. Kör följande kommandon:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Ta bort
För att **ta bort** användarinställningarna Cloud Shell gör du till exempel önskad shell, storlek och teckensnitt som kör följande kommandon. Nästa gång du startar Cloud Shell blir du ombedd att publicera en filresurs igen. 

Den faktiska Azure Files resurs inte tas bort om du tar bort dina inställningar, gå till Azure Files för att slutföra åtgärden.

1. Starta Bash i Cloudshell
2. Kör följande kommandon:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
