---
title: Översikt över arkitekturen - Azure Active Directory | Microsoft Docs
description: Lär dig vad en Azure Active Directory-klient är och hur du hanterar Azure med hjälp av Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b124475b44778ef3bb0dc9eba0c59bb3a277b85a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562057"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Vad är Azure Active Directory-arkitekturen?
Med Azure AD (Active Directory Azure) kan du på ett säkert sätt hantera åtkomsten till Azure-tjänster och -resurser för dina användare. En komplett uppsättning identitetshanteringsfunktioner ingår i Azure AD. Information om funktionerna i Azure AD finns i [Vad är Azure Active Directory?](active-directory-whatis.md)

Med Azure AD kan du skapa och hantera användare och grupper och aktivera behörigheter för att tillåta och neka åtkomst till företagsresurser. Information om identitetshantering finns i [The fundamentals of Azure identity management](active-directory-whatis.md) (Grunderna om Azure-identitetshantering).

## <a name="azure-ad-architecture"></a>Azure AD-arkitekturen
Azure Active Directorys geografiskt distribuerade arkitektur kombinerar omfattande övervakning, automatisk omdirigering, redundans och funktioner, vilket ger en företagsomfattande tillgänglighet och prestanda för kunder.

Följande arkitekturelement beskrivs i den här artikeln:
 *  Tjänstarkitekturens design
 *  Skalbarhet
 *  Kontinuerlig tillgänglighet
 *  Datacenter

### <a name="service-architecture-design"></a>Tjänstarkitekturens design
Det vanligaste sättet att bygga ett tillgängligt och användbart, data omfattande system är genom oberoende Bygg stenar eller skalnings enheter. För data nivån för Azure AD kallas skalnings enheter *partitioner*. 

Datanivån har flera frontend-tjänster som tillhandahåller läs-och skrivfunktioner. Diagrammet nedan visar hur komponenterna i en partition med en enda katalog levereras i geografiskt distribuerade Data Center. 

  ![Partition diagram med en enda katalog](./media/active-directory-architecture/active-directory-architecture.png)

Komponenterna i Azure AD-arkitekturen består av en primär replik och flera sekundära repliker.

**Primär replik**

Den *primära repliken* tar emot alla *skrivningar* för den partition som den tillhör. Alla skrivåtgärder replikeras omedelbart till en sekundär replik i ett annat datacenter innan anroparen får en bekräftelse om att åtgärden lyckades. Den här metoden garanterar geo-redundant hållbarhet för skrivningar.

**Sekundära repliker**

Alla katalog *läsningar* betjänas från *sekundära repliker*, som finns på Data Center som är fysiskt placerade i olika geografiska områden. Det finns många sekundära repliker eftersom data replikeras asynkront. Katalog läsningar, till exempel autentiseringsbegäranden, betjänas från data Center som är nära kunder. De sekundära replikerna ansvarar för läsningarnas skalbarhet.

### <a name="scalability"></a>Skalbarhet

Skalbarhet syftar på en tjänsts förmåga att skala upp för att möta ökade krav på prestanda. Skalbarhet för skrivningar uppnås genom datapartitionering. Skalbarhet för läsningar uppnås genom att data replikeras från en partition till flera sekundära repliker runtom i världen.

Begäranden från katalogprogram dirigeras till de datacenter som de ligger närmats. Skrivningar omdirigeras transparent till den primära repliken för att tillhandahålla konsekventa läsningar och skrivningar. Sekundära repliker utökar avsevärt partitionernas skalning eftersom katalogerna normalt hanterar läsningar större delen av tiden.

Katalogprogram ansluter till de närmaste datacentren. Den här anslutningen förbättrar prestanda och det är möjligt att skala upp. Eftersom en katalogpartition kan ha många sekundära repliker, kan sekundära repliker placeras närmare katalogklienterna. Endast interna katalogtjänstkomponenter som är skrivningsintensiva riktar sig direkt till den aktiva primära repliken.

### <a name="continuous-availability"></a>Kontinuerlig tillgänglighet

Tillgängligheten (eller drifttiden) syftar på ett systems möjlighet att köra kontinuerligt. Nyckeln till Azure AD: s hög tillgänglighet är att tjänsterna snabbt kan flytta trafik över flera geografiskt distribuerade Data Center. Varje data Center är oberoende, vilket möjliggör icke-korrelerade fellägen. Med den här designen av hög tillgänglighet krävs ingen nedtid för underhålls aktiviteter i Azure AD.

