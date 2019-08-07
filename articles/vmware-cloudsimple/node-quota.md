---
title: Azure VMware-lösning av CloudSimple-CloudSimple Node-kvot
description: Beskriver kvot gränserna för CloudSimple-noder och hur du begär en ökning av kvoten
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e765d7c87f2f941a5e2d558b71c4e5a71d2df9b1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816668"
---
# <a name="cloudsimple-node-quota-limits"></a>Kvot gränser för CloudSimple-noden

Fyra noder är den standardkvantitet som är tillgänglig för etablering när prenumerationen har Aktiver ATS för CloudSimple-tjänsten.  Du kan etablera vilken [nodtyp](cloudsimple-node.md) som helst från Azure Portal.  Minst tre noder av samma SKU krävs för att skapa ett privat moln.  Om du har etablerat noderna kan ett fel meddelande visas när du försöker etablera ytterligare noder.

## <a name="quota-increase"></a>Kvot ökning

Du kan öka kvoten för en nod genom att skicka en support förfrågan. Tjänst drifts teamet utvärderar begäran och arbetar med dig för att öka kvoten för noden.  Välj följande alternativ när du öppnar en ny biljett:

* Typ av problem: **Produkt**
* Prenumeration: **Ditt prenumerations-ID**
* Tjänst typ: **VMware-lösning av CloudSimple**
* Problem typ: **Kvot för dedikerade noder**
* Problem under typ: **Öka kvoten för dedikerade noder**
* Ämne: **Kvot ökning**

I information om support ärendet anger du det antal noder och den nod-SKU som krävs.

Du kan också kontakta din Microsoft-konto-representant [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) för att öka kvoten för din prenumeration.  Du måste ange:

* Prenumerations-ID:t
* Node-SKU
* Antal ytterligare noder som du begär att kvoten ska ökas för

## <a name="next-steps"></a>Nästa steg

* [Etablera noder](create-nodes.md)
* [Översikt över CloudSimple-noder](cloudsimple-node.md)