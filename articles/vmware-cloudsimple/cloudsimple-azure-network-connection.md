---
title: VMware-lösning av CloudSimple – Azure nätverks anslutningar
description: Lär dig hur du ansluter ditt virtuella Azure-nätverk till ditt CloudSimple regions nätverk
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8e99da05f71cb01744111b41c301b11a0969057
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812708"
---
# <a name="azure-network-connections-overview"></a>Översikt över Azure nätverks anslutningar

När du skapar en CloudSimple-tjänst i en region:

* Skapar en Azure ExpressRoute-krets och kopplar den till tjänsten i regionen
* Tillåter anslutning från ditt CloudSimple region nätverk till ditt virtuella Azure-nätverk eller ditt lokala nätverk med Azure ExpressRoute
* Tillhandahåller åtkomst tjänster som körs i din Azure-prenumeration, eller ditt lokala nätverk, från din privata moln miljö

Anslutningen är:

* Skydda
* Privat
* Hög bandbredd
* Låg latens

## <a name="benefits"></a>Fördelar

Med Azure nätverks anslutning kan du:

* Använd Azure som ett säkerhets kopierings mål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration för att kryptera ditt privata moln virtuellt San data lager.
* Använd hybrid program där webb nivån för programmet körs i det offentliga molnet medan program-och databas nivåerna körs i ditt privata moln.

## <a name="azure-virtual-network-connection"></a>Azure Virtual Network-anslutning

Privata moln kan anslutas till dina Azure-resurser med hjälp av ExpressRoute.  Du kan använda den här anslutningen för att få åtkomst till olika resurser som körs i din Azure-prenumeration från ditt privata moln.  Med den här anslutningen kan du utöka ditt privata moln nätverk till ditt virtuella Azure-nätverk.

![Azure ExpressRoute-anslutning till virtuellt nätverk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute anslutning till lokalt nätverk

Du kan ansluta din befintliga Azure ExpressRoute-krets till din CloudSimple-region. ExpressRoute-funktionen Global Reach används för att ansluta de två kretsarna med varandra.  En anslutning upprättas mellan lokala och CloudSimple ExpressRoute-kretsar.  Med den här anslutningen kan du utöka ditt lokala nätverk till ett privat moln nätverk.

![Anslutning till lokalt ExpressRoute – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Nästa steg

* [Hämta peering-information för Azure Virtual Network till CloudSimple-anslutning](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Ansluta från den lokala platsen till CloudSimple med ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
