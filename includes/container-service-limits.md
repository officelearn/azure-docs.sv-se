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
ms.openlocfilehash: 32edd880e5e455393e60c87f4f963d71a3e59a20
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026908"
---
| Resurs | Gräns |
| --- | :--- |
| Maximalt antal kluster per prenumeration | 100 |
| Maximalt antal noder per kluster med tillgänglighets uppsättningar för virtuella datorer och Basic Load Balancer SKU  | 100 |
| Maximalt antal noder per kluster med Virtual Machine Scale Sets och [standard load BALANCER SKU][standard-load-balancer] | 1000 (100 noder per [Node-pool][node-pool]) |
| Maximalt antal poddar per nod: [grundläggande nätverk][basic-networking] med Kubernetes | 110 |
| Maximalt antal poddar per nod: [Avancerat nätverk][advanced-networking] med Azure Container Networking Interface | Azure CLI-distribution: 30<sup>1</sup><br />Azure Resource Manager mall: 30<sup>1</sup><br />Portaldistribution: 30 |

<sup>1</sup> När du distribuerar ett Azure Kubernetes service-kluster (AKS) med Azure CLI eller en Resource Manager-mall, kan det här värdet konfigureras upp till 250 poddar per nod. Du kan inte konfigurera maximalt poddar per nod efter att du redan har distribuerat ett AKS-kluster, eller om du distribuerar ett kluster med hjälp av Azure Portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest