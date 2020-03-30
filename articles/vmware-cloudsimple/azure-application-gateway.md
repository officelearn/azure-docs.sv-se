---
title: Använda Azure Application Gateway med virtuella virtuella VMware-datorer
description: Beskriver hur du använder Azure-programgatewayen för att hantera inkommande webbtrafik för webbservrar som körs i virtuella VMware-datorer vinner CloudSimple Private Cloud-miljön
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015464"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Använda Azure Application Gateway med virtuella VMware-datorer i cloudsimple-miljön för privata moln

Du kan använda Azure Application Gateway för att hantera inkommande webbtrafik för dina webbservrar som körs i virtuella VMware-datorer i din CloudSimple Private Cloud-miljö.

Genom att utnyttja Azure Application Gateway i en offentlig-privat hybriddistribution kan du hantera webbtrafik till dina program, tillhandahålla en säker frontend och avlasta SSL-bearbetning för deras tjänster som körs i VMware-miljö. Azure Application Gateway dirigerar inkommande webbtrafik till serverdelspoolinstanser som finns i VMware-miljöer enligt konfigurerade regler och hälsoavsökningar.

Den här Azure Application Gateway-lösningen kräver att du:

* Ha en Azure-prenumeration.
* Skapa och konfigurera ett virtuellt Azure-nätverk och ett undernät i det virtuella nätverket.
* Skapa och konfigurera NSG-regler och peer ditt virtuella nätverk med ExpressRoute till ditt CloudSimple Private Cloud.
* Skapa & Konfigurera ditt privata moln.
* Skapa & Konfigurera din Azure Application Gateway.

## <a name="azure-application-gateway-deployment-scenario"></a>Distributionsscenario för Azure Application Gateway

I det här fallet azure application gateway körs i din Azure virtuella nätverk. Det virtuella nätverket är anslutet till ditt privata moln via en ExpressRoute-krets. Alla undernät i det privata molnet kan nås ip från det virtuella nätverkets undernät.

![Azure belastningsutjämnare i virtuella Azure-nätverk](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Så här distribuerar du lösningen

Distributionsprocessen består av följande uppgifter:

1. [Kontrollera att förutsättningarna är uppfyllda](#1-verify-prerequisites)
2. [Ansluta din virtuella Azure-anslutning till det privata molnet](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Distribuera en Azure-programgateway](#3-deploy-an-azure-application-gateway)
4. [Skapa och konfigurera VM-pool för webbserver i det privata molnet](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Kontrollera förutsättningarna

Kontrollera att dessa förutsättningar är uppfyllda:

* En Azure Resource Manager och ett virtuellt nätverk har redan skapats.
* Ett dedikerat undernät (för Application Gateway) i ditt virtuella Azure-nätverk har redan skapats.
* Ett CloudSimple Private Cloud har redan skapats.
* Det finns ingen IP-konflikt mellan IP-undernät i det virtuella nätverket och undernäten i det privata molnet.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Anslut ditt virtuella Azure-nätverk till ditt privata moln

Följ den här processen om du vill ansluta ditt virtuella Azure-nätverk till ditt privata moln.

1. [Kopiera ExpressRoute-peering-informationen i CloudSimple-portalen](virtual-network-connection.md).

2. [Konfigurera en virtuell nätverksgateway för ditt virtuella Azure-nätverk](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Länka ditt virtuella nätverk till CloudSimple ExpressRoute-kretsen](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Använd peering-informationen som du kopierade för att länka det virtuella nätverket till ExpressRoute-kretsen](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Distribuera en Azure-programgateway

Detaljerade instruktioner för detta är tillgängliga i [Skapa en programgateway med sökvägsbaserade routningsregler med Hjälp av Azure-portalen](../application-gateway/create-url-route-portal.md). Här är en sammanfattning av de steg som krävs:

1. Skapa ett virtuellt nätverk i din prenumerations- och resursgrupp.
2. Skapa ett undernät (som ska användas som dedikerat undernät) i det virtuella nätverket.
3. Skapa en standard application gateway (aktivera WAF): På Startsidan för Azure-portalen klickar du på **Resource** > **Networking** > **Application Gateway** längst upp till vänster på sidan. Välj standard-SKU och storlek och ange Azure-prenumeration, resursgrupp och platsinformation. Om det behövs skapar du en ny offentlig IP för den här programgatewayen och anger information om det virtuella nätverket och det dedikerade undernätet för programgatewayen.
4. Lägg till en serverdpool med virtuella datorer och lägg till den i programgatewayen.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Skapa och konfigurera en VM-pool för webbserver i ditt privata moln

I vCenter skapar du virtuella datorer med det operativsystem och den webbserver som du väljer (till exempel Windows/IIS eller Linux/Apache). Välj ett undernät/VLAN som är avsett för webbnivån i ditt privata moln. Kontrollera att minst ett virtuella nätverkskort för virtuella webbserverns datorer finns på webbnivås undernät.
