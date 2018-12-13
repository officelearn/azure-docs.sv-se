---
title: Översikt över IPv6 för Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Förstå IPv6-stöd för Azure Load Balancer och belastningsutjämnade virtuella datorer.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, azure-belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 894a56c2e51e8fa8a2d72253563d218416ace4cb
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161941"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Översikt över IPv6 för Azure Load Balancer


>[!NOTE] 
>Azure Load Balancer stöder två typer: Basic eller Standard. Den här artikeln beskriver den grundläggande lastbalanseraren. Mer information om Standardbelastningsutjämnaren finns i [översikt över Standard Load Balancer](load-balancer-standard-overview.md).

Internetuppkopplad belastningsutjämnare kan distribueras med en IPv6-adress. Förutom IPv4-anslutningen kan följande funktioner:

* Enhetlig slutpunkt till slutpunkt IPv6-anslutning mellan offentliga Internet-klienter och Azure-datorer (VM) via belastningsutjämnaren.
* Intern slutpunkt till slutpunkt IPv6 utgående anslutningar mellan virtuella datorer och offentliga Internet IPv6-aktiverade klienter.

Följande bild illustrerar IPv6-funktioner för Azure Load Balancer.

![Azure Load Balancer med IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

När distribuerats kan en IPv4- eller IPv6-aktiverad Internet-klient kommunicera med den offentliga IPv4 eller IPv6-adresser (eller värdnamn) för internetaktiverad Azure-belastningsutjämnaren. Belastningsutjämnaren dirigerar IPv6-paket till de privata IPv6-adresserna för de virtuella datorerna med hjälp av network adress translation (NAT). IPv6-Internet-klienten inte kan kommunicera direkt med IPv6-adressen för de virtuella datorerna.

## <a name="features"></a>Funktioner

Inbyggt IPv6-stöd för virtuella datorer som distribueras via Azure Resource Manager innehåller:

1. Belastningsutjämnad IPv6-tjänster för IPv6-klienter på Internet
2. Inbyggd IPv6 och IPv4-slutpunkter på virtuella datorer (”dubbla liggande”)
3. Inkommande och utgående-initierad interna IPv6-anslutningar
4. Protokoll som stöds, till exempel TCP, UDP och HTTP (S) Aktivera en fullständig uppsättning tjänstarkitekturer

## <a name="benefits"></a>Fördelar

Den här funktionen gör det möjligt för följande viktiga fördelar:

* Uppfyll statliga regleringar kräver att nya program vara tillgänglig för endast IPv6-klienter
* Aktivera mobile och Internet of things (IOT)-utvecklare kan använda dubbla Staplad (IPv4 + IPv6) Azure-datorer för att hantera växande mobilappar och IOT-marknader

## <a name="details-and-limitations"></a>Information och begränsningar

Information

* Azure DNS-tjänsten innehåller poster för både IPv4-A- och IPv6-AAAA namn och svarar med båda posterna för belastningsutjämnaren. Klienten väljer vilken adress (IPv4 eller IPv6) ska kunna kommunicera med.
* När en virtuell dator upprättar en anslutning till en offentlig IPv6 Internet-ansluten enhet, är den Virtuella datorns källan IPv6-adress nätverksadress översättas (NAT) till den offentliga IPv6-adressen för belastningsutjämnaren.
* Virtuella datorer som kör Linux-operativsystem måste konfigureras för att ta emot en IPv6-IP-adress via DHCP. Många av Linux-avbildningar i Azure-galleriet är redan konfigurerad för att stödja IPv6 utan modifiering. Mer information finns i [konfigurera DHCPv6 för virtuella Linux-datorer](load-balancer-ipv6-for-linux.md)
* Om du väljer att använda en hälsoavsökning med din belastningsutjämnare, skapar en IPv4-avsökning och använder den med både IPv4 och IPv6-slutpunkter. Om tjänsten på den virtuella datorn slutar att fungera tas både IPv4 och IPv6-slutpunkter bort från roteringen.

Begränsningar

* Du kan inte lägga till IPv6-belastningsutjämningsregler i Azure-portalen. Reglerna kan bara skapas via mallen, CLI, PowerShell.
* Du kan inte uppgradera befintliga virtuella datorer för att använda IPv6-adresser. Du måste distribuera nya virtuella datorer.
* En IPv6-adress kan tilldelas till ett enda nätverksgränssnitt på varje virtuell dator.
* Offentliga IPv6-adresser kan inte tilldelas till en virtuell dator. De kan endast tilldelas till en belastningsutjämnare.
* Du kan inte konfigurera omvänd DNS-sökning för din offentliga IPv6-adresser.
* De virtuella datorerna med IPv6-adresser kan inte vara medlemmar i en Azure-molntjänst. De kan anslutas till Azure Virtual Network (VNet) och kommunicera med varandra via sina IPv4-adresser.
* Privata IPv6-adresser kan distribueras på enskilda virtuella datorer i en resursgrupp, men kan inte distribueras i en resursgrupp via Scale Sets.
* Virtuella Azure-datorer kan inte ansluta via IPv6 till andra virtuella datorer, andra Azure-tjänster eller lokala enheter. De kan endast kommunicera med Azure load balancer över IPv6. De kan dock kommunicera med dessa resurser som använder IPv4.
* Nätverkssäkerhetsgrupp (NSG) skydd för IPv4 stöds i dual-stack (IPv4 + IPv6)-distributioner. NSG: er gäller inte för IPv6-slutpunkter.
* IPv6-ändpunkten på den virtuella datorn visas inte direkt till internet. Det är bakom en belastningsutjämnare. Endast de portar som anges i regler för belastningsutjämnaren är tillgänglig via IPv6.
* Ändra parametern IdleTimeout för IPv6 är **för närvarande inte**. Standardvärdet är fyra minuter.
* Ändra parametern loadDistributionMethod för IPv6 är **för närvarande inte**.
* Reserverade IPv6 IP-adresser (där ipallocationmethod inställt = statisk) är **för närvarande inte**.
* NAT64 (översättning av IPv6 till IPv4) stöds inte.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att distribuera en belastningsutjämnare med IPv6.

* [Tillgängligheten för IPv6 efter region](https://go.microsoft.com/fwlink/?linkid=828357)
* [Distribuera en belastningsutjämnare med IPv6 med en mall](load-balancer-ipv6-internet-template.md)
* [Distribuera en belastningsutjämnare med IPv6 med hjälp av Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Distribuera en belastningsutjämnare med IPv6 med Azure CLI](load-balancer-ipv6-internet-cli.md)
