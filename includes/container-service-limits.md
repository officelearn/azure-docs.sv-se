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
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072890"
---
| Resource | Standardgräns |
| --- | :--- |
| Maximal kluster per prenumeration | 100 |
| Högsta antalet noder per kluster | 100 |
| Maximal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubenet | 110 |
| Maximal poddar per nod: [Avancerat nätverk] [ advanced-networking] med Azure Container nätverkets gränssnitt | Azure CLI-distribution: 30<sup>1</sup><br />Azure Resource Manager-mall: 30<sup>1</sup><br />Portaldistribution: 30 |

<sup>1</sup>när du distribuerar ett Azure Kubernetes Service (AKS)-kluster med Azure CLI eller en Resource Manager-mall kan det här värdet kan konfigureras för upp till 250 poddar per nod. Du kan inte konfigurera maximal poddar per nod när du redan har distribuerat ett AKS-kluster, eller om du distribuerar ett kluster med hjälp av Azure portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
