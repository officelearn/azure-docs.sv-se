---
title: Identifieringsfunktioner i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur identifieringsfunktionerna i Azure Security Center fungerar.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 4c5599cc-99a1-430f-895f-601615ef12a0
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: 5f0f6a6d2c3e919c59f030e3cf47fc208f14caea
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233430"
---
# <a name="azure-security-center-detection-capabilities"></a>Identifieringsfunktioner i Azure Security Center
Det här dokumentet beskriver de avancerade identifieringsfunktionerna i Azure Security Center, som hjälper dig att identifiera aktiva hot mot dina Microsoft Azure-resurser och som ger dig de insikter som du behöver för att reagera snabbt.

Avancerade identifieringar är tillgängliga på standardnivån för Azure Security Center. En kostnadsfri 60-dagars utvärderingsversion är tillgänglig. Information om hur du uppgraderar prisnivån finns i avsnittet om [säkerhetsprinciper](security-center-policies.md). Mer information om priser finns på [sidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/). 

> [!NOTE]
> Security Center har för den begränsade förhandsversionen publicerat en ny uppsättning identifieringar som utnyttjar granskad poster, ett gemensamt granskningsramverk för att upptäcka skadligt beteende på Linux-datorer. Skicka ett e-postmeddelande med dina prenumerations-id:n till [oss](mailto:ASC_linuxdetections@microsoft.com) för att ta del av förhandsversionen.

## <a name="responding-to-todays-threats"></a>Svara på vår tids hot
Hoten i dag ser helt annorlunda ut mot för 20 år sedan. Dåtidens företag behövde oftast bara oroa sig för angrepp mot deras webbplatser av enskilda individer, som oftast bara var ute efter att ”se vad de kunde göra”. Dagens angripare är mycket mer sofistikerade och organiserade. De har ofta specifika ekonomiska och strategiska mål. De har också fler resurser till hands eftersom de kan finansieras av stater eller kriminella organisationer.

Allt detta har gett upphov till en helt ny nivå av sofistikerade angrepp. Dagens angripare är inte intresserade av att bara vanställa en webbplats. Nu vill de stjäla information, konton och personliga data – som kan säljas på den öppna marknaden eller utnyttjas i affärsrelaterat, politiskt eller militärt syfte. Ännu mer oroande än angripare med ekonomiska mål är de som bryter sig in i nätverk för att skada infrastruktur och individer.

