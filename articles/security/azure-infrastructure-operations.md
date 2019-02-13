---
title: Azure produktionsåtgärder och hantering
description: Den här artikeln innehåller en allmän beskrivning av hanteringen och driften av Azure-produktionsnätverket.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 0099eb61d97f813f7adca320b47c195fa1aabbdc
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104559"
---
# <a name="azure-production-operations-and-management"></a>Azure produktionsåtgärder och hantering    
Hantering och driften av Azure-produktionsnätverket är koordinera driftteam av Azure och Azure SQL Database. Hur team flera system och övervakning av programprestanda hanteringsverktygen i miljön. Och de använder lämpliga verktyg för att övervaka nätverksenheter, servrar, tjänster och program bearbetar.

För att säkerställa säker körning av tjänster som körs i Azure-miljön måste implementera driftteam flera nivåer av övervakning, loggning och rapportering, bland annat följande:

- Främst Microsoft Monitoring Agent (MMA) samlar in information från övervakning och diagnostik på många olika sätt, inklusive infrastrukturkontrollanten (FC) och rotoperativsystemet (OS), och skriver den till loggfiler. Agenten skickar till slut en uppdelade del av informationen till en förkonfigurerad Azure storage-konto. Dessutom fristående övervakning och diagnostik tjänsten läser olika övervaknings- och diagnostikdata loggdata och sammanfattar informationen. Övervakning och diagnostik tjänsten skriver informationen till en integrerad loggfilen. Azure använder den anpassade Azure säkerhetsövervakning, vilket är ett tillägg till Azure-övervakningssystem. Den har komponenter som se, analysera och rapportera om säkerhet relevanta händelser från olika platser i plattformen.

- Azure SQL Database Windows Fabric-plattformen ger hantering, distribution, utveckling och drift tillsyn tjänster för Azure SQL Database. Plattformen erbjuder distribuerade, flera steg distributionstjänster, hälsoövervakning, automatiska reparationer och tjänsten version efterlevnad. Den tillhandahåller följande tjänster:

   - Tjänsten modellering funktioner med hifi-utvecklingsmiljö (datacenter kluster är dyrt och begränsade).
   - Distribution och uppgradera arbetsflöden för tjänsten bootstrap och underhåll.
   - Hälsa rapportering med automatisk reparation arbetsflöden för att aktivera självåterställning.
   - Realtidsövervakning, aviseringar och felsökning verksamhet över noderna i ett distribuerat system.
   - Centraliserad samling av driftdata och mått för distribuerade orsaka analys- och insikter.
   - Drift verktyg för distribution, ändringshantering och övervakning.
   - Azure SQL Database Windows Fabric-plattformen och watchdog skript köras kontinuerligt och övervaka i realtid.

Om avvikelser sker, aktiveras den incidenthanteringsprocess följt av Azure incident prioritering-teamet. Den lämpliga Azure supportpersonal meddelas att svara på incidenten. Spårning och upplösning dokumenteras och hanteras i en centraliserad biljettsystem. System drifttid mått är tillgängliga under sekretessavtal (NDA) och på begäran.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Företagsnätverket och flerfunktionsåtkomstkontroll till produktion
Företagsnätverket användarbas innehåller Azure supportpersonal. Företagets nätverk stöder interna företagets funktioner och ger åtkomst till interna program som används för Azure teknisk support. Företagets nätverk är logiskt och fysiskt avgränsade från Azure-produktionsnätverket. Azure personal tillgång till företagets nätverk med hjälp av Azure arbetsstationer och bärbara datorer. Alla användare måste ha en Azure Active Directory (Azure AD)-konto, inklusive ett användarnamn och lösenord för att komma åt företagets nätverksresurser. Åtkomst till företagets nätverk använder Azure AD-konton som är utfärdat till alla Microsoft-Personal, och underleverantörer och hanteras av Microsofts IT-avdelning. Unikt-ID: n skilja personal baserat på deras anställningsstatus på Microsoft.

Åtkomst till interna Azure-program styrs via autentisering med Active Directory Federation Services (AD FS). AD FS är en tjänst som tillhandahålls genom Microsofts IT-avdelning som tillhandahåller autentisering av företagets nätverksanvändare genom att använda en säker token och användaren anspråk. AD FS kan intern Azure-program att autentisera användare mot Microsoft företagets active directory-domänen. Användare måste autentisera genom att använda multifaktorautentisering för att komma åt företagets nätverk från företagets nätverk-miljö.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)
