---
title: Azure VMware-lösningar (AVS)-eskalera AVS-behörighet
description: Beskriver hur du eskalerar AVS-behörigheter för att utföra administrativa funktioner i molnets privata moln vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025341"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>Eskalera AVS-behörigheter för att utföra administrativa funktioner i molnets privata moln vCenter

AVS-behörigheterna är utformad för att ge vCenter-användare de behörigheter de behöver för att utföra normala åtgärder. I vissa fall kan en användare behöva ytterligare behörighet för att utföra en viss uppgift. Du kan eskalera privilegier för en vCenter SSO-användare under en begränsad period.

Orsaker till att eskalerade privilegier kan vara följande:

* Konfiguration av identitets källor
* Användarhantering
* Borttagning av distribuerad port grupp
* Installera vCenter-lösningar (till exempel säkerhets kopierings program)
* Skapa tjänst konton

> [!WARNING]
> Åtgärder som vidtas i det eskalerade privilegierade läget kan påverka systemet negativt och kan göra att systemet blir otillgängligt. Utför endast de åtgärder som krävs under eskalering perioden.

I AVS-portalen [eskalerar du behörigheter](escalate-private-cloud-privileges.md) för den lokala CloudOwner-användaren på vCenter SSO. Du kan bara eskalera fjärran vändare behörighet om ytterligare identitetsprovider har kon figurer ATS på vCenter. Eskalering av privilegier innebär att lägga till den valda användaren i den inbyggda gruppen vSphere-administratörer. Endast en användare kan ha eskalerade privilegier. Om du behöver eskalera en annan användares behörigheter måste du först ta bort privilegierna för de aktuella användarna.

Användare från ytterligare identitets källor måste läggas till som medlemmar i gruppen CloudOwner.

> [!CAUTION]
> Nya användare får endast läggas till i *moln-ägar-grupp*, *Cloud-global-Cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* eller, *Cloud-global-VM-admin-Group*.  Användare som lagts till i gruppen *Administratörer* tas då bort automatiskt.  Endast tjänst konton måste läggas till i *Administratörs* gruppen och tjänst kontona får inte användas för att logga in på vSphere-webbgränssnittet.
Under eskaleringen använder AVS automatisk övervakning med associerade aviserings aviseringar för att identifiera eventuella ändringar i miljön.
