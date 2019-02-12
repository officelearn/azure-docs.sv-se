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
ms.openlocfilehash: 23c25953d2f493d2dd799bfd11dbbb69db002d1b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736215"
---
| Resurs | Standardgräns |
| --- | :--- |
| Maximalt antal kluster per prenumeration | 100 |
| Maximalt antal noder per kluster | 100 |
| Maximalt antal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubenet | 110 |
| Maximalt antal poddar per nod: [Avancerade nätverk][advanced-networking] med Azure CNI | Azure CLI-distribution: 30<sup>1</sup><br />Resource Manager-mall: 30<sup>1</sup><br />Portaldistribution: 30 |

<sup>1</sup> När du distribuerar ett AKS-klister med Azure CLI eller en Resource Manager-mall kan det här värdet konfigureras upp till **110 poddar per nod**. Du kan inte konfigurera max poddar per nod när du redan har distribuerat ett AKS-kluster eller om du distribuerar ett kluster med hjälp av Azure-portalen.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
