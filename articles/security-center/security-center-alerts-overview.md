---
title: Säkerhets aviseringar i Azure Security Center | Microsoft Docs
description: I det här avsnittet beskrivs vilka säkerhets aviseringar som är och vilka olika typer som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 514de1435519282335124bfd67bac82669240b78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616521"
---
# <a name="security-alerts-in-azure-security-center"></a>Säkerhetsaviseringar i Azure Security Center

I Azure Security Center finns det många olika typer av aviseringar för många olika resurs typer. Security Center genererar aviseringar för resurser som har distribuerats i Azure och även för resurser som distribueras i lokala miljöer och hybrid moln miljöer.

Säkerhets aviseringar utlöses av avancerade identifieringar och är bara tillgängliga på standard nivån för Azure Security Center. Det finns en kostnadsfri utvärderingsversion. Information om hur du uppgraderar prisnivån finns i avsnittet om [säkerhetsprinciper](security-center-pricing.md). Mer information om priser finns på [sidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/).

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

* **Integrerad Hot information**: söker efter kända dåliga aktörer genom att dra nytta av global Hot information från Microsofts produkter och tjänster, Microsoft Digital brottslighet Unit (DCU), Microsoft Security Response Center (MSRC) och externa feeds.
* **Beteende analys**: använder kända mönster för att identifiera skadligt beteende.
* **Avvikelse identifiering**: använder statistisk profilering för att bygga en historisk bas linje. Den här typen av identifiering varnar vid avvikelser från upprättade baslinjer som matchar vektorn för ett potentiellt angrepp.

I avsnitten nedan beskrivs var och en av dessa analyser i detalj.

### <a name="integrated-threat-intelligence"></a>Integrerad Hot information

Microsoft har tillgång till en enorm mängd global hotinformation. Telemetri flödar från flera källor, till exempel Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital brottslighet Unit (DCU) och Microsoft Security Response Center (MSRC). Forskare får också information om hot information som delas mellan större leverantörer av moln tjänster och feeds från andra tredje parter. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer.

### <a name="behavioral-analytics"></a>Beteendeanalys

Beteendeanalys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder, och av expertanalytiker genom noggrann analys av skadliga beteenden. Azure Security Center kan använda beteende analys för att identifiera komprometterade resurser baserat på analyser av loggar för virtuella datorer, loggar för virtuella nätverks enheter, infrastruktur loggar, krasch dum par och andra källor.

Dessutom finns det en korrelation med andra signaler för att kontrol lera om det finns stödjande bevis för en omfattande kampanj. Den här korrelationen gör det möjligt att identifiera händelser som matchar fastställda hotindikatorer. 

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Security Center använder också avvikelseidentifiering för att identifiera hot. Till skillnad mot beteendeanalyser (som är beroende av kända mönster som härleds från stora datamängder) är avvikelseidentifiering mer ”anpassad” och utgår från standarder som är specifika för dina distributioner. Maskininlärning tillämpas för att fastställa om aktiviteten i dina distributioner är normal. Sedan genereras regler som definierar avvikande förhållanden som kan representera en säkerhetshändelse.

## <a name="how-are-alerts-classified"></a>Hur klassificeras aviseringar?

Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

> [!NOTE]
> Aviseringens allvarlighets grad visas på ett annat sätt i portalen och versioner av den REST API som är dagens datum 01-01-2019. Om du använder en äldre version av API: t uppgraderar du för den konsekventa upplevelsen som beskrivs nedan.

- **Hög:** Det finns en hög sannolikhet för att din resurs komprometteras. Du bör titta på det direkt. Security Center har hög exakthet i både den skadliga avsikten och i de resultat som används för att utfärda aviseringen. Till exempel en avisering som identifierar körningen av ett känt skadligt verktyg, till exempel Mimikatz, ett vanligt verktyg som används för stöld av autentiseringsuppgifter.
- **Medel:** Detta är förmodligen en misstänkt aktivitet som kan tyda på att en resurs komprometteras.
Security Center tillförlitlighet i analys eller sökning är medel och säkerheten för den skadliga avsikten är medel hög till hög. Detta brukar vara maskin inlärning eller avvikande identifieringar. Till exempel ett inloggnings försök från en avvikande plats.
- **Låg:** Detta kan vara ett ofarligt positivt eller blockerat angrepp.
   * Security Center är inte tillräckligt tryggt att avsikten är skadlig och att aktiviteten kan vara Innocent. Logg rensning är till exempel en åtgärd som kan inträffa när en angripare försöker dölja sina spår, men i många fall är en rutin åtgärd som utförs av administratörer.
   * Security Center meddelar dig normalt inte när attacker blockeras, om det inte är ett intressant ärende som vi föreslår att du tittar på. 
