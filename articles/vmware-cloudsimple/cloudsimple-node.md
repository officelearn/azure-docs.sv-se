---
title: Översikt över Azure VMware-lösningar (AVS) – noder
description: Lär dig mer om AVS-noder och-koncept.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024933"
---
# <a name="avs-nodes-overview"></a>Översikt över AVS-noder

Noder är Bygg stenarna i ett moln privat moln. En nod är:

* En dedikerad beräknings värd för Bare Metal där en VMware ESXi-hypervisor installeras  
* En enhet med data behandling som du kan köpa eller reservera för att skapa moln privata moln
* Tillgängligt för köp eller reserv i en region där AVS-tjänsten är tillgänglig

Du skapar ett privat AVS-moln från de köpta noderna. Om du vill skapa ett moln privat moln måste du ha minst tre noder av samma SKU. Om du vill expandera ett moln privat moln lägger du till ytterligare noder. Du kan lägga till noder i ett befintligt kluster eller skapa ett nytt kluster genom att köpa noder i Azure Portal och associera dem med AVS-tjänsten. Alla köpta noder visas under AVS-tjänsten. 

## <a name="provisioned-nodes"></a>Etablerade noder

Etablerade noder ger kapacitet för att betala per användning. Med etablerings noderna kan du snabbt skala ditt VMware-kluster på begäran. Du kan lägga till noder vid behov eller ta bort en etablerad nod för att skala ned VMware-klustret. Etablerade noder debiteras per månad och debiteras för prenumerationen där de är etablerade.

* Om du betalar för Azure-prenumerationen via kredit kort faktureras kortet direkt.
* Om du faktureras per faktura visas avgifterna på nästa faktura.

## <a name="vmware-solution-by-avs-nodes-sku"></a>VMware-lösning per AVS-noder SKU

Följande typer av noder är tillgängliga för etablering eller reservation.

| SKU           | CS28-nod                 | CS36-nod                 | CS36m-nod                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | USA, östra, USA, västra            | USA, östra, USA, västra            | Europa, västra                 |
| Processor           | 2x 2,2 GHz, 28 kärnor (56 HT) | 2x 2,3 GHz, 36 kärnor (72 HT) | 2x 2,3 GHz, 36 kärnor (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cachedisk    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Kapacitets disk | 5,625 TB RAW                | 11,25 TB RAW                | 15,36 TB RAW                |
| Lagrings typ  | Alla Flash                   | Alla Flash                   | Alla Flash                   |

## <a name="limits"></a>Begränsningar

Följande Node-gränser gäller för moln privata moln.

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat AVS-moln | 3 |
| Maximalt antal noder i ett kluster i ett privat AVS-moln | 16 |
| Maximalt antal noder i ett privat AVS-moln | 64 |
| Minsta antal noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [köper noder](create-nodes.md)
* Lär dig mer om [moln privata moln](cloudsimple-private-cloud.md)
* Lär dig hur du [etablerar noder](create-nodes.md)
* Lär dig mer om [privata moln](cloudsimple-private-cloud.md)
