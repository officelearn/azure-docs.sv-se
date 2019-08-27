---
title: Säkerhets aviseringar i Azure Security Center | Microsoft Docs
description: I det här avsnittet beskrivs vilka säkerhets aviseringar som är och vilka olika typer som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/25/2019
ms.author: v-mohabe
ms.openlocfilehash: 1a3ce29750351d6037b376ebb93eb73e141c0727
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047578"
---
# <a name="security-alerts-in-azure-security-center"></a>Säkerhetsaviseringar i Azure Security Center

I Azure Security Center finns det många olika typer av aviseringar för många olika resurs typer. Security Center genererar aviseringar för resurser som har distribuerats i Azure och även för resurser som distribueras i lokala miljöer och hybrid moln miljöer.

Avancerade identifieringar är tillgängliga på standardnivån för Azure Security Center. Det finns en kostnadsfri utvärderingsversion. Information om hur du uppgraderar prisnivån finns i avsnittet om [säkerhetsprinciper](security-center-pricing.md). Mer information om priser finns på [sidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## Svara på dagens hot <a name="respond-threats"></a>

Hoten i dag ser helt annorlunda ut mot för 20 år sedan. Dåtidens företag behövde oftast bara oroa sig för angrepp mot deras webbplatser av enskilda individer, som oftast bara var ute efter att ”se vad de kunde göra”. Dagens angripare är mycket mer sofistikerade och organiserade. De har ofta specifika ekonomiska och strategiska mål. De har också fler resurser till hands eftersom de kan finansieras av stater eller kriminella organisationer.

Dessa förändringar av verklighet har lett till en oöverträffad nivå av yrkesutbildning i angrepps rang. Dagens angripare är inte intresserade av att bara vanställa en webbplats. De är nu intresserade av att stjäla information, finansiella konton och privata data – alla som de kan använda för att generera kontanter på den öppna marknaden eller för att utnyttja ett visst affärs-, politisk-eller militärt läge. Ännu mer oroande än angripare med ekonomiska mål är de som bryter sig in i nätverk för att skada infrastruktur och individer.

För att skydda sig mot dessa situationer distribuerar dagens organisationer ofta flera punktlösningar, som fokuserar på att skydda företagets nätverk eller slutpunkter genom att leta efter kända attacksignaturer. Dessa lösningar genererar ofta stora volymer av relativt otillförlitliga varningar, som en säkerhetsanalytiker måste testa och undersöka. De flesta organisationer har inte den tid och kunskap som krävs för att agera på dessa varningar, vilket gör att många av dem inte åtgärdas.  

Dessutom har angripare utvecklats sina metoder för att kringgå många signaturer och [anpassa sig till moln miljöer](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nya metoder krävs för att snabbt identifiera nya hot och påskynda identifieringen och företagets agerande.

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?

Aviseringar är de meddelanden som Security Center genererar när de identifierar hot på dina resurser. Security Center prioriterar och listar aviseringarna, tillsammans med den information som behövs för att snabbt undersöka problemet. Security Center innehåller också rekommendationer för hur du kan åtgärda ett angrepp.

## Hur identifierar Security Center hot? <a name="detect-threats"> </a>

Microsofts säkerhetsforskare söker hela tiden efter hot. På grund av Microsofts globala närvaro i molnet och lokalt, har de till gång till en mycket uppsättning telemetri. Med den breda samlingen av data uppsättningar kan du upptäcka nya angrepps mönster och trender i sina lokala konsument-och företags produkter, samt dess onlinetjänster. Detta gör i sin tur att identifieringsalgoritmerna i Security Center snabbt kan uppdateras allt eftersom angripare hittar nya och alltmer sofistikerade sätt att utnyttja systemen. Och för dig som kund innebär det att du kan hålla jämna steg med dagens snabbt föränderliga hotmiljö.

Security Center samlar in, analyserar och integrerar loggdata från dina Azure-resurser och nätverket för att identifiera verkliga hot och minska antalet falska positiva identifieringar. Den fungerar även med anslutna partner lösningar som brand Väggs-och slut punkts skydds lösningar. Security Center analyserar informationen, som ofta korrelerar information från flera källor, för att identifiera hot.

![Datainsamling och datapresentation i Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Framsteg inom stordata- och [maskininlärningstekniker](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) utnyttjas för att utvärdera händelser i hela molninfrastrukturen, vilket gör det möjligt att upptäcka hot som skulle vara omöjliga att identifiera med manuella metoder, samtidigt som det blir lättare att förutsäga utvecklingen av nya attacker. Dessa säkerhetsanalyser omfattar:

* **Integrerad Hot information**: Söker efter kända dåliga aktörer genom att använda global Hot information från Microsofts produkter och tjänster, Microsoft Digital brottslighet-enheten (DCU), Microsoft Security Response Center (MSRC) och externa feeds.
* **Beteende analys**: Använder kända mönster för att identifiera skadligt beteende.
* **Avvikelse identifiering**: Använder statistisk profilering för att bygga en historisk bas linje. Den här typen av identifiering varnar vid avvikelser från upprättade baslinjer som matchar vektorn för ett potentiellt angrepp.

I följande avsnitt beskrivs var och en av dessa analyser i detalj.

### <a name="integrated-threat-intelligence"></a>Integrerad Hot information

Microsoft har tillgång till en enorm mängd global hotinformation. Telemetri flödar in från flera källor, till exempel Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) och Microsoft Security Response Center (MRSC). Forskare får även hotinformation som delas med viktiga providers och prenumeranter av molntjänster och i feeds om hotinformation från tredje parter. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer.

### <a name="behavioral-analytics"></a>Beteendeanalys

Beteendeanalys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder, och av expertanalytiker genom noggrann analys av skadliga beteenden. Azure Security Center kan använda beteendeanalyser för att identifiera resurser som har komprometterats baserat på analysen av loggar för virtuella datorer, loggar för virtuella nätverksenheter, infrastrukturloggar, kraschdumpfiler och andra källor.

Dessutom korreleras data med andra tecken för att hitta bevis på utbredda attacker. Den här korrelationen gör det möjligt att identifiera händelser som matchar fastställda hotindikatorer. 

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Security Center använder också avvikelseidentifiering för att identifiera hot. Till skillnad mot beteendeanalyser (som är beroende av kända mönster som härleds från stora datamängder) är avvikelseidentifiering mer ”anpassad” och utgår från standarder som är specifika för dina distributioner. Maskininlärning tillämpas för att fastställa om aktiviteten i dina distributioner är normal. Sedan genereras regler som definierar avvikande förhållanden som kan representera en säkerhetshändelse.

## <a name="continuous-monitoring-and-assessments"></a>Kontinuerlig övervakning och bedömningar

Azure Security Center fördelarna med att ha säkerhets forskning och data vetenskaps team i hela Microsoft som kontinuerligt övervakar för förändringar i hotets landskap. Bland annat kan följande projekt nämnas:

* **Övervakning av hot information**: Hot information omfattar mekanismer, indikatorer, effekter och åtgärds bara råd om befintliga eller nya hot. Den här informationen delas i säkerhets-communityn och Microsoft övervakar feeds om hotinformation från interna och externa källor.
* **Signal delning**: Insikter från säkerhets team i Microsofts breda portfölj med moln tjänster och lokala tjänster, servrar och klient slut enheter delas och analyseras.
* **Microsoft**-säkerhetsspecialister: Pågående engagemang med team i Microsoft som arbetar i specialiserade säkerhets fält, t. ex. data utredning och identifiering av webb attacker.
* **Identifierings justering**: Algoritmer körs mot verkliga kund data uppsättningar och säkerhets forskare arbetar med kunder för att verifiera resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade ansträngningar resulterar i nya och förbättrade identifieringar, som du kan dra nytta av direkt – ingen åtgärd krävs av dig.

## Säkerhets aviserings <a name="security-alert-types"></a> typer

Följande avsnitt vägleder dig genom de olika aviseringarna, beroende på resurs typer:

* [Aviseringar om virtuella datorer och servrar i IaaS](security-center-alerts-iaas.md)
* [Interna beräknings aviseringar](security-center-alerts-compute.md)
* [Aviseringar för data tjänster](security-center-alerts-data-services.md)

I följande avsnitt förklaras hur Security Center använder olika telemetri som samlas in från integrering med Azure-infrastrukturen för att kunna använda ytterligare skydds lager för resurser som distribueras i Azure:

* [Aviseringar för service nivå](security-center-alerts-service-layer.md)
* [Integrering med Azures säkerhets produkter](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Vad är säkerhets incidenter?

En säkerhets incident är en samling relaterade aviseringar i stället för att lista varje avisering individuellt. Security Center använder [Cloud Smart Alert-korrelation](security-center-alerts-cloud-smart.md) för att korrelera olika aviseringar och låg åter givning av signaler till säkerhets incidenter.

Med hjälp av incidenter ger Security Center en enda vy över en angrepps kampanj och alla relaterade aviseringar. Med den här vyn kan du snabbt förstå vilka åtgärder som angriparen tog och vilka resurser som påverkades. Mer information finns i [Cloud Smart Alert-korrelation](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de olika typerna av aviseringar som är tillgängliga i Security Center. Mer information finns i:

* [Planerings- och bruksanvisning för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Vanliga frågor och svar om Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq).

