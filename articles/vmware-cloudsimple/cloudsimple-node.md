---
title: Azure VMware Solution by CloudSimple - Noder översikt
description: Läs mer om CloudSimple-noder och koncept.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024933"
---
# <a name="cloudsimple-nodes-overview"></a>Översikt över CloudSimple-noder

Noder är byggstenarna i ett privat moln. En nod är:

* En dedikerad bare metal compute-värd där en VMware ESXi hypervisor är installerad  
* En beräkningsenhet som du kan etablera eller reservera för att skapa privata moln
* Tillgänglig för etablering eller reserv i en region där CloudSimple-tjänsten är tillgänglig

Du skapar ett privat moln från de etablerade noderna. För att skapa ett privat moln behöver du minst tre noder av samma SKU. Om du vill expandera ett privat moln lägger du till ytterligare noder.  Du kan lägga till noder i ett befintligt kluster eller skapa ett nytt kluster genom att etablera noder i Azure-portalen och associera dem med CloudSimple-tjänsten.  Alla etablerade noder visas under CloudSimple-tjänsten.  

## <a name="provisioned-nodes"></a>Etablerade noder

Etablerade noder ger kapacitet för betalning per betalning. Etableringsnoder hjälper dig att snabbt skala ditt VMware-kluster på begäran. Du kan lägga till noder efter behov eller ta bort en etablerad nod för att skala ned VMware-klustret. Etablerade noder faktureras månadsvis och debiteras prenumerationen där de etableras.

* Om du betalar för din Azure-prenumeration med kreditkort debiteras kortet omedelbart.
* Om du faktureras med faktura visas avgifterna på nästa faktura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-lösning från CloudSimple noder SKU

Följande typer av noder är tillgängliga för etablering eller bokning.

| SKU           | CS28 - Nod                 | CS36 - Nod                 | CS36m - Nod                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Östra USA, västra USA            | Östra USA, västra USA            | Europa, västra                 |
| Processor           | 2x2,2 GHz, 28 kärnor (56 HT) | 2x2,3 GHz, 36 kärnor (72 HT) | 2x2,3 GHz, 36 kärnor (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cachedisk    | 1,6 TB NVMe                 | 3,2 TB NVMe                 | 3,2 TB NVMe                 |
| Kapacitet Disk | 5.625 TB Rå                | 11,25 TB Rå                | 15,36 TB Rå                |
| Lagringstyp  | Alla Flash                   | Alla Flash                   | Alla Flash                   |

## <a name="limits"></a>Begränsningar

Följande nodgränser gäller för privata moln.

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat moln | 3 |
| Maximalt antal noder i ett kluster i ett privat moln | 16 |
| Maximalt antal noder i ett privat moln | 64 |
| Minsta antal noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [etablerar noder](create-nodes.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)
