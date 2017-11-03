---
title: "Översikt över Azure interna belastningsutjämnare | Microsoft Docs"
description: "Hur fungerar en intern belastningsutjämnare i Azure och scenarier för att konfigurera interna slutpunkter."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 54e390dbdb07cb4c45c801b638099aa0dcc6db1a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="overview-of-azure-internal-load-balancer"></a>Översikt över Azure interna belastningsutjämnare

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure interna belastningen belastningsutjämnare (ILB) dirigerar endast trafik till resurser som inte ingår i en molntjänst eller som använder en VPN-anslutning för att få åtkomst till Azure-infrastrukturen. I detta avseende ILB skiljer sig från en extern belastningsutjämnare. Azure-infrastrukturen begränsar åtkomsten till de belastningsutjämnade virtuella IP-adresserna (VIP) för en molnbaserad tjänst eller till ett virtuellt nätverk. VIP-adresser och virtuella nätverk exponeras aldrig direkt till en internet-slutpunkt. Interna line-of-business-program körs i Azure och kan nås från Azure eller från lokala resurser.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Varför du kanske behöver en intern belastningsutjämnare

Interna belastningsutjämnare för belastningsutjämning mellan virtuella datorer (VM) som finns i en molnbaserad tjänst eller ett virtuellt nätverk med ett regionalt omfång. Information om virtuella nätverk med ett regionalt omfång finns [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) i Azure-bloggen. Befintliga virtuella nätverk som har konfigurerats för en tillhörighetsgrupp kan inte använda ILB.

ILB kan följande typer av nätverksbelastning:

* Inom en tjänst i molnet: belastningsutjämning från virtuella datorer till en uppsättning virtuella datorer som finns inom samma tjänst i molnet. Se den här <a href="#figure1">exempel</a>.
* Ett virtuellt nätverk: belastningsutjämning från virtuella datorer i det virtuella nätverket till en uppsättning virtuella datorer i samma molntjänst för det virtuella nätverket. Se den här <a href="#figure2">exempel</a>.
* För ett virtuellt nätverk mellan platser: belastningsutjämning från lokala datorer till en uppsättning virtuella datorer i samma molntjänst för det virtuella nätverket. Se den här <a href="#figure3">exempel</a>.
* För program på flera nivåer: belastningsutjämning för internet-riktade flera nivåer program där backend-nivåer inte är mot internet. Backend-nivåer krävs trafik belastningsutjämning från lager mot internet.
* För line-of-business-program: belastningsutjämning för line-of-business-program som finns i Azure utan ytterligare belastningen belastningsutjämnaren maskinvara eller programvara. Det här scenariot innehåller lokala servrar som finns i en uppsättning datorer vars trafik Utjämning av nätverksbelastning.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Belastningsutjämning för program mot internet på flera nivåer

Webbnivån har Internetriktade slutpunkter för internet-klienter och är en del av en belastningsutjämnad uppsättning. ILB distribuerar inkommande trafik från webbklienter för TCP-port 443 (HTTPS) till webbservrar.

Databasservrar som finns bakom en ILB-slutpunkt som webbservrar använder för lagring. ILB-slutpunkten är en databas belastningsutjämnade tjänstslutpunkt. Trafiken är belastningsutjämnad över databasservrar i uppsättningen ILB.

Följande bild visar intern belastningsutjämning för internet-riktade flernivåapp inom samma tjänst i molnet.

<a name="figure1"></a>
![Internet-riktade flernivåapp](./media/load-balancer-internal-overview/IC736321.png)

Ett annat scenario är tillgänglig för program på flera nivåer. Belastningsutjämnaren har distribuerats till en annan molntjänst än det som använder tjänsten för ILB.

Molntjänster som använder samma virtuella nätverk kan komma åt ILB-slutpunkten. Följande bild visar frontend-webbservrar som finns i en annan molntjänst från databasen serverdel. Frontend-servrar använda ILB-slutpunkten inom samma virtuella nätverk som serverdel.

<a name="figure2"></a>
![Servrar i en annan molntjänst](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Belastningsutjämning för intranät line-of-business-program

Trafiken från klienter på det lokala nätverket är belastningsutjämnad över flera line-of-business-servrar som använder en VPN-anslutning till Azure-nätverk.

Klientdatorn har åtkomst till en IP-adress från Azure VPN-tjänsten med en punkt-till-plats-VPN. Line-of-business-program kan finnas bakom ILB-slutpunkten.

<a name="figure3"></a>
![Line-of-business-program som finns bakom ILB-slutpunkten](./media/load-balancer-internal-overview/IC744148.png)

Ett annat scenario för line-of-business-program är en plats-till-plats VPN-anslutning till det virtuella nätverket där ILB-slutpunkten är konfigurerad. Lokal trafik dirigeras till ILB-slutpunkten.

<a name="figure4"></a>
![Lokala nätverkstrafik dirigeras till ILB-slutpunkten](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Begränsningar

Den interna belastningsutjämnaren konfigurationer stöder inte SNAT. I den här artikeln avser SNAT scenarier för port låtsas källa nätverksadresser. En virtuell dator i en pool för belastningsutjämnare belastningen måste nå frontend IP-adressen för respektive intern belastningsutjämnare. Anslutningsfel uppstå när flödet är belastningsutjämnad till den virtuella datorn som kommer från flödet. Dessa scenarier stöds inte för ILB. En belastningsutjämnare för proxy-format måste användas i stället.

## <a name="next-steps"></a>Nästa steg

* [Azure Resource Manager-stöd för Azure belastningsutjämnare](load-balancer-arm.md)
* [Komma igång med att konfigurera en Internetriktade belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md)
* [Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)
* [Konfigurera distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