För att skydda sig mot dessa situationer distribuerar dagens organisationer ofta flera punktlösningar, som fokuserar på att skydda företagets nätverk eller slutpunkter genom att leta efter kända attacksignaturer. Dessa lösningar genererar ofta stora volymer av relativt otillförlitliga varningar, som en säkerhetsanalytiker måste testa och undersöka. De flesta organisationer har inte den tid och kunskap som krävs för att agera på dessa varningar, vilket gör att många av dem inte åtgärdas.  Samtidigt har angriparna utvecklat sina metoder för att kringgå många signaturbaserade skydd och [anpassa sig till dagens molnmiljöer](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nya metoder krävs för att snabbt identifiera nya hot och påskynda identifieringen och företagets agerande. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Så här identifierar och svarar Azure Security Center på hot
Microsofts säkerhetsforskare söker hela tiden efter hot. De har tillgång till en omfattande uppsättning telemetri från Microsofts globala närvaro i molnet och i lokala infrastrukturer. Med den här omfattande och mångfaldiga samlingen data kan Microsoft identifiera nya angreppsmönster och trender i lokala konsument- och företagsprodukter, och i onlinetjänster. Detta gör i sin tur att identifieringsalgoritmerna i Security Center snabbt kan uppdateras allt eftersom angripare hittar nya och alltmer sofistikerade sätt att utnyttja systemen. Och för dig som kund innebär det att du kan hålla jämna steg med dagens snabbt föränderliga hotmiljö. 

Hotidentifieringen i Security Center sker genom automatisk insamling av säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas.

![Datainsamling och datapresentation i Security Center](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Framsteg inom stordata- och [maskininlärningstekniker](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) utnyttjas för att utvärdera händelser i hela molninfrastrukturen, vilket gör det möjligt att upptäcka hot som skulle vara omöjliga att identifiera med manuella metoder, samtidigt som det blir lättare att förutsäga utvecklingen av nya attacker. Dessa säkerhetsanalyser omfattar: 

* **Integrerad hotinformation**: Letar efter kända illvilliga aktörer genom att utnyttja global hotinformation från Microsofts produkter och tjänster, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa informationskällor.
* **Beteendeanalys**: Tillämpar kända mönster för att identifiera skadligt beteende. 
* **Avvikelseidentifiering**: Använder statistisk profilering för att skapa en historisk baslinje. Den här typen av identifiering varnar vid avvikelser från upprättade baslinjer som matchar vektorn för ett potentiellt angrepp.

### <a name="threat-intelligence"></a>Hotinformation
Microsoft har tillgång till en enorm mängd global hotinformation. Telemetri flödar in från flera källor, till exempel Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) och Microsoft Security Response Center (MRSC). Forskare får även hotinformation som delas med viktiga providers och prenumeranter av molntjänster och i feeds om hotinformation från tredje parter. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer. Några exempel är:

* **Utgående kommunikation till en skadlig IP-adress**: Utgående trafik till ett känt botnät eller ”darknet” tyder sannolikt på att resursen har komprometterats och att en angripare försöker köra kommandon i systemet eller stjäla data. Azure Security Center jämför nätverkstrafik med Microsofts globala hotdatabas och varnar dig om kommunikation till en skadlig IP-adress identifieras.

## <a name="behavioral-analytics"></a>Beteendeanalys
Beteendeanalys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder, och av expertanalytiker genom noggrann analys av skadliga beteenden. Azure Security Center kan använda beteendeanalyser för att identifiera resurser som har komprometterats baserat på analysen av loggar för virtuella datorer, loggar för virtuella nätverksenheter, infrastrukturloggar, kraschdumpfiler och andra källor. 

Dessutom korreleras data med andra tecken för att hitta bevis på utbredda attacker. Den här korrelationen gör det möjligt att identifiera händelser som matchar fastställda hotindikatorer. Några exempel är:

* **Misstänkt körning av processer**: Angripare använder flera metoder för att köra skadlig programvara utan identifiering. Till exempel kan en angripare ge skadlig kod samma namn som legitima systemfiler, men placera filerna på en annan plats, använda ett namn som liknar namnet på en oskadlig fil eller maskera filens egentliga filnamnstillägg. Security Center-modellerna bearbetar beteenden och övervakar körningen av processer för att identifiera avvikelser som dessa.  
* **Dold skadlig kod och försök att utnyttja system**: Avancerad skadlig kod kan ta sig förbi traditionella produkter mot skadlig kod genom att aldrig skriva till en disk eller genom att kryptera programvarukomponenter som lagras på en disk.  Sådan skadlig kod kan dock identifieras med minnesanalyser eftersom den skadliga koden måste lämna spår i minnet för att kunna fungera. När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen.  Genom att analysera minnet i kraschdumpen kan Azure Security Center identifiera tekniker som utnyttjar sårbarheter i programvara, som kommer åt känsliga data och som ligger gömda på en komprometterad dator, utan att påverka datorns prestanda.
* **Lateral förflyttning och intern rekognosering**: För att vara kvar i ett komprometterat nätverk och lokalisera/samla in värdefulla data försöker angripare ofta röra sig lateralt från den komprometterade datorn till andra datorer i samma nätverk. Security Center övervakar process- och inloggningsaktiviteter för att identifiera försök att expandera en angripares fäste i nätverket, till exempel kommandokörning via fjärranslutning, nätverksavsökning och kontouppräkning.
* **Skadliga PowerShell-skript**: PowerShell används av angripare för att köra skadlig kod på virtuella måldatorer för en rad olika syften. Security Center inspekterar PowerShell-aktivitet för att hitta tecken på misstänkt aktivitet. 
* **Utgående attacker**: Angripare attackerar ofta resurser i molnet med avsikten att använda dessa resurser för att bygga upp fler attacker. Komprometterade virtuella datorer kan till exempel användas för att köra råstyrkeattacker mot andra virtuella datorer, skicka skräppost eller söka av öppna portar och andra enheter på Internet. Genom att använda maskininlärning för nätverkstrafiken kan Security Center upptäcka avvikande utgående nätverkskommunikation. När det gäller skräppost kan Security Center även korrelera ovanlig e-posttrafik med information fån Office 365 för att avgöra om e-posten är skräppost eller en legitim e-postkampanj.  

### <a name="anomaly-detection"></a>Avvikelseidentifiering
Azure Security Center använder också avvikelseidentifiering för att identifiera hot. Till skillnad mot beteendeanalyser (som är beroende av kända mönster som härleds från stora datamängder) är avvikelseidentifiering mer ”anpassad” och utgår från standarder som är specifika för dina distributioner. Maskininlärning tillämpas för att fastställa om aktiviteten i dina distributioner är normal. Sedan genereras regler som definierar avvikande förhållanden som kan representera en säkerhetshändelse. Här är ett exempel:

* **Inkommande råstyrkeattacker med RDP/SSH**: Dina distributioner kan ha upptagna virtuella datorer med ett stort antal inloggningar varje dag och andra virtuella datorer som bara har ett fåtal om ens någon inloggning. Azure Security Center kan fastställa standardvärden för inloggningsaktiviteten för dessa virtuella datorer och använda maskininlärning för att definiera vad som avviker från normal inloggningsaktivitet. Om antalet inloggningar eller tiden på dagen då inloggningarna sker eller den plats som inloggningarna begärs från eller andra inloggningsrelaterade egenskaper kraftigt avviker från standardvärdena så kan en varning skickas. Maskininlärningen avgör vad som är viktigt.

## <a name="continuous-threat-intelligence-monitoring"></a>Kontinuerlig övervakning av hotinformation
Azure Security Center är knutet till säkerhetsforskning och datavetenskapsteam som hela tiden bevakar ändringar i hotlandskapet. Bland annat kan följande projekt nämnas:

* **Övervakning av hotinformation**: Hotinformation inbegriper mekanismer, indikatorer, effekter och rekommendationer relaterade till befintliga eller nya hot. Den här informationen delas i säkerhets-communityn och Microsoft övervakar feeds om hotinformation från interna och externa källor.
* **Informationsdelning**: Säkerhetsteamen som arbetar med Microsofts breda utbud av molnbaserade och lokala tjänster, servrar och enheter för klientslutpunkter delar och analyserar sina insikter. 
* **Microsofts säkerhetsexperter**: Kontinuerligt arbete i team inom hela Microsoft som arbetar inom specialiserade säkerhetsområden, exempelvis datautredning och identifiering av webbattacker.
* **Identifieringsjustering**: Algoritmer körs mot kundens verkliga datauppsättningar och säkerhetsanalytiker arbetar med kunden för att granska resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade ansträngningar resulterar i nya och förbättrade identifieringar, som du kan dra nytta av direkt – ingen åtgärd krävs av dig.

## <a name="see-also"></a>Se också
I det här dokumentet har vi tittat på identifieringsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Säkerhetsaviseringar per typ i Azure Security Center](security-center-alerts-type.md)
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

