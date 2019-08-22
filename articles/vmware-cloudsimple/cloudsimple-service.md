---
title: Azure VMware-lösning per CloudSimple-tjänst
description: Innehåller en översikt över CloudSimple-tjänster och-koncept.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877661"
---
# <a name="cloudsimple-service-overview"></a>Översikt över CloudSimple-tjänsten

Med CloudSimple-tjänsten kan du använda Azure VMware-lösningen från CloudSimple.  Genom att skapa tjänsten kan du köpa noder, reservera noder och skapa privata moln.  Du skapar CloudSimple-tjänsten i varje Azure-region där CloudSimple-tjänsten är tillgänglig. Tjänsten definierar Edge-nätverket för Azure VMware-lösningen från CloudSimple. Edge-nätverket stöder tjänster som omfattar VPN, ExpressRoute och Internet anslutning till dina privata moln.

## <a name="gateway-subnet"></a>Gatewayundernät

Ett Gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där det skapats. Gateway-undernätet används när du skapar Edge-nätverket och kräver ett/28 CIDR-block.  Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön. De nätverk som kommunicerar med CloudSimple inkluderar lokala nätverk och Azure Virtual Network.  Det går inte att ta bort ett Gateway-undernät när det har skapats.  Gateway-undernätet tas bort när tjänsten tas bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md).
