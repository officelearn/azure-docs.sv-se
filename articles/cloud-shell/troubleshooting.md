---
title: Azure Cloud Shell-felsökning | Microsoft Docs
description: Felsöka Azure-molnet Shell
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
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: 3c01a31eae2b90ecb54cbfba7f565fd140db3773
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Felsökning av & begränsningar i Azure Cloud Shell

Kända lösningar för att felsöka problem i Azure Cloud Shell inkluderar:

## <a name="general-troubleshooting"></a>Allmän felsökning

### <a name="early-timeouts-in-firefox"></a>Tidig tidsgränser Firefox
- **Information om**: molnet Shell använder en öppen websocket att skicka in-/ utdata till webbläsaren. FireFox har förinställda principer som kan stänga websocket tidigt orsakar timeout för tidigt i molnet Shell.
- **Lösning**: öppna FireFox och gå till ”om: config” i rutan URL. Sök efter ”network.websocket.timeout.ping.request” och ändra värdet från 0 till 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Lagring dialogruta - fel: 403 RequestDisallowedByPolicy
- **Information om**: när du skapar ett lagringskonto via molnet Shell, är det misslyckas på grund av en Azure-princip placeras av din administratör. Felmeddelande innehåller: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Lösning**: Kontakta Azure-administratören om du vill ta bort eller uppdatera Azure principen neka lagring skapas.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Lagring dialogruta - fel: 400 DisallowedOperation
 - **Information om**: när du använder en prenumeration på Azure Active Directory, du kan inte skapa lagring.
 - **Lösning**: använda en Azure-prenumeration kan skapa lagringsresurser. Azure AD-prenumerationer går inte att skapa Azure-resurser.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal utdata - fel: Det gick inte att ansluta en terminal: websocket kan inte upprättas. Tryck på `Enter` återansluta.
 - **Information om**: molnet Shell kräver möjlighet att upprätta en websocket-anslutning till molnet Shell-infrastruktur.
 - **Lösning**: Kontrollera att du har konfigurerat inställningarna för att aktivera skicka https-förfrågningar och websocket-begäranden till domäner på *. console.azure.com.

## <a name="bash-troubleshooting"></a>Bash felsökning

### <a name="cannot-run-az-login"></a>Det går inte att köra az inloggning

- **Information om**: kör `az login` fungerar inte som du redan har autentiserats under kontot som används för att logga in på molnet Shell eller Azure-portalen.
- **Lösning**: använda ditt konto som används för att logga in eller logga ut och autentiseras med ditt avsedda Azure-konto.

### <a name="cannot-run-the-docker-daemon"></a>Det går inte att köra docker-daemon

