---
title: Begränsningar för Azure Cloud Shell | Microsoft Docs
description: Översikt över begränsningar för Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 1f2c218ed9ba2f5f9285c60b8d4c11704825c0f5
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563889"
---
# <a name="limitations-of-azure-cloud-shell"></a>Begränsningar för Azure Cloudshell

Azure Cloud Shell har följande kända begränsningar:

## <a name="general-limitations"></a>Allmänna begränsningar

### <a name="system-state-and-persistence"></a>Systemtillstånd och persistence

Den dator som ger Cloud Shell-sessionen är tillfälligt och den återanvänds när sessionen är inaktiv i 20 minuter. Cloudshell kräver en Azure-filresurs som ska monteras. Prenumerationen måste därför att kunna konfigurera lagringsresurser för att komma åt Cloud Shell. Annat att tänka på är:

* Med monterade lagringen, endast ändringar i den `$Home` directory sparas.
* Azure-filresurser kan monteras endast från din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Kör i Bash, `env` att hitta din region som `ACC_LOCATION`.

### <a name="browser-support"></a>Webbläsarstöd

Cloudshell har stöd för de senaste versionerna av Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox och Apple Safari. Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

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

Om du kör ett kommando som skapar en Windows-dialogruta som `Connect-AzureAD`, `Connect-AzureRmAccount`, eller `Connect-AzAccount` något som ser ett felmeddelande visas: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-crashes-psreadline"></a>Tabbifyllning kraschar PSReadline

Om användarens EditMode i PSReadline har angetts till Emacs användaren försöker att visa alla möjligheter via tabbifyllning, och fönsterstorleken är för liten för att visa alla möjligheter, PSReadline kraschar.

### <a name="large-gap-after-displaying-progress-bar"></a>Stort mellanrum efter visar förloppsindikator

Om användaren utför en åtgärd som visar en förloppsindikator, en sådan flik Slutför när i den `Azure:` enhet, så är det möjligt att markören inte är korrekt en lucka visas där förloppsindikatorn var tidigare.

### <a name="random-characters-appear-inline"></a>Slumpmässiga tecken visas infogade

Markörens position sekvensen kodar `5;13R`, kan visas i användardata.  Tecknen kan tas bort manuellt.

## <a name="next-steps"></a>Nästa steg

[Felsöka Cloudshell](troubleshooting.md) <br>
[Snabbstart för Bash](quickstart.md) <br>
[Snabbstart för PowerShell](quickstart-powershell.md)
