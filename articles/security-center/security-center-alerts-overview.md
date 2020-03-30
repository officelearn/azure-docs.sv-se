---
title: Säkerhetsaviseringar i Azure Security Center | Microsoft-dokument
description: I det här avsnittet beskrivs vilka säkerhetsaviseringar som är och de olika typerna som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 697c038a2fefdde8e488dad23a4e38e0b2b7b288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415857"
---
# <a name="security-alerts-in-azure-security-center"></a>Säkerhetsaviseringar i Azure Security Center

I Azure Security Center finns det en mängd aviseringar för många olika resurstyper. Security Center genererar aviseringar för resurser som distribueras på Azure och även för resurser som distribueras i lokala och hybridmolnmiljöer.

Säkerhetsaviseringar utlöses av avancerade identifieringar och är endast tillgängliga i standardnivån för Azure Security Center. Det finns en kostnadsfri utvärderingsversion. Information om hur du uppgraderar prisnivån finns i avsnittet om [säkerhetsprinciper](security-center-pricing.md). Mer information om priser finns på [sidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="responding-to-todays-threats"></a>Svar på dagens hot <a name="respond-threats"> </a>

Hoten i dag ser helt annorlunda ut mot för 20 år sedan. Tidigare har företag vanligtvis bara behövt oroa sig för webbplats defacement av enskilda angripare som var mest intresserade av att se "vad de kunde göra". Dagens angripare är mycket mer sofistikerade och organiserade. De har ofta specifika ekonomiska och strategiska mål. De har också fler resurser till hands eftersom de kan finansieras av stater eller kriminella organisationer.

Dessa förändrade realiteter har lett till en aldrig tidigare skådad nivå av professionalism i angriparen leden. Dagens angripare är inte intresserade av att bara vanställa en webbplats. De är nu intresserade av att stjäla information, finansiella konton och privata data - som alla de kan använda för att generera pengar på den öppna marknaden eller för att utnyttja en viss verksamhet, politisk eller militär position. Ännu mer oroande än angripare med ekonomiska mål är de som bryter sig in i nätverk för att skada infrastruktur och individer.

För att skydda sig mot dessa situationer distribuerar dagens organisationer ofta flera punktlösningar, som fokuserar på att skydda företagets nätverk eller slutpunkter genom att leta efter kända attacksignaturer. Dessa lösningar genererar ofta stora volymer av relativt otillförlitliga varningar, som en säkerhetsanalytiker måste testa och undersöka. De flesta organisationer har inte den tid och kunskap som krävs för att agera på dessa varningar, vilket gör att många av dem inte åtgärdas.  

Dessutom har angripare utvecklat sina metoder för att undergräva många signatur-baserade försvar och [anpassa sig till molnmiljöer](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nya metoder krävs för att snabbt identifiera nya hot och påskynda identifieringen och företagets agerande.

## <a name="what-are-security-alerts-and-security-incidents"></a>Vad är säkerhetsvarningar och säkerhetsincidenter? 

**Aviseringar** är de meddelanden som Security Center genererar när de upptäcker hot mot dina resurser. Security Center prioriterar och listar aviseringarna, tillsammans med den information som behövs för att du snabbt ska kunna undersöka problemet. Security Center ger även rekommendationer för hur du kan åtgärda ett angrepp.

**En säkerhetsincident** är en samling relaterade aviseringar, i stället för att ange varje avisering individuellt. Security Center använder [Cloud Smart Alert Correlation](security-center-alerts-cloud-smart.md) för att korrelera olika aviseringar och lågåtergivningssignaler till säkerhetsincidenter.

Med hjälp av incidenter ger Security Center dig en enda vy av en attackkampanj och alla relaterade aviseringar. Med den här vyn kan du snabbt förstå vilka åtgärder angriparen vidtog och vilka resurser som påverkades. Mer information finns i [Cloud smart alert korrelation](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>Hur upptäcker Security Center hot? <a name="detect-threats"> </a>

Microsofts säkerhetsforskare söker hela tiden efter hot. På grund av Microsofts globala närvaro i molnet och lokalt har de tillgång till en expansiv uppsättning telemetri. Den omfattande och mångsidiga insamlingen av datamängder gör det möjligt att upptäcka nya attackmönster och trender i sina lokala konsument- och företagsprodukter samt dess onlinetjänster. Detta gör i sin tur att identifieringsalgoritmerna i Security Center snabbt kan uppdateras allt eftersom angripare hittar nya och alltmer sofistikerade sätt att utnyttja systemen. Och för dig som kund innebär det att du kan hålla jämna steg med dagens snabbt föränderliga hotmiljö.

För att identifiera verkliga hot och minska falska positiva identifieringar samlar Security Center in, analyserar och integrerar loggdata från dina Azure-resurser och nätverket. Det fungerar också med anslutna partnerlösningar, som brandväggs- och slutpunktsskyddslösningar. Security Center analyserar den här informationen, som ofta korrelerar information från flera källor, för att identifiera hot.

![Datainsamling och datapresentation i Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Framsteg inom stordata- och [maskininlärningstekniker](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) utnyttjas för att utvärdera händelser i hela molninfrastrukturen, vilket gör det möjligt att upptäcka hot som skulle vara omöjliga att identifiera med manuella metoder, samtidigt som det blir lättare att förutsäga utvecklingen av nya attacker. Dessa säkerhetsanalyser omfattar:

* **Integrerad hotinformation**: Microsoft har en enorm mängd global hotinformation. Telemetri flödar in från flera källor, till exempel Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) och Microsoft Security Response Center (MSRC). Forskare får också information om hotinformation som delas mellan stora molntjänstleverantörer och flöden från andra tredje parter. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer.

* **Beteendeanalys**: Beteendeanalys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder, och av expertanalytiker genom noggrann analys av skadliga beteenden. Azure Security Center kan använda beteendeanalys för att identifiera komprometterade resurser baserat på analys av loggar för virtuella datorer, loggar för virtuella nätverksenheter, infrastrukturloggar, kraschdumpar och andra källor.

* **Avvikelseidentifiering**: Azure Security Center använder också avvikelseidentifiering för att identifiera hot. I motsats till beteendeanalys (som beror på kända mönster som härrör från stora datauppsättningar) är avvikelseidentifiering mer "anpassad" och fokuserar på baslinjer som är specifika för dina distributioner. Maskininlärning tillämpas för att fastställa om aktiviteten i dina distributioner är normal. Sedan genereras regler som definierar avvikande förhållanden som kan representera en säkerhetshändelse.

## <a name="how-are-alerts-classified"></a>Hur klassificeras varningar?

Security Center tilldelar en allvarlighetsgrad till aviseringar, så att du kan prioritera den ordning i vilken du deltar i varje avisering, så att när en resurs äventyras kan du komma åt den direkt. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen.

> [!NOTE]
> Allvarlighetsgrad för aviseringar visas på olika sätt i portalen och versioner av REST API som föredämde 01-01-2019. Om du använder en äldre version av API:et uppgraderar du för den konsekventa upplevelsen som beskrivs nedan.

- **Hög:** Sannolikheten är stor att din resurs äventyras. Du borde kolla upp det direkt. Security Center har stort förtroende för både det skadliga uppsåt och de resultat som används för att utfärda aviseringen. Till exempel en avisering som identifierar körningen av ett känt skadligt verktyg som Mimikatz, ett vanligt verktyg som används för stöld av autentiseringsuppgifter.
- **Medium:** Detta är förmodligen en misstänkt aktivitet kan tyda på att en resurs äventyras.
Security Center förtroende för analytiska eller konstaterande är medium och förtroendet för den skadliga avsikten är medelhög till hög. Dessa skulle vanligtvis vara maskininlärning eller anomali-baserade identifieringar. Ett inloggningsförsök från en avvikande plats.
- **Låg:** Detta kan vara en godartad positiv eller en blockerad attack.
   * Security Center är inte säker nog att avsikten är skadlig och verksamheten kan vara oskyldig. Logga rensa är till exempel en åtgärd som kan inträffa när en angripare försöker dölja sina spår, men i många fall är en rutinåtgärd som utförs av administratörer.
   * Säkerhetscenter brukar inte säga när attacker blockerades, om det inte är ett intressant fall som vi föreslår att du undersöker. 
- **Information:** Du ser bara informationsaviseringar när du detaljgranskar ned till en säkerhetsincident, eller om du använder REST API med ett specifikt varnings-ID. En incident består vanligtvis av ett antal aviseringar, varav vissa kan verka på egen hand vara endast informativa, men i samband med de andra aviseringarna kan vara värda en närmare titt. 

## <a name="continuous-monitoring-and-assessments"></a>Kontinuerlig övervakning och bedömningar

Azure Security Center drar nytta av att ha säkerhetsforskning och datavetenskap team i hela Microsoft som kontinuerligt övervakar för förändringar i hotlandskapet. Bland annat kan följande projekt nämnas:

* **Övervakning av hotinformation:** Hotinformation omfattar mekanismer, indikatorer, konsekvenser och användbara råd om befintliga eller framväxande hot. Den här informationen delas i säkerhets-communityn och Microsoft övervakar feeds om hotinformation från interna och externa källor.
* **Signaldelning**: Insikter från säkerhetsteam i Microsofts breda portfölj av moln- och lokala tjänster, servrar och klientslutpunktsenheter delas och analyseras.
* **Microsofts säkerhetsexperter**: Kontinuerligt arbete i team inom hela Microsoft som arbetar inom specialiserade säkerhetsområden, exempelvis datautredning och identifiering av webbattacker.
* **Identifieringsjustering**: Algoritmer körs mot kundens verkliga datauppsättningar och säkerhetsanalytiker arbetar med kunden för att granska resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade insatser kulminerar i nya och förbättrade identifieringar, som du kan dra nytta av direkt - det finns ingen åtgärd för dig att vidta.


## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig om de olika typer av aviseringar som är tillgängliga i Security Center. Mer information finns i:

* [Skydd mot hot i Azure Security Center](threat-protection.md) – För en kort beskrivning av källorna till säkerhetsaviseringarna som visas av Azure Security Center 
* **Säkerhetsaviseringar i Azure Activity Log** – Förutom att vara tillgängliga i Azure-portalen eller programmässigt granskas säkerhetsaviseringar och incidenter som händelser i Azure Activity [Log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). Mer information om händelseschemat finns [i Säkerhetsaviseringar i Azure Activity-loggen](https://go.microsoft.com/fwlink/?linkid=2114113)

