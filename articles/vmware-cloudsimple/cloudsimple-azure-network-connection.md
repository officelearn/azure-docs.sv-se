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
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563161"
---
# <a name="azure-network-connections-overview"></a>Översikt över Azure nätverks anslutningar

När du skapar en CloudSimple-tjänst i en region:

* Skapar en Azure ExpressRoute-krets och kopplar den till tjänsten i den regionen.
* Ansluter ditt CloudSimple regions nätverk till ditt virtuella Azure-nätverk eller ditt lokala nätverk med Azure ExpressRoute.
* Ger åtkomst till tjänster som körs i din Azure-prenumeration eller ditt lokala nätverk från din privata moln miljö.

ExpressRoute-anslutningen har hög bandbredd med låg latens.

## <a name="benefits"></a>Fördelar

Med Azure nätverks anslutning kan du:

* Använd Azure som ett säkerhets kopierings mål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration för att kryptera ditt privata moln virtuellt San data lager.
* Använd hybrid program där webb nivån för programmet körs i det offentliga molnet medan program-och databas nivåerna körs i ditt privata moln.

## <a name="azure-virtual-network-connection"></a>Azure Virtual Network-anslutning

Privata moln kan anslutas till dina Azure-resurser med hjälp av ExpressRoute.  Med ExpressRoute-anslutningen kan du komma åt resurser som körs i din Azure-prenumeration från ditt privata moln.  Med den här anslutningen kan du utöka ditt privata moln nätverk till ditt virtuella Azure-nätverk.

[![Azure ExpressRoute-anslutning till virtuellt nätverk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute anslutning till lokalt nätverk

Du kan ansluta din befintliga Azure ExpressRoute-krets till din CloudSimple-region. ExpressRoute-funktionen Global Reach används för att ansluta de två kretsarna med varandra.  En anslutning upprättas mellan lokala och CloudSimple ExpressRoute-kretsar.  Med den här anslutningen kan du utöka ditt lokala nätverk till ett privat moln nätverk.

![Anslutning till lokalt ExpressRoute – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Nästa steg

* [Anslut Azure Virtual Network till CloudSimple med ExpressRoute](virtual-network-connection.md)
* [Ansluta från den lokala platsen till CloudSimple med ExpressRoute](on-premises-connection.md)
