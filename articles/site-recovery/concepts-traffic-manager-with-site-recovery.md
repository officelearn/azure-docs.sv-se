---
title: Azure Traffic Manager med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du använder Azure Traffic Manager med Azure Site Recovery för katastrofåterställning och migrering
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 8f303ec3349b0003ef764e437c6f06cf498dcc57
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353262"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager med Azure Site Recovery

Med Azure Traffic Manager kan du styra distributionen av trafiken mellan slutpunkterna för din. En slutpunkt är en Internetansluten tjänst i eller utanför Azure.

Traffic Manager använder Domain Name System (DNS) för att dirigera klientbegäranden till den lämpligaste slutpunkten baserat på en trafikdirigeringsmetoden och hälsotillståndet för slutpunkterna. Traffic Manager tillhandahåller en uppsättning [trafikdirigeringsmetoder](../traffic-manager/traffic-manager-routing-methods.md) och [alternativ för slutpunktsövervakning](../traffic-manager/traffic-manager-monitoring.md) som passar olika programbehov och modeller för automatisk redundansväxling. Klienterna ansluter direkt till den valda slutpunkten. Traffic Manager är inte en proxy eller en gateway och den kan inte se den trafik som passerar mellan klienten och tjänsten.

Den här artikeln beskrivs hur du kan kombinera Azure trafik Monitor intelligent routning med Azure Site Recoverys kraftfulla haveriberedskap och migreringsfunktioner.

## <a name="on-premises-to-azure-failover"></a>Lokalt till Azure-redundans

Överväg att för det första scenariot **företag A** som har alla dess infrastruktur som körs i sin lokala miljö. För kontinuitet och efterlevnad affärsskäl, **företag A** bestämmer sig för att använda Azure Site Recovery för att skydda sina program.

**Företag A** kör program med offentliga slutpunkter och vill att sömlöst omdirigera trafik till Azure i ett haveri. Den [prioritet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) trafikdirigeringsmetoden i Azure Traffic Manager gör det möjligt för företag A du enkelt kan implementera det här mönstret för redundans.

