---
title: VMware-lösning genom CloudSimple - Azure nätverksanslutningar
description: Lär dig mer om Azure-nätverk att ansluta till nätverket CloudSimple region
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497603"
---
# <a name="azure-network-connections-overview"></a>Översikt över Azure-nätverk

När du skapar en CloudSimple-tjänst i en region, den:

* Skapar en Azure ExpressRoute-krets och kopplar den till tjänsten i den regionen
* Tillåter anslutning från nätverket CloudSimple region till din Azure-nätverk eller ditt lokala nätverk som använder Azure ExpressRoute
* Tillhandahåller åtkomst till tjänster som körs i din Azure-prenumeration eller ditt lokala nätverk, från miljön privat moln

Anslutningen är:

* Skydda
* Privat
* Hög bandbredd
* Låg latens

## <a name="benefits"></a>Fördelar

Azure nätverksanslutningen kan du:

* Använd Azure som ett säkerhetskopieringsmål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration att kryptera dina privata moln virtuellt SAN-datalager.
* Använda hybridprogram där webbnivån av programmet körs i det offentliga molnet när programmet och databasnivåer köras i ditt privata moln.

## <a name="azure-virtual-network-connection"></a>Azure virtuell nätverksanslutning

Privata moln kan anslutas till dina Azure-resurser med hjälp av ExpressRoute.  Du kan använda den här anslutningen för att få åtkomst till olika resurser som körs i Azure-prenumerationen från ditt privata moln.  Den här anslutningen kan du utöka du privat moln nätverk till Azure-nätverk.

![Azure ExpressRoute-anslutning till virtuellt nätverk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-anslutning till lokalt nätverk

Du kan ansluta din befintliga Azure ExpressRoute-krets till din CloudSimple region. ExpressRoute Global räckvidd funktionen används för att ansluta två kretsar med varandra.  En anslutning upprättas mellan lokala och CloudSimple ExpressRoute-kretsar.  Den här anslutningen kan du utöka ditt lokala nätverk till nätverk för privata moln.

![Den lokala ExpressRoute-anslutning – Global räckvidd](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Nästa steg

* [Hämta peering information för Azure-nätverket till CloudSimple anslutning](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Ansluta från en lokal plats till CloudSimple med ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
