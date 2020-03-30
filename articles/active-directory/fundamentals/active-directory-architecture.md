---
title: Arkitekturöversikt - Azure Active Directory | Microsoft-dokument
description: Lär dig vad en Azure Active Directory-klient är och hur du hanterar Azure med Azure Active Directory.
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
ms.openlocfilehash: 854fb4649f8c1113f20abe5807dd0ce473ba6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368060"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Vad är Azure Active Directory-arkitekturen?

Med Azure AD (Active Directory Azure) kan du på ett säkert sätt hantera åtkomsten till Azure-tjänster och -resurser för dina användare. En komplett uppsättning identitetshanteringsfunktioner ingår i Azure AD. Information om funktionerna i Azure AD finns i [Vad är Azure Active Directory?](active-directory-whatis.md)

Med Azure AD kan du skapa och hantera användare och grupper och aktivera behörigheter för att tillåta och neka åtkomst till företagsresurser. Information om identitetshantering finns i [The fundamentals of Azure identity management](active-directory-whatis.md) (Grunderna om Azure-identitetshantering).

## <a name="azure-ad-architecture"></a>Azure AD-arkitekturen

Azure AD:s geografiskt distribuerade arkitektur kombinerar omfattande funktioner för övervakning, automatisk omdirigering, redundans och återställning, som levererar företagsomfattande tillgänglighet och prestanda till kunder.

Följande arkitekturelement beskrivs i den här artikeln:

*   Tjänstarkitekturens design
*   Skalbarhet
*   Kontinuerlig tillgänglighet
*   Datacenter

### <a name="service-architecture-design"></a>Tjänstarkitekturens design

Det vanligaste sättet att bygga ett tillgängligt och användbart, datarikt system är genom oberoende byggstenar eller skalenheter. För Azure AD-datanivån kallas skalningsenheter *partitioner*.

Datanivån har flera frontend-tjänster som tillhandahåller läs-och skrivfunktioner. Diagrammet nedan visar hur komponenterna i en enkatalogpartition levereras i geografiskt distribuerade datacenter.

  ![Partitionsdiagram för en katalog](./media/active-directory-architecture/active-directory-architecture.png)

Komponenterna i Azure AD-arkitekturen består av en primär replik och flera sekundära repliker.

#### <a name="primary-replica"></a>Primär replik

Den *primära repliken* tar emot alla *skrivningar* för den partition som den tillhör. Alla skrivåtgärder replikeras omedelbart till en sekundär replik i ett annat datacenter innan anroparen får en bekräftelse om att åtgärden lyckades. Den här metoden garanterar geo-redundant hållbarhet för skrivningar.

#### <a name="secondary-replicas"></a>Sekundära repliker

Alla *katalogläsningar* servas från *sekundära repliker*, som finns i datacenter som är fysiskt placerade över olika geografiska områden. Det finns många sekundära repliker eftersom data replikeras asynkront. Katalogläsningar, till exempel autentiseringsbegäranden, betjänas från datacenter som ligger nära kunder. De sekundära replikerna ansvarar för läsningarnas skalbarhet.

### <a name="scalability"></a>Skalbarhet

Skalbarhet syftar på en tjänsts förmåga att skala upp för att möta ökade krav på prestanda. Skalbarhet för skrivningar uppnås genom datapartitionering. Skalbarhet för läsningar uppnås genom att data replikeras från en partition till flera sekundära repliker runtom i världen.

Begäranden från katalogprogram dirigeras till det datacenter som de är fysiskt närmast. Skrivningar omdirigeras transparent till den primära repliken för att tillhandahålla konsekventa läsningar och skrivningar. Sekundära repliker utökar avsevärt partitionernas skalning eftersom katalogerna normalt hanterar läsningar större delen av tiden.

Katalogprogram ansluter till de närmaste datacentren. Den här anslutningen förbättrar prestanda och därför är det möjligt att skala ut. Eftersom en katalogpartition kan ha många sekundära repliker, kan sekundära repliker placeras närmare katalogklienterna. Endast interna katalogtjänstkomponenter som är skrivningsintensiva riktar sig direkt till den aktiva primära repliken.

### <a name="continuous-availability"></a>Kontinuerlig tillgänglighet

Tillgängligheten (eller drifttiden) syftar på ett systems möjlighet att köra kontinuerligt. Nyckeln till Azure AD:s högtillgänglighet är att tjänsterna snabbt kan flytta trafik över flera geografiskt distribuerade datacenter. Varje datacenter är oberoende, vilket möjliggör de-korrelerade fellägen. Genom den här designen med hög tillgänglighet kräver Azure AD inga driftstopp för underhållsaktiviteter.

Azure AD:s partitionsdesign förenklas jämfört med FÖRETAGETS AD-design med hjälp av en design med en huvudhanterare som innehåller en noggrant iscensatt och deterministisk primär replikväxlingsprocess.

#### <a name="fault-tolerance"></a>Feltolerans

Ett system är mer tillgängligt om det är tolerant för maskinvaru-, nätverks- och programvarurelaterade fel. För varje partition i katalogen finns det en huvudreplik med hög tillgänglighet: den primära repliken. Endast skrivningar till partitionen utförs på den här repliken. Repliken övervakas kontinuerligt och noggrant, och skrivningar kan omedelbart växlas till en annan replik (som blir den nya primära repliken) om det uppstår ett fel. Under en redundansväxling kan det uppstå ett avbrott i skrivtillgängligheten på 1 till 2 minuter. Lästillgängligheten påverkas inte under den här tiden.

