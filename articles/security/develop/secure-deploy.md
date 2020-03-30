---
title: Distribuera säkra program på Microsoft Azure
description: I den här artikeln beskrivs metodtips som ska beaktas under utgivnings- och svarsfaserna för webbprogramprojektet.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934875"
---
# <a name="deploy-secure-applications-on-azure"></a>Distribuera säkra program på Azure
I den här artikeln presenterar vi säkerhetsaktiviteter och kontroller att tänka på när du distribuerar program för molnet. Säkerhetsfrågor och begrepp att tänka på under utgivnings- och svarsfaserna för [Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att distribuera ett säkrare program.

Följande SDL-faser behandlas i den här artikeln:

- Frisläpp
- Svar

## <a name="release"></a>Frisläpp
Fokus för utgivningsfasen är att förbereda ett projekt för offentlig publicering.
Detta inkluderar planering av sätt att effektivt utföra serviceuppgifter efter publicering och åtgärda säkerhetsproblem som kan uppstå senare.

### <a name="check-your-applications-performance-before-you-launch"></a>Kontrollera programmets prestanda innan du startar

Kontrollera programmets prestanda innan du startar det eller distribuera uppdateringar till produktionen. Kör molnbaserade [belastningstester](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) med hjälp av Visual Studio för att hitta prestandaproblem i ditt program, förbättra distributionskvaliteten, se till att ditt program alltid är uppe eller tillgängligt och att ditt program kan hantera trafik för lanseringen.

### <a name="install-a-web-application-firewall"></a>Installera en brandvägg för webbprogram

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Vanliga bland dessa bedrifter är SQL-injektionsattacker och skriptattacker över flera webbplatser. Att förhindra dessa attacker i programkoden kan vara en utmaning. Det kan kräva rigoröst underhåll, korrigering och övervakning på många lager av programtopologin. En centraliserad WAF hjälper till att göra säkerhetshanteringen enklare. En WAF-lösning kan också reagera på ett säkerhetshot genom att korrigera ett känt säkerhetsproblem på en central plats jämfört med att skydda varje enskilt webbprogram.

[Azure Application Gateway WAF](../../application-gateway/waf-overview.md) ger centraliserat skydd av dina webbprogram från vanliga kryphål och sårbarheter. WAF baseras på regler från [OWASP-kärnregeluppsättningarna](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9.

### <a name="create-an-incident-response-plan"></a>Skapa en plan för incidenthantering

Att förbereda en incidenthanteringsplan är avgörande för att hjälpa dig att hantera nya hot som kan uppstå över tid. Förbereda en incidentsvarsplan inkluderar att identifiera lämpliga säkerhetshanteringskontakter och upprätta säkerhetsserviceplaner för kod som ärvs från andra grupper i organisationen och för licensierad tredjepartskod.

### <a name="conduct-a-final-security-review"></a>Genomföra en slutlig säkerhetsgranskning

Genom att avsiktligt granska alla säkerhetsaktiviteter som utfördes kan du säkerställa beredskapen för din programvaruversion eller ditt program. Den slutliga säkerhetsgranskningen (FSR) omfattar vanligtvis att undersöka hotmodeller, verktygsutdata och prestanda mot kvalitetsgrindar och felstaplar som definierades i kravfasen.

### <a name="certify-release-and-archive"></a>Certifiera utgivning och arkiv

Att certifiera programvara innan en release säkerställer att säkerhets- och sekretesskraven uppfylls. Arkivering av alla relevanta data är viktigt för att utföra serviceuppgifter efter publiceringen. Arkivering bidrar också till att sänka de långsiktiga kostnaderna i samband med ihållande programvaruteknik.

## <a name="response"></a>Svar
Svarsfasen efter lanseringen fokuserar på att utvecklingsteamet kan och är tillgängliga för att svara på lämpligt sätt på alla rapporter om nya programvaruhot och sårbarheter.

### <a name="execute-the-incident-response-plan"></a>Kör incidenthanteringsplanen

Att kunna implementera incidenthanteringsplanen som inrättats i utgivningsfasen är avgörande för att skydda kunder från sårbarheter för programvarusäkerhet eller sekretess som uppstår.

### <a name="monitor-application-performance"></a>Övervaka programprestanda

Löpande övervakning av ditt program efter att det har distribuerats kan hjälpa dig att upptäcka prestandaproblem samt säkerhetsproblem.
Azure-tjänster som hjälper till med programövervakning är:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Application Insights identifierar automatiskt prestandaavvikelser. Den innehåller kraftfulla analysverktyg som hjälper dig att diagnostisera problem och förstå vad användarna faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-intro.md) hjälper dig att förebygga, identifiera och svara på hot med ökad insyn i (och kontroll över) säkerheten för dina Azure-resurser, inklusive webbprogram. Azure Security Center hjälper till att identifiera hot som annars kan gå obemärkt förbi. Det fungerar med olika säkerhetslösningar.

Security Centers kostnadsfria nivå erbjuder begränsad säkerhet endast för dina Azure-resurser. [Säkerhetscenterstandardnivån](../../security-center/security-center-onboarding.md) utökar dessa funktioner till lokala resurser och andra moln.
Security Center Standard hjälper dig att:

  - Hitta och åtgärda säkerhetsproblem.
  - Använd åtkomst- och programkontroller för att blockera skadlig aktivitet.
  - Identifiera hot med hjälp av analys och intelligens.
  - Svara snabbt när du blir attackerad.

## <a name="next-steps"></a>Nästa steg
I följande artiklar rekommenderar vi säkerhetskontroller och aktiviteter som kan hjälpa dig att utforma och utveckla säkra program.

- [Utforma säkra program](secure-design.md)
- [Utveckla säkra program](secure-develop.md)
