---
title: "Tillgänglighet för SAP HANA över Azure-regioner | Microsoft Docs"
description: "Innehåller en översikt över för tillgänglighet överväganden när du kör SPA HANA på Azure Virtual Machines"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Tillgänglighet för SAP HANA över Azure-regioner
I den här artikeln beskrivs och beskrivs scenarier runt SAP HANA tillgänglighet över olika Azure-regioner. Med tanke på att olika Azure-regioner har större avståndet mellan dem, finns det speciella överväganden som anges i den här artikeln.

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>Skälet till att distribuera över flera Azure-regioner
Olika Azure-regioner avgränsas med ett större avstånd. Beroende på geopolitiska region, detta kan vara hundratals mil eller även flera tusen miles som i USA. På grund av avståndet mellan olika Azure-regioner nätverkstrafik mellan tillgångar som distribuerats i två olika Azure-regioner upplevelser betydande roundtrip Nätverksfördröjningen. Betydande tillräckligt för att utesluta synkront datautbyte mellan två instanser av SAP HANA under normal SAP belastning. På den andra sidan står du ofta inför det faktum att det finns definierade krav på avståndet mellan ditt primära datacenter och ett sekundärt datacenter för att ge tillgänglighet vid naturkatastrof träffa ett större område. Till exempel orkaner nådde som området Västindien och Florida i September och oktober 2017. Eller minst ett krav på minsta avstånd. I de flesta fall kunden definitionen för denna minsta avstånd som kräver att du att utforma för hög tillgänglighet över [Azure-regioner](https://azure.microsoft.com/regions/). Eftersom avståndet är för stort mellan kan två Azure-regioner att använda synkron replikeringsläge HANA, krav för RTO och Återställningspunktmål tvinga dig att distribuera tillgänglighet konfigurationer i en region och sedan komplettera med ytterligare distributioner på en sekund region.

En annan aspekt beaktas när dessa konfigurationer är växling vid fel och omdirigerings-klienten. Förutsättningen är att växling mellan SAP HANA-instanser i två olika Azure-regioner alltid är en manuell växling vid fel. Eftersom replikering läget för SAP HANA System replikering är satt asynkrona, finns det risk att data i den primära HANA-instansen inte har gjort det ännu till den sekundära HANA-instansen. Automatisk redundans kan därför inte accepteras för konfigurationer där den replikeras asynkront. Du måste vidta åtgärder för att se till att alla data som utförts på den primära sidan gjorde det till den sekundära instansen innan du flyttar manuellt till andra Azure-regionen även med manuell kontrollerade redundans, som i ett failover-övningen.
 
Eftersom du använder med en annan IP-adressintervall i Azure-Vnet, som har distribuerats i andra Azure-regionen, SAP HANA klienter antingen behöver ändras i konfigurationen eller sätt bättre måste du placera steg för att ändra namnmatchningen. Så att klienterna ska komma omdirigeras till den nya sekundära datorns IP-adress. SAP-artikel på [klienten anslutning återställning efter övertag](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) blir mer information.   

## <a name="simple-availability-between-two-azure-regions"></a>Enkel tillgänglighet mellan två Azure-regioner
I det här scenariot valt du inte att upprätta någon konfiguration för tillgänglighet inom en enskild region. Men du har behovet att arbetsbelastningen hanteras vid en katastrof. Typiska fall då man system som som är icke-system. Även om du kan klara om du vill att systemet ned för en halv dag eller ännu en dag, kan du låta systemet inte vara tillgängliga 48 timmar eller mer. För att göra installationen mindre kostsam, du kör ett annat system som är även mindre viktiga med den virtuella datorn som fungerar som ett mål eller ändra storlek på den virtuella datorn i sekundära regionen som är mindre och väljer att inte före läsa in data. Eftersom växling vid fel ska vara manuell och innebär att många fler steg att redundansväxla den hela appen-stacken, tillåts mer tid att ta ner den virtuella datorn, ändra storlek på den och starta den virtuella datorn igen.

> [!NOTE]
> Även om du inte före datainläsning i HANA System replikeringsmål, behöver du minst 64 GB minne och utöver det tillräckligt med minnet för att skydda rowstore data i minnet på målinstansen.

![Två virtuella datorer över två områden](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> I den här konfigurationen kan du ange ett Återställningpunktsmål = 0 eftersom HANA System Replication-läge är asynkron. Om du måste ange ett Återställningpunktsmål = 0, den här konfigurationen är inte konfigurationen av valet.

En mindre ändring i konfigurationen är att konfigurera förinläsning av data. Men eftersom manuell växling vid fel och det faktum att programmet lager måste flytta till den andra regionen, det inte kan vara klokt att i förväg ladda data. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinera tillgänglighet inom en region och över regioner 
En kombination av tillgänglighet inom och över regioner kan drivas av:

- Kravet för Återställningspunktmål = 0 i en Azure-Region.
- Inte vill eller kan ha globala åtgärder på företaget som påverkas av en större fysiska allvarlig händelse som påverkar ett större område. Det skett av vissa orkaner som träffar Västindien under de senaste åren.
- Förordningar som begäran sträckor mellan primär och sekundär plats som är tydligt utöver vad Azure tillgänglighet zoner kan ge.

 
I sådana fall måste du konfigurera vilka SAP-anrop en [replikeringskonfiguration för SAP HANA Multitier System](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) med HANA System replikering. Arkitekturen ser ut som:

![tre virtuella datorer över två områden](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Den här konfigurationen tillhandahåller ett Återställningpunktsmål = 0 med små RTO gånger inom den primära regionen och dessutom ger fallande Återställningspunktmål vid en flytta över till den andra regionen. Återställningstidsmålet gånger i andra region är beroende av om du konfigurerar före datainläsning eller inte. I många fall för kunden används den virtuella datorn i den sekundära regionen för att köra ett testsystem. På grund av att användning av kan data inte före laddas.

> [!NOTE]
> Eftersom du använder arbetsläge logreplay för HANA System replikering ska nivå 2 (synkron replikering på primär region) från nivå 1, får inte replikering mellan nivå 2 och nivå 3 (replikering till sekundär plats) vara i delta_datashipping åtgärd läge. Information om arbetslägen och vissa begränsningar som finns dokumenterade av SAP i [arbetslägen för SAP HANA System replikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Nästa steg
Om du behöver steg-för-steg-anvisningar om hur du ställer in en sådan konfiguration i Azure läser du i artiklar:

- [Konfigurera SAP HANA System Replication i virtuella Azure-datorer](sap-hana-high-availability.md)
- [Din SAP på Azure – del 4 – hög tillgänglighet för SAP HANA med System-replikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
