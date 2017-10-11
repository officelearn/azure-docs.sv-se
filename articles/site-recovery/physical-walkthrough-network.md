---
title: "Planera nätverk för fysisk serverreplikering till Azure | Microsoft Docs"
description: "Den här artikeln beskrivs planering av krävs när du replikerar fysiska servrar till Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 71db2435-b5ce-4263-83f6-093d10d1d4e1
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: f8a20b45b50f71631122e574b634818c1912f12e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-4-plan-networking-for-physical-server-replication-to-azure"></a>Steg 4: Planera nätverk för fysisk serverreplikering till Azure

Den här artikeln sammanfattar nätverket några saker att tänka på när du replikera lokala fysiska servrar till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="connect-to-replica-azure-vms"></a>Anslut till replik virtuella Azure-datorer

När du planerar din replikering och redundansstrategi, är en av viktiga frågor hur du ansluter till den virtuella Azure-datorn efter redundans. Det finns ett par alternativ när du utformar din strategi för nätverket för replik virtuella Azure-datorer:

- **Använd en annan IP-adress**: du kan välja för att använda en annan IP-adressintervall för replikerade Virtuella Azure-nätverket. I det här fallet hämtar datorn en ny IP-adress efter växling vid fel och en DNS-uppdatering krävs.
- **Använd samma IP-adress**: du kanske vill använda samma IP-adressintervall som på din primär lokal plats för Azure-nätverket efter redundans. Behålla samma IP adresser förenklar återställning genom att minska nätverksrelaterade problem efter växling vid fel. Men när du replikerar till Azure måste uppdatera vägar med den nya platsen för IP-adresser efter växling vid fel.

## <a name="retain-ip-addresses"></a>Behåll IP-adresser

Site Recovery tillhandahåller möjligheten att behålla fasta IP-adresser när du redundansväxlar till Azure, med ett undernät redundans.
Med undernätet redundans finns ett specifikt undernät på plats 1 eller 2 för platsen, men aldrig på båda platser samtidigt. För att upprätthålla IP-adressutrymme vid en växling vid fel, ordna du programmässigt router-infrastruktur att flytta undernäten från en plats till en annan. Under växling vid fel flytta undernäten med de associera skyddade virtuella datorerna. Den största nackdelen är att vid fel, måste du flytta hela undernätet.

### <a name="failover-example"></a>Exempel för växling vid fel

Nu ska vi titta på ett exempel för redundans till Azure.

- Ett ficticious företag, Woodgrove Bank har en lokal infrastruktur som är värd för sina appar. Sina mobila program finns på Azure.
- Anslutningen mellan Woodgrove Bank virtuella datorer i Azure och lokala servrar som en plats-till-plats (VPN)-anslutning mellan lokala edge nätverk och virtuella Azure-nätverket.
- Den här VPN innebär att företagets virtuella nätverk i Azure visas som ett tillägg för sina lokala nätverk.
- Woodgrove vill använda Site Recovery replikera lokala arbetsbelastningar till Azure.
 - Woodgrove måste hantera program och konfigurationer som är beroende av hårdkodade IP-adresser och måste därför behålla IP-adresser för sina program efter en redundansväxling till Azure.
 - Woodgrove har tilldelats IP-adresser från intervallet 172.16.1.0/24, 172.16.2.0/24 till dess resurser som körs i Azure.


För Woodgrove ska kunna behöver replikera sina servrar till Azure samtidigt som de IP-adresserna, härs vad företaget göra:

1. Skapa ett virtuellt Azure-nätverk. Det bör vara ett tillägg till det lokala nätverket, så att program kan växla över sömlöst.
2. Azure kan du lägga till plats-till-plats VPN-anslutning förutom punkt-till-plats-anslutning till virtuella nätverk som skapats i Azure.
3. När du ställer in plats-till-plats-anslutningen i Azure-nätverk kan du dirigera trafik till lokal plats (lokalt nätverk) bara om IP-adressintervallet skiljer sig från det lokala IP-adressintervallet.
    - Det beror på att Azure inte stöder sträckta undernät. Så om du har undernät 192.168.1.0/24 lokalt kan du inte lägga till 192.168.1.0/24 ett lokalt nätverk i Azure-nätverk.
    - Detta är förväntat eftersom Azure inte vet att det inte finns några aktiva datorer i undernät och undernätet skapas för endast katastrofåterställning.
    - Om du vill kunna korrekt undernät i nätverket och det lokala nätverket dirigera nätverkstrafik utanför ett Azure-nätverk får inte är i konflikt.

![Innan du undernät redundans](./media/physical-walkthrough-network/network-design7.png)

#### <a name="before-failover"></a>Innan du redundans

1. Skapa ytterligare ett nätverk (till exempel återställning nätverk). Det här är nätverket som redundansväxlade virtuella datorer skapas.
2. Att se till att IP-adressen för en dator finns kvar efter en redundansväxling i egenskaperna för datorn > **konfigurera**, ange samma IP-adress att servern har lokalt och på **spara**.
3. När datorn har redundansväxlats tilldelas den angivna IP-adressen till den i Azure Site Recovery.
4. När redundansväxlingen är utlösare utlöses och de virtuella datorerna ska skapas i Azure med nödvändiga IP-adress, du kan ansluta till nätverket med en [Vnet Vnet-anslutningen till](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Den här åtgärden kan skriptas.
5. Vägar behöver ändras på lämpligt sätt så att den återger den 192.168.1.0/24 har nu flyttats till Azure.

    ![Efter undernät redundans](./media/physical-walkthrough-network/network-design9.png)

#### <a name="after-failover"></a>Efter växling vid fel

Om du inte har ett Azure-nätverk som visas ovan, kan du skapa en plats-till-plats VPN-anslutning mellan den primära platsen och Azure, efter växling vid fel.

## <a name="change-ip-addresses"></a>Ändra IP-adresser

Detta [blogginlägget](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) förklarar hur du ställer in Azure nätverksinfrastrukturen när du inte behöver behålla IP-adresser efter växling vid fel. Den börjar med en beskrivning av programmet, ta en titt på hur du ställer in nätverk lokalt och i Azure och avslutas med information om att köra redundansväxlingar.  

## <a name="next-steps"></a>Nästa steg

Gå till [steg 5: Förbered Azure](physical-walkthrough-prepare-azure.md)
