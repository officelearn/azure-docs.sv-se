---
title: VMware-lösning av CloudSimple-tjänsten
description: Innehåller en översikt över CloudSimple service och begrepp.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595316"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple tjänstöversikt

Du kan använda Azure VMware-lösning genom CloudSimple med CloudSimple-tjänsten. När du har skapat tjänsten kan du etablera noder, Reservera noder och skapar privata moln. Du har skapat tjänsten CloudSimple i varje Azure-region där CloudSimple-tjänsten är tillgänglig. Tjänsten definierar edge-nätverk med Azure VMware-lösning genom CloudSimple. Edge-nätverk stöder tjänster som omfattar VPN, Azure ExpressRoute och internet-anslutning till dina privata moln.

## <a name="gateway-subnet"></a>Gateway-undernät

En gateway-undernätet krävs per CloudSimple tjänst och är unik för den region där den skapas. Gateway-undernätet används när du skapar edge-nätverk och kräver en/28 CIDR-block. Adressutrymme för gateway-undernätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljö. De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och virtuella Azure-nätverk. Ett gateway-undernät kan inte tas bort när den har skapats. Gateway-undernätet tas bort när tjänsten tas bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md).
