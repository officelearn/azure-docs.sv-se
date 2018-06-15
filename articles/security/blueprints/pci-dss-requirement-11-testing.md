---
title: Azure betalning bearbetning modell - krav på tester
description: PCI DSS krav 11
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 21cb854ba35adcf7576874e3c6c7149fbd33d998
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894990"
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>Testa krav för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-11"></a>PCI DSS krav 11

**Regelbundet testa säkerhetssystem och processer**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

Säkerhetsproblem identifieras kontinuerligt genom obehörig forskare och som introduceras av ny programvara. Komponenter, processer och anpassade program bör testas ofta för att säkerställa säkerhetsåtgärder fortsätter att ändra miljö.

## <a name="pci-dss-requirement-111"></a>PCI DSS krav 11,1

**11,1** implementera processer för att testa efter trådlösa åtkomstpunkter (802.11) och upptäcka och identifiera alla behörighet och obehörig trådlösa åtkomstpunkter kvartalsvis.

> [!NOTE]
> Metoder som kan användas i processen omfattar men är inte begränsat till trådlösa nätverk sökningar, fysiska logiska kontroller av komponenter och infrastruktur, åtkomstkontrollen för nätverk (NAC) eller trådlösa-ID: N/IP-adresser.
Oavsett vilken metoder som används måste de vara tillräcklig för att upptäcka och identifiera både verifierade och overifierade enheter.


**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure inte Tillåt eller Tillåt trådlösa anslutningar i Azure nätverksmiljön. Interna säkerheten team söker regelbundet efter falsk trådlösa signaler kvartalsvis och falska signaler undersökas och tas bort. Kunder tillåts inte att distribuera trådlös teknik i Azure-miljön. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Trådlösa och SNMP har inte implementerats i lösningen.|



### <a name="pci-dss-requirement-1111"></a>PCI DSS krav 11.1.1

**11.1.1** Upprätthåll en förteckning över auktoriserade trådlösa åtkomstpunkter inklusive dokumenterade affärsjustering.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 11,1](#pci-dss-requirement-11-1). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Trådlösa och SNMP har inte implementerats i lösningen.|



### <a name="pci-dss-requirement-1112"></a>PCI DSS krav 11.1.2

**11.1.2** implementera incidenter procedurer i händelseloggen obehörig trådlösa åtkomstpunkter har identifierats.


**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 11,1](#pci-dss-requirement-11-1). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Trådlösa och SNMP har inte implementerats i lösningen.|



## <a name="pci-dss-requirement-112"></a>PCI DSS krav 11.2

**11.2** kör interna och externa nätverk säkerhetsproblem skannar åtminstone kvartalsvis och efter några betydande ändringar i nätverket (till exempel nya system komponentinstallationer, ändringar i nätverkstopologin, brandvägg regeln ändringar, produkt uppgraderingar). 

> [!NOTE]
> Flera genomsökning rapporter kan kombineras för varje kvartal skanningsprocessen att visa att alla datorer har genomsökts och alla tillämpliga sårbarheter har åtgärdats. Ytterligare dokumentation kan krävas för att verifiera ej åtgärdad säkerhetsrisker håller står inför.
> Inledande PCI DSS godkännande, är det inte krävs att fyra kvartal passera genomsökningar utföras om utvärderare verifierar 1) den senaste genomsökningen resultatet var en Skicka-sökning, 2) entiteten har dokumenterats principer och procedurer som kräver kvartalsvis genomsökning och 3) säkerhetsproblem som anges i resultatet av genomsökningen har korrigerats enligt en re-scan(s). Tillväxttakten efter första PCI DSS granska måste fyra kvartal passera genomsökningar ha inträffat.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure utför kvartalsvis interna och externa sårbarhetsgenomsökningar. Genomsökningar utförs av kvalificerad personal. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har testats penna och säkerhetsproblem genomsöks vi 'som är'. Testresultaten penna kan dupliceras med hjälp av gemensamma verktyg, till exempel nmap eller pentest tools.com. Resultatet av testet penna ger tveksam attacker med inga utvinningsbara objekt. Dessutom [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations) informera säkerhetsproblem och reparation.|



### <a name="pci-dss-requirement-1121"></a>PCI DSS krav 11.2.1

