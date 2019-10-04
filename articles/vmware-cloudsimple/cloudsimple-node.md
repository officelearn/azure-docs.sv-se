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
ms.openlocfilehash: 729b832c068dcd401fa6e9d2f4af9193d3859a83
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845547"
---
# <a name="cloudsimple-nodes-overview"></a>Översikt över CloudSimple-noder

Noder är Bygg blocken i ett privat moln. En nod är:

* En dedikerad beräknings värd för Bare Metal där en VMware ESXi-hypervisor installeras  
* En enhet med data behandling som du kan köpa eller reservera för att skapa privata moln
* Tillgängligt för köp eller reserv i en region där CloudSimple-tjänsten är tillgänglig

Du skapar ett privat moln från de köpta noderna. Om du vill skapa ett privat moln måste du ha minst tre noder av samma SKU. Lägg till ytterligare noder för att expandera ett privat moln.  Du kan lägga till noder i ett befintligt kluster eller skapa ett nytt kluster genom att köpa noder i Azure Portal och associera dem med CloudSimple-tjänsten.  Alla köpta noder är synliga under CloudSimple-tjänsten.  

## <a name="purchased-nodes"></a>Köpta noder

De köpta noderna ger en kapacitet för betala per användning. Med inköps noderna kan du snabbt skala ditt VMware-kluster på begäran. Du kan lägga till noder vid behov eller ta bort en köpt nod för att skala upp VMware-klustret. Köpta noder debiteras per månad och debiteras för prenumerationen där de köps.

* Om du betalar för Azure-prenumerationen via kredit kort faktureras kortet direkt.
* Om du faktureras per faktura visas avgifterna på nästa faktura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-lösning av CloudSimple Nodes SKU

Följande typer av noder är tillgängliga för köp eller reservation.

| SKU           | CS28-nod                 | CS36-nod                 | CS36m-nod                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Östra USA, västra USA            | Östra USA, västra USA            | Västra Europa                 |
| Processor           | 2x 2,2 GHz, 28 kärnor (56 HT) | 2x 2,3 GHz, 36 kärnor (72 HT) | 2x 2,3 GHz, 36 kärnor (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cachedisk    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Kapacitets disk | 5,625 TB RAW                | 11,25 TB RAW                | 15,36 TB RAW                |
| Lagringstyp  | Alla Flash                   | Alla Flash                   | Alla Flash                   |

## <a name="limits"></a>Begränsningar

Följande Node-gränser gäller för privata moln.

| Resource | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat moln | 3 |
| Maximalt antal noder i ett kluster i ett privat moln | 16 |
| Maximalt antal noder i ett privat moln | 64 |
| Minsta antal noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [köper noder](create-nodes.md)
* Lär dig mer om [privata moln](cloudsimple-private-cloud.md)
