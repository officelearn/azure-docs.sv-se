---
title: Översikt över IPv6 - Azure Load Balancer
description: Med den här utbildningssökvägen kommer du igång med IPv6-stöd för Azure Load Balancer och belastningsbalanserade virtuella datorer.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, public ip, native ipv6, mobile, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 6bc7f45d84d525156a3d25bdceef4d1012844afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931977"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Översikt över IPv6 för Azure Load Balancer


>[!NOTE] 
>Det här innehållet har ersatts av [IPv6 för Azure VNet Overview](https://docs.microsoft.com/azure/virtual-network/ipv6-overview). Azure rekommenderar nya IPv6-distributioner med de nya IPv6 för Azure Virtual Networks-funktionerna.

>[!NOTE]
>Azures Load Balancer stöder två typer: grundläggande och standard. Den här artikeln beskriver den grundläggande lastbalanseraren. Mer information om standardbelastningsutjämnare finns i [översikt över standardbelastningsutjämning](load-balancer-standard-overview.md).

Grundläggande SKU Internet-vända belastningsutjämnare kan distribueras med en IPv6-adress. Förutom IPv4-anslutning möjliggör detta följande funktioner:

* Inbyggd IPv6-anslutning från slutpunkt till slutpunkt mellan offentliga Internet-klienter och virtuella Azure-datorer (virtuella datorer) via belastningsutjämnaren.
* Inbyggd utgående IPv6-utgående anslutning mellan virtuella datorer och offentliga Internet IPv6-aktiverade klienter.

Följande bild illustrerar IPv6-funktionen för Azure Load Balancer.

![Azure belastningsutjämning med IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

När en IPv4- eller IPv6-aktiverad Internet-klient har distribuerats kan den kommunicera med de offentliga IPv4- eller IPv6-adresserna (eller värdnamnen) för Azure Internet-facing Load Balancer. Belastningsutjämnaren dirigerar IPv6-paketen till de privata IPv6-adresserna för de virtuella datorerna med hjälp av NAT (Network Address Translation). IPv6 Internet-klienten kan inte kommunicera direkt med IPv6-adressen för de virtuella datorerna.

## <a name="features"></a>Funktioner

Native IPv6-stöd för virtuella datorer som distribueras via Azure Resource Manager ger:

1. Belastningsbalanserade IPv6-tjänster för IPv6-klienter på Internet
2. Inbyggda IPv6- och IPv4-slutpunkter på virtuella datorer ("dubbla staplade")
3. Inkommande och utgående initierade inbyggda IPv6-anslutningar
4. Protokoll som stöds, till exempel TCP, UDP och HTTP(S) möjliggör ett komplett utbud av tjänstarkitekturer

## <a name="benefits"></a>Fördelar

Den här funktionen möjliggör följande viktiga fördelar:

* Möt myndighetsregler som kräver att nya program ska vara tillgängliga för IPv6-klienter
* Aktivera mobila och Internet of things (IOT) utvecklare att använda dual-stacked (IPv4 + IPv6) Azure Virtual Machines för att ta itu med den växande mobila & IOT-marknader

## <a name="details-and-limitations"></a>Detaljer och begränsningar

Information

* Azure DNS-tjänsten innehåller både IPv4 A- och IPv6 AAAA-namnposter och svarar med båda posterna för belastningsutjämnaren. Klienten väljer vilken adress (IPv4 eller IPv6) som ska kommuniceras med.
* När en virtuell dator initierar en anslutning till en offentlig Internet IPv6-ansluten enhet, är den virtuella datorns källa IPv6-adress översatt (NAT) till den offentliga IPv6-adressen för belastningsutjämnaren.
* Virtuella datorer som kör Operativsystemet Linux måste konfigureras för att ta emot en IPv6 IP-adress via DHCP. Många av Linux-avbildningarna i Azure Gallery är redan konfigurerade för att stödja IPv6 utan ändringar. Mer information finns i [Konfigurera DHCPv6 för virtuella Linux-datorer](load-balancer-ipv6-for-linux.md)
* Om du väljer att använda en hälsoavsökning med lastbalanseraren skapar du en IPv4-avsökning och använder den med slutpunkterna IPv4 och IPv6. Om tjänsten på den virtuella datorn går ned tas både IPv4- och IPv6-slutpunkterna ur rotation.

Begränsningar

* Du kan inte lägga till IPv6-belastningsutjämningsregler i Azure-portalen. Reglerna kan bara skapas via mallen CLI, PowerShell.
* Du får inte uppgradera befintliga virtuella datorer för att använda IPv6-adresser. Du måste distribuera nya virtuella datorer.
* En enda IPv6-adress kan tilldelas ett enda nätverksgränssnitt i varje virtuell dator.
* Offentliga IPv6-adresser kan inte tilldelas en virtuell dator. De kan bara tilldelas en belastningsutjämnare.
* Du kan inte konfigurera omvänd DNS-sökning för dina offentliga IPv6-adresser.
* De virtuella datorerna med IPv6-adresserna kan inte vara medlemmar i en Azure Cloud Service. De kan anslutas till ett Virtuellt Azure-nätverk (VNet) och kommunicera med varandra via sina IPv4-adresser.
* Privata IPv6-adresser kan distribueras på enskilda virtuella datorer i en resursgrupp men kan inte distribueras till en resursgrupp via skalningsuppsättningar.
* Virtuella Azure-datorer kan inte ansluta över IPv6 till andra virtuella datorer, andra Azure-tjänster eller lokala enheter. De kan bara kommunicera med Azure-belastningsutjämnaren över IPv6. De kan dock kommunicera med dessa andra resurser med IPv4.
* NSG-skydd (Network Security Group) för IPv4 stöds i IPv4+IPv6-distributioner med dubbla staplar. NSG-enheter gäller inte för IPv6-slutpunkterna.
* IPv6-slutpunkten på den virtuella datorn exponeras inte direkt för internet. Det ligger bakom en belastningsutjämnare. Endast de portar som anges i belastningsutjämna reglerna är tillgängliga via IPv6.
* Det **går inte att**ändra parametern IdleTimeout för IPv6 . Standardvärdet är fyra minuter.
* Det **går inte att**ändra parametern loadDistributionMethod för IPv6.
* Reserverade IPv6-IP-adresser (där IPAllocationMethod = statisk) **för närvarande inte stöds**.
* NAT64 (översättning av IPv6 till IPv4) stöds inte.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en belastningsutjämnare med IPv6.

* [Tillgänglighet för IPv6 efter region](https://go.microsoft.com/fwlink/?linkid=828357)
* [Distribuera en belastningsutjämnare med IPv6 med hjälp av en mall](load-balancer-ipv6-internet-template.md)
* [Distribuera en belastningsutjämnare med IPv6 med Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Distribuera en belastningsutjämnare med IPv6 med Azure CLI](load-balancer-ipv6-internet-cli.md)
