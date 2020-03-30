---
title: ta med fil
description: ta med fil
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334759"
---
| Resurs | Gräns |
| --- | :--- |
| Maximalt antal kluster per prenumeration | 100 |
| Maximala noder per kluster med tillgänglighetsuppsättningar för virtuella datorer och SKU för grundläggande belastningsutjämnare  | 100 |
| Maximala noder per kluster med skalningsuppsättningar för virtuella datorer och [SKU för standardbelastningsutjämning][standard-load-balancer] | 1000 (100 noder per [nodpool][node-pool]) |
| Maximal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubenet | 110 |
| Maximala poddar per nod: [Avancerat nätverk][advanced-networking] med Azure Container Networking Interface | Azure CLI-distribution: 30<sup>1</sup><br />Azure Resource Manager-mall: 30<sup>1</sup><br />Portaldistribution: 30 |

<sup>1.</sup> När du distribuerar ett AKS-kluster (Azure Kubernetes Service) med Azure CLI- eller Resource Manager-mallen kan det här värdet konfigureras upp till 250 poddar per nod. Du kan inte konfigurera maximala poddar per nod när du redan har distribuerat ett AKS-kluster, eller om du distribuerar ett kluster med hjälp av Azure-portalen.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