**11.2.1** utföra kvartalsvis interna sårbarhetsgenomsökningar. Åtgärdar säkerhetsproblem och utföra söka igen för att kontrollera att alla ”hög risk” sårbarheter löst i enlighet med entitetens säkerhetsproblem rangordning (per krav 6.1). Genomsökningar måste utföras av kvalificerad personal.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utför sökningar för säkerhetsriskerna omfattas underliggande infrastruktur. Microsoft Azure implementerar genomsökning av server-operativsystem, databaser och nätverksenheter med lämpliga säkerhetsproblem genomsökningsverktyg säkerhetsproblem. Azure webbprogram genomsöks med lämpliga industrin genomsökning lösningar. Sårbarhetsgenomsökningar utförs varje kvartal.<br /><br />Söker utförs vid behov mot alla system, tills alla ”hög risk” sårbarheter (som identifieras i kravet 6.1) har åtgärdats. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har testats penna och säkerhetsproblem genomsöks vi 'som är'. Testresultaten penna kan dupliceras med hjälp av gemensamma verktyg, till exempel nmap eller pentest tools.com. Resultatet av testet penna ger tveksam attacker med inga utvinningsbara objekt. Dessutom [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations) informera säkerhetsproblem och reparation.|



### <a name="pci-dss-requirement-1122"></a>PCI DSS krav 11.2.2

**11.2.2** utföra kvartalsvis externa sårbarhetsgenomsökningar, via en godkänd genomsökning leverantör (ASV) godkändes av betalningen Card Industry säkerhet standarder rådet (PCI SSC). Utföra söker efter behov, till dess passera genomsökningar uppnås. 

> [!NOTE]
> Varje kvartal externa sårbarhetsgenomsökningar måste utföras av en godkänd genomsökning leverantör (ASV), godkändes av betalningen Card Industry säkerhet standarder rådet (PCI SSC).
> Uppgraderingsguiden för ASV programmet publiceras på webbplatsen PCI SSC för genomsökning kundansvar, förberedelse av genomsökning, osv.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utför externa sökningar för säkerhetsriskerna omfattas underliggande infrastruktur som är tillgänglig externt. Genomsökningar utförs av en godkänd skanna leverantör (ASV).<br /><br />Microsoft Azure prenumererar på MSRC/OSSC månatliga korrigering meddelanden och söker efter säkerhetsrisker anställda. Identifierade säkerhetsrisker utvärderas och repareras per etablerade tidslinjen baserat på nivån av risk.<br /><br />Varje kvartal riktade omfattande säkerhetsproblem genomsöker mot prioriterad komponenter i Microsoft Azure-miljön utförs för att identifiera säkerhetsproblem. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | När distribuera Contoso Webstore, kunder i demonstrationen ansvarar för att utföra kvartalsvis externa säkerhetsproblem genomsökningar och söker efter behov mot PaaS alla instanser i sina miljöer för Kortinnehavarens data (CDE) med hjälp av en godkänd genomsökning leverantör (ASV) godkänd av betalningen Card Industry säkerhet standarder rådet.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>PCI DSS krav 11.2.3

**11.2.3** utföra interna och externa genomsökningar och söker efter några betydande ändringar. Genomsökningar måste utföras av kvalificerad personal.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Resultatet rapporteras till intressenter och reparation spåras av Azure Security Team till slut. Azure testresultaten kan delas med kunder sekretessavtal. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Kunder ansvarar för att utföra kvartalsvis interna och externa sårbarhetsgenomsökningar och söker efter behov mot alla PaaS-instanser i sina CDE. Genomsökningar ska utföras efter betydande förändringar i i scope-miljö.<br /><br />Genomsökningar måste utföras av en ASV eller personal organisationens oberoende.|



## <a name="pci-dss-requirement-113"></a>PCI DSS krav 11,3

