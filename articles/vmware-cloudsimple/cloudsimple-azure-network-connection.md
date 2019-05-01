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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577007"
---
# <a name="azure-network-connection-overview"></a>Översikt över Azure network-anslutning

När du skapar en CloudSimple-tjänst i en region, den:

* Skapar Azure ExpressRoute-krets och kopplar den till tjänsten i den regionen
* Ansluter nätverket CloudSimple region till din Azure-nätverk eller ditt lokala nätverk som använder Azure ExpressRoute
* Tillhandahåller åtkomst till tjänster som körs i din Azure-prenumeration eller ditt lokala nätverk, från din privata molnmiljö

Den här anslutningen är hög bandbredd med kort svarstid.

## <a name="benefits"></a>Fördelar

Azure nätverksanslutningen kan du:

* Använd Azure som ett säkerhetskopieringsmål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration att kryptera dina privata moln virtuellt SAN-datalager.
* Använda hybridprogram där webbnivån av programmet körs i det offentliga molnet när programmet och databasnivåer köras i ditt privata moln.

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-anslutning till lokalt nätverk

Du kan ansluta din befintliga Azure ExpressRoute-krets till din CloudSimple region. ExpressRoute Global räckvidd funktionen används för att ansluta två kretsar med varandra.  En anslutning upprättas mellan lokala och CloudSimple ExpressRoute-kretsar.

Den här metoden upprättar en anslutning mellan de två miljöer som är:

* Skydda
* Privat
* Hög bandbredd
* Låg latens

Skapa en ExpressRoute-anslutning till ett lokalt nätverk [supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera virtuell nätverksanslutning](https://docs.azure.cloudsimple.com/virtual-network-connection)