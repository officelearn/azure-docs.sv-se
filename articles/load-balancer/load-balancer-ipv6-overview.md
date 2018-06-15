---
title: Översikt över IPv6 för Azure belastningsutjämnare | Microsoft Docs
description: Så här fungerar IPv6-stöd för Azure belastningsutjämnare och belastningsutjämnade virtuella datorer.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
keywords: IPv6, azure belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot
ms.assetid: 6a1d583f-a305-40fd-a94b-fa42e1943bbb
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 9622ad4922aa98efe093e7f809a490a8797eb1fd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189672"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Översikt över IPv6 för Azure belastningsutjämnare


>[!NOTE] 
>Azures Load Balancer stöder två typer: grundläggande och standard. Den här artikeln beskriver den grundläggande belastningsutjämnaren. Mer information om Standard belastningsutjämnaren finns [Standard belastningsutjämnaren översikt](load-balancer-standard-overview.md).

Internetriktade belastningsutjämnare kan distribueras med en IPv6-adress. Detta gör följande funktioner utöver IPv4-anslutning:

* Intern slutpunkt till slutpunkt IPv6-anslutning mellan offentliga Internet-klienter och Azure virtuella datorer (VM) via belastningsutjämnaren.
* Intern slutpunkt till slutpunkt IPv6 utgående anslutningar mellan virtuella datorer och offentliga Internet IPv6-aktiverade klienter.

Följande bild illustrerar IPv6-funktioner för Azure-belastningsutjämnaren.

![Azure belastningsutjämnare med IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

När har distribuerats, kan en IPv4- eller IPv6-aktiverad Internet-klient kommunicera med den offentliga IPv4- eller IPv6-adresser (eller värdnamn) för Azure Internetriktade belastningsutjämnare. Belastningsutjämnaren skickar IPv6-paket till de virtuella datorerna med hjälp av network address translation (NAT) privata IPv6-adresser. IPv6-Internet-klienten inte kan kommunicera direkt med IPv6-adressen för de virtuella datorerna.

## <a name="features"></a>Funktioner

Inbyggt IPv6-stöd för virtuella datorer som distribueras via Azure Resource Manager innehåller:

1. Utjämning av nätverksbelastning IPv6-tjänster för IPv6-klienter på Internet
2. Inbyggd IPv6 och IPv4-slutpunkterna på virtuella datorer (”dubbla Staplad”)
3. Inkommande och utgående initieras inbyggda IPv6-anslutningar
4. Protokoll som stöds till exempel TCP, UDP- och HTTP (S) Aktivera en mängd olika arkitekturer för tjänsten

## <a name="benefits"></a>Fördelar

Den här funktionen gör följande viktiga fördelar:

* Uppfyller offentliga regleringar som kräver att nya program vara tillgänglig för endast IPv6-klienter
* Aktivera mobil- och Internet saker (IOT) utvecklare att använda dubbla Staplad (IPv4 + IPv6) Azure virtuella datorer för att möta växande mobile & IOT marknader

## <a name="details-and-limitations"></a>Information och begränsningar

Information

* Azure DNS-tjänsten innehåller poster för både IPv4-A- och IPv6-AAAA namn och svarar med båda posterna för belastningsutjämnaren. Klienten väljer vilken adress (IPv4 eller IPv6) ska kunna kommunicera med.
* När en virtuell dator upprättar en anslutning till offentliga Internet IPv6-anslutna enheter, är källa IPv6-adress nätverksadress översättas (NAT) till den offentliga IPv6-adressen för belastningsutjämnaren.
* Virtuella datorer som kör Linux-operativsystem måste konfigureras för att få en IPv6-IP-adress via DHCP. Många av Linux-avbildningar i Azure-galleriet har redan konfigurerats för att stödja IPv6 utan modifiering. Mer information finns i [konfigurera DHCPv6 för virtuella Linux-datorer](load-balancer-ipv6-for-linux.md)
* Om du väljer att använda en hälsoavsökningen med din belastningsutjämnare, skapa en IPv4-avsökning och använda den med både IPv4 och IPv6-slutpunkter. Om tjänsten på den virtuella datorn stängs av, tas både IPv4 och IPv6-slutpunkter bort från rotationen.

Begränsningar

* Du kan inte lägga till IPv6-belastningsutjämningsregler i Azure-portalen. Regler kan bara skapas via mallen CLI, PowerShell.
* Du kan inte uppgradera befintliga virtuella datorer för att använda IPv6-adresser. Du måste distribuera nya virtuella datorer.
* En IPv6-adress kan tilldelas ett nätverksgränssnitt på varje virtuell dator.
* Offentliga IPv6-adresser kan inte tilldelas till en virtuell dator. De kan endast tilldelas till en belastningsutjämnare.
* Du kan konfigurera omvänd DNS-sökning för din offentliga IPv6-adresser.
* De virtuella datorerna med IPv6-adresser kan inte vara medlemmar i en Azure-molntjänst. De kan vara ansluten till ett Azure Virtual Network (VNet) och kommunicera med varandra via sina IPv4-adresser.
* Privat IPv6-adresser kan distribueras på enskilda virtuella datorer i en resursgrupp, men kan inte distribueras i en resursgrupp via Skaluppsättningar.
* Virtuella Azure-datorer kan inte ansluta via IPv6 till andra virtuella datorer, andra Azure-tjänster eller lokala enheter. De kan endast kommunicera med Azure belastningsutjämnare via IPv6. De kan dock kommunicera med andra resurserna med IPv4.
* Nätverkssäkerhetsgrupp (NSG)-skydd för IPv4 stöds i dual stack (IPv4 + IPv6) distributioner. NSG: er gäller inte för IPv6-slutpunkter.
* IPv6-ändpunkten på den virtuella datorn visas inte direkt till internet. Det är bakom en belastningsutjämnare. Endast de portar som angetts i regler för inläsning belastningsutjämnaren är tillgänglig via IPv6.
* Ändra parametern IdleTimeout för IPv6 är **stöds för närvarande inte**. Standardvärdet är fyra minuter.
* Ändra parametern loadDistributionMethod för IPv6 är **stöds för närvarande inte**.
* Reserverad IP-IPv6-adresser (där ipallocationmethod inställt = statisk) är **stöds för närvarande inte**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en belastningsutjämnare med IPv6.

* [Tillgängligheten för IPv6 efter region](https://go.microsoft.com/fwlink/?linkid=828357)
* [Distribuera en belastningsutjämnare med IPv6 med en mall](load-balancer-ipv6-internet-template.md)
* [Distribuera en belastningsutjämnare med IPv6 med hjälp av Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Distribuera en belastningsutjämnare med IPv6 med Azure CLI](load-balancer-ipv6-internet-cli.md)