**11,3** implementera en metod för att testa intrång som innehåller följande:
- Baserat på branschen accepteras intrång testning metoder (till exempel NIST SP800-115)
- Täcker hela CDE perimeternätverket och kritiska system
- Innehåller testa från både inom och utanför nätverket
- Innehåller tester för att verifiera alla segmentering och minskning av omfång kontroller
- Definierar programnivå intrång testerna för att innehålla minst de säkerhetsrisker som anges i kravet 6.5
- Definierar nätverksnivån intrång tester för att inkludera komponenter som stöder funktioner för nätverk samt operativsystem
- Granska och beaktandet av hot och säkerhetsrisker som inträffade under de senaste 12 månaderna
- Anger kvarhållning av intrång testning resultat och reparation aktiviteter resultat

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure verifierar tjänster med hjälp av intrång i tredje parts tio med CREST-certifierade Testare testa baserat på OWASP (Öppna Security Webbapprojektet) främsta. Resultat av försök spåras via ett register för risker som är granskas och granskas regelbundet för att säkerställa att att säkerhetsåtgärder. <br /><br />Microsoft använder också Red Teaming mot Microsoft-hanterad infrastruktur, tjänster och program. Inga slutet kundinformation riktar avsiktligt under Red Teaming och liveplatsen intrång testning. Testerna är mot Microsoft Azure-infrastrukturen och plattformar som Microsofts egna program och data. Kund-klienter, program och data som finns på Azure riktas aldrig.<br /><br />Microsoft Azure har anställd en oberoende utvärderare att utveckla en plan för riskbedömning av systemet och genomföra en utvärdering av kontroller. Kontroller bedömningar utförs varje år och resultatet rapporteras till relevanta parter. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har testats penna och säkerhetsproblem genomsöks vi 'som är'. Testresultaten penna kan dupliceras med hjälp av gemensamma verktyg, till exempel nmap eller pentest tools.com. Resultatet av testet penna ger tveksam attacker med inga utvinningsbara objekt. Dessutom [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations) informera säkerhetsproblem och reparation.|



### <a name="pci-dss-requirement-1131"></a>PCI DSS krav 11.3.1

**11.3.1** utför *externa* intrång testning minst årligen och efter några betydande infrastruktur eller uppgradering av programmet eller ändring (till exempel en uppgradering av operativsystemet, ett underordnade nätverk läggs till i miljö eller en webbserver som lagts till i miljön).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 11,3](#pci-dss-requirement-11-3). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har testats penna och säkerhetsproblem genomsöks vi 'som är'. Testresultaten penna kan dupliceras med hjälp av gemensamma verktyg, till exempel nmap eller pentest tools.com. Resultatet av testet penna ger tveksam attacker med inga utvinningsbara objekt. Dessutom [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations) informera säkerhetsproblem och reparation.|



### <a name="pci-dss-requirement-1132"></a>PCI DSS krav 11.3.2

**11.3.2** utför *interna* intrång testning minst årligen och efter några betydande infrastruktur eller uppgradering av programmet eller ändring (till exempel en uppgradering av operativsystemet, ett underordnade nätverk läggs till den miljö eller en webbserver som lagts till i miljön).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure-kontrakt med oberoende experter att utföra intrång tester för Microsoft Azure-gräns. Röd Team övningarna utförs även regelbundet och resultat som används för att göra förbättringar av säkerhet. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har testats penna och säkerhetsproblem genomsöks vi 'som är'. Testresultaten penna kan dupliceras med hjälp av gemensamma verktyg, till exempel nmap eller pentest tools.com. Resultatet av testet penna ger tveksam attacker med inga utvinningsbara objekt. Dessutom [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations) informera säkerhetsproblem och reparation.|



### <a name="pci-dss-requirement-1133"></a>PCI DSS krav 11.3.3

**11.3.3** utvinningsbara säkerhetsproblem hittas under testningen intrång korrigeras och testa upprepas för att verifiera korrigeringarna.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Procedurer har fastställts för att övervaka komponenter i Microsoft Azure-plattformen för kända säkerhetsproblem. <br /><br /><br /><br />Varje kvartal riktade omfattande säkerhetsproblem genomsöker mot prioriterad komponenter i Azure produktionsmiljön utförs för att identifiera säkerhetsproblem. Resultatet rapporteras till intressenter och reparation spåras av team till slut. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations), som ger säkerhetsproblem information och reparationer har använts för att säkerställa att alla återstående frågor har reparerats för Contoso Webstore demo CDE.|



### <a name="pci-dss-requirement-1134"></a>PCI DSS krav 11.3.4

**11.3.4** om segmentering används för att isolera CDE från andra nätverk, utföra intrång test och minst en gång om året efter ändringar segmentering kontroller/metoder för att verifiera att segmentering metoder är i drift och effektivt, och isolera alla out omfattas system från system i CDE.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Procedurer har fastställts för att övervaka komponenter i Microsoft Azure-plattformen för kända säkerhetsproblem. <br /><br /><br /><br />Varje kvartal riktade omfattande säkerhetsproblem genomsöker mot prioriterad komponenter i Azure produktionsmiljön utförs för att identifiera säkerhetsproblem. Resultatet rapporteras till intressenter och reparation spåras av team till slut. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations), som ger säkerhetsproblem information och reparationer har använts för att säkerställa att alla återstående frågor har reparerats för Contoso Webstore demo CDE.|



