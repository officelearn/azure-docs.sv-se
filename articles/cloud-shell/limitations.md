---
title: Begränsningar för Azure Cloud Shell | Microsoft-dokument
description: Översikt över begränsningar i Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951487"
---
# <a name="limitations-of-azure-cloud-shell"></a>Begränsningar för Azure Cloud Shell

Azure Cloud Shell har följande kända begränsningar:

## <a name="general-limitations"></a>Allmänna begränsningar

### <a name="system-state-and-persistence"></a>Systemtillstånd och uthållighet

Datorn som tillhandahåller din Cloud Shell-session är tillfällig och återvinns efter att sessionen är inaktiv i 20 minuter. Cloud Shell kräver att en Azure-filresurs monteras. Därför måste din prenumeration kunna konfigurera lagringsresurser för att komma åt Cloud Shell. Andra överväganden är:

* Med monterad lagring sparas `$Home` endast ändringar i katalogen.
* Azure-filresurser kan endast monteras inifrån din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * I Bash, `env` kör för att `ACC_LOCATION`hitta din region som .

### <a name="browser-support"></a>Stöd för webbläsare

Cloud Shell stöder de senaste versionerna av Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox och Apple Safari. Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>För en viss användare kan endast ett skal vara aktivt

Användare kan bara starta en typ av skal i taget, antingen **Bash** eller **PowerShell**. Du kan dock ha flera instanser av Bash eller PowerShell körs samtidigt. Om du byter mellan Bash eller PowerShell med hjälp av menyn startas Cloud Shell om, vilket avslutar befintliga sessioner. Alternativt kan du köra bash inuti PowerShell genom att `bash`skriva , och `pwsh`du kan köra PowerShell inuti bash genom att skriva .

### <a name="usage-limits"></a>Användningsgränser

Cloud Shell är avsett för interaktiva användningsfall. Därför avslutas alla långvariga icke-interaktiva sessioner utan förvarning.

## <a name="bash-limitations"></a>Bash begränsningar

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheter anges som vanliga användare utan sudo-åtkomst. Någon installation `$Home` utanför katalogen sparas inte.

### <a name="editing-bashrc-or-profile"></a>Redigera .bashrc eller $PROFILE

Var försiktig när du redigerar .bashrc eller PowerShells $PROFILE-fil, och gör det kan orsaka oväntade fel i Cloud Shell.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="azuread-module-name"></a>`AzureAD`modulnamn

Modulnamnet `AzureAD` är `AzureAD.Standard.Preview`för närvarande , modulen ger samma funktionalitet.

### <a name="sqlserver-module-functionality"></a>`SqlServer`modulfunktionalitet

Modulen `SqlServer` som ingår i Cloud Shell har endast stöd för förhandsversioner för PowerShell Core. I synnerhet `Invoke-SqlCmd` är inte tillgänglig ännu.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standardfilplats när den skapas från Azure-enheten:

Med PowerShell-cmdletar kan användare inte skapa filer under Azure:-enheten. När användare skapar nya filer med andra verktyg, till exempel vim eller nano, sparas filerna till `$HOME` som standard. 

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

Om användaren kör ett kommando som skulle skapa en Windows-dialogruta `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`visas ett felmeddelande som: .

### <a name="large-gap-after-displaying-progress-bar"></a>Stort mellanrum efter att ha visat förloppsindikator

Om användaren utför en åtgärd som visar ett förloppspel, `Azure:` till exempel en flik som slutförs i enheten, är det möjligt att markören inte är korrekt inställd och ett mellanrum visas där förloppsindikatorn tidigare.

## <a name="next-steps"></a>Nästa steg

[Felsöka Cloud Shell](troubleshooting.md) <br>
[Snabbstart för Bash](quickstart.md) <br>
[Snabbstart för PowerShell](quickstart-powershell.md)
