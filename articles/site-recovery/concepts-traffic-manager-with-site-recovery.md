---
title: Azure Traffic Manager med Azure Site Recovery | Microsoft-dokument
description: Beskriver hur du använder Azure Traffic Manager med Azure Site Recovery för haveriberedskap och migrering
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60947818"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager med Azure Site Recovery

Med Azure Traffic Manager kan du styra distributionen av trafik mellan programslutpunkter. En slutpunkt är en Internetansluten tjänst i eller utanför Azure.

Traffic Manager använder DNS (Domain Name System) för att dirigera klientbegäranden till den lämpligaste slutpunkten, baserat på en trafikroutningsmetod och slutpunkternas hälsotillstånd. Traffic Manager tillhandahåller en uppsättning [trafikdirigeringsmetoder](../traffic-manager/traffic-manager-routing-methods.md) och [alternativ för slutpunktsövervakning](../traffic-manager/traffic-manager-monitoring.md) som passar olika programbehov och modeller för automatisk redundansväxling. Klienterna ansluter direkt till den valda slutpunkten. Traffic Manager är inte en proxy eller en gateway, och den ser inte trafiken som passerar mellan klienten och tjänsten.

I den här artikeln beskrivs hur du kan kombinera Azure Traffic Monitors intelligenta routning med Azure Site Recoverys kraftfulla funktioner för haveriberedskap och migrering.

## <a name="on-premises-to-azure-failover"></a>Lokal till Azure-redundans

I det första scenariot bör du tänka på **företag A** som har all sin programinfrastruktur som körs i sin lokala miljö. Av affärskontinuitets- och efterlevnadsskäl bestämmer **sig företag A** för att använda Azure Site Recovery för att skydda sina program.

**Företag A** kör program med offentliga slutpunkter och vill ha möjlighet att sömlöst omdirigera trafik till Azure i en katastrofhändelse. Metoden [Prioriterad](../traffic-manager/traffic-manager-configure-priority-routing-method.md) trafikdirigering i Azure Traffic Manager gör det möjligt för företag A att enkelt implementera det här redundansmönstret.