### <a name="pci-dss-requirement-11341"></a>PCI DSS krav 11.3.4.1

**11.3.4.1** *ytterligare krav för endast tjänstleverantörer:* om segmentering används bekräfta PCI DSS omfattningen genom att utföra tester på segmentering intrång avgör var sex månader och efter ändringar segmentering kontroller/metoder.

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 11.3.4](#pci-dss-requirement-11-3-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations), som ger säkerhetsproblem information och reparationer har använts för att säkerställa att alla återstående frågor har reparerats för Contoso Webstore demo CDE.|



## <a name="pci-dss-requirement-114"></a>PCI DSS krav 11,4

**11,4** använda identifiering av adressintrång och/eller intrångsskydd metoder för att identifiera och/eller förhindra intrång i nätverket. Övervaka all trafik på perimeternätverket Kortinnehavarens data miljön samt vid kritiska punkter i Kortinnehavarens datamiljö och Avisera personal till misstänkta kompromisser.
Kontinuerligt alla identifiering av adressintrång och förebygga motorer baslinjer och signaturer.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utför realtid analys av händelser inom driftsmiljön och ID: N system genererar nära realtid aviseringar om händelser som potentiellt kan äventyra systemet. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en PaaS-tjänst och intrångsidentifiering för nätverket och förebygga refererar till Azures ansvar. [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [Azure Advisor](/azure/advisor/advisor-security-recommendations) ange intrång varningar och reparation.|



## <a name="pci-dss-requirement-115"></a>PCI DSS krav 11,5

**11,5** distribuera en mekanism för identifiering av ändring av (till exempel-integritet övervakningsverktyg) till avisering personal att obehöriga ändringar (inklusive ändringar, tillägg och borttagningar) av kritiska systemfiler, konfigurationsfiler eller innehåll filer. och konfigurera programvaran för att utföra fil jämförelser minst en gång i veckan. 

> [!NOTE]
> För identifiering av ändring av är är viktiga filer vanligtvis de som inte ändrar regelbundet, men ändringen av som kan tyda på ett systemproblem eller risken för angrepp. Ändra identifieringsmetoder, till exempel-integritet övervakning produkter har vanligtvis redan konfigurerats med viktiga filer för relaterade operativsystemet. Andra viktiga filer, till exempel för anpassade program måste utvärderas och definieras av entiteten (det vill säga handlarens eller service provider).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure underhåller och meddelar kunder eventuella ändringar och händelser som kan påverka säkerheten eller tillgängligheten för tjänster via en online-tjänsten-instrumentpanelen. På webbplatsen Microsoft Azure uppdateras ändringar säkerhetsåtaganden och säkerhet skyldigheter av Microsoft Azure-kunder inom rimlig tid.<br /><br />Installationen eller ändringar av programvaran på Microsoft Azure produktionsmiljö är begränsat till auktoriserade administration personal och sätt ändra rutiner. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore demo är en PaaS-tjänst och identifiering av ändring av har implementerats med hjälp av logganalys. Mer information finns i [PCI vägledning - förinstallerat hanteringslösningar](payment-processing-blueprint.md#management-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>PCI DSS krav 11.5.1

**11.5.1** implementerar du en process för att svara på alla aviseringar som genereras av identifiering av ändring av lösningen.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure övervakning händelseregler ger ökad övervakning för hög risk åtgärder och tillgångar. Azure-hanterade nätverksenheter övervakas för att uppfylla etablerade säkerhetskrav. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore aviseringar för ändringar som tillhandahålls av logganalys-implementering. Mer information finns i [PCI vägledning - förinstallerat hanteringslösningar](payment-processing-blueprint.md#management-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>PCI DSS krav 11.6

**11.6** se till att säkerhetsprinciper och operativa procedurer för säkerhetsövervakning och testa dokumenterade används, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore aviseringar för ändringar som tillhandahålls av logganalys-implementering. Mer information finns i [PCI vägledning - förinstallerat hanteringslösningar](payment-processing-blueprint.md#management-solutions).<br /><br /><br /><br />|




