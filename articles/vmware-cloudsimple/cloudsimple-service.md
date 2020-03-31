---
title: Azure VMware-lösning från CloudSimple - Tjänst
description: Ger en översikt över CloudSimple-tjänsten och koncepten.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024967"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple-tjänstöversikt

Med CloudSimple-tjänsten kan du använda Azure VMware-lösning från CloudSimple.  Genom att skapa tjänsten kan du köpa noder, reservera noder och skapa privata moln.  Du skapar CloudSimple-tjänsten i varje Azure-region där CloudSimple-tjänsten är tillgänglig. Tjänsten definierar edge-nätverket för Azure VMware-lösning av CloudSimple. Edge-nätverket stöder tjänster som inkluderar VPN, ExpressRoute och internetanslutning till dina privata moln.

## <a name="gateway-subnet"></a>Gateway-undernät 

Ett gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där den skapas. Gateway-undernätet används när edge-nätverket skapas och kräver ett /28 CIDR-block.  Gateway-undernätsadressutrymmet måste vara unikt. Den får inte överlappa med något nätverk som kommunicerar med CloudSimple-miljön. De nätverk som kommunicerar med CloudSimple inkluderar lokala nätverk och virtuella Azure-nätverk.  Det går inte att ta bort ett gateway-undernät när det har skapats.  Gateway-undernätet tas bort när tjänsten tas bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md).
