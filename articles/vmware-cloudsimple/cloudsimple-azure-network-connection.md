---
title: VMware-lösningar (AVS) – Azure nätverks anslutningar
description: Lär dig mer om att ansluta ditt virtuella Azure-nätverk till ditt AVS-områdes nätverk
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025086"
---
# <a name="azure-network-connections-overview"></a>Översikt över Azure nätverks anslutningar

När du skapar en AVS-tjänst i en region och skapar noder kan du:

* Begär en Azure ExpressRoute-krets och koppla den till AVS-nätverket i den regionen.
* Anslut ditt AVS-områdes nätverk till ditt virtuella Azure-nätverk eller ditt lokala nätverk med Azure ExpressRoute.
* Ge åtkomst till tjänster som körs i din Azure-prenumeration eller ditt lokala nätverk från din privata moln miljö.

ExpressRoute-anslutningen har hög bandbredd med låg latens.

## <a name="benefits"></a>Erbjudande

Med Azure nätverks anslutning kan du:

* Använd Azure som ett säkerhets kopierings mål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration för att kryptera ditt privata moln virtuellt San data lager.
* Använd hybrid program där webb nivån för programmet körs i det offentliga molnet i molnet medan program-och databas nivåerna körs i ditt privata moln.

## <a name="azure-virtual-network-connection"></a>Azure Virtual Network-anslutning

AVS-privata moln kan anslutas till dina Azure-resurser med ExpressRoute. Med ExpressRoute-anslutningen kan du komma åt resurser som körs i din Azure-prenumeration från ditt moln. Med den här anslutningen kan du utöka ditt moln nätverk i molnet till ditt virtuella Azure-nätverk. Vägar från ett AVS-nätverk kommer att bytas ut mot ditt virtuella Azure-nätverk via BGP. Om du har konfigurerat det virtuella nätverkets peering kan alla peer-kopplade virtuella nätverk nås från ditt AVS-nätverk.

![Azure ExpressRoute-anslutning till virtuellt nätverk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute anslutning till lokalt nätverk

Du kan ansluta din befintliga Azure ExpressRoute-krets till din AVS-region. ExpressRoute-funktionen Global Reach används för att ansluta de två kretsarna med varandra. En anslutning upprättas mellan de lokala och AVS ExpressRoute-kretsarna. Med den här anslutningen kan du utöka dina lokala nätverk till ett nätverk för molnets privata moln. Vägar från ditt AVS-nätverk kommer att utbytas via BGP med ditt lokala nätverk.

![Anslutning till lokalt ExpressRoute – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Anslutning till lokalt nätverk och Azure Virtual Network

Anslutningar till det lokala nätverket och det virtuella Azure-nätverket kan samverka från ditt AVS-nätverk. Anslutningen använder BGP för att utväxla vägar mellan ett lokalt nätverk, ett virtuellt Azure-nätverk och ett AVS-nätverk. När du ansluter ditt AVS-nätverk till ditt virtuella Azure-nätverk i närvaro av en Global Reach anslutning, kommer Azures virtuella nätverks vägar att synas i ditt lokala nätverk. Route Exchange sker i Azure mellan de yttre routrarna.

![Lokal ExpressRoute-anslutning med Azure Virtual Network-anslutning](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Viktiga överväganden

Genom att ansluta till ett AVS-nätverk från ett lokalt nätverk och/eller från ett virtuellt Azure-nätverk kan du dirigera Exchange mellan alla nätverk.

* Ett virtuellt Azure-nätverk visas både i ett lokalt nätverk och i ett AVS-nätverk.
* Om du har anslutit till ditt virtuella Azure-nätverk från ett lokalt nätverk, kan anslutning till ett AVS-nätverk med Global Reach ge åtkomst till virtuella nätverk från AVS-nätverket.
* Under näts adresserna **får inte** överlappa något av nätverken som är anslutna.
* AVS annonserar **inte** en standard väg till ExpressRoute-anslutningarna
* Om din lokala router annonserar standard vägen, kommer trafiken från AVS-nätverket och det virtuella Azure-nätverket använda den annonserade standard vägen. Därför går det inte att komma åt virtuella datorer på Azure med offentliga IP-adresser.

## <a name="next-steps"></a>Nästa steg

* [Anslut Azure Virtual Network till AVS med ExpressRoute](virtual-network-connection.md)
* [Ansluta från lokalt till AVS med ExpressRoute](on-premises-connection.md)
