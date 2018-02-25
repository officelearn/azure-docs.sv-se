---
title: Azure Traffic Manager med Azure Site Recovery | Microsoft Docs
description: "Beskriver hur du använder Azure Traffic Manager med Azure Site Recovery för migrering och katastrofåterställning"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: manayar
ms.openlocfilehash: e0790bbb9138f3f8b4c72a8210878006192092bf
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager med Azure Site Recovery

Azure Traffic Manager kan du styra distributionen av trafik över dina slutpunkter för programmet. En slutpunkt är alla mot Internet-tjänst som finns i eller utanför Azure.

Traffic Manager använder Domain Name System (DNS) för att dirigera klientbegäranden till den lämpligaste slutpunkten baserat på en metod för routning av nätverkstrafik och hälsotillståndet för slutpunkter. Traffic Manager erbjuder en uppsättning [routning av nätverkstrafik metoder](../traffic-manager/traffic-manager-routing-methods.md) och [endpoint övervakningsalternativ](../traffic-manager/traffic-manager-monitoring.md) efter programbehov för olika och automatisk redundans modeller. Klienterna ansluter direkt till den valda slutpunkten. Traffic Manager är inte en proxy eller gateway och den kan inte se trafik som skickas mellan klienten och tjänsten.

Den här artikeln beskriver hur du kan kombinera Azure trafik övervakaren intelligent routning med Azure Site Recovery kraftfulla katastrofåterställning och migreringsfunktioner.

## <a name="on-premises-to-azure-failover"></a>Lokal till Azure-redundans

Överväg att för det första scenariot **företag A** som har alla dess infrastruktur körs i sin lokala miljö. För verksamhetskontinuitet och efterlevnad affärsskäl, **företag A** bestämmer sig för att använda Azure Site Recovery för att skydda sina program.

**Företag A** program körs med offentliga slutpunkter och vill sömlöst omdirigera trafik till Azure i en händelse för katastrofåterställning. Den [prioritet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) routning av nätverkstrafik metoden i Azure Traffic Manager kan företag A att enkelt implementera det här mönstret för växling vid fel.

Installationsprogrammet är följande:
- **Företag A** skapar en [trafikhanterarprofil](../traffic-manager/traffic-manager-create-profile.md).
- Använder den **prioritet** routningsmetoden, **företag A** skapar två slutpunkter – **primära** för lokalt och **redundans** för Azure. **Primär** är tilldelad prioritet 1 och **redundans** är tilldelad prioritet 2.
- Eftersom den **primära** slutpunkt ligger utanför Azure, slutpunkten har skapats som en [externa](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) slutpunkt.
- Med Azure Site Recovery har på Azure site inte några virtuella datorer eller program som körs före redundans. Så den **redundans** slutpunkt skapas också som en **externa** slutpunkt.
- Som standard omdirigeras användartrafik till lokala program eftersom denna slutpunkt har högst prioritet som är kopplade till den. Ingen trafik dirigeras till Azure om den **primära** slutpunkten är felfri.

