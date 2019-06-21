---
title: Översikt över noder för VMware-lösning genom CloudSimple - Azure
description: Läs mer om CloudSimple noder och begrepp.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165805"
---
# <a name="cloudsimple-nodes-overview"></a>Översikt över CloudSimple-noder

En nod är:

* En dedikerad utan operativsystem compute värd där VMware ESXi-hypervisor är installerad  
* En enhet med databehandling du kan etablera eller reservera för att skapa privata moln  
* Tillgängliga att etablera eller i en region där CloudSimple-tjänsten är tillgänglig

Noder är byggblocken i ett privat moln.  Om du vill skapa ett privat moln, behöver du minst tre noder i samma SKU.  Lägga till fler noder för att expandera ett privat moln.  Du kan lägga till noder i ett befintligt kluster. Du kan också skapa ett nytt kluster genom att etablera noder i Azure-portalen och associera dem med CloudSimple-tjänsten.  Alla noder som har etablerats visas CloudSimple i tjänsten.  Du kan skapa ett privat moln från de etablerade noderna på CloudSimple Portal.

## <a name="provisioned-nodes"></a>Etablerade noder

Etablerade noder ge användningsbaserad kapacitet. Etablera noder kan du snabbt skalar dina VMware-kluster på begäran. Du kan lägga till noder efter behov, eller ta bort en etablerad nod att skala ned din VMware-kluster. etablerade noder debiteras per månad och debiteras till prenumerationen där de är etablerade:

* Om du betalar för din Azure-prenumeration med kreditkort debiteras kortet omedelbart.
* Om du faktureras med faktura, kostnaderna som anges på din nästa faktura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-lösning genom CloudSimple noder SKU

Följande typer av noder är tillgängliga för etablering eller reservation.

| SKU | CS28 - nod | CS36 - nod |
|-----|-------------|-------------|
| Processor | 2x2.2 GHz, 28 kärnor (56 HT) | 2x2.3 GHz, 36 kärnor (72 HT) |
| RAM | 256 GB | 512 GB |
| Cache Disk |  1.6 TB NVMe | 3.2 TB NVMe |
| Kapacitet för Disk | 5.625 TB rådata | 11,25 TB rådata |
| Lagringstyp | Alla Flash | Alla Flash |

## <a name="limits"></a>Limits

Noden följande begränsningar gäller för privata moln.

| Resource | Gräns |
|----------|-------|
| Minsta antalet noder för att skapa ett privat moln | 3 |
| Högsta antalet noder i ett kluster på ett privat moln | 16 |
| Högsta antalet noder i ett privat moln | 64 |
| Minsta antalet noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [etablera noder](create-nodes.md)
* Lär dig mer om [privat moln](cloudsimple-private-cloud.md)