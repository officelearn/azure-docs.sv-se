---
title: Azure VMware-lösning av CloudSimple-eskalera CloudSimple-privilegier
description: Beskriver hur du eskalerar CloudSimple-behörigheter för att utföra administrativa funktioner i det privata molnet vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 524772578ad724e969bbeab0be0a3edcf32a845f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619617"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Eskalera CloudSimple-privilegier för att utföra administrativa funktioner i privat moln vCenter

CloudSimple-behörigheterna är utformad för att ge vCenter-användare de behörigheter de behöver för att utföra normala åtgärder. I vissa fall kan en användare behöva ytterligare behörighet för att utföra en viss uppgift.  Du kan eskalera privilegier för en vCenter SSO-användare under en begränsad period.

Orsaker till att eskalerade privilegier kan vara följande:

* Konfiguration av identitets källor
* Användarhantering
* Borttagning av distribuerad port grupp
* Installera vCenter-lösningar (till exempel säkerhets kopierings program)
* Skapa tjänst konton

> [!WARNING]
> Åtgärder som vidtas i det eskalerade privilegierade läget kan påverka systemet negativt och kan göra att systemet blir otillgängligt. Utför endast de åtgärder som krävs under eskalering perioden.

Från CloudSimple-portalen eskalerar du [privilegier](escalate-private-cloud-privileges.md) för den lokala CloudOwner-användaren på vCenter SSO.  Du kan bara eskalera fjärran vändare behörighet om ytterligare identitetsprovider har kon figurer ATS på vCenter.  Eskalering av privilegier innebär att lägga till den valda användaren i den inbyggda gruppen vSphere-administratörer.  Endast en användare kan ha eskalerade privilegier.  Om du behöver eskalera en annan användares behörigheter måste du först ta bort privilegierna för de aktuella användarna.

Användare från ytterligare identitets källor måste läggas till som medlemmar i gruppen CloudOwner.

Under perioden för eskaleringen använder CloudSimple automatisk övervakning med tillhör ande aviseringar för att identifiera eventuella ändringar i miljön.
