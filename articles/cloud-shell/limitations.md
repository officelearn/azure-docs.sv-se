---
title: "Azure Cloud Shell-begränsningar | Microsoft Docs"
description: "Översikt över begränsningar i Azure Cloud Shell"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 245e2e1ca52e7d3c5bd22d5f2569e3e8d7ae6671
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="limitations-of-azure-cloud-shell"></a>Begränsningar i Azure-molnet Shell

Azure Cloud Shell har följande kända begränsningar:

## <a name="general-limitations"></a>Allmänna begränsningar

### <a name="system-state-and-persistence"></a>Systemtillstånd och beständiga

Den dator som innehåller molnet Shell sessionen är temporär och den återanvänds när sessionen har varit inaktiv i 20 minuter. Molnet Shell kräver en Azure-filresurs som ska monteras. Därför kan måste din prenumeration kunna ställa in lagringsresurser för att få åtkomst till molnet Shell. Andra överväganden omfattar:

* Med monterade storage kan endast ändringar i den `clouddrive` directory sparas. I Bash, din `$Home` directory sparas också.
* Azure-filresurser kan monteras endast från din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Kör i Bash, `env` att hitta din region som `ACC_LOCATION`.

### <a name="browser-support"></a>Stöd för webbläsare

Molnet Shell stöder de senaste versionerna av Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox och Apple Safari. Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

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

### <a name="no-home-directory-persistence"></a>No $Home directory beständiga

Data som skrivs till `$Home` av alla program (exempel: git vim och andra) inte har behållits över PowerShell-sessioner. En lösning [visas här](troubleshooting.md#powershell-troubleshooting).

### <a name="default-file-location-when-created-from-azure-drive"></a>Standardplatsen för när de skapas från Azure enhet:

Med PowerShell-cmdlets, kan användare inte skapa filer under Azure enhet. När användarna skapar nya filer med andra verktyg, till exempel vim eller nano, sparas filerna i mappen C:\Users som standard. 

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

Om användaren som kör ett kommando som skulle skapa en Windows-dialogruta som `Connect-AzureAD` eller `Login-AzureRMAccount`, något som ser ett felmeddelande visas: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="next-steps"></a>Nästa steg

[Felsöka Cloud Shell](troubleshooting.md) <br>
[Snabbstart för Bash](quickstart.md) <br>
[Snabbstart för PowerShell](quickstart-powershell.md)
