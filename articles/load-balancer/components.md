---
title: Azure Load Balancer-komponenter
description: Översikt över Azure Load Balancer-komponenter
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 532dc313a673d28ffe4fc66060d6dcb491ce866c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691274"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer-komponenter

Azure Load Balancer innehåller flera viktiga komponenter för åtgärden.  

Dessa komponenter kan konfigureras i din prenumeration via Azure Portal, Azure CLI, Azure PowerShell eller mallar.

## <a name="frontend-ip-configurations"></a>IP-konfigurationer för klient del

Belastnings utjämningens IP-adress. Det är kontakt punkten för-klienter. Dessa adresser kan vara antingen:

- **Offentlig IP-adress**
- **Privat IP-adress**

Valet av IP-adress avgör vilken **typ** av belastningsutjämnare som skapats. Val av privat IP-adress skapar en intern belastningsutjämnare. Val av offentlig IP-adress skapar en offentlig belastningsutjämnare.

## <a name="backend-pool"></a>Serverdelspool

Gruppen med virtuella datorer eller instanser i en skalnings uppsättning för virtuella datorer som hanterar den inkommande begäran. Om du vill skala kostnads effektivt för att möta stora volymer inkommande trafik rekommenderar vi att du lägger till fler instanser i Server dels poolen. 

Belastnings utjämning konfigurerar genast om sig själv via automatisk omkonfiguration när du skalar upp eller ned instanser. Om du lägger till eller tar bort virtuella datorer från backend-poolen omkonfigureras belastningsutjämnaren utan ytterligare åtgärder. Omfånget för backend-poolen är en virtuell dator i det virtuella nätverket. 

När du överväger att utforma en backend-pool bör du utforma för det minsta antalet enskilda resurser för Server dels poolen för att optimera längden på hanterings åtgärder. Det finns ingen skillnad i prestanda eller skalning för data planet.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

En hälso avsökning används för att fastställa hälso tillståndet för instanserna i backend-poolen. Du kan definiera tröskelvärdet för hälso avsökningar. När en avsökning inte svarar, slutar belastningsutjämnaren att skicka nya anslutningar till felaktiga instanser. Ett avsöknings haveri påverkar inte befintliga anslutningar. Anslutningen fortsätter tills programmet:

- Avslutar flödet
- Tids gräns för inaktivitet inträffar
- Den virtuella datorn stängs av

Load Balancer innehåller olika typer av hälso avsökningar för slut punkter:

- TCP
- HTTP
- HTTPS

Den grundläggande belastningsutjämnaren stöder inte HTTPS-avsökningar. Den grundläggande belastningsutjämnaren stänger alla TCP-anslutningar (inklusive etablerade anslutningar).

## <a name="load-balancing-rules"></a>Belastnings Utjämnings regler

Belastnings Utjämnings regler anger vilken belastnings utjämning som ska utföras. En belastnings Utjämnings regel mappar en bestämd IP-konfiguration och port för klient delen till flera Server dels IP-adresser och portar.

## <a name="inbound-nat-rules"></a>Ingående NAT-regler

Inkommande NAT-regler vidarebefordrar trafik från klient delens IP-adress till en server dels instans i det virtuella nätverket. Vidarebefordran av portar utförs av samma hash-baserade distribution som belastnings utjämning. 

Exempel på användning är Remote Desktop Protocol (RDP) eller Secure Shell-sessioner (SSH) för att separera VM-instanser i ett virtuellt nätverk. Flera interna slut punkter kan mappas till portar på samma IP-adress för klient delen. Klient delens IP-adresser kan användas för att fjärradministrera dina virtuella datorer utan någon ytterligare hopp ruta.

## <a name="outbound-rules"></a>Regler för utgående trafik

En utgående regel konfigurerar utgående NAT (Network Address Translation) för alla virtuella datorer eller instanser som identifieras av backend-poolen.

Den grundläggande belastningsutjämnaren stöder inte utgående regler.
![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.
- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
- Lär dig mer om den [offentliga IP-adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Läs mer om [privat IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Lär dig mer om att använda [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Lär dig mer om [standard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md).
- Läs mer om [TCP-återställning vid inaktivitet](load-balancer-tcp-reset.md).
- Lär dig mer om att [standard Load Balancer med belastnings Utjämnings regler för belastnings utjämning](load-balancer-ha-ports-overview.md).
- Lär dig mer om att använda [Load Balancer med flera klient](load-balancer-multivip-overview.md)delar.
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
- Lär dig mer om [avsöknings typer](load-balancer-custom-probe-overview.md#types).
- Läs mer om [gränser för belastnings utjämning](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Lär dig mer om att använda [vidarebefordran av portar](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
- Läs mer om [utgående regler för belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
