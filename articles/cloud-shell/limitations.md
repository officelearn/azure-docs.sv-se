---
title: "Begränsningar för Azure Cloud Shell (förhandsversion) | Microsoft Docs"
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
ms.date: 11/06/2017
ms.author: juluk
ms.openlocfilehash: 66f07481d6e62b4375dd2752c5e7d6f8e02d4a3c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Begränsningar i Azure-molnet Shell

Azure Cloud Shell har följande kända begränsningar:

## <a name="general-limitations"></a>Allmänna begränsningar

### <a name="system-state-and-persistence"></a>Systemtillstånd och beständiga

Den dator som innehåller molnet Shell sessionen är temporär och den återanvänds när sessionen har varit inaktiv i 20 minuter. Molnet Shell kräver en filresurs som ska monteras. Därför kan måste din prenumeration kunna ställa in lagringsresurser för att få åtkomst till molnet Shell. Andra överväganden omfattar:

* Med monterade storage kan endast ändringar i den `clouddrive` directory sparas. I Bash, din `$Home` directory sparas också.
* Filresurser kan monteras endast från din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Kör i Bash, `env` att hitta din region som `ACC_LOCATION`.
* Azure Files stöder endast lokalt redundant lagring och konton för geo-redundant lagring.

### <a name="browser-support"></a>Stöd för webbläsare

Molnet Shell stöder de senaste versionerna av Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox och Apple Safari. Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Endast en shell för en viss användare kan vara aktiv

Användare kan endast starta en typ av shell samtidigt, antingen **Bash** eller **PowerShell**. Du kan dock ha flera instanser av Bash eller PowerShell körs samtidigt. Växla mellan Bash eller PowerShell orsaker molnet Shell måste startas om, vilket avslutar befintliga sessioner.

### <a name="usage-limits"></a>Gränserna för Resursanvändning

Moln-gränssnittet är avsedd för interaktiva användningsfall. Därför kan avslutas alla icke-interaktiv tidskrävande sessioner utan varning.

## <a name="bash-limitations"></a>Bash-begränsningar

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheterna anges som en vanlig användare utan åtkomst till sudo. En installation utanför din `$Home` directory sparas inte.
Även om vissa kommandon inom den `clouddrive` katalogen som `git clone`, har inte rätt behörighet din `$Home` directory har behörighet.

### <a name="editing-bashrc"></a>Redigera .bashrc

Vara försiktig när du redigerar .bashrc, gör det kan orsaka oväntade fel i moln Shell.

### <a name="bashhistory"></a>.bash_history

Historik för bash kommandon kan vara inkonsekvent på grund av avbrott i molnet Shell session eller samtidiga sessioner.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="slow-startup-time"></a>Långsam starten

PowerShell Azure Cloud Shell kan ta upp till 60 sekunder att initiera under förhandsgranskningen.

### <a name="no-home-directory-persistence"></a>No $Home directory beständiga

Data som skrivs till `$Home` av alla program (exempel: git vim och andra) inte har behållits över PowerShell-sessioner. En lösning [visas här](troubleshooting.md#powershell-resolutions).

### <a name="default-file-location-when-created-from-azure-drive"></a>Standardplatsen för när de skapas från Azure enhet:
Med PowerShell-cmdlets, kan användare inte skapa filer under Azure enhet. När användarna skapar nya filer med andra verktyg, till exempel vim eller nano, sparas filerna i mappen C:\Users som standard. 

## <a name="next-steps"></a>Nästa steg

[Felsöka Cloud Shell](troubleshooting.md) <br>
[Snabbstart för Bash](quickstart.md) <br>
[Snabbstart för PowerShell](quickstart-powershell.md)
