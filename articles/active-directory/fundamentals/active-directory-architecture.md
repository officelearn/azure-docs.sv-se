---
title: Översikt över arkitekturen - Azure Active Directory | Microsoft Docs
description: Lär dig vad en Azure Active Directory-klient är och hur du hanterar Azure med hjälp av Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: c23bdba74ab528a0774b73598dbee8888ebfdc7e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076125"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Vad är Azure Active Directory-arkitekturen?
Med Azure AD (Active Directory Azure) kan du på ett säkert sätt hantera åtkomsten till Azure-tjänster och -resurser för dina användare. En komplett uppsättning identitetshanteringsfunktioner ingår i Azure AD. Information om funktionerna i Azure AD finns i [Vad är Azure Active Directory?](active-directory-whatis.md)

Med Azure AD kan du skapa och hantera användare och grupper och aktivera behörigheter för att tillåta och neka åtkomst till företagsresurser. Information om identitetshantering finns i [The fundamentals of Azure identity management](active-directory-whatis.md) (Grunderna om Azure-identitetshantering).

## <a name="azure-ad-architecture"></a>Azure AD-arkitekturen
Azure Active Directorys geografiskt distribuerade arkitektur kombinerar omfattande övervakning, automatisk omdirigering, redundans och funktioner, vilket ger en företagsomfattande tillgänglighet och prestanda för kunder.

Följande arkitekturelement beskrivs i den här artikeln:
 *  Tjänstarkitekturens design
 *  Användbarhet 
 *  Kontinuerlig tillgänglighet
 *  Datacenter

### <a name="service-architecture-design"></a>Tjänstarkitekturens design
Det vanligaste sättet att skapa en tillgänglig och kan användas, dataintensivt system är genom oberoende byggblock eller skalningsenheter för Azure AD-datanivån, skalningsenheterna kallas *partitioner*. 

Datanivån har flera frontend-tjänster som tillhandahåller läs-och skrivfunktioner. Diagrammet nedan visar hur komponenterna i en enda-katalogpartition levereras i geografiskt utspridda datacenter. 

  ![Partitioner med en enda katalog](./media/active-directory-architecture/active-directory-architecture.png)

Komponenterna i Azure AD-arkitekturen består av en primär replik och flera sekundära repliker.

**Primär replik**

Den *primära repliken* tar emot alla *skrivningar* för den partition som den tillhör. Alla skrivåtgärder replikeras omedelbart till en sekundär replik i ett annat datacenter innan anroparen får en bekräftelse om att åtgärden lyckades. Den här metoden garanterar geo-redundant hållbarhet för skrivningar.

**Sekundära repliker**

Alla *katalogläsningar* hanteras från *sekundära repliker*. Sekundära repliker är datacenter som är fysiskt placerade i olika geografiska områden. Det finns många sekundära repliker eftersom data replikeras asynkront. Directory läsningar, till exempel autentiseringsbegäranden, hanteras från datacenter som finns nära kunder. De sekundära replikerna ansvarar för läsningarnas skalbarhet.

### <a name="scalability"></a>Skalbarhet

Skalbarhet syftar på en tjänsts förmåga att skala upp för att möta ökade krav på prestanda. Skalbarhet för skrivningar uppnås genom datapartitionering. Skalbarhet för läsningar uppnås genom att data replikeras från en partition till flera sekundära repliker runtom i världen.

Begäranden från katalogprogram dirigeras till de datacenter som de ligger närmats. Skrivningar omdirigeras transparent till den primära repliken för att tillhandahålla konsekventa läsningar och skrivningar. Sekundära repliker utökar avsevärt partitionernas skalning eftersom katalogerna normalt hanterar läsningar större delen av tiden.

Katalogprogram ansluter till de närmaste datacentren. Den här anslutningen förbättrar prestanda och det är möjligt att skala upp. Eftersom en katalogpartition kan ha många sekundära repliker, kan sekundära repliker placeras närmare katalogklienterna. Endast interna katalogtjänstkomponenter som är skrivningsintensiva riktar sig direkt till den aktiva primära repliken.

### <a name="continuous-availability"></a>Kontinuerlig tillgänglighet

Tillgängligheten (eller drifttiden) syftar på ett systems möjlighet att köra kontinuerligt. Nyckeln till Azure Active Directorys hög tillgänglighet är att tjänsterna snabbt kan växla trafik över flera geografiskt utspridda datacenter. Alla datacenter är oberoende av varandra, vilket innebär att fellägen kan hållas åtskilda.

Azure Active Directorys partitionsdesign är förenklad jämfört med AD-företagsdesignen, med hjälp av en enskild design som innehåller en noggrant styrd och deterministisk primärreplik redundansväxling.

**Feltolerans**

Ett system är mer tillgängligt om det är tolerant för maskinvaru-, nätverks- och programvarurelaterade fel. För varje partition i katalogen finns det en huvudreplik med hög tillgänglighet: den primära repliken. Endast skrivningar till partitionen utförs på den här repliken. Repliken övervakas kontinuerligt och noggrant, och skrivningar kan omedelbart växlas till en annan replik (som blir den nya primära repliken) om det uppstår ett fel. Under en redundansväxling kan det uppstå ett avbrott i skrivtillgängligheten på 1 till 2 minuter. Lästillgängligheten påverkas inte under den här tiden.

