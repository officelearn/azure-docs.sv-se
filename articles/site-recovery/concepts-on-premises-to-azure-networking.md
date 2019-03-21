---
title: Konfigurera IP-adresser för att ansluta till efter haveriberedskap och redundans till Azure med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du ställer in IP-adresser för att ansluta till virtuella Azure-datorer efter haveriberedskap och redundans från lokalt med Azure Site Recovery
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 334537e77d5f291dc38e008af7fed9a4ea973265
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108856"
---
# <a name="set-up-ip-addressing-to-connect-to-azure-vms-after-failover"></a>Konfigurera IP-adresser för att ansluta till virtuella Azure-datorer efter redundans

Den här artikeln förklarar nätverkskraven för att ansluta till virtuella Azure-datorer när du har använt den [Azure Site Recovery](site-recovery-overview.md) för replikering och redundans till Azure.

I den här artikeln lär du dig om:

> [!div class="checklist"]
> * Anslutningsmetoder som du kan använda
> * Hur du använder en annan IP-adress för replikerade virtuella Azure-datorer
> * Hur behåller jag IP-adresser för virtuella Azure-datorer efter redundansväxling

## <a name="connecting-to-replica-vms"></a>Ansluta till replikering av VMs

När du planerar din replikering och redundansstrategi, är en av de viktiga frågorna att ansluta Azure-datorn efter redundans. Det finns ett par alternativ när du utformar din strategi för nätverk för virtuella datorer i Azure-replikering:

- **Använd olika IP-adressen**: Du kan välja för att använda en annan IP-adressintervall för det replikerade Virtuella Azure-nätverket. I det här scenariot hämtar den virtuella datorn en ny IP-adress efter redundansväxling, och en DNS-uppdatering krävs.
- **Behålla samma IP-adress**: Du kanske vill använda samma IP-adressintervall som på din primära lokala plats för Azure-nätverket efter redundans. Att behålla samma IP adresser förenklar återställningen genom att minska nätverksproblem efter en redundansväxling. När du replikerar till Azure kommer du dock behöva uppdatera vägar med den nya platsen för IP-adresser efter redundansväxling.

## <a name="retaining-ip-addresses"></a>Behålla IP-adresser

Site Recovery ger möjlighet att behålla fasta IP-adresser när redundansväxlingen till Azure, med ett undernät redundans.

- Med undernätet redundans är ett specifikt undernät tillgänglig vid 1 eller plats 2, men aldrig på båda platser samtidigt.
- För att upprätthålla IP-adressutrymmet i händelse av redundans, ordna du programmässigt för router-infrastruktur för att flytta undernät från en plats till en annan.
- Under redundansväxlingen flytta undernät med de associera skyddade virtuella datorerna. Den huvudsakliga Nackdelen är att om ett fel inträffar, måste du flytta hela undernätet.


### <a name="failover-example"></a>Exempel för redundans

Nu ska vi titta på ett exempel för redundans till Azure med ett fiktivt företag, Woodgrove Bank.

- Woodgrove Bank är värd för sina appar i en lokal plats. De ha sina mobila appar på Azure.
- Det finns VPN plats-till-plats-anslutning mellan sina lokala edge-nätverk och virtuella Azure-nätverket. På grund av VPN-anslutningen visas det virtuella nätverket i Azure som ett tillägg till det lokala nätverket.
- Woodgrove vill replikera lokala arbetsbelastningar till Azure med Site Recovery.
  - Woodgrove har appar som är beroende av hårdkodad IP-adresser, så att de behöver för att behålla IP-adresser för apparna efter redundansväxlingen till Azure.
  - Resurser som körs i Azure använder IP-adressintervallet 172.16.1.0/24 172.16.2.0/24.

![Innan du undernätet redundans](./media/site-recovery-network-design/network-design7.png)

**Infrastruktur före redundans**


För Woodgrove ska kunna replikera dess virtuella datorer till Azure samtidigt som företaget behåller de IP-adresserna, härs vad måste företaget göra:


1. Skapa Azure-nätverk som virtuella Azure-datorer kommer att skapas efter redundans för lokala datorer. Det bör vara ett tillägg till det lokala nätverket, så att program kan växla över sömlöst.
2. Före redundans i Site Recovery kan tilldela de samma IP-adress i egenskaperna för datorn. Efter en redundansväxling tilldelar Webbplatsåterställning den här adressen Azure-datorn.
3. När redundansen körs och virtuella Azure-datorer skapas med samma IP-adress kan de ansluta till nätverket med hjälp av en [anslutning mellan virtuella nätverk](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Den här åtgärden kan skriptas.
4. De måste du ändra vägar för att återspegla den 192.168.1.0/24 har nu flyttats till Azure.


**Infrastruktur efter redundans**

![Efter en redundansväxling för undernät](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Plats-till-plats-anslutning

Förutom vnet-till-vnet-anslutning efter redundansväxlingen kan Woodgrove ställa in plats-till-plats VPN-anslutningen:
- När du konfigurerar en plats-till-plats-anslutning i Azure-nätverket du bara kan vidarebefordra skiljer trafik till den lokala platsen (lokala nätverk) om IP-adressintervall sig från den lokala IP-adressintervall. Det beror på att Azure inte stöder sträckt undernät. Så om du har undernät 192.168.1.0/24 lokalt kan du inte lägga till ett lokalt nätverk 192.168.1.0/24 i Azure-nätverket. Detta är förväntat eftersom Azure inte vet att det inte finns några aktiva virtuella datorer i undernätet och att undernätet skapas för disaster recovery endast.
- Om du vill kunna korrekt dirigera nätverkstrafik från ett Azure-nätverk, undernät i nätverket och det lokala nätverket får inte stå i konflikt.




## <a name="assigning-new-ip-addresses"></a>Tilldela nya IP-adresser

Detta [blogginlägget](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) förklarar hur du ställer in nätverksinfrastrukturen i Azure när du inte behöver behålla IP-adresser efter redundansväxling. Det börjar med en beskrivning av programmet, tittar på hur du ställer in nätverk både lokalt och i Azure och avslutas med information om att köra redundansväxlingar.

## <a name="next-steps"></a>Nästa steg
[Köra en redundans](site-recovery-failover.md)