![På-lokal-till-Azure före redundans](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

I en katastrof händelse företag A kan utlösa en [redundans](site-recovery-failover.md) till Azure och återställa dess program på Azure. När Azure Traffic Manager upptäcker att den **primära** slutpunkten inte längre är felfri, används automatiskt den **redundans** slutpunkt i DNS-svar och användare ansluta till programmet återställas på Azure.

![På-lokal-till-Azure efter växling vid fel](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Beroende på företagets krav, **företag A** kan välja en högre eller lägre [sökning frekvens](../traffic-manager/traffic-manager-monitoring.md) växla mellan lokalt till Azure i en händelse för katastrofåterställning och se till att minimal avbrottstid för användare.

Inneslutna haveriet **företag A** kan återställning från Azure till sin lokala miljö ([VMware](site-recovery-how-to-failback-azure-to-vmware.md) eller [Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md)) med hjälp av Azure Site Recovery. Nu när Traffic Manager upptäcker att den **primära** slutpunkten är felfri igen, det automatiskt använder den **primära** slutpunkt i dess DNS-svar.

## <a name="on-premises-to-azure-migration"></a>Lokalt till Azure-migreringen

Förutom katastrofåterställning, Azure Site Recovery kan också [migrering till Azure](site-recovery-migrate-to-azure.md). Med funktioner för Azure Site Recovery kraftfulla testa redundans bedöma kunder programprestanda på Azure utan att påverka deras lokala miljö. Och när kunder är redo att migrera, kan de välja att migrera hela arbetsbelastningar tillsammans eller välja att migrera och skala gradvis.

Azure Traffic Manager [viktat](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) routningsmetoden kan användas för att styra vissa delar av inkommande trafik till Azure när dirigera flesta till den lokala miljön. Den här metoden kan hjälpa dig att bedöma skala prestanda som du kan fortsätta att öka vikten som tilldelats Azure när du migrerar mer och mer av dina arbetsbelastningar till Azure.

Till exempel **företag B** väljer att migrera i faser, flytta några av dess Programmiljö samtidigt som företaget behåller den rest lokalt. Under de första stegen när de flesta av miljön är lokalt, tilldelas en större vikt till lokala miljö. Traffic manager returnerar en slutpunkt som baseras på viktningar som tilldelats tillgängliga slutpunkter.

![På lokal-till-Azure-migrering](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Under migreringen, båda slutpunkterna är aktiva och de flesta av trafiken dirigeras till den lokala miljön. Medan migreringen utförs en större vikt kan tilldelas till slutpunkten på Azure och slutligen den lokala slutpunkten kan vara inaktiverade efter migreringen.

## <a name="azure-to-azure-failover"></a>Azure till Azure-redundans

Det här exemplet överväga **företagets C** som har alla dess program i infrastrukturen med Azure. För verksamhetskontinuitet och efterlevnad affärsskäl, **företagets C** bestämmer sig för att använda Azure Site Recovery för att skydda sina program.

**Företag C** program körs med offentliga slutpunkter och vill kunna sömlöst omdirigera trafik till en annan Azure region i en händelse för katastrofåterställning. Den [prioritet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) routning av nätverkstrafik metoden kan **företagets C** enkelt implementera det här mönstret för växling vid fel.

Installationsprogrammet är följande:
- **Företag C** skapar en [trafikhanterarprofil](../traffic-manager/traffic-manager-create-profile.md).
- Använder den **prioritet** routningsmetoden, **företagets C** skapar två slutpunkter – **primära** för källa region (Azure Östasien) och **redundans** för återställning region (Azure Sydostasien). **Primär** är tilldelad prioritet 1 och **redundans** är tilldelad prioritet 2.
- Eftersom den **primära** slutpunkt finns i Azure, slutpunkten kan fungera som en [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) slutpunkt.
- Med Azure Site Recovery har återställningen Azure site inte några virtuella datorer eller program som körs före redundans. Så den **redundans** endpoint kan skapas som ett [externa](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) slutpunkt.
- Som standard omdirigeras användartrafik till programmet region (Östasien) som denna slutpunkt har högst prioritet som är kopplade till den. Ingen trafik dirigeras till regionen som recovery om den **primära** slutpunkten är felfri.

![Azure till Azure före redundans](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

I en katastrof händelse **företagets C** kan utlösa en [redundans](azure-to-azure-tutorial-failover-failback.md) och återställa dess program på Azure-region för återställning. När Azure Traffic Manager upptäcker att den primära slutpunkten inte längre är felfri, automatiskt används den **redundans** slutpunkt i DNS-svar och användare ansluta till programmet återställas vid återställning Azure-region ( Sydöstra Asien).

![Azure till Azure efter växling vid fel](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Beroende på företagets krav, **företagets C** kan välja en högre eller lägre [sökning frekvens](../traffic-manager/traffic-manager-monitoring.md) växla mellan käll- och regioner och se till att minimal avbrottstid för användare.

Inneslutna haveriet **företagets C** kan återställning efter fel från Azure-region som recovery käll-Azure-region med hjälp av Azure Site Recovery. Nu när Traffic Manager upptäcker att den **primära** slutpunkten är felfri igen, det automatiskt använder den **primära** slutpunkt i dess DNS-svar.

## <a name="protecting-multi-region-enterprise-applications"></a>Skydda flera regioner företagsprogram

Globala företag förbättra ofta kundupplevelsen genom att skräddarsy sina program för att hantera regionala behov. Lokalisering och minskar latens och kan leda till program i infrastrukturen dela över regioner. Företag som är bundna av regionala lagar i vissa områden och välja att isolera en del sina program i infrastrukturen i regionala gränser.  

Nu ska vi titta ett exempel där **företagets D** har delats upp dess programslutpunkter så att den stöder separat Tyskland och resten av världen. **Företag D** använder Azure Traffic Manager [geografiska](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) routningsmetoden för att ställa in. All trafik som kommer från Tyskland omdirigeras till **slutpunkt 1** och all trafik med ursprung utanför Tyskland dirigeras till **slutpunkt 2**.

Problemet med den här installationen är att om **slutpunkt 1** slutar fungera av någon anledning, det finns inga omdirigering av trafik till **slutpunkt 2**. Trafik från Tyskland fortsätter att dirigeras till **slutpunkt 1** oavsett hälsotillståndet för slutpunkten, lämnar du tyska användare utan åtkomst till **företagets D**'s program. På liknande sätt, om **slutpunkt 2** offline, går det inte finns några omdirigering av trafik till **slutpunkt 1**.

![Flera regioner programmet innan](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Undvik detta problem och kontrollera att programmet återhämtning **företagets D** använder [kapslade Traffic Manager-profiler](../traffic-manager/traffic-manager-nested-profiles.md) med Azure Site Recovery. I en kapslad profilinställningarna dirigeras trafik inte till enskilda slutpunkter, utan att andra Traffic Manager-profiler. Här är hur den här installationen fungerar:
- I stället för att använda geografiska routning med enskilda slutpunkter **företagets D** använder geografiska routning med Traffic Manager-profiler.
- Varje underordnad trafikhanterarprofil använder **prioritet** routning med en primär och en slutpunkt för återställning, därför kapsla **prioritet** routning inom **geografiska** routning.
- Om du vill aktivera programmet använder varje distribution av arbetsbelastning Azure Site Recovery ska gå över till en återställning region baserat vid en katastrof-händelse.
- När överordnat Traffic Manager tar emot en DNS-fråga, omdirigeras den till den relevanta underordnat Traffic Manager som svarar på frågan med en tillgänglig slutpunkt.

![Flera regioner program efter](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Till exempel om slutpunkten i Tyskland Central misslyckas, kan programmet snabbt återställas till Tyskland nordöst. Ny slutpunkt hanterar trafik från Tyskland med minimal avbrottstid för användare. På samma sätt kan ett avbrott för slutpunkten i västra Europa hanteras av återställa programmet arbetsbelastningen till Nordeuropa, med Azure Traffic Manager-hantering DNS som omdirigerar till tillgängliga slutpunkten.

Inställningarna ovan kan utökas med så många region och slutpunkten kombinationer som krävs. Traffic Manager kan upp till 10 nivåer av kapslade profiler och tillåts inte slingor i kapslade konfigurationen.

## <a name="recovery-time-objective-rto-considerations"></a>Tid mål för Återställningstid överväganden

I de flesta organisationer hanteras att lägga till eller ändra DNS-poster av ett separat team eller av någon utanför organisationen. Detta gör att ändra DNS-poster som är mycket svårt. Den tid det tar att uppdatera DNS-poster med andra team eller organisationer som hanterar DNS-infrastruktur varierar mellan olika företag och påverkar Återställningstidsmålet av programmet.

Genom att använda Traffic Manager kan du frontload arbetet som krävs för DNS-uppdateringar. Ingen manuell eller skriptad åtgärd krävs vid tidpunkten för faktiska växling vid fel. Den här metoden kan i Snabbväxling (och därför sänker RTO) samt undvika kostsamma tidskrävande DNS-ändringen fel i en händelse för katastrofåterställning. Med Traffic Manager även steg för återställning efter fel är automatisk, som annars skulle behöva hanteras separat.

Ange rätt [sökning intervall](../traffic-manager/traffic-manager-monitoring.md) via enkel eller snabb intervall hälsa kan kontroller betydligt avslutar Återställningstidsmålet under växling vid fel och minska avbrottstiden för användare.

Dessutom kan du optimera DNS Time to Live (TTL) värdet för Traffic Manager-profilen. TTL-värde är det värde som en DNS-post ska cachelagras av en klient. För en post skulle DNS inte efterfrågas två gånger inom loppet av TTL-värde. Varje DNS-posten har ett TTL-värde som är kopplade till den. Minska det här värdet resulterar i flera DNS-frågor i Traffic Manager men kan minska RTO genom att identifiera avbrott snabbare.

TTL-värdet som klienten har erfarit också ökar inte om du ökar antalet DNS-matchare mellan klienten och den auktoritära DNS-servern. DNS-matchare 'nedräkningen' TTL-värdet och bara vidarebefordra ett TTL-värde som motsvarar tid som gått sedan posten cachelagrades. Detta säkerställer att DNS-posten hämtar uppdateras på klienten när TTL, oavsett antalet DNS-matchare i kedjan.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om Traffic Manager [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).
- Lär dig mer om [kapslade Traffic Manager-profiler](../traffic-manager/traffic-manager-nested-profiles.md).
- Lär dig mer om [slutpunktsövervakning](../traffic-manager/traffic-manager-monitoring.md).
- Lär dig mer om [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans för programmet.
