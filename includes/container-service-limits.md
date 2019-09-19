---
title: ta med fil
description: ta med fil
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "67187706"
---
| Resource | Standardgräns |
| --- | :--- |
| Maximalt antal kluster per prenumeration | 100 |
| Maximalt antal noder per kluster | 100 |
| Maximalt antal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubernetes | 110 |
| Maximalt antal poddar per nod: [Avancerat nätverk][advanced-networking] med Azure Container Networking Interface | Azure CLI-distribution: 30<sup>1</sup><br />Azure Resource Manager mall: 30<sup>1</sup><br />Portaldistribution: 30 |

<sup>1</sup> När du distribuerar ett Azure Kubernetes service-kluster (AKS) med Azure CLI eller en Resource Manager-mall, kan det här värdet konfigureras upp till 250 poddar per nod. Du kan inte konfigurera maximalt poddar per nod efter att du redan har distribuerat ett AKS-kluster, eller om du distribuerar ett kluster med hjälp av Azure Portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
