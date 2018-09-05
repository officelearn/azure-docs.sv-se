---
title: ta med fil
description: ta med fil
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666910"
---
| Resurs | Standardgräns |
| --- | :--- |
| Maximalt antal kluster per prenumeration | 100 |
| Maximalt antal noder per kluster | 100 |
| Maximalt antal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubenet | 110 |
| Maximalt antal poddar per nod: [Avancerade nätverk][advanced-networking] med Azure CNI | Azure CLI-distribution: 110<sup>1</sup><br />Resource Manager-mall: 110<sup>1</sup><br />Portaldistribution: 30 |

<sup>1</sup> Det här värdet kan konfigureras vid distribution av kluster när du distribuerar ett AKS-kluster med Azure CLI eller en Resource Manager-mall.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
