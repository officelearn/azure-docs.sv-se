---
title: Hantering av Azure-produktionsnätverk – Microsoft Azure
description: I den här artikeln beskrivs hur Microsoft hanterar och driver Azure-produktionsnätverket för att skydda Azure-datacenter.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727120"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Hantering och drift av Azure-produktionsnätverket    
I den här artikeln beskrivs hur Microsoft hanterar och driver Azure-produktionsnätverket för att skydda Azure-datacenter.

## <a name="monitor-log-and-report"></a>Övervaka, logga och rapportera

Hantering och drift av Azure-produktionsnätverket är en samordnad insats mellan driftteamen i Azure och Azure SQL Database. Teamen använder flera verktyg för övervakning av system och programprestanda i miljön. Och de använder lämpliga verktyg för att övervaka nätverksenheter, servrar, tjänster och programprocesser.

För att säkerställa en säker körning av tjänster som körs i Azure-miljön implementerar driftteamen flera nivåer av övervakning, loggning och rapportering, inklusive följande åtgärder:

- I första hand samlar Microsoft Monitoring Agent (MMA) in övervaknings- och diagnostiklogginformation från många platser, inklusive fabric controller (FC) och rotoperativsystemet (OS) och skriver den för att logga filer. Agenten skickar så småningom en sammansmält delmängd av informationen till ett förkonfigurerat Azure-lagringskonto. Dessutom läser den fristående övervaknings- och diagnostiktjänsten olika övervaknings- och diagnostikloggdata och sammanfattar informationen. Övervaknings- och diagnostiktjänsten skriver informationen till en integrerad logg. Azure använder den specialbyggda Azure-säkerhetsövervakningen, som är ett tillägg till Azure-övervakningssystemet. Den har komponenter som observerar, analyserar och rapporterar om säkerhetsrelaterade händelser från olika punkter i plattformen.

- Azure SQL Database Windows Fabric-plattformen tillhandahåller hanterings-, distributions-, utvecklings- och driftstillsynstjänster för Azure SQL Database. Plattformen erbjuder distribuerade distributionstjänster i flera steg, hälsoövervakning, automatiska reparationer och efterlevnad av serviceversioner. Det tillhandahåller följande tjänster:

   - Funktioner för tjänstmodellering med utvecklingsmiljö för hög återgivning (datacenterkluster är dyra och knappa).
   - Distributions- och uppgraderingsarbetsflöden med ett klick för services bootstrap och underhåll.
   - Hälsorapportering med automatiserade reparationsarbetsflöden för att möjliggöra självläkning.
   - Övervakning, aviseringar och felsökning i realtid över noderna i ett distribuerat system.
   - Centraliserad samling av operativa data och mått för distribuerad grundorsaksanalys och tjänstinsikt.
   - Operativa verktyg för distribution, ändringshantering och övervakning.
   - Azure SQL Database Windows Fabric-plattformen och watchdog-skript körs kontinuerligt och övervakas i realtid.

Om några avvikelser inträffar aktiveras incidentsvarsprocessen följt av Azure-incidentens triage-teamet. Lämplig Azure-supportpersonal meddelas för att svara på incidenten. Problemspårning och lösning dokumenteras och hanteras i ett centraliserat biljettsystem. Systemtidsmått är tillgängliga enligt sekretessavtalet (NDA) och på begäran.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Företagsnätverk och multifaktortillgång till produktion
Användarbasen för företagsnätverket innehåller Azure-supportpersonal. Företagsnätverket stöder interna företagsfunktioner och inkluderar åtkomst till interna program som används för Azure-kundsupport. Företagsnätverket är både logiskt och fysiskt separerat från Azure-produktionsnätverket. Azure-personal får åtkomst till företagsnätverket med hjälp av Azure-arbetsstationer och bärbara datorer. Alla användare måste ha ett Azure Active Directory-konto (Azure AD), inklusive ett användarnamn och lösenord, för att komma åt företagets nätverksresurser. Företagsnätverksåtkomst använder Azure AD-konton som utfärdas till all Microsoft-personal, leverantörer och leverantörer och hanteras av Microsoft Information Technology. Unika användaridentifierare skiljer personal baserat på deras anställningsstatus hos Microsoft.

Åtkomst till interna Azure-program styrs genom autentisering med Active Directory Federation Services (AD FS). AD FS är en tjänst som tillhandahålls av Microsoft Information Technology och som tillhandahåller autentisering av företagsnätverksanvändare genom att tillämpa en säker token och användaranspråk. AD FS gör det möjligt för interna Azure-program att autentisera användare mot Microsofts aktiva katalogdomän. För att komma åt produktionsnätverket från företagsnätverksmiljön måste användarna autentisera med hjälp av multifaktorautentisering.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Azure-produktionsnätverk](production-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
- [Azure-kunddataskydd](protection-customer-data.md)
