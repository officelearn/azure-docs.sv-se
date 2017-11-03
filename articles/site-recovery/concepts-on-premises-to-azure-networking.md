---
title: "Nätverk för VM-anslutning efter en redundansväxling till Azure med Azure Site Recovery | Microsoft Docs"
description: "Nätverk vägledning för att ansluta till virtuella Azure-datorer efter redundans från lokala med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: f02cdbea-0940-48bf-9fa5-f38d9e584fae
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/12/2017
ms.author: raynew
ms.openlocfilehash: 01c8e664465350b9dd382502c65cc3fda350797c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="networking-for-vm-connectivity-after-failover"></a>Nätverk för VM-anslutning efter växling vid fel

Den här artikeln förklarar nätverkskrav för att ansluta till virtuella Azure-datorer när du använder den [Azure Site Recovery](site-recovery-overview.md) tjänsten för replikering och redundans till Azure.

I den här artikeln lär du dig hur:

> [!div class="checklist"]
> * Anslutningsmetoder som du kan använda
> * Hur du använder en annan IP-adress för replikerade virtuella Azure-datorer
> * Hur du bevara IP-adresser för virtuella Azure-datorer efter redundans

## <a name="connecting-to-replica-vms"></a>Ansluter till replik virtuella datorer

När du planerar din replikering och redundansstrategi, är en av viktiga frågor hur du ansluter till den virtuella Azure-datorn efter redundans. Det finns ett par alternativ när du utformar din strategi för nätverket för replik virtuella Azure-datorer:

- **Använd en annan IP-adress**: du kan välja för att använda en annan IP-adressintervall för replikerade Virtuella Azure-nätverket. I det här scenariot den virtuella datorn får en ny IP-adress efter växling vid fel och en DNS-uppdatering krävs.
- **Behålla samma IP-adress**: du kanske vill använda samma IP-adressintervall som på din primär lokal plats för Azure-nätverket efter redundans. Behålla samma IP adresser förenklar återställning genom att minska nätverksrelaterade problem efter växling vid fel. Men när du replikerar till Azure måste uppdatera vägar med den nya platsen för IP-adresser efter växling vid fel. 

## <a name="retaining-ip-addresses"></a>Behålla IP-adresser

Site Recovery tillhandahåller möjligheten att behålla fasta IP-adresser när du redundansväxlar till Azure, med ett undernät redundans.

- Med undernätet redundans finns ett specifikt undernät på plats 1 eller 2 för platsen, men aldrig på båda platser samtidigt.
- För att upprätthålla IP-adressutrymme vid en växling vid fel, ordna du programmässigt router-infrastruktur att flytta undernäten från en plats till en annan.
- Under växling vid fel flytta undernäten med de associera skyddade virtuella datorerna. Den största nackdelen är att vid fel, måste du flytta hela undernätet.


### <a name="failover-example"></a>Exempel för växling vid fel

Nu ska vi titta på ett exempel för redundans till Azure usng ett ficticious företag, Woodgrove Bank.

- Woodgrove Bank är värd för de affärsappar i en lokal plats. De värd för sin mobila appar på Azure.
- Det finns en VPN plats-till-plats-anslutning mellan sina lokala edge nätverk och virtuella Azure-nätverket. På grund av VPN-anslutningen visas det virtuella nätverket i Azure som ett tillägg till det lokala nätverket.
- Woodgrove vill replikera lokala arbetsbelastningar till Azure med Site Recovery.
 - Woodgrove har appar som är beroende av hårdkodade IP-adresser så att de behöver för att behålla IP-adresser för appar, efter en redundansväxling till Azure.
 - Resurser som körs i Azure använder IP-adressintervallet 172.16.1.0/24 172.16.2.0/24.

![Innan du undernät redundans](./media/site-recovery-network-design/network-design7.png)

**Infrastruktur före redundans**


För Woodgrove ska kunna behöver replikera dess virtuella datorer till Azure samtidigt som de IP-adresserna, härs vad företaget göra:


1. Skapa Azure-nätverk som virtuella Azure-datorer kommer att skapas efter växling vid fel för lokala datorer. Det bör vara ett tillägg till det lokala nätverket, så att program kan växla över sömlöst.
2. Innan du redundans i Site Recovery tilldela de samma IP-adress i egenskaperna för datorn. Efter växling vid fel tilldelar Site Recovery den här adressen Azure VM.
3. När redundans körs och virtuella Azure-datorer skapas med samma IP-adress, de ansluter till nätverket med en [Vnet Vnet-anslutningen till](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Den här åtgärden kan skriptas.
4. De måste du ändra vägar för att återspegla den 192.168.1.0/24 har nu flyttats till Azure.


**Infrastrukturen efter växling vid fel**

![Efter undernät redundans](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Plats-till-plats-anslutning

Förutom vnet-till-vnet-anslutningen efter växling vid fel, kan Woodgrove ställa in plats-till-plats VPN-anslutningen:
- När du ställer in en plats-till-plats-anslutning i Azure-nätverket kan du bara vidarebefordra skiljer trafik till den lokala platsen (lokal ntwork) om IP-adressintervall sig från det lokala IP-adressintervallet. Det beror på att Azure inte stöder sträckta undernät. Så om du har undernät 192.168.1.0/24 lokalt kan du inte lägga till 192.168.1.0/24 ett lokalt nätverk i Azure-nätverk. Detta är förväntat eftersom Azure inte vet att det inte finns några aktiva virtuella datorer i undernät och undernätet skapas för endast katastrofåterställning.
- Om du vill kunna korrekt dirigera nätverkstrafik utanför ett Azure-nätverk av undernät i nätverket och det lokala nätverket får inte står i konflikt.




## <a name="assigning-new-ip-addresses"></a>Tilldela nya IP-adresser

Detta [blogginlägget](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) förklarar hur du ställer in Azure nätverksinfrastrukturen när du inte behöver behålla IP-adresser efter växling vid fel. Den börjar med en beskrivning av programmet, ta en titt på hur du ställer in nätverk lokalt och i Azure och avslutas med information om att köra redundansväxlingar. 

## <a name="next-steps"></a>Nästa steg
[Kör en redundansväxling](site-recovery-failover.md)




