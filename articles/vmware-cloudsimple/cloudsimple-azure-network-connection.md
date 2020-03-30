---
title: VMware-lösning från CloudSimple - Azure-nätverksanslutningar
description: Lär dig mer om hur du ansluter ditt virtuella Azure-nätverk till ditt CloudSimple-regionnätverk
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025086"
---
# <a name="azure-network-connections-overview"></a>Översikt över Azure-nätverksanslutningar

När du skapar en CloudSimple-tjänst i en region och skapar noder kan du:

* Begär en Azure ExpressRoute-krets och bifoga den till CloudSimple-nätverket i den regionen.
* Anslut ditt CloudSimple-regionnätverk till ditt virtuella Azure-nätverk eller ditt lokala nätverk med Azure ExpressRoute.
* Ge åtkomst till tjänster som körs i din Azure-prenumeration eller ditt lokala nätverk från din private cloud-miljö.

ExpressRoute-anslutningen är hög bandbredd med låg latens.

## <a name="benefits"></a>Fördelar

Med Azure-nätverksanslutning kan du:

* Använd Azure som ett säkerhetskopieringsmål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration för att kryptera ditt privata moln vSAN-datalager.
* Använd hybridprogram där webbnivån för programmet körs i det offentliga molnet medan program- och databasnivåerna körs i ditt privata moln.

## <a name="azure-virtual-network-connection"></a>Virtuell azure-nätverksanslutning

Privata moln kan anslutas till dina Azure-resurser med ExpressRoute.  Med ExpressRoute-anslutningen kan du komma åt resurser som körs i din Azure-prenumeration från ditt privata moln.  Med den här anslutningen kan du utöka ditt Private Cloud-nätverk till ditt virtuella Azure-nätverk.  Vägar från CloudSimple-nätverket kommer att utbytas med ditt virtuella Azure-nätverk via BGP.  Om du har konfigurerat virtuell nätverks peering kommer alla peered virtuella nätverk att vara tillgängliga från ditt CloudSimple-nätverk.

![Azure ExpressRoute-anslutning till virtuellt nätverk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute anslutning till lokalt nätverk

Du kan ansluta din befintliga Azure ExpressRoute-krets till din CloudSimple-region. ExpressRoute Global Reach-funktionen används för att ansluta de två kretsarna med varandra.  En anslutning upprättas mellan de lokala och CloudSimple ExpressRoute-kretsarna.  Med den här anslutningen kan du utöka dina lokala nätverk till Private Cloud-nätverket. Rutter från ditt CloudSimple-nätverk kommer att utbytas via BGP med ditt lokala nätverk.

![Lokal ExpressRoute-anslutning - Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Anslutning till lokalt nätverk och virtuellt Azure-nätverk

Anslutningar till lokalt nätverk och virtuellt Azure-nätverk kan samexistera från ditt CloudSimple-nätverk.  Anslutningen använder BGP för att utbyta vägar mellan lokalt nätverk, azure virtuellt nätverk och CloudSimple-nätverk.  När du ansluter ditt CloudSimple-nätverk till ditt virtuella Azure-nätverk i närvaro av Global Reach-anslutning visas Azure virtuella nätverksvägar i ditt lokala nätverk.  Ruttutbyte sker i Azure mellan kantroutrarna.

![Lokal ExpressRoute-anslutning med virtuell Azure-nätverksanslutning](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Att tänka på

Om du ansluter till CloudSimple-nätverket från lokalt nätverk och från virtuella Azure-nätverk kan dirigera utbyte mellan alla nätverk.

* Azure virtuella nätverk kommer att vara synligt från både lokalt nätverk och CloudSimple-nätverk.
* Om du har anslutit till ditt virtuella Azure-nätverk från lokalt nätverk, tillåter anslutning till CloudSimple-nätverk med Global Reach åtkomst till virtuella nätverk från CloudSimple-nätverk.
* Undernätsadresser **får inte** överlappa varandra mellan något av de anslutna nätverken.
* CloudSimple kommer **inte** att annonsera standardvägen till ExpressRoute-anslutningarna
* Om din lokala router annonserar standardvägen använder trafiken från CloudSimple-nätverket och det virtuella Azure-nätverket den annonserade standardvägen.  Därför kan virtuella datorer på Azure inte nås med offentliga IP-adresser.

## <a name="next-steps"></a>Nästa steg

* [Ansluta virtuella Azure-nätverk till CloudSimple med ExpressRoute](virtual-network-connection.md)
* [Ansluta från lokalt till CloudSimple med ExpressRoute](on-premises-connection.md)
