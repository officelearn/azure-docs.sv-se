---
title: Azure VMware-lösning per CloudSimple-tjänst
description: Läs mer om CloudSimple-tjänsten med sin översikt. Genom att skapa tjänsten kan du köpa noder, reservera noder och skapa privata moln.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ec77209c0995bf5aec0957c8d4d2269f27d743bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142136"
---
# <a name="cloudsimple-service-overview"></a>Översikt över CloudSimple-tjänsten

Med CloudSimple-tjänsten kan du använda Azure VMware-lösningen från CloudSimple.  Genom att skapa tjänsten kan du köpa noder, reservera noder och skapa privata moln.  Du skapar CloudSimple-tjänsten i varje Azure-region där CloudSimple-tjänsten är tillgänglig. Tjänsten definierar Edge-nätverket för Azure VMware-lösningen från CloudSimple. Edge-nätverket stöder tjänster som omfattar VPN, ExpressRoute och Internet anslutning till dina privata moln.

## <a name="gateway-subnet"></a>Gatewayundernät

Ett Gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där det skapats. Gateway-undernätet används när du skapar Edge-nätverket och kräver ett/28 CIDR-block.  Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön. De nätverk som kommunicerar med CloudSimple inkluderar lokala nätverk och Azure Virtual Network.  Det går inte att ta bort ett Gateway-undernät när det har skapats.  Gateway-undernätet tas bort när tjänsten tas bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md).
