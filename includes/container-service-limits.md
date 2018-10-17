---
title: ta med fil
description: ta med fil
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48873999"
---
| Resurs | Standardgräns |
| --- | :--- |
| Maximalt antal kluster per prenumeration | 100 |
| Maximalt antal noder per kluster | 100 |
| Maximalt antal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubenet | 110 |
| Maximalt antal poddar per nod: [Avancerade nätverk][advanced-networking] med Azure CNI | Azure CLI-distribution: 30<sup>1</sup><br />Resource Manager-mall: 30<sup>1</sup><br />Portaldistribution: 30 |

<sup>1</sup> Det här värdet kan konfigureras vid distribution av kluster när du distribuerar ett AKS-kluster med Azure CLI eller en Resource Manager-mall.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
