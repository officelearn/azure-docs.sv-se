---
title: "Förstå Azure Active Directory-arkitekturen | Microsoft Docs"
description: "I den här artikeln lär du dig vad en Azure AD-klient är och hur du hanterar Azure via Azure Active Directory"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 5c60fa737c0133482af8b653f795bf9086c39969
ms.lasthandoff: 03/28/2017


---
# <a name="understand-azure-active-directory-architecture"></a>Förstå Azure Active Directory-arkitekturen
Med Azure AD (Active Directory Azure) kan du på ett säkert sätt hantera åtkomsten till Azure-tjänster och -resurser för dina användare. En komplett uppsättning identitetshanteringsfunktioner ingår i Azure AD. Information om funktionerna i Azure AD finns i [Vad är Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis)

Med Azure AD kan du skapa och hantera användare och grupper och aktivera behörigheter för att tillåta och neka åtkomst till företagsresurser. Information om identitetshantering finns i [The fundamentals of Azure identity management](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity) (Grunderna om Azure-identitetshantering).

## <a name="azure-ad-architecture"></a>Azure AD-arkitekturen
Azure Active Directorys geografiskt distribuerade arkitektur kombinerar omfattande funktioner för övervakning, automatisk omdirigering, redundans och återställning som hjälper oss att leverera tillgänglighet och prestanda på företagsnivå till våra kunder.

Följande arkitekturelement beskrivs i den här artikeln:
 *    Tjänstarkitekturens design
 *    Skalbarhet 
 *    Kontinuerlig tillgänglighet
 *    Datacenter

### <a name="service-architecture-design"></a>Tjänstarkitekturens design
Det vanligaste sättet att bygga ett skalbart, högtillgängligt, dataintensivt system är genom oberoende byggblock eller skalningsenheter på Azure AD-datanivån. Skalningsenheterna kallas för *partitioner*. 

Datanivån har flera frontend-tjänster som tillhandahåller läs-och skrivfunktioner. Diagrammet nedan visar hur komponenterna i en partition med en enda katalog distribueras i geografiskt utspridda datacenter. 

  ![Partitioner med en enda katalog](./media/active-directory-architecture/active-directory-architecture.png)

Komponenterna i Azure AD-arkitekturen består av en primär replik och flera sekundära repliker.

**Primär replik**

Den *primära repliken* tar emot alla *skrivningar* för den partition som den tillhör. Alla skrivåtgärder replikeras omedelbart till en sekundär replik i ett annat datacenter innan anroparen får en bekräftelse om att åtgärden lyckades. Den här metoden garanterar geo-redundant hållbarhet för skrivningar.

**Sekundära repliker**

Alla *katalogläsningar* hanteras från *sekundära repliker*. Sekundära repliker är datacenter som är fysiskt placerade i olika geografiska områden. Det finns många sekundära repliker eftersom data replikeras asynkront. Katalogläsningar, till exempel autentiseringsbegäranden, hanteras från datacenter som finns nära våra kunder. De sekundära replikerna ansvarar för läsningarnas skalbarhet.

### <a name="scalability"></a>Skalbarhet

Skalbarhet syftar på en tjänsts förmåga att skala upp för att möta ökade krav på prestanda. Skalbarhet för skrivningar uppnås genom datapartitionering. Skalbarhet för läsningar uppnås genom att data replikeras från en partition till flera sekundära repliker runtom i världen.

Begäranden från katalogprogram dirigeras normalt till de datacenter som de ligger närmats fysiskt. Skrivningar omdirigeras transparent till den primära repliken för att tillhandahålla konsekventa läsningar och skrivningar. Sekundära repliker utökar avsevärt partitionernas skalning eftersom katalogerna normalt hanterar läsningar större delen av tiden.

Katalogprogram ansluter till de närmaste datacentren. Detta förbättrar prestanda vilket gör det möjligt att skala upp. Eftersom en katalogpartition kan ha många sekundära repliker, kan sekundära repliker placeras närmare katalogklienterna. Endast interna katalogtjänstkomponenter som är skrivningsintensiva riktar sig direkt till den aktiva primära repliken.

### <a name="continuous-availability"></a>Kontinuerlig tillgänglighet

Tillgängligheten (eller drifttiden) syftar på ett systems möjlighet att köra kontinuerligt. Nyckeln till Azure Active Directorys höga tillgänglighet är att våra tjänster snabbt kan växla trafik över flera geografiskt utspridda datacenter. Alla datacenter är oberoende av varandra, vilket innebär att fellägen kan hållas åtskilda.

Azure Active Directorys partitionsdesign är förenklad jämfört med AD-företagsdesignen, vilket är viktigt för uppskalning av systemet. Vi har implementerat en design med en enskild huvudreplik och en noggrant styrd och deterministisk redundans mellan repliker.

**Feltolerans**

Ett system är mer tillgängligt om det är tolerant för maskinvaru-, nätverks- och programvarurelaterade fel. För varje partition i katalogen finns det en huvudreplik med hög tillgänglighet: den primära repliken. Endast skrivningar till partitionen utförs på den här repliken. Repliken övervakas kontinuerligt och noggrant, och skrivningar kan omedelbart växlas till en annan replik (som blir den nya primära repliken) om det uppstår ett fel. Under en redundansväxling kan det uppstå ett avbrott i skrivtillgängligheten på 1 till 2 minuter. Lästillgängligheten påverkas inte under den här tiden.

