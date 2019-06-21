---
title: Azure VMware-lösning av CloudSimple - CloudSimple noden kvot
description: Beskriver kvotgränserna för CloudSimple noder och hur du begär för en ökning av kvoten
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fd1416befb74a7299136ea497eccc8a06b7f0f6a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164869"
---
# <a name="cloudsimple-node-quota-limits"></a>Kvotgränser för CloudSimple nod

Fyra noder är antalet standard för att etablera, när prenumerationen har aktiverats för CloudSimple-tjänsten.  Du kan etablera någon [nodtyp](cloudsimple-node.md) från Azure-portalen.  Minst tre noder i samma SKU krävs för att skapa ett privat moln.  Om du har etablerat noderna kan se du ett fel vid försök att etablera ytterligare noder.

## <a name="quota-increase"></a>Öka kvoten

Du kan öka kvoten för noden genom att skicka en supportförfrågan. Driftsteamet tjänsten utvärderar begäran, och arbeta med dig för att öka kvoten för noden.  Välj följande alternativ när du öppnar en ny biljett:

* Typ av problem: **Teknisk**
* Prenumeration: **Ditt prenumerations-ID**
* Tjänstetyp: **VMware-lösning genom CloudSimple**
* Problemtyp: **Dedikerade noder kvot**
* Undertyp av problemet: **Öka kvoten för dedikerade noder**
* Ämne: **Öka kvoten**

Ange det begärda antalet noder och noden SKU i information om support-ärende.

Du kan även kontakta din Microsoft-kontorepresentant på [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) att öka kvoten för noden för din prenumeration.  Du måste ange den:

* Prenumerations-ID:t
* Noden SKU
* Antal ytterligare noder som du ska få kvot

## <a name="next-steps"></a>Nästa steg

* [Etablera noder](create-nodes.md)
* [Översikt över CloudSimple-noder](cloudsimple-node.md)