Inställningen är följande:
- **Företag A** skapar en [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Med hjälp av metoden Prioritetsdirigering skapar **företag A** två slutpunkter – **Primär** för lokala och **redundans** för Azure. **Priority** **Primär** tilldelas Prioritet 1 och **Redundans** tilldelas Prioritet 2.
- Eftersom den **primära** slutpunkten finns utanför Azure skapas slutpunkten som en [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) slutpunkt.
- Med Azure Site Recovery har Azure-platsen inga virtuella datorer eller program som körs före redundans. Slutpunkten **för redundans** skapas därför också som en **extern** slutpunkt.
- Som standard dirigeras användartrafik till det lokala programmet eftersom slutpunkten har högsta prioritet. Ingen trafik dirigeras till Azure om den **primära** slutpunkten är felfri.

![Lokalt-till-Azure före redundans](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

I en katastrofhändelse kan företag A utlösa en [redundans](site-recovery-failover.md) till Azure och återställa sina program på Azure. När Azure Traffic Manager upptäcker att den **primära** slutpunkten inte längre är felfri, använder den automatiskt **redundansslutpunkten** i DNS-svaret och användare ansluter till programmet som återställs på Azure.

![Lokalt-till-Azure efter redundans](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Beroende på affärskrav kan **företag A** välja en högre eller lägre [utplaningsfrekvens](../traffic-manager/traffic-manager-monitoring.md) för att växla mellan lokalt till Azure i en katastrofhändelse och säkerställa minimal stilleståndstid för användare.

När katastrofen innesluts kan **företag A** återställas från Azure till sin lokala miljö ([VMware](vmware-azure-failback.md) eller [Hyper-V)](hyper-v-azure-failback.md)med Hjälp av Azure Site Recovery. Nu, när Traffic Manager upptäcker att den **primära** slutpunkten är felfri igen, använder den automatiskt den **primära** slutpunkten i sina DNS-svar.

## <a name="on-premises-to-azure-migration"></a>Lokal till Azure-migrering

Förutom haveriberedskap möjliggör Azure Site Recovery också [migreringar till Azure](migrate-overview.md). Med hjälp av Azure Site Recoverys kraftfulla testväxlingsfunktioner kan kunder bedöma programprestanda på Azure utan att påverka deras lokala miljö. Och när kunderna är redo att migrera kan de välja att migrera hela arbetsbelastningar tillsammans eller välja att migrera och skala gradvis.

Azure Traffic Manager's [Weighted](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) routing metod kan användas för att styra en del av inkommande trafik till Azure samtidigt styra majoriteten till den lokala miljön. Den här metoden kan hjälpa till att bedöma skalprestanda eftersom du kan fortsätta att öka vikten som tilldelats Azure när du migrerar mer och mer av dina arbetsbelastningar till Azure.

**Företag B** väljer till exempel att migrera i faser, flytta en del av sin programmiljö samtidigt som resten behålls lokalt. Under de inledande stadierna när större delen av miljön är lokal, tilldelas en större vikt till den lokala miljön. Traffic Manager returnerar en slutpunkt baserat på vikter som tilldelats tillgängliga slutpunkter.

![Lokal-till-Azure-migrering](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Under migreringen är båda slutpunkterna aktiva och större delen av trafiken dirigeras till den lokala miljön. När migreringen fortsätter kan en större vikt tilldelas slutpunkten på Azure och slutligen kan den lokala slutpunkten inaktiveras efter migreringen.

## <a name="azure-to-azure-failover"></a>Azure till Azure redundans

I det här exemplet bör du tänka på **företag C** som har all sin programinfrastruktur som kör Azure. Av affärskontinuitets- och efterlevnadsskäl bestämmer **sig företag C** för att använda Azure Site Recovery för att skydda sina program.

**Företag C** kör program med offentliga slutpunkter och vill ha möjlighet att sömlöst omdirigera trafik till en annan Azure-region i en katastrofhändelse. Med metoden [Prioriterad](../traffic-manager/traffic-manager-configure-priority-routing-method.md) trafikdirigering kan **företag C** enkelt implementera det här redundansmönstret.

Inställningen är följande:
- **Företag C** skapar en [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Med hjälp av metoden Prioritetsdirigering skapar **företag C** två slutpunkter – **Primär** för källregionen (Azure East Asia) och **Redundans** för återställningsregionen (Azure Sydostasien). **Priority** **Primär** tilldelas Prioritet 1 och **Redundans** tilldelas Prioritet 2.
- Eftersom den **primära** slutpunkten finns i Azure kan slutpunkten vara som en [Azure-slutpunkt.](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints)
- Med Azure Site Recovery har azure-platsen för återställning inte några virtuella datorer eller program som körs före redundans. Slutpunkten **för redundans** kan därför skapas som en [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) slutpunkt.
- Som standard dirigeras användartrafik till källregion (Östasien) eftersom slutpunkten har högsta prioritet. Ingen trafik dirigeras till återställningsregionen om den **primära** slutpunkten är felfri.

![Azure-till-Azure före redundans](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

I en katastrofhändelse kan **företag C** utlösa en [redundans](azure-to-azure-tutorial-failover-failback.md) och återställa sina program på Azure-regionen för återställning. När Azure Traffic Manager upptäcker att den primära slutpunkten inte längre är felfri, använder den automatiskt **redundansslutpunkten** i DNS-svaret och användare ansluter till programmet som återställs på återställnings-Azure-regionen (Sydostasien).

![Azure-till-Azure efter redundans](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Beroende på affärskrav kan **företag C** välja en högre eller lägre [utplaningsfrekvens](../traffic-manager/traffic-manager-monitoring.md) för att växla mellan käll- och återställningsregioner och säkerställa minimal stilleståndstid för användarna.

När katastrofen innesluts kan **företag C** återställas från Azure-regionen för återställning till azure-källregionen med Azure Site Recovery. Nu, när Traffic Manager upptäcker att den **primära** slutpunkten är felfri igen, använder den automatiskt den **primära** slutpunkten i sina DNS-svar.

## <a name="protecting-multi-region-enterprise-applications"></a>Skydda företagsprogram med flera regioner

Globala företag förbättrar ofta kundupplevelsen genom att skräddarsy sina applikationer för att tillgodose regionala behov. Lokalisering och reditminskning kan leda till att programinfrastrukturen delas upp mellan regioner. Företagen är också bundna av regionala datalagar inom vissa områden och väljer att isolera en del av sin applikationsinfrastruktur inom regionala gränser.  

Låt oss överväga ett exempel där **företag D** har delat sina programslutpunkter för att separat betjäna Tyskland och resten av världen. **Företag D** använder Azure Traffic Manager [geografiska](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) routningsmetod för att ställa in detta. All trafik från Tyskland dirigeras till **endpoint 1** och all trafik som kommer från tyskland dirigeras till **slutpunkt 2**.

Problemet med den här inställningen är att om **slutpunkt 1** slutar fungera av någon anledning, finns det ingen omdirigering av trafik till **slutpunkt 2**. Trafiken från Tyskland fortsätter att dirigeras till **endpoint 1** oavsett slutpunktens hälsa, vilket innebär att tyska användare inte har tillgång till **företag D:s**ansökan. Om **slutpunkt 2** kopplas från finns det ingen omdirigering av trafiken till **slutpunkt 1**.

![Program i flera regioner tidigare](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

För att undvika att stöta på det här problemet och säkerställa programåterhämtning använder **företag D** [kapslade Traffic Manager-profiler](../traffic-manager/traffic-manager-nested-profiles.md) med Azure Site Recovery. I en kapslad profilinställning dirigeras inte trafiken till enskilda slutpunkter, utan i stället till andra Traffic Manager-profiler. Så här fungerar den här inställningen:
- I stället för att använda geografisk routning med enskilda slutpunkter använder **företag D** Geografisk routning med Traffic Manager-profiler.
- Varje underordnad Traffic Manager-profil använder Prioritetsdirigering med en primär och en återställningsslutpunkt, därav kapsling **prioriterad** routning inom **geografisk** routning. **Priority**
- För att aktivera programåterhämtning använder varje arbetsbelastningsdistribution Azure Site Recovery för att redundans till en återställningsregion som baseras i händelse av en katastrofhändelse.
- När den överordnade Traffic Manager tar emot en DNS-fråga dirigeras den till relevant underordnad Trafikhanterare som svarar på frågan med en tillgänglig slutpunkt.

![Program i flera regioner efter](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Om slutpunkten i Tyskland Central till exempel misslyckas kan programmet snabbt återställas till Nordöstra Tyskland. Den nya slutpunkten hanterar trafik från Tyskland med minimal stilleståndstid för användare. På samma sätt kan ett slutpunktsstopp i Västeuropa hanteras genom att återställa programarbetsbelastningen till Norra Europa, med Azure Traffic Manager som hanterar DNS-omdirigeringar till den tillgängliga slutpunkten.

Ovanstående inställningar kan utökas så att den omfattar så många region- och slutpunktskombinationer som krävs. Traffic Manager tillåter upp till 10 nivåer av kapslade profiler och tillåter inte loopar inom den kapslade konfigurationen.

## <a name="recovery-time-objective-rto-considerations"></a>Överväganden om återställningstid (RTO)

I de flesta organisationer hanteras lägger till eller ändrar DNS-poster antingen av ett separat team eller av någon utanför organisationen. Detta gör uppgiften att ändra DNS-poster mycket utmanande. Den tid det tar att uppdatera DNS-poster av andra team eller organisationer som hanterar DNS-infrastruktur varierar från organisation till organisation och påverkar PROGRAMMETS RTO.

Genom att använda Traffic Manager kan du vidarebefordra det arbete som krävs för DNS-uppdateringar. Ingen manuell eller skriptåtgärd krävs vid tidpunkten för den faktiska redundansen. Den här metoden hjälper till att snabbt växla (och därmed sänka RTO) samt undvika kostsamma tidskrävande DNS-ändringsfel i en katastrofhändelse. Med Traffic Manager automatiseras även återställningssteget, vilket annars måste hanteras separat.

Om du ställer in rätt [avsökningsintervall](../traffic-manager/traffic-manager-monitoring.md) genom grundläggande eller snabba hälsokontroller kan rto-systemet minskas avsevärt och driftstoppet minskas för användarna.

Du kan dessutom optimera DNS Time to Live (TTL) för Traffic Manager-profilen. TTL är det värde som en DNS-post skulle cachelagras för av en klient. För en post skulle DNS inte efterfrågas två gånger inom TTL:s intervall. Varje DNS-post har en TTL associerad med den. Om du minskar det här värdet resulterar det i fler DNS-frågor till Traffic Manager, men kan minska RTO genom att upptäcka avbrott snabbare.

Den TTL som klienten upplever ökar inte heller om antalet DNS-resolvers mellan klienten och den auktoritära DNS-servern ökar. DNS-resolvers "räkna ner" TTL och bara vidarebefordra ett TTL-värde som återspeglar den förflutna tiden sedan posten cachelagrades. Detta säkerställer att DNS-posten uppdateras på klienten efter TTL, oavsett antalet DNS-resolvers i kedjan.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Routningsmetoder för](../traffic-manager/traffic-manager-routing-methods.md)Trafikhanteraren .
- Läs mer om [kapslade Traffic Manager-profiler](../traffic-manager/traffic-manager-nested-profiles.md).
- Läs mer om övervakning av [slutpunkter](../traffic-manager/traffic-manager-monitoring.md).
- Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) för att automatisera programundans.