- **Information om**: molnet Shell använder en behållare som värd för din miljö för shell, kör därför daemon är inte tillåtet.
- **Lösning**: använda [docker-datorn](https://docs.docker.com/machine/overview/), som installeras som standard för att hantera behållare med docker från en fjärrvärd med Docker.

## <a name="powershell-troubleshooting"></a>Felsökning av PowerShell

### <a name="no-home-directory-persistence"></a>No $Home directory beständiga

- **Information**: data programmet (exempel: git vim och andra) skriver till `$Home` inte beständig i PowerShell-sessioner.
- **Lösning**: skapa en symbolisk länk till specifika program-mappen i i profilen för PowerShell `clouddrive` till $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>CTRL + C Avsluta inte Kommandotolken Cmdlet

- **Information**: när du försöker avsluta Kommandotolken Cmdlet `Ctrl+C` inte avslutas i prompten.
- **Lösning**: Om du vill avsluta Kommandotolken, tryck på `Ctrl+C` sedan `Enter`.

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

- **Information om**: om en användare startar en GUI-app, frågan inte returnerar. När en användare klonar en privat GitHub-repo-som är aktiverad för tvåfaktorsautentisering, till exempel visas en dialogruta för att slutföra två faktorautentisering.  
- **Lösning**: Stäng och öppna gränssnittet.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online går inte att öppna hjälpsidan

- **Information om**: om en användare anger `Get-Help Find-Module -online`, något som ser ett felmeddelande visas: `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Lösning**: Kopiera webbadressen och öppna den manuellt i din webbläsare.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Felsökning av fjärrhantering av virtuella Azure-datorer

- **Information om**: på grund av Windows-brandväggen standardinställningarna för WinRM kan användaren se följande fel: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Lösning**: se till att den virtuella datorn körs. Du kan köra `Get-AzureRmVM -Status` ta reda på VM-statusen.  Lägg sedan till en ny brandväggsregel på fjärranslutna VM att tillåta WinRM anslutningar från alla undernät, t.ex.

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Du kan använda [tillägget för anpassat skript för Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) att undvika att logga in på din fjärranslutna VM för att lägga till nya brandväggsregeln.
 Du kan spara skriptet till en fil, säg `addfirerule.ps1`, och överföra den till Azure storage-behållare.
 Försök sedan följande kommando:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` cachelagrar resultatet i Azure-enhet

- **Information om**: resultatet av `dir` cachelagras i Azure-enheten.
- **Lösning**: när du skapar eller ta bort en resurs i vyn Azure enhet kör `dir -force` att uppdatera.

## <a name="general-limitations"></a>Allmänna begränsningar
Azure Cloud Shell har följande kända begränsningar:

### <a name="system-state-and-persistence"></a>Systemtillstånd och beständiga

Den dator som innehåller molnet Shell sessionen är temporär och den återanvänds när sessionen har varit inaktiv i 20 minuter. Molnet Shell kräver en Azure-filresurs som ska monteras. Därför kan måste din prenumeration kunna ställa in lagringsresurser för att få åtkomst till molnet Shell. Andra överväganden omfattar:

* Med monterade storage kan endast ändringar i den `clouddrive` directory sparas. I Bash, din `$Home` directory sparas också.
* Azure-filresurser kan monteras endast från din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Kör i Bash, `env` att hitta din region som `ACC_LOCATION`.
* Azure Files stöder endast lokalt redundant lagring och konton för geo-redundant lagring.

### <a name="browser-support"></a>Stöd för webbläsare

Molnet Shell stöder de senaste versionerna av Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox och Apple Safari. Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Endast en shell för en viss användare kan vara aktiv

Användare kan endast starta en typ av shell samtidigt, antingen **Bash** eller **PowerShell**. Du kan dock ha flera instanser av Bash eller PowerShell körs samtidigt. Växla mellan Bash eller PowerShell orsaker molnet Shell måste startas om, vilket avslutar befintliga sessioner.

### <a name="usage-limits"></a>Användningsgränser

Moln-gränssnittet är avsedd för interaktiva användningsfall. Därför kan avslutas alla icke-interaktiv tidskrävande sessioner utan varning.

## <a name="bash-limitations"></a>Bash-begränsningar

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheterna anges som en vanlig användare utan åtkomst till sudo. En installation utanför din `$Home` directory sparas inte.

### <a name="editing-bashrc"></a>Redigera .bashrc

Vara försiktig när du redigerar .bashrc, gör det kan orsaka oväntade fel i moln Shell.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="slow-startup-time"></a>Långsam starten

PowerShell Azure Cloud Shell (förhandsversion) kan ta upp till 60 sekunder att initiera under förhandsgranskningen.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standardplatsen för när de skapas från Azure enhet:

Med PowerShell-cmdlets, kan användare inte skapa filer under Azure enhet. När användarna skapar nya filer med andra verktyg, till exempel vim eller nano, sparas filerna i mappen C:\Users som standard. 

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

Om användaren som kör ett kommando som skulle skapa en Windows-dialogruta som `Connect-AzureAD` eller `Connect-AzureRmAccount`, något som ser ett felmeddelande visas: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="gdpr-compliance-for-cloud-shell"></a>BNPR kompatibilitet för molnet Shell

Azure Cloud Shell allvarligt tar personliga data, data skapas och lagras av Azure Cloud Shell-tjänsten används för att ange standardvärden för din upplevelse som de senast använda shell, önskad storlek, önskade teckensnitt, och filresursen information den bakre clouddrive. Bör du vill exportera eller ta bort data, har vi tagit med följande instruktioner.

### <a name="export"></a>Exportera
För att **exportera** användarinställningar molnet Shell som sparar du önskade shell, storlek och teckensnitt som kör följande kommandon.

1. Starta Bash i molnet Shell
2. Kör följande kommandon:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Ta bort
För att **ta bort** dina inställningar molnet Shell som sparar du önskade shell, storlek och teckensnitt som kör följande kommandon. Nästa gång du startar molnet Shell blir du ombedd att publicera en filresurs igen. 

De faktiska Azure filer resurs inte tas bort om du tar bort dina inställningar, gå till Azure-filer för att slutföra åtgärden.

1. Starta Bash i molnet Shell
2. Kör följande kommandon:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