- **Information:** Endast informations aviseringar visas när du ökar detalj nivån i en säkerhets incident, eller om du använder REST API med ett angivet aviserings-ID. En incident består vanligt vis av ett antal aviseringar, varav vissa kan visas på egen hand som endast information, men i samband med de andra aviseringarna kan det vara betrodd av en närmare titt. 
 

## <a name="continuous-monitoring-and-assessments"></a>Kontinuerlig övervakning och bedömningar

Azure Security Center fördelarna med att ha säkerhets forskning och data vetenskaps team i hela Microsoft som kontinuerligt övervakar för förändringar i hotets landskap. Bland annat kan följande projekt nämnas:

* **Övervakning av hot information**: Hot information innehåller mekanismer, indikatorer, effekter och åtgärds bara råd om befintliga eller nya hot. Den här informationen delas i säkerhets-communityn och Microsoft övervakar feeds om hotinformation från interna och externa källor.
* **Informationsdelning**: Säkerhetsteamen som arbetar med Microsofts breda utbud av molnbaserade och lokala tjänster, servrar och enheter för klientslutpunkter delar och analyserar sina insikter.
* **Microsofts säkerhetsexperter**: Kontinuerligt arbete i team inom hela Microsoft som arbetar inom specialiserade säkerhetsområden, exempelvis datautredning och identifiering av webbattacker.
* **Identifieringsjustering**: Algoritmer körs mot kundens verkliga datauppsättningar och säkerhetsanalytiker arbetar med kunden för att granska resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade ansträngningar resulterar i nya och förbättrade identifieringar, som du kan dra nytta av direkt – ingen åtgärd krävs av dig.

## Säkerhets aviserings <a name="security-alert-types"></a> typer

Följande avsnitt vägleder dig genom de olika aviseringarna, beroende på resurs typer:

* [Aviseringar för IaaS Windows-datorer](threat-protection.md#windows-machines)
* [Aviseringar för IaaS Linux-datorer](threat-protection.md#linux-machines)
* [Aviseringar för Azure App Service](threat-protection.md#app-services)
* [Aviseringar för Azure-behållare](threat-protection.md#azure-containers)
* [Aviseringar för SQL Database och SQL Data Warehouse](threat-protection.md#data-sql)
* [Aviseringar för Azure Storage](threat-protection.md#azure-storage)
* [Aviseringar för Cosmos DB](threat-protection.md#cosmos-db)

I följande avsnitt förklaras hur Security Center använder olika telemetri som samlas in från integrering med Azure-infrastrukturen för att kunna använda ytterligare skydds lager för resurser som distribueras i Azure:

* [Aviseringar för Azure Management Layer (Azure Resource Manager) (för hands version)](threat-protection.md#management-layer)
* [Aviseringar för Azure Key Vault (för hands version)](threat-protection.md#azure-keyvault)
* [Aviseringar för Azure nätverks lager](threat-protection.md#network-layer)
* [Aviseringar från andra tjänster](threat-protection.md#alerts-other)

## <a name="what-are-security-incidents"></a>Vad är säkerhets incidenter?

En säkerhets incident är en samling relaterade aviseringar i stället för att lista varje avisering individuellt. Security Center använder [Cloud Smart Alert-korrelation](security-center-alerts-cloud-smart.md) för att korrelera olika aviseringar och låg åter givning av signaler till säkerhets incidenter.

Med hjälp av incidenter ger Security Center en enda vy över en angrepps kampanj och alla relaterade aviseringar. Med den här vyn kan du snabbt förstå vilka åtgärder som angriparen tog och vilka resurser som påverkades. Mer information finns i [Cloud Smart Alert-korrelation](security-center-alerts-cloud-smart.md).

## <a name="security-alerts-in-azure-activity-log"></a>Säkerhets aviseringar i Azure aktivitets logg

Förutom att vara tillgängliga i Azure Portal eller program mässigt granskas säkerhets aviseringar och incidenter som händelser i [Azure aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). Mer information om händelse schemat finns i [säkerhets aviseringar i Azure aktivitets logg](https://go.microsoft.com/fwlink/?linkid=2114113).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de olika typerna av aviseringar som är tillgängliga i Security Center. Mer information finns i:

* [Planerings- och bruksanvisning för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Vanliga frågor och svar om Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq).