Azure Active Directorys partitionsdesign är förenklad jämfört med AD-företagsdesignen, med hjälp av en enskild design som innehåller en noggrant styrd och deterministisk primärreplik redundansväxling.

**Feltolerans**

Ett system är mer tillgängligt om det är tolerant för maskinvaru-, nätverks- och programvarurelaterade fel. För varje partition i katalogen finns en huvud replik med hög tillgänglighet: Den primära repliken. Endast skrivningar till partitionen utförs på den här repliken. Repliken övervakas kontinuerligt och noggrant, och skrivningar kan omedelbart växlas till en annan replik (som blir den nya primära repliken) om det uppstår ett fel. Under en redundansväxling kan det uppstå ett avbrott i skrivtillgängligheten på 1 till 2 minuter. Lästillgängligheten påverkas inte under den här tiden.

Läsåtgärder (som är avsevärt många fler än skrivåtgärderna) skickas endast till sekundära repliker. Eftersom sekundära repliker är idempotenta kompenseras förlusten av en replik i en viss partition enkelt genom att läsningarna dirigeras till en annan replik, vanligtvis i samma datacenter.

**Datahållbarhet**

Varaktigt allokeras till minst två Data Center innan det bekräftas. Detta inträffar när du först genomför skrivningen på den primära servern och sedan omedelbart replikerar skrivningen till minst ett annat data Center. Den här Skriv åtgärden garanterar att en potentiell förlust av data Center som är värd för den primära inte leder till data förlust.

Azure Active Directorys noll [Recovery mål för återställningstid (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) att inte data går förlorade vid redundansväxlingar. Det här omfattar:
-  Utfärdande av token och directory läsningar
-  Så att bara cirka 5 minuter RTO för directory-skrivningar

### <a name="datacenters"></a>Datacenter

Azure Active Directorys repliker lagras i datacenter runtom i världen. Mer information finns i [Azure Global Infrastructure](https://azure.microsoft.com/global-infrastructure/).

Azure AD fungerar i Data Center med följande egenskaper:

 * Autentisering, Graph och andra AD-tjänster finns bakom en Gateway-tjänsten. Gatewaytjänsten hanterar belastningsutjämningen av dessa tjänster. Den växlar över automatiskt om alla servrar som innehåller fel identifieras med hjälp av transaktionella hälsotillståndsavsökningar. Utifrån dessa hälso avsökningar dirigerar gatewayen trafiken dynamiskt till felfria data Center.
 * För *läsningar*har katalogen sekundära repliker och motsvarande front-end-tjänster i en aktiv-aktiv konfiguration i flera data Center. Om det uppstår ett problem med ett helt data Center dirigeras trafiken automatiskt till ett annat data Center.
 *  För *skrivningar*växlar katalogen över primär replik (Master) över data Center via planerat (ny primär synkroniseras till gammal primär) eller nödfall. Data hållbarhet uppnås genom att du replikerar varje incheckning till minst två Data Center.

**Datakonsekvens**

Directory-modellen är en av eventuell konsekvenser. Ett typiskt problem med distribuerade asynkrona replikeringssystem är att de data som returneras från en ”viss” replik inte kanske är uppdaterad. 

Azure AD tillhandahåller läs- och skrivkonsekvens för program som skriver och läser till en viss sekundär replik genom att skrivningarna dirigeras till den primära repliken, varefter de hämtas tillbaka synkront till den sekundära repliken.

Programskrivningar som använder Graph-API:et för Azure AD abstraheras från tillhörighetsmappningen till en katalogreplik för läs- och skrivkonsekvens. Azure AD Graph-tjänsten underhåller en logisk session som har tillhörighet till en sekundär replik som används för läsningar. tillhörigheten samlas in i en "replik-token" som Graph-tjänsten cachelagrar med hjälp av en distribuerad cache i det sekundära replik data centret. Denna token används sedan för efterföljande åtgärder i samma logiska session. Om du vill fortsätta använda samma logiska session måste efterföljande begär Anden dirigeras till samma Azure AD-datacenter. Det går inte att fortsätta en logisk session om katalog klient begär Anden dirigeras till flera Azure AD-datacenter. om detta inträffar har klienten flera logiska sessioner som har oberoende Läs-och skriv åtgärder.

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