Läsåtgärder (som är avsevärt många fler än skrivåtgärderna) skickas endast till sekundära repliker. Eftersom sekundära repliker är idempotenta kompenseras förlusten av en replik i en viss partition enkelt genom att läsningarna dirigeras till en annan replik, vanligtvis i samma datacenter.

**Datahållbarhet**

Skrivningarnas hållbarhet säkerställs genom skrivning till minst två datacenter innan åtgärden bekräftas som lyckad. Detta sker genom att skrivningarna först skrivs till den primära repliken och sedan direkt efter till minst ett annat datacenter. Den här Skrivåtgärden säkerställer att en potentiell oåterkallelig förlust av datacentret som är värd för primärt inte leder till förlust av data.

Azure Active Directorys noll [Recovery mål för återställningstid (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) att inte data går förlorade vid redundansväxlingar. Det här omfattar:
-  Utfärdande av token och directory läsningar
-  Så att bara cirka 5 minuter RTO för directory-skrivningar

### <a name="data-centers"></a>Datacenter

Azure Active Directorys repliker lagras i datacenter runtom i världen. Mer information finns i avsnittet om [Azure-datacenter](https://azure.microsoft.com/overview/datacenters).

Azure AD körs i datacenter med följande funktioner:

 * Autentisering, Graph och andra AD-tjänster finns bakom en Gateway-tjänsten. Gatewaytjänsten hanterar belastningsutjämningen av dessa tjänster. Den växlar över automatiskt om alla servrar som innehåller fel identifieras med hjälp av transaktionella hälsotillståndsavsökningar. Baserat på dessa hälsotillståndsavsökningar dirigerar gatewaytjänsten trafiken dynamiskt till felfria datacenter.
 * För *läsningar* har katalogen sekundära repliker och motsvarande frontend-tjänster i en ”aktiv-aktiv”-konfiguration i flera datacenter. Om ett helt datacenter får problem dirigeras trafiken automatiskt till ett annat datacenter.
 *  För *skriver*, katalogen växlar över primärreplik (huvudrepliken) över datacenter via planerat (nya primära repliken synkroniseras gamla primära) eller nödväxlingar. Datahållbarheten uppnås genom skrivning till minst två datacenter.

**Datakonsekvens**

Directory-modellen är en av eventuell konsekvenser. Ett typiskt problem med distribuerade asynkrona replikeringssystem är att de data som returneras från en ”viss” replik inte kanske är uppdaterad. 

Azure AD tillhandahåller läs- och skrivkonsekvens för program som skriver och läser till en viss sekundär replik genom att skrivningarna dirigeras till den primära repliken, varefter de hämtas tillbaka synkront till den sekundära repliken.

Programskrivningar som använder Graph-API:et för Azure AD abstraheras från tillhörighetsmappningen till en katalogreplik för läs- och skrivkonsekvens. Azure AD Graph-tjänsten upprättar en logisk session som är mappad till en sekundär replik som används för läsningar. Mappningen registreras i en ”repliktoken” som diagramtjänsten cachelagrar med hjälp av en distribuerad cache. Denna token används sedan för efterföljande åtgärder i samma logiska session. 

 >[!NOTE]
 >Skrivningar replikeras direkt till den sekundära repliken som den logiska sessionens läsningar skickades till.
 >

**Skydd av säkerhetskopior**

Katalogen implementerar mjuka borttagningar, i stället för hårda borttagningar, för användare och klientorganisationer så att oavsiktliga borttagningar av en kund enkelt kan återställas. Om din Innehavaradministratör oavsiktligt tar bort användare, kan de enkelt ångra och återställa de borttagna användarna. 

Azure AD implementerar dagliga säkerhetskopieringar av alla data och kan därför auktoritativt återställa data i händelse av logiska borttagningar eller skadade data. Datanivån använder korrigera koder, så att den kan söka efter fel och automatiskt korrigering av särskilda typer av diskfel.

**Mätvärden och övervakare**

Körning av en tjänst med hög tillgänglighet kräver förstklassiga mät- och övervakningsfunktioner. Azure AD analyserar och rapporterar kontinuerligt viktiga mätvärden och framgångskriterier rörande tjänsternas hälsa för var och en av dess tjänster. Det finns också kontinuerlig utveckling och justering av mått och övervakning och avisering för varje scenario i varje Azure AD-tjänsten och för alla tjänster.

Om alla Azure AD-tjänsten inte fungerar som förväntat, åtgärd omedelbart att återställa funktionerna så snabbt som möjligt. De viktigaste måttet som Azure AD spårar är hur snabbt live-webbplatsen problem kan identifieras och undvikas för kunder. Vi har gjort stora investeringar inom övervakning och avisering för att minimera tiden för upptäckt (målsättning: < 5 minuter) och inom systemberedskap för att minimera tiden för avhjälpande åtgärder (målsättning: < 30 minuter).

**Säkra åtgärder**

Använder operativa kontroller, till exempel multifaktorautentisering (MFA) för alla åtgärder, samt granskning av alla åtgärder. Dessutom använder en just-in-time-utvärderingssystem för att bevilja nödvändig tillfällig åtkomst för alla operativa uppgiften på begäran med jämna mellanrum. Mer information finns i [The Trusted Cloud](https://azure.microsoft.com/support/trust-center) (Det säkra molnet).

## <a name="next-steps"></a>Nästa steg
[Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