Läsåtgärder (som är avsevärt många fler än skrivåtgärderna) skickas endast till sekundära repliker. Eftersom sekundära repliker är idempotenta kompenseras förlusten av en replik i en viss partition enkelt genom att läsningarna dirigeras till en annan replik, vanligtvis i samma datacenter.

#### <a name="data-durability"></a>Datahållbarhet

En skrivning är vederbörligen engagerad i minst två datacenter innan den bekräftas. Detta händer genom att först binda skrivningen på den primära och sedan omedelbart replikera skriva till minst ett annat datacenter. Den här skrivåtgärden säkerställer att en potentiell katastrofal förlust av det datacenter som är värd för den primära inte resulterar i dataförlust.

Azure AD har noll [återställningstidsmål (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) för att inte förlora data om redundans. Det här omfattar:

* Tokenutgivning och katalogläsningar
* Tillåter endast ca 5 minuter RTO för katalogskrivningar

### <a name="datacenters"></a>Datacenter

Azure Active Directorys repliker lagras i datacenter runtom i världen. Mer information finns i [Azures globala infrastruktur](https://azure.microsoft.com/global-infrastructure/).

Azure AD fungerar över datacenter med följande egenskaper:

* Autentisering, diagram och andra AD-tjänster finns bakom gateway-tjänsten. Gatewaytjänsten hanterar belastningsutjämningen av dessa tjänster. Det kommer att växla över automatiskt om några felaktiga servrar upptäcks med hjälp av transaktionella hälsoavsökningar. Baserat på dessa hälsoavsökningar dirigerar gatewayen dynamiskt trafik till felfria datacenter.
* För *läsningar*har katalogen sekundära repliker och motsvarande frontend-tjänster i en aktiv aktiv konfiguration som arbetar i flera datacenter. I händelse av ett fel på ett helt datacenter dirigeras trafiken automatiskt till ett annat datacenter.
 *För *skrivningar*växlar katalogen över primär (huvudreplik) över datacenter via planerade (nya primära synkroniseras med gamla primära) eller nödväxlingsprocedurer. Datahållbarhet uppnås genom att replikera alla åtaganden till minst två datacenter.

#### <a name="data-consistency"></a>Datakonsekvens

Katalogmodellen är en av eventuella konsekvenser. Ett typiskt problem med distribuerade asynkront replikera system är att data som returneras från en "viss" replik kanske inte är uppdaterade. 

Azure AD tillhandahåller läs- och skrivkonsekvens för program som skriver och läser till en viss sekundär replik genom att skrivningarna dirigeras till den primära repliken, varefter de hämtas tillbaka synkront till den sekundära repliken.

Programskrivningar med Microsoft Graph API för Azure AD är abstraherade från att upprätthålla tillhörighet till en katalogreplik för läs-skriv-konsekvens. Microsoft Graph API-tjänsten upprätthåller en logisk session, som har tillhörighet till en sekundär replik som används för läsningar. tillhörighet fångas i en "repliktoken" som tjänsten cachelagrar med hjälp av en distribuerad cache i det sekundära replikdatacentret. Denna token används sedan för efterföljande åtgärder i samma logiska session. Om du vill fortsätta använda samma logiska session måste efterföljande begäranden dirigeras till samma Azure AD-datacenter. Det går inte att fortsätta en logisk session om katalogklientbegäranden dirigeras till flera Azure AD-datacenter. Om detta inträffar har klienten flera logiska sessioner som har oberoende läs-skriv-konsekvens.

 >[!NOTE]
 >Skrivningar replikeras direkt till den sekundära repliken som den logiska sessionens läsningar skickades till.

#### <a name="backup-protection"></a>Skydd av säkerhetskopior

Katalogen implementerar mjuka borttagningar, i stället för hårda borttagningar, för användare och klientorganisationer så att oavsiktliga borttagningar av en kund enkelt kan återställas. Om klientadministratören av misstag tar bort användare kan de enkelt ångra och återställa de borttagna användarna.

Azure AD implementerar dagliga säkerhetskopieringar av alla data och kan därför auktoritativt återställa data i händelse av logiska borttagningar eller skadade data. Datanivån använder felkorrigering av koder, så att den kan söka efter fel och automatiskt korrigera vissa typer av diskfel.

#### <a name="metrics-and-monitors"></a>Mätvärden och övervakare

Körning av en tjänst med hög tillgänglighet kräver förstklassiga mät- och övervakningsfunktioner. Azure AD analyserar och rapporterar kontinuerligt viktiga mätvärden och framgångskriterier rörande tjänsternas hälsa för var och en av dess tjänster. Det finns också kontinuerlig utveckling och justering av mått och övervakning och avisering för varje scenario, inom varje Azure AD-tjänst och för alla tjänster.

Om någon Azure AD-tjänst inte fungerar som förväntat vidtas åtgärder omedelbart för att återställa funktioner så snabbt som möjligt. Det viktigaste måttet Azure AD spår är hur snabbt levande webbplats problem kan identifieras och mildras för kunder. Vi har gjort stora investeringar inom övervakning och avisering för att minimera tiden för upptäckt (målsättning: < 5 minuter) och inom systemberedskap för att minimera tiden för avhjälpande åtgärder (målsättning: < 30 minuter).

#### <a name="secure-operations"></a>Säkra åtgärder

Använda driftskontroller som MFA (Multi Factor Authentication) för alla åtgärder samt granskning av alla åtgärder. Dessutom, med hjälp av en just-in-time höjd system för att bevilja nödvändig tillfällig tillgång för alla operativa task-on-demand på en löpande basis. Mer information finns i [The Trusted Cloud](https://azure.microsoft.com/support/trust-center) (Det säkra molnet).

## <a name="next-steps"></a>Nästa steg

[Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop)