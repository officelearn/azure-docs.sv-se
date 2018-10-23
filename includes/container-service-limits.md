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
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400346"
---
| Resurs | Standardgräns |
| --- | :--- |
| Maximalt antal kluster per prenumeration | 100 |
| Maximalt antal noder per kluster | 100 |
| Maximalt antal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubenet | 110 |
| Maximalt antal poddar per nod: [Avancerade nätverk][advanced-networking] med Azure CNI | Azure CLI-distribution: 30<sup>1</sup><br />Resource Manager-mall: 30<sup>1</sup><br />Portaldistribution: 30 |

<sup>1</sup> När du distribuerar ett AKS-klister med Azure CLI eller en Resource Manager-mall kan det här värdet konfigureras upp till **110 poddar per nod**. Du kan inte konfigurera max poddar per nod när du redan har distribuerat ett AKS-kluster eller om du distribuerar ett kluster med hjälp av Azure-portalen.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
