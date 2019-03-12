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
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554694"
---
| Resurs | Standardgräns |
| --- | :--- |
| Maximal kluster per prenumeration | 100 |
| Högsta antalet noder per kluster | 100 |
| Maximal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubenet | 110 |
| Maximal poddar per nod: [Avancerat nätverk] [ advanced-networking] med Azure Container nätverkets gränssnitt | Azure CLI-distribution: 30<sup>1</sup><br />Azure Resource Manager-mall: 30<sup>1</sup><br />Portaldistribution: 30 |

<sup>1</sup>när du distribuerar ett Azure Kubernetes Service (AKS)-kluster med Azure CLI eller en Resource Manager-mall kan det här värdet kan konfigureras för upp till 110 poddar per nod. Du kan inte konfigurera maximal poddar per nod när du redan har distribuerat ett AKS-kluster, eller om du distribuerar ett kluster med hjälp av Azure portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
