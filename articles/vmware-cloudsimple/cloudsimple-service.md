---
title: Azure VMware-lösningar (AVS)-service
description: Innehåller en översikt över AVS-tjänster och-koncept.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024967"
---
# <a name="avs-service-overview"></a>Översikt över AVS-tjänsten

Med AVS-tjänsten kan du använda Azure VMware-lösningen enligt AVS. Genom att skapa tjänsten kan du köpa noder, reservera noder och skapa moln privata moln. Du skapar AVS-tjänsten i varje Azure-region där AVS-tjänsten är tillgänglig. Tjänsten definierar Edge-nätverket för Azure VMware-lösningen enligt AVS. Edge-nätverket stöder tjänster som omfattar VPN, ExpressRoute och Internet anslutning till dina moln privata moln.

## <a name="gateway-subnet"></a>Gateway-undernät

Ett Gateway-undernät krävs per AVS-tjänst och är unikt för den region där det skapats. Gateway-undernätet används när du skapar Edge-nätverket och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med AVS-miljön. De nätverk som kommunicerar med AVS inkluderar lokala nätverk och Azure Virtual Network. Det går inte att ta bort ett Gateway-undernät när det har skapats. Gateway-undernätet tas bort när tjänsten tas bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en AVS-tjänst på Azure](quickstart-create-cloudsimple-service.md).