Läsåtgärder (som är avsevärt många fler än skrivåtgärderna) skickas endast till sekundära repliker. Eftersom sekundära repliker är idempotenta kompenseras förlusten av en replik i en viss partition enkelt genom att läsningarna dirigeras till en annan replik, vanligtvis i samma datacenter.

**Datahållbarhet**

Skrivningarnas hållbarhet säkerställs genom skrivning till minst två datacenter innan åtgärden bekräftas som lyckad. Detta sker genom att skrivningarna först skrivs till den primära repliken och sedan direkt efter till minst ett annat datacenter. Detta säkerställer att en potentiell oåterkallelig förlust av datacentret som är värd för den primära repliken inte resulterar i förlust av data.

Azure Active Directorys [mål för återställningstid (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) är noll för utfärdande av token och katalogläsningar och bara några minuter (~ 5 minuter) för katalogskrivningar. [Målet för återställningspunkter (RPO)](https://en.wikipedia.org/wiki/Recovery_point_objective) är också noll, och inga data går förlorade vid redundansväxlingar.

### <a name="data-centers"></a>Datacenter

Azure Active Directorys repliker lagras i datacenter runtom i världen. Mer information finns i avsnittet om [Azure-datacenter](https://azure.microsoft.com/en-us/overview/datacenters).

Azure AD körs i datacenter med följande funktioner:

 * Autentisering, Graph och andra AD-tjänster finns bakom gatewaytjänsten. Gatewaytjänsten hanterar belastningsutjämningen av dessa tjänster. Den växlar automatiskt över om servrar med feltillstånd identifieras under transaktionella hälsotillståndsavsökningar. Baserat på dessa hälsotillståndsavsökningar dirigerar gatewaytjänsten trafiken dynamiskt till felfria datacenter.
 * För *läsningar* har katalogen sekundära repliker och motsvarande frontend-tjänster i en ”aktiv-aktiv”-konfiguration i flera datacenter. Om ett helt datacenter får problem dirigeras trafiken automatiskt till ett annat datacenter.
 *    För *skrivningar* redundansväxlar katalogtjänsten den primära repliken (huvudrepliken) över datacenter genom planerade redundansväxlingar eller nödväxlingar (den nya primära repliken synkroniseras med den gamla primära repliken). Datahållbarheten uppnås genom skrivning till minst två datacenter.

**Datakonsekvens**

Katalogmodellen tillhandahåller konsekvens. Ett typiskt problem med distribuerade asynkrona replikeringssystem är att de data som returneras från en specifik replik kanske inte är uppdaterade. 

Azure AD tillhandahåller läs- och skrivkonsekvens för program som skriver och läser till en viss sekundär replik genom att skrivningarna dirigeras till den primära repliken, varefter de hämtas tillbaka synkront till den sekundära repliken.

Programskrivningar som använder Graph-API:et för Azure AD abstraheras från tillhörighetsmappningen till en katalogreplik för läs- och skrivkonsekvens. Azure AD Graph-tjänsten upprättar en logisk session som är mappad till en sekundär replik som används för läsningar. Mappningen registreras i en ”repliktoken” som diagramtjänsten cachelagrar med hjälp av en distribuerad cache. Denna token används sedan för efterföljande åtgärder i samma logiska session. 

 >[!NOTE]
 >Skrivningar replikeras direkt till den sekundära repliken som den logiska sessionens läsningar skickades till.
 >

**Skydd av säkerhetskopior**

Katalogen implementerar mjuka borttagningar, i stället för hårda borttagningar, för användare och klientorganisationer så att oavsiktliga borttagningar av en kund enkelt kan återställas. Om administratören för en klientorganisation oavsiktligt tar bort användare kan han eller hon enkelt ångra åtgärden och återställa de borttagna användarna. 

Azure AD implementerar dagliga säkerhetskopieringar av alla data och kan därför auktoritativt återställa data i händelse av logiska borttagningar eller skadade data. Vår datanivå använder felkorrigeringskoder för felkontroll och automatisk korrigering av särskilda typer av diskfel.

**Mätvärden och övervakare**

Körning av en tjänst med hög tillgänglighet kräver förstklassiga mät- och övervakningsfunktioner. Azure AD analyserar och rapporterar kontinuerligt viktiga mätvärden och framgångskriterier rörande tjänsternas hälsa för var och en av dess tjänster. Vi utvecklar och finjusterar kontinuerligt mätvärden, övervakning och aviseringar för alla scenarier i varje Azure AD-tjänst och för alla tjänster.

Om en Azure AD-tjänst inte fungerar som förväntat vidtar vi omedelbart åtgärder för att återställa funktionerna så snabbt som möjligt. Det viktigaste måttet som Azure AD spårar är hur snabbt vi kan identifiera och undvika ett kundproblem eller problem med en live-webbplats. Vi har gjort stora investeringar inom övervakning och avisering för att minimera tiden för upptäckt (målsättning: < 5 minuter) och inom systemberedskap för att minimera tiden för avhjälpande åtgärder (målsättning: < 30 minuter).

**Säkra åtgärder**

Vi använder operativa kontroller, till exempel multifaktorautentisering (MFA) för alla åtgärder, samt granskning av alla åtgärder. Dessutom använder vi ett JIT-utvärderingssystem (Just-In-Time) för att löpande bevilja nödvändig tillfällig åtkomst för operativa uppgifter på begäran. Mer information finns i [The Trusted Cloud](https://azure.microsoft.com/en-us/support/trust-center) (Det säkra molnet).

## <a name="next-steps"></a>Nästa steg
[Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)


