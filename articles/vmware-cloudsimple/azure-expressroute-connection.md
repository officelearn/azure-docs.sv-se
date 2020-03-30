---
title: Azure VMware Solution by CloudSimple – Anslut privat moln till Azure-nätverk med ExpressRoute
description: Beskriver hur du ansluter din CloudSimple Private Cloud-miljö till det virtuella Azure-nätverket med ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015243"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Anslut din CloudSimple Private Cloud-miljö till det virtuella Azure-nätverket med ExpressRoute

Ditt CloudSimple Private Cloud kan anslutas till ditt virtuella Azure-nätverk med Azure ExpressRoute.  Med den här anslutningen med låg bandbredd med låg latens kan du komma åt tjänster som körs i din Azure-prenumeration från din private cloud-miljö.

Med virtual network-anslutning kan du:

* Använd Azure som ett säkerhetskopieringsmål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration för att kryptera ditt privata moln vSAN-datalager.
* Använd hybridprogram där webbnivån för programmet körs i det offentliga molnet medan program- och databasnivåerna körs i ditt privata moln.

![Azure ExpressRoute-anslutning till virtuellt nätverk](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Konfigurera en virtuell nätverksanslutning

Om du vill konfigurera den virtuella nätverksanslutningen till ditt privata moln behöver du din auktoriseringsnyckel, peer circuit URI och åtkomst till din Azure-prenumeration. Den här informationen är tillgänglig på sidan Anslutning till virtuellt nätverk i CloudSimple-portalen. Instruktioner finns i [Hämta peering-information för virtuella Azure-nätverk till CloudSimple-anslutning](virtual-network-connection.md). Om du har några problem med att få informationen skickar du en <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supportbegäran</a>.

> [!TIP]
> Om du redan har ett virtuellt Azure-nätverk, gateway-undernät och virtuell nätverksgateway kan du hoppa till steg 4.

1. Skapa ett virtuellt nätverk på din Azure-prenumeration och kontrollera att adressutrymmet du väljer skiljer sig från adressutrymmet i ditt privata moln.  Om du redan har ett virtuellt Azure-nätverk kan du använda det befintliga.  Mer information finns i [Skapa ett virtuellt nätverk med Azure-portalen](../virtual-network/quick-create-portal.md).
2. Skapa gateway-undernätet i ditt virtuella Azure-nätverk.  Om du redan har ett gateway-undernät i ditt virtuella Azure-nätverk kan du använda det befintliga. Mer information finns i [Skapa gateway-undernätet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Skapa den virtuella nätverksgatewayen i det virtuella nätverket.  Om du har en befintlig virtuell nätverksgateway kan du använda den befintliga. Mer information finns i [Skapa den virtuella nätverksgatewayen](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Skapa anslutningen mellan ditt virtuella nätverk och ditt privata moln genom att lösa in auktoriseringsnyckeln enligt beskrivningen i [Anslut ett virtuellt nätverk till en krets - en annan prenumeration](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Om du använder en befintlig virtuell nätverksgateway och den har en ExpressRoute-anslutning till samma plats som CloudSimple ExpressRoute-kretsen, upprättas inte anslutningen.  Skapa ett nytt virtuellt nätverk och följ föregående steg.

## <a name="test-the-virtual-network-connection"></a>Testa den virtuella nätverksanslutningen

När anslutningen har skapats kan du kontrollera anslutningens status genom att välja **Egenskaper** under **Inställningar**.  Status och etableringstillstånd ska visa **lyckades**.

![Anslutningsstatus](media/azure-expressroute-connection.png)

Så här testar du den virtuella nätverksanslutningen:

1. Skapa en virtuell dator i din Azure-prenumeration.
2. Hitta IP-adressen för ditt privata moln vCenter (se ditt välkomstmeddelande).
3. Pinga ditt Cloud vCenter från den virtuella datorn som skapats i ditt virtuella Azure-nätverk.
4. Pinga din virtuella Azure-dator från en virtuell dator som körs i ditt privata moln vCenter.

Om du har några problem med att upprätta anslutningen skickar du en <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supportbegäran</a>.
