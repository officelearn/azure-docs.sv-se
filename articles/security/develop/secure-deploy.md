---
title: Distribuera säkra program i Microsoft Azure
description: Den här artikeln beskrivs bästa praxis att överväga under versionen och svaret faserna i webbprojektet för programmet.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144456"
---
# <a name="deploy-secure-applications-on-azure"></a>Distribuera säkra program i Azure
I den här artikeln visar vi säkerhetsaktiviteter och kontroller att tänka på när du distribuerar program till molnet. Säkerhetsfrågor och begrepp att överväga under versionen och svaret faser av Microsofts [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att distribuera en säkrare program.

Följande faser för SDL-processen beskrivs i den här artikeln:

- Frisläpp
- Svar

## <a name="release"></a>Frisläpp
Fokus i fasen versionen är readying ett projekt för publiceringen.
Detta omfattar att planera sätt att effektivt uppgifter efter produktlanseringen underhåll och åtgärda säkerhetsproblem som kan uppstå senare.

### <a name="check-your-applications-performance-before-you-launch"></a>Kontrollera prestanda för ditt program innan du startar

Kontrollera prestanda för ditt program innan du startar den eller distribuera uppdateringar till produktion. Kör molnbaserade [belastningstester](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) genom att använda Visual Studio för att hitta problem med prestanda i ditt program kan förbättra kvaliteten för distribution, se till att ditt program alltid är igång eller vara tillgängliga och att ditt program kan hantera trafik för din start.

### <a name="install-a-web-application-firewall"></a>Installera en brandvägg för webbaserade program

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Vanliga bland annat är SQL-inmatningsattacker och cross-site skriptattacker. Kan vara svårt att förhindra dessa attacker i programkoden. Det kan kräva ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. En centraliserad WAF gör säkerhetshantering enklare. En brandväggslösning kan också reagera på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med säkra varje enskilt webbprogram.

Den [Azure Application Gateway WAF](https://docs.microsoft.com/azure/application-gateway/waf-overview) ger ett centraliserat skydd för dina webbprogram mot vanliga kryphål och säkerhetsproblem. WAF är baserat på regler från den [OWASP core rule sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9.

### <a name="create-an-incident-response-plan"></a>Skapa en plan för incidentsvar

Förbereda en plan för incidentsvar är avgörande för att hjälpa dig att hantera nya hot som kan dyker upp med tiden. Förbereda en plan för incidentsvar ingår identifiera lämpliga nödfall säkerhetskontakter och upprätta security serviceplaner för kod som har ärvts från andra grupper i organisationen och licensierade från tredje part-kod.

### <a name="conduct-a-final-security-review"></a>Utför en slutlig säkerhetsgranskning

Granska avsiktligt alla säkerhetsaktiviteter som utförts säkerställer beredskap för din version av programvaran eller ditt program. Den slutliga säkerhetsgranskningen (FSR) innehåller vanligtvis undersöka hotmodeller, verktyg för utdata och prestanda mot kvalitet gates och bugg staplar som definierades i fasen krav.

### <a name="certify-release-and-archive"></a>Certifiera versionen och arkivering

Certifiera programvara innan en version hjälper till att säkerställa att säkerhet och sekretess krav är uppfyllda. Det är viktigt för att utföra underhåll efter publiceringsuppgifter arkivering av alla relevanta data. Arkivering också hjälper till att lägre långsiktig kostnaderna för varaktigt programvaruutveckling.

## <a name="response"></a>Svar
Svar efter produktlanseringen fas affärsappars Utvecklingsteamet kan och tillgängliga att någon information om den nya programvaruhot och sårbarheter.

### <a name="execute-the-incident-response-plan"></a>Köra incidentsvarsplanen

Att kunna implementera incidentsvarsplanen inleds i fasen versionen är viktigt för att skydda kunder från säkerhets- eller säkerhetsproblem för programvaran som dyker upp.

### <a name="monitor-application-performance"></a>Övervaka programprestanda

Kontinuerlig övervakning av ditt program när den har distribuerats potentiellt hjälper dig att identifiera problem med prestanda, samt säkerhetsproblem.
Azure-tjänster som hjälper med övervakning av är:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar tjänst Application Performance Management (APM) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Application Insights identifierar automatiskt prestandaavvikelser. Den inkluderar kraftfulla analysverktyg som hjälper dig att diagnostisera problem och förstå vad användare faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) hjälper dig att förhindra, upptäcka och svara på hot med ökad insyn i (och kontroll över) säkerheten för dina Azure-resurser, inklusive webbprogram. Azure Security Center hjälper dig att identifiera hot som kan annars oupptäckta. Det fungerar med olika säkerhetslösningar.

Security Centers kostnadsfria nivån ger begränsad säkerhet för dina Azure-resurser endast. Den [Security Center Standard-nivån](https://docs.microsoft.com/azure/security-center/security-center-onboarding) utökar funktionerna till lokala resurser och andra moln.
Security Center Standard kan du:

  - Hitta och åtgärda säkerhetsproblem.
  - Tillämpa åtkomst- och programkontroller för att blockera skadlig aktivitet.
  - Identifiera hot med analys och för.
  - Svara snabbt under attacker.

## <a name="next-steps"></a>Nästa steg
I följande artiklar, rekommenderar vi säkerhetskontroller och aktiviteter som kan hjälpa dig att utforma och utveckla säkra program.

- [Utforma säkra program](secure-design.md)
- [Utveckla säkra program](secure-develop.md)
