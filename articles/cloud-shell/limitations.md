---
title: Begränsningar för Azure Cloud Shell | Microsoft Docs
description: Översikt över begränsningar för Azure Cloud Shell
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
ms.openlocfilehash: 8fd88221818d28c227c33719c03e522e815a408b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097073"
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

Användarna kan bara starta en typ av gränssnittet i taget, antingen **Bash** eller **PowerShell**. Du kan dock ha flera instanser av Bash eller PowerShell som körs på en gång. Växlar mellan Bash eller PowerShell med hjälp av menyn gör Cloud Shell för att starta om, vilket avslutar befintliga sessioner. Du kan också köra bash i PowerShell genom att skriva `bash`, och du kan köra PowerShell i bash genom att skriva `pwsh`.

### <a name="usage-limits"></a>Användningsgränser

Cloudshell är avsedd för interaktiva användningsfall. Därför kan avslutas alla tidskrävande icke-interaktiva sessioner utan varning.

## <a name="bash-limitations"></a>Bash-begränsningar

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheter har angetts som vanliga användare utan sudo-åtkomst. Valfri installation utanför din `$Home` directory behålls inte.

### <a name="editing-bashrc-or-profile"></a>Redigera .bashrc eller $PROFILE

Vara försiktig när du redigerar .bashrc eller PowerShell-$PROFILE-fil som gör det kan orsaka oväntade fel i Cloud Shell.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="azuread-module-name"></a>`AzureAD` Modulnamn

Den `AzureAD` Modulnamn är för närvarande `AzureAD.Standard.Preview`, modulen fungerar på samma sätt.

### <a name="sqlserver-module-functionality"></a>`SqlServer` modulfunktioner

Den `SqlServer` modulen som ingår i Cloud Shell har endast förhandsversioner stöd för PowerShell Core. I synnerhet `Invoke-SqlCmd` är inte tillgänglig än.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standardplatsen för när de skapas från Azure-enheten:

Med hjälp av PowerShell-cmdletar, användare kan inte skapa filer under Azure: enhet. När användarna skapar nya filer med andra verktyg, till exempel vim eller nano filerna sparas i den `$HOME` som standard. 

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

Om du kör ett kommando som skapar en Windows-dialogruta, något som ser ett felmeddelande visas: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Stort mellanrum efter visar förloppsindikator

Om användaren utför en åtgärd som visar en förloppsindikator, en sådan flik Slutför när i den `Azure:` enhet, så är det möjligt att markören inte är korrekt en lucka visas där förloppsindikatorn var tidigare.

## <a name="next-steps"></a>Nästa steg

[Felsöka Cloudshell](troubleshooting.md) <br>
[Snabbstart för Bash](quickstart.md) <br>
[Snabbstart för PowerShell](quickstart-powershell.md)
