---
title: Översikt över Azure VMware-lösning av CloudSimple-noder
description: Lär dig mer om CloudSimple-noder och-koncept.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899022"
---
# <a name="cloudsimple-nodes-overview"></a>Översikt över CloudSimple-noder

Noder är Bygg blocken i ett privat moln. En nod är:

* En dedikerad beräknings värd för Bare Metal där en VMware ESXi-hypervisor installeras  
* En beräknings enhet som du kan etablera eller reservera för att skapa privata moln
* Tillgängligt för etablering eller reserv i en region där CloudSimple-tjänsten är tillgänglig

Du skapar ett privat moln från de etablerade noderna. Om du vill skapa ett privat moln måste du ha minst tre noder av samma SKU. Lägg till ytterligare noder för att expandera ett privat moln.  Du kan lägga till noder i ett befintligt kluster eller skapa ett nytt kluster genom att etablering av noder i Azure Portal och associera dem med CloudSimple-tjänsten.  Alla etablerade noder är synliga under CloudSimple-tjänsten.  

## <a name="provisioned-nodes"></a>Etablerade noder

Etablerade noder ger kapacitet för att betala per användning. Med etablerings noderna kan du snabbt skala ditt VMware-kluster på begäran. Du kan lägga till noder vid behov eller ta bort en etablerad nod för att skala ned VMware-klustret. Etablerade noder debiteras per månad och debiteras för prenumerationen där de är etablerade.

* Om du betalar för Azure-prenumerationen via kredit kort faktureras kortet direkt.
* Om du faktureras per faktura visas avgifterna på nästa faktura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-lösning av CloudSimple Nodes SKU

Följande typer av noder är tillgängliga för etablering eller reservation.

| SKU           | CS28-nod                 | CS36-nod                 | CS36m-nod                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Östra USA, västra USA            | Östra USA, västra USA            | Europa, västra                 |
| Processor           | 2x 2,2 GHz, 28 kärnor (56 HT) | 2x 2,3 GHz, 36 kärnor (72 HT) | 2x 2,3 GHz, 36 kärnor (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cachedisk    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Kapacitets disk | 5,625 TB RAW                | 11,25 TB RAW                | 15,36 TB RAW                |
| Lagrings typ  | Alla Flash                   | Alla Flash                   | Alla Flash                   |

## <a name="limits"></a>Begränsningar

Följande Node-gränser gäller för privata moln.

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat moln | 3 |
| Maximalt antal noder i ett kluster i ett privat moln | 16 |
| Maximalt antal noder i ett privat moln | 64 |
| Minsta antal noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [etablerar noder](create-nodes.md)
* Lär dig mer om [privata moln](cloudsimple-private-cloud.md)
