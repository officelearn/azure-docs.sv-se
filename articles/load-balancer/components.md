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
ms.openlocfilehash: 84857315e4b6b4375ed5b78520b4c6ff0d66751a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684980"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer-komponenter

Azure Load Balancer består av några viktiga komponenter. De kan konfigureras i din prenumeration via Azure Portal, Azure CLI, Azure PowerShell eller mallar.

## <a name="frontend-ip-configuration"></a>IP-konfiguration för klient del<a name = "frontend-ip-configurations"></a>

IP-adressen för din Azure Load Balancer. Det är kontakt punkten för-klienter. Följande IP-adresser kan vara antingen:

- **Offentlig IP-adress**
- **Privat IP-adress**

IP-adressens typ avgör vilken **typ** av belastningsutjämnare som skapats. Val av privat IP-adress skapar en intern belastningsutjämnare. Val av offentlig IP-adress skapar en offentlig belastningsutjämnare.

|  | Offentlig lastbalanserare  | Intern lastbalanserare |
| ---------- | ---------- | ---------- |
| IP-konfiguration för klient del| Offentlig IP-adress | Privat IP-adress|
| Beskrivning | En offentlig belastningsutjämnare mappar den offentliga IP-adressen och porten för inkommande trafik till den privata IP-adressen och porten för den virtuella datorn. Belastnings utjämning mappar trafik på det andra sättet för svars trafiken från den virtuella datorn. Du kan distribuera vissa typer av trafik över flera virtuella datorer eller tjänster genom att använda belastnings Utjämnings regler. Du kan till exempel sprida belastningen av trafik för webbegäranden på flera webbservrar.| En intern belastningsutjämnare distribuerar trafik till resurser som finns i ett virtuellt nätverk. Azure begränsar åtkomsten till klient delens IP-adresser för ett virtuellt nätverk som är belastningsutjämnad. IP-adresser och virtuella nätverk på klient sidan exponeras aldrig direkt till en Internet-slutpunkt. Interna verksamhetsspecifika appar körs i Azure och nås i Azure eller via lokala resurser. |
| SKU: er som stöds | Basic, standard | Basic, standard |

![Exempel på nivå för belastnings utjämning](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>Serverdelspool

Gruppen med virtuella datorer eller instanser i en skalnings uppsättning för virtuella datorer som hanterar den inkommande begäran. Om du vill skala kostnads effektivt för att möta stora volymer inkommande trafik rekommenderar vi att du lägger till fler instanser i Server dels poolen.

Belastnings utjämning konfigurerar genast om sig själv via automatisk omkonfiguration när du skalar upp eller ned instanser. Om du lägger till eller tar bort virtuella datorer från backend-poolen omkonfigureras belastningsutjämnaren utan ytterligare åtgärder. Omfånget för backend-poolen är en virtuell dator i det virtuella nätverket.

När du överväger att utforma en backend-pool bör du utforma för det minsta antalet enskilda resurser för Server dels poolen för att optimera längden på hanterings åtgärder. Det finns ingen skillnad i prestanda eller skalning för data planet.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

En hälso avsökning används för att fastställa hälso status för instanserna i backend-poolen. När du skapar en Load Balancer måste du konfigurera en hälso avsökning som Load Balancer kan använda för att avgöra om en instans är felfri och dirigera trafik till den.

Du kan definiera tröskelvärdet för hälso avsökningar. När en avsökning inte svarar, Load Balancer sluta att skicka nya anslutningar till felaktiga instanser. Ett avsöknings haveri påverkar inte befintliga anslutningar. Anslutningen fortsätter tills programmet:

- Avslutar flödet
- Tids gräns för inaktivitet inträffar
- Den virtuella datorn stängs av

Load Balancer innehåller olika typer av hälso avsökningar för slut punkter: TCP, HTTP och HTTPS.

Basic Load Balancer stöder inte HTTPS-avsökningar. Grundläggande Load Balancer stänger alla TCP-anslutningar (inklusive etablerade anslutningar).

## <a name="load-balancing-rules"></a>Belastnings Utjämnings regler

En Load Balancer regel används för att definiera hur inkommande trafik distribueras till **alla** instanser i backend-poolen. En belastnings Utjämnings regel mappar en bestämd IP-konfiguration och port för klient delen till flera Server dels IP-adresser och portar.

Om du till exempel vill att trafik på port 80 (eller en annan port) för klient delens IP-adress ska dirigeras till port 80 av alla Server dels instanser använder du en belastnings Utjämnings regel för att uppnå detta.

## <a name="inbound-nat-rules"></a>Ingående NAT-regler

En inkommande NAT-regel vidarebefordrar inkommande trafik som skickas till en vald klient dels-IP-adress och port kombination till en **specifik** virtuell dator eller instans i backend-poolen. Vidarebefordran av portar utförs av samma hash-baserade distribution som belastnings utjämning.

Till exempel, om du vill att Remote Desktop Protocol (RDP) eller Secure Shell-sessioner (SSH) ska separera VM-instanser i en backend-pool. Flera interna slut punkter kan mappas till portar på samma IP-adress för klient delen. Klient delens IP-adresser kan användas för att fjärradministrera dina virtuella datorer utan någon ytterligare hopp ruta.

## <a name="outbound-rules"></a>Regler för utgående trafik

En utgående regel konfigurerar utgående NAT (Network Address Translation) för alla virtuella datorer eller instanser som identifieras av backend-poolen. Detta gör det möjligt för instanser i Server delen att kommunicera (utgående) till Internet eller andra slut punkter.

Den grundläggande belastningsutjämnaren stöder inte utgående regler.

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.
- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
- Lär dig mer om den [offentliga IP-adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Läs mer om [privat IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Lär dig mer om att använda [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Lär dig mer om [standard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md).
- Läs mer om [TCP-återställning vid inaktivitet](load-balancer-tcp-reset.md).
- Lär dig mer om att [standard Load Balancer med belastnings Utjämnings regler för belastnings utjämning](load-balancer-ha-ports-overview.md).
- Lär dig mer om att använda [Load Balancer med flera klient dels-IP-konfigurationer](load-balancer-multivip-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
- Lär dig mer om [avsöknings typer](load-balancer-custom-probe-overview.md#types).
- Läs mer om [gränser för belastnings utjämning](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Lär dig mer om att använda [vidarebefordran av portar](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
- Läs mer om [utgående regler för belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
