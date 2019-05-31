---
title: VMware-lösningar genom att CloudSimple - tjänsten
description: Innehåller en översikt över CloudSimple service och begrepp.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358115"
---
# <a name="cloudsimple-service-overview"></a>Översikt över CloudSimple Service

Tjänsten CloudSimple kan du använda Azure VMware-lösning genom CloudSimple.  Skapandet av tjänsten kan du köpa noder, Reservera noder och skapa privata moln.  Du har skapat tjänsten CloudSimple i varje Azure-region där CloudSimple-tjänsten är tillgänglig.  Tjänsten definierar edge-nätverk med Azure VMware-lösning genom CloudSimple.  Edge-nätverk stöder tjänster som omfattar VPN och ExpressRoute internet-anslutning till ditt privata moln.

## <a name="gateway-subnet"></a>Gateway-undernät 

En gateway-undernätet krävs per CloudSimple tjänst och är unik för den region där den skapas. Gateway-undernätet används när du skapar edge-nätverket och kräver en/28 CIDR-block.  Adressutrymme för gateway-undernätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljö. De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och Azure-nätverk.  Ett gateway-undernät kan inte tas bort när den har skapats.  Gateway-undernätet tas bort när tjänsten tas bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md)