Installationen är följande:
- **Företag A** skapar en [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Med den **prioritet** routningsmetod, **företag A** skapar två slutpunkter – **primära** för on-premises och **redundans** för Azure. **Primär** är tilldelad prioritet 1 och **redundans** är tilldelad prioritet 2.
- Eftersom den **primära** tjänstslutpunkten finns utanför Azure, slutpunkten har skapats som en [externa](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) slutpunkt.
- Med Azure Site Recovery har Azure-webbplatsen inte några virtuella datorer eller program som körs före redundans. Därför den **redundans** slutpunkten skapas också som en **externa** slutpunkt.
- Som standard dirigeras användartrafik till dina lokala program eftersom slutpunkten har högst prioritet som är associerade med den. Ingen trafik dirigeras till Azure om den **primära** slutpunkten är felfri.

![På-lokalt till Azure före redundans](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

I ett haveri företag A kan utlösa en [redundans](site-recovery-failover.md) till Azure och återställa dess program på Azure. När Azure Traffic Manager upptäcker att den **primära** slutpunkten inte längre är felfri, används automatiskt den **redundans** slutpunkten i DNS-svar och användarna ansluta till programmet återställas på Azure.

![På-lokalt till Azure efter redundansväxling](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Beroende på företagets krav, **företag A** kan välja ett högre eller lägre [avsökning frekvens](../traffic-manager/traffic-manager-monitoring.md) att växla mellan en lokal plats till Azure i ett haveri och se till att minimal avbrottstid för användare.

När katastrofen finns **företag A** kan återställning efter fel från Azure till sin lokala miljö ([VMware](vmware-azure-failback.md) eller [Hyper-V](hyper-v-azure-failback.md)) med Azure Site Recovery. Nu när Traffic Manager upptäcker att den **primära** slutpunkten är felfri igen, det använder automatiskt den **primära** slutpunkten i dess DNS-svar.

## <a name="on-premises-to-azure-migration"></a>Lokalt till Azure-migrering

Förutom haveriberedskap, Azure Site Recovery kan också [migreringar till Azure](migrate-overview.md). Med Azure Site Recoverys kraftfulla test växling kan utvärdera kunder programprestanda på Azure utan att påverka deras lokala miljö. Och när kunder är redo att migrera, kan de välja att migrera hela arbetsbelastningar tillsammans eller välja att migrera och skala gradvis.

Azure Traffic Manager [viktat](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) routningsmetod kan användas för att dirigera någon del av inkommande trafik till Azure när dirigera flesta till den lokala miljön. Den här metoden kan hjälpa dig att utvärdera skala prestanda som du kan fortsätta att öka vikten som tilldelats Azure när du migrerar mer och mer av dina arbetsbelastningar till Azure.

Till exempel **företag B** väljer att migrera i faser, flyttar några av dess Programmiljö samtidigt som företaget behåller den rest lokalt. Under de första stegen när de flesta av miljön är på plats, tilldelas en större vikt till den lokala miljön. Traffic manager returnerar en slutpunkt som baseras på vikterna som tilldelats tillgängliga slutpunkter.

![På-plats-till-Azure-migrering](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Båda slutpunkterna är aktiva under migreringen, och de flesta av trafiken dirigeras till den lokala miljön. Medan migreringen utförs kan en större vikt kan tilldelas till slutpunkter i Azure och slutligen den lokala slutpunkten kan vara inaktiverade efter migreringen.

## <a name="azure-to-azure-failover"></a>Redundans i Azure-datorer

Överväg att för det här exemplet **företagets C** som har alla dess infrastruktur som kör Azure. För kontinuitet och efterlevnad affärsskäl, **företagets C** bestämmer sig för att använda Azure Site Recovery för att skydda sina program.

**Företagets C** kör program med offentliga slutpunkter och vill att sömlöst omdirigera trafik till en annan Azure-region i ett haveri. Den [prioritet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) trafikdirigeringsmetoden tillåter **företagets C** du enkelt kan implementera det här mönstret för redundans.

Installationen är följande:
- **Företagets C** skapar en [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Med den **prioritet** routningsmetod, **företagets C** skapar två slutpunkter – **primära** för källregionen (Azure Östasien) och **redundans** för återställningsregionen (Azure Sydostasien). **Primär** är tilldelad prioritet 1 och **redundans** är tilldelad prioritet 2.
- Eftersom den **primära** tjänstslutpunkten finns i Azure, det kan vara som en [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) slutpunkt.
- Med Azure Site Recovery har recovery Azure-plats inte några virtuella datorer eller program som körs före redundans. Därför den **redundans** slutpunkt kan skapas som en [externa](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) slutpunkt.
- Som standard dirigeras trafiken till region (Östasien) programmet eftersom slutpunkten har högst prioritet som är associerade med den. Ingen trafik dirigeras till återställningsregionen om den **primära** slutpunkten är felfri.

![Azure till Azure före redundans](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

I ett haveri **företagets C** kan utlösa en [redundans](azure-to-azure-tutorial-failover-failback.md) och återställa dess program på Azure-region för återställning. När Azure Traffic Manager upptäcker att den primära slutpunkten inte längre är felfri, den använder automatiskt den **redundans** slutpunkten i DNS-svar och användarna ansluta till programmet återställas på recovery () Azure-region Sydostasien).

![Azure till Azure efter redundansväxling](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Beroende på företagets krav, **företagets C** kan välja ett högre eller lägre [avsökning frekvens](../traffic-manager/traffic-manager-monitoring.md) att växla mellan käll- och regioner och se till att minimal avbrottstid för användare.

När katastrofen finns **företagets C** kan återställning efter fel från recovery Azure-region till källan med hjälp av Azure Site Recovery. Nu när Traffic Manager upptäcker att den **primära** slutpunkten är felfri igen, det använder automatiskt den **primära** slutpunkten i dess DNS-svar.

## <a name="protecting-multi-region-enterprise-applications"></a>Skydda företagsprogram i flera regioner

Globala företag kan du ofta förbättra kundupplevelsen genom att skräddarsy programmen ska fungera regionala behov. Lokalisering och minskad fördröjning kan leda till programinfrastruktur delas upp i flera regioner. Företag är också bundna av regionala lagar i vissa områden och välja att isolera en del sin infrastruktur inom gränserna för regionala.  

Vi tar ett exempel där **företagets D** har delat dess slutpunkterna så att den stöder separat Tyskland och resten av världen. **Företag D** använder Azure Traffic Manager [geografisk](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) routningsmetod för att konfigurera detta. All trafik som kommer från Tyskland dirigeras till **slutpunkt 1** och all trafik som kommer från platser utanför Tyskland dirigeras till **slutpunkt 2**.

Problem med den här konfigurationen är att om **slutpunkt 1** slutar fungera av någon anledning, det finns ingen omdirigering av trafik till **slutpunkt 2**. Trafik från Tyskland fortsätter att dirigeras till **slutpunkt 1** oavsett hälsotillståndet för slutpunkten, lämnar du tyska användare utan åtkomst till **företagets D**'s program. På samma sätt, om **slutpunkt 2** går offline, det finns ingen omdirigering av trafik till **slutpunkt 1**.

![Program i flera regioner innan](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Att undvika detta problem och se till att programåterhämtning, **företagets D** använder [kapslade Traffic Manager-profiler](../traffic-manager/traffic-manager-nested-profiles.md) med Azure Site Recovery. I en kapslad profilinställningarna dirigeras trafik inte till enskilda slutpunkter, men i stället att andra Traffic Manager-profiler. Så fungerar här den här konfigurationen:
- I stället för att använda geografisk routning med enskilda slutpunkter **företagets D** använder geografisk routning med Traffic Manager-profiler.
- Varje underordnade Traffic Manager-profilen använder **prioritet** routning med en primär och en slutpunkt för återställning, därför kapsla **prioritet** routning inom **geografisk** routning.
- Om du vill aktivera programåterhämtning, använder varje Arbetsdistribution Azure Site Recovery för redundansväxling till en återställningsregion baserat vid ett haveri.
- När överordnat Traffic Manager tar emot en DNS-fråga, omdirigeras den till den relevanta underordnat Traffic Manager som svarar på frågan med en tillgänglig slutpunkt.

![Program i flera regioner efter](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Till exempel om det inte går att slutpunkten i Tyskland, centrala, kan programmet snabbt återställas till nordöstra Tyskland. Den nya slutpunkten hanterar trafik från Tyskland med minimal avbrottstid för användare. På samma sätt kan driftstörningar slutpunkten i Europa, västra hanteras genom att återställa programbelastningen till Norra Europa, med Azure Traffic Manager hantering av DNS som omdirigerar till tillgänglig slutpunkt.

Inställningarna ovan kan utökas med så många kombinationer av region och slutpunkt krävs. Traffic Manager kan upp till 10 nivåer av kapslade profiler och tillåter inte loopar i kapslade-konfigurationen.

## <a name="recovery-time-objective-rto-considerations"></a>Mål för återställningstid (RTO) överväganden

I de flesta organisationer är hanteras att lägga till eller ändra DNS-poster av ett separat team eller av någon utanför organisationen. Detta gör uppgiften att ändra DNS-poster som är mycket svårt. Den tid det tar att uppdatera DNS-poster av andra team eller organisationer som hanterar DNS-infrastrukturen varierar mellan olika företag och påverkar RTO av programmet.

Genom att använda Traffic Manager kan kan du frontload det arbete som krävs för DNS-uppdateringar. Ingen manuell eller skriptad åtgärd krävs vid tidpunkten för riktig redundansväxling. Den här metoden hjälper i Snabbväxling (och därmed minskar RTO) samt hur du undviker kostsamma tidskrävande DNS-ändringen fel i ett haveri. Med Traffic Manager, även steg för återställning efter fel är automatiserad, som annars skulle behöva hanteras separat.

Ange rätt [Kontrollintervall](../traffic-manager/traffic-manager-monitoring.md) genom grundläggande eller snabba intervallet health kan kontroller avsevärt påverkar RTO under redundans och minska stopptiden för användare.

Du kan också optimera DNS Time to Live (TTL) värde för Traffic Manager-profilen. TTL-värde är det värde som en DNS-post ska cachelagras av en klient. För en post skulle DNS inte efterfrågas två gånger inom loppet av TTL-värde. Varje DNS-post har ett TTL-värde som är kopplade till den. Minska det här värdet resulterar i fler DNS-frågor till Traffic Manager, men du kan minska RTO genom att identifiera avbrott snabbare.

TTL-värdet som klienten har erfarit också ökar inte om antalet DNS-matchare mellan klienten och den auktoritativa DNS-servern ökar. DNS-matchare ”count” TTL-värdet och endast skicka vidare ett TTL-värde som visar hur lång tid eftersom posten cachelagrades. Detta garanterar att DNS-posten hämtar uppdateras på klienten när TTL-värdet, oavsett antalet DNS-matchare i kedjan.

## <a name="next-steps"></a>Nästa steg
- Läs mer om Traffic Manager [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).
- Läs mer om [kapslade Traffic Manager-profiler](../traffic-manager/traffic-manager-nested-profiles.md).
- Läs mer om [slutpunktsövervakning](../traffic-manager/traffic-manager-monitoring.md).
- Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans i programmet.
