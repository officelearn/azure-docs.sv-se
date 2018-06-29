---
title: Azure produktion och hantering
description: Den här artikeln innehåller en allmän beskrivning av hanteringen och drift av Azure-produktionsnätverket.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: dc389f5f5c155555deb860f041b15b0ea49ee416
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102703"
---
# <a name="azure-production-operations-and-management"></a>Azure produktion och hantering    
Hantering och drift av Azure-produktionsnätverket är koordinera operations-team i Azure och Azure SQL Database. Flera system- och övervakningsverktyg används i miljön. Nätverksenheter, servrar, tjänster och program övervakas med lämpliga verktyg.

Flera nivåer av övervakning, loggning och rapportering implementeras för att säkerställa säker körning av tjänster som körs i Microsoft Azure-miljö, inklusive följande:

- Främst Microsoft Azure Monitoring Agent (MA) samlar in övervakning och diagnostik logginformation på många olika sätt inklusive FC och roten OS och skriver den till loggfiler. Den skickas så småningom en uppdelade del av informationen i en förkonfigurerad Azure Storage-konto. Dessutom är övervakning och diagnostik Service (MDS) en fristående tjänst som läser olika loggdata för övervakning och diagnostik och sammanfattar informationen. MDS skriver informationen till en integrerad logg. Azure använder den specialbyggt Azure Security övervakning (ASM), vilket är ett tillägg till Azure övervakningssystemet. Den har komponenter som se, analysera och rapportera om säkerhet relevanta händelser från olika punkter i plattformen.
- Microsoft Azure SQL Database WinFabric-plattformen ger operativa tillsyn tjänster för Microsoft Azure SQL Database, utveckling, distribution och hantering. Den erbjuder distribuerade, flera steg deployment services, hälsoövervakning, automatisk reparation och kompatibilitet för tjänsten version. Den tillhandahåller följande tjänster:

   - Tjänsten modeling funktioner med hög återgivning utvecklingsmiljö (datacenter kluster är dyrt och begränsade).
   - Ett klick distribution och uppgradera arbetsflöden för tjänsten bootstrap och underhåll.
   - Rapportering med automatisk reparation arbetsflöden för att aktivera självåterställning hälsa.
   - Realtid övervakning, aviseringar och felsökning verksamhet mellan noder i ett distribuerat system.
   - Centraliserad insamling av användningsdata och mått för distribuerade roten orsaka analys- och insikter.
   - Drift tooling för distribution, ändringshantering och övervakning.
   - Microsoft Azure SQL Database WinFabric plattform och watchdog skript kör kontinuerligt och övervaka i realtid.

Om alla avvikelser sker, aktiveras Incident svar processen följt av Azure Incident prioritering-teamet. Den lämpliga Azure supportpersonalen meddelas att svara på incidenten. Spåra problem och lösningar dokumenteras och hanteras i ett centraliserad biljettsystem. System drifttid mått är tillgängliga under icke-avslöjande av avtalet (Sekretessavtalet) och på begäran.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Företagsnätverket och Flerfunktionsåtkomstkontroll till produktion
Företagsnätverk användarbas innehåller supporttekniker för Microsoft Azure. Företagsnätverket stöder interna företagets funktioner och innehåller åtkomst till interna program som används för Azure kundsupport. Företagets nätverk är logiskt och fysiskt åtskilda från Azure-produktionsnätverket. Microsoft Azure-personal åtkomst till företagsnätverket med hjälp av Microsoft Azure-arbetsstationer och bärbara datorer. Alla användare måste ha ett konto med Active Directory (AD), inklusive ett användarnamn och lösenord för åtkomst till företagets nätverksresurser. Åtkomst till företagsnätverket använder AD-konton, som är utfärdat till alla Microsoft-Personal, leverantörer, leverantörer och hanteras av MSIT. Unikt ID: n skilja personal baserat på deras anställningsstatus hos Microsoft.

Åtkomst till interna Azure program styrs via autentisering med Active Directory Federation Services (ADFS). AD FS är en tjänst som värd MSIT som tillhandahåller autentisering av CorpNet användare genom att använda en säker anspråk token och användare. AD FS kan interna Microsoft Azure-program att autentisera användare mot Microsoft företagets AD-domänen. För att komma åt företagets nätverk från CorpNet-miljön, måste användaren autentisera med hjälp av multifaktorautentisering.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)
