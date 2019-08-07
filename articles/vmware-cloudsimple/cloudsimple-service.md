---
title: VMware-lösning av CloudSimple-tjänsten
description: Beskriver översikten över CloudSimple-tjänster och-koncept.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812508"
---
# <a name="cloudsimple-service-overview"></a>Översikt över CloudSimple-tjänsten

Med CloudSimple-tjänsten kan du använda Azure VMware-lösningen från CloudSimple. När du har skapat tjänsten kan du etablera noder, reservera noder och skapa privata moln. Du skapar CloudSimple-tjänsten i varje Azure-region där CloudSimple-tjänsten är tillgänglig. Tjänsten definierar Edge-nätverket för Azure VMware-lösningen från CloudSimple. Edge-nätverket stöder tjänster som omfattar VPN, Azure-ExpressRoute och Internet anslutning till dina privata moln.

## <a name="gateway-subnet"></a>Gatewayundernät

Ett Gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där det skapats. Gateway-undernätet används när du skapar Edge-nätverket och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön. De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och virtuella Azure-nätverk. Det går inte att ta bort Gateway-undernätet när det har skapats. Gateway-undernätet tas bort när tjänsten tas bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md).
