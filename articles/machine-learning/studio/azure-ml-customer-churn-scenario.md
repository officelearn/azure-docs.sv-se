---
title: Analysera kundomsättning
titleSuffix: ML Studio (classic) - Azure
description: Fallstudie av att utveckla en integrerad modell för att analysera och göra bedömning av kundomsättning med Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204536"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analysera kundomsättning med Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Översikt
Den här artikeln innehåller en referensimplementering av ett analysprojekt för kundomsättning som skapas med hjälp av Azure Machine Learning Studio (klassisk). I den här artikeln diskuterar vi associerade generiska modeller för att holistiskt lösa problemet med industriell kundomsättning. Vi mäter också noggrannheten hos modeller som är byggda med hjälp av Machine Learning och utvärderar anvisningar för vidareutveckling.  

### <a name="acknowledgements"></a>Bekräftelser
Det här experimentet har utvecklats och testats av Serge Berger, Principal Data Scientist på Microsoft, och Roger Barga, tidigare Product Manager för Microsoft Azure Machine Learning Studio (klassisk). Azure-dokumentationsteamet bekräftar tacksamt sin expertis och tackar dem för att de delar det här faktabladet.

> [!NOTE]
> De data som används för det här experimentet är inte allmänt tillgängliga. Ett exempel på hur du skapar en maskininlärningsmodell för omsättningsanalys finns i: [Modellmall för butiksomsättning](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) i [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problemet med kundomsättning
Företag på konsumentmarknaden och inom alla företagssektorer måste hantera omsättning. Ibland churn är överdriven och påverkar politiska beslut. Den traditionella lösningen är att förutsäga hög benägenhet churners och tillgodose deras behov via en concierge-tjänst, marknadsföringskampanjer, eller genom att tillämpa särskilda dispenser. Dessa metoder kan variera från industri till industri. De kan till och med variera från ett visst konsumentkluster till en annan inom en bransch (t.ex. telekommunikation).

Den gemensamma faktorn är att företagen måste minimera dessa särskilda kundlojalitet insatser. Således skulle en naturlig metod vara att göra mål varje kund med sannolikheten för churn och ta itu med de bästa N dem. De bästa kunderna kan vara de mest lönsamma. I mer sofistikerade scenarier används till exempel en vinstfunktion vid urvalet av kandidater för särskild dispens. Dessa överväganden är dock bara en del av den fullständiga strategin för att hantera omsättning. Företagen måste också ta hänsyn till risk (och tillhörande risktolerans), nivån och kostnaden för interventionen och rimlig kundsegmentering.  

## <a name="industry-outlook-and-approaches"></a>Industrins utsikter och tillvägagångssätt
Sofistikerad hantering av churn är ett tecken på en mogen industri. Det klassiska exemplet är telekommunikationsindustrin där abonnenterna ofta är kända för att byta från en leverantör till en annan. Denna frivilliga omsättning är en viktig fråga. Dessutom har leverantörerna samlat på sig betydande kunskap om *churn-drivrutiner*, vilket är de faktorer som driver kunderna att byta.

Till exempel, handenhet eller enhet val är en välkänd drivrutin för churn i mobiltelefonen verksamheten. Som ett resultat är en populär politik att subventionera priset på en telefon för nya abonnenter och ta ut ett fullt pris till befintliga kunder för en uppgradering. Historiskt sett har denna policy lett till att kunder hoppar från en leverantör till en annan för att få en ny rabatt. Detta har i sin tur föranlett leverantörerna att förfina sina strategier.

Hög volatilitet i luren erbjudanden är en faktor som snabbt ogiltigförklarar modeller av churn som är baserade på nuvarande handenhet modeller. Dessutom mobiltelefoner är inte bara telekommunikation enheter, de är också mode uttalanden (anser iPhone). Dessa sociala prediktorer omfattas inte av vanliga telekommunikationsdatamängder.

Nettoresultatet för modellering är att du inte kan utforma en sund politik helt enkelt genom att eliminera kända skäl för churn. I själva verket är en kontinuerlig modellering strategi, inklusive klassiska modeller som kvantifierar kategoriska variabler (t.ex. beslut träd), **obligatoriskt**.

Med hjälp av stordatauppsättningar på sina kunder utför organisationer stordataanalys (i synnerhet omsättningsidentifiering baserat på stordata) som en effektiv metod för problemet. Du kan hitta mer om big data-metoden för problemet med omsättning i avsnittet Rekommendationer på ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metod för att modellera kundomsättning
En vanlig problemlösningsprocess för att lösa kundomsättning skildras i figurerna 1-3:  

1. Med en riskmodell kan du överväga hur åtgärder påverkar sannolikhet och risk.
2. Med en interventionsmodell kan du överväga hur interventionsnivån kan påverka sannolikheten för omsättning och mängden kundlivslängdsvärde (CLV).
3. Denna analys lämpar sig för en kvalitativ analys som eskaleras till en proaktiv marknadsföringskampanj som riktar sig till kundsegment för att leverera det optimala erbjudandet.  

![Diagram som visar hur risktolerans plus beslutsmodeller ger användbara insikter](./media/azure-ml-customer-churn-scenario/churn-1.png)

Denna framåtblickande strategi är det bästa sättet att behandla omsättning, men det kommer med komplexitet: vi måste utveckla en flermodell arketyp och spår beroenden mellan modellerna. Interaktionen mellan modeller kan kapslas in enligt följande diagram:  

![Interaktionsdiagram för omsättningsmodell](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figur 4: Enhetlig arketyp med flera modeller*  

Interaktion mellan modellerna är nyckeln om vi ska kunna leverera en helhetssyn på kundlojalitet. Varje modell försämras nödvändigtvis med tiden; Därför är arkitekturen en implicit loop (liknande den arketyp som anges av CRISP-DM-data mining standard, [***3***]).  

Den övergripande cykeln av risk-beslut-marknadsföring segmentering / nedbrytning är fortfarande en generaliserad struktur, som är tillämplig på många affärsproblem. Churn analys är helt enkelt en stark representant för denna grupp av problem eftersom det uppvisar alla egenskaper hos ett komplext affärsproblem som inte tillåter en förenklad prediktiv lösning. De sociala aspekterna av det moderna tillvägagångssättet för churn lyfts inte bestämt i att närma sig, men de sociala aspekterna inkapslas in i modellerar arketypen, som de skulle är i någon modellerar.  

Ett intressant tillägg här är big data analytics. Dagens telekommunikations- och detaljhandelsföretag samlar in uttömmande data om sina kunder, och vi kan lätt förutse att behovet av multi-model-konnektivitet kommer att bli en gemensam trend, med tanke på nya trender som Sakernas Internet och allestädes närvarande enheter, vilket gör det möjligt för företag att använda smarta lösningar vid flera lager.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementera modellarketypen i Machine Learning Studio (klassisk)
Med tanke på det beskrivna problemet, vilket är det bästa sättet att genomföra en integrerad modellering och scoring strategi? I det här avsnittet visar vi hur vi åstadkom detta genom att använda Azure Machine Learning Studio (klassisk).  

Multimodellmetoden är ett måste när du utformar en global arketyp för omsättning. Även poängsättning (prediktiv) del av metoden bör vara multi-modell.  

Följande diagram visar prototypen vi skapade, som använder fyra bedömningsalgoritmer i Machine Learning Studio (klassisk) för att förutsäga omsättning. Anledningen till att använda en multi-modell strategi är inte bara att skapa en ensemble klassificerare för att öka noggrannheten, men också för att skydda mot över-montering och för att förbättra föreskrivande funktionen urval.  

![Skärmbild som visar en komplex Studio-arbetsyta (klassisk) med många sammankopplade moduler](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figur 5: Prototyp av en omsättning modellering strategi*  

Följande avsnitt innehåller mer information om den modell för prototypbedömning som vi implementerade med Machine Learning Studio (klassisk).  

### <a name="data-selection-and-preparation"></a>Val och förberedelse av uppgifter
De data som används för att bygga modeller och poäng kunder erhölls från en CRM vertikal lösning, med data fördunklad för att skydda kundernas integritet. Data innehåller information om 8 000 prenumerationer i USA och kombinerar tre källor: etableringsdata (prenumerationsmetadata), aktivitetsdata (användning av systemet) och kundsupportdata. Uppgifterna innehåller inte någon affärsrelaterad information om kunderna. Den innehåller till exempel inte lojalitetsmetadata eller kreditpoäng.  

För enkelhetens skull är ETL- och datarensningsprocesser utanför tillämpningsområdet eftersom vi antar att dataförberedelse redan har gjorts någon annanstans.

Funktionsval för modellering baseras på preliminär signifikansbedömning av uppsättningen prediktorer, som ingår i processen som använder den slumpmässiga skogsmodulen. För implementeringen i Machine Learning Studio (klassisk) beräknade vi medelvärdet, medianen och intervallen för representativa funktioner. Vi har till exempel lagt till aggregat för kvalitativa data, till exempel lägsta och högsta värden för användaraktivitet.

Vi fångade också tidsinformation för de senaste sex månaderna. Vi analyserade data för ett år och vi konstaterade att även om det fanns statistiskt signifikanta trender, är effekten på churn kraftigt minskat efter sex månader.  

Den viktigaste punkten är att hela processen, inklusive ETL, funktionsval och modellering implementerades i Machine Learning Studio (klassisk), med hjälp av datakällor i Microsoft Azure.   

Följande diagram illustrerar de data som användes.  

![Skärmbild som visar ett exempel på de data som används med råvärden](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Figur 6: Utdrag ur datakällan (fördunklad)*  

![Skärmbild som visar statistiska funktioner som extraherats från datakällan](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Figur 7: Funktioner som extraherats från datakällan*
 

> Observera att dessa data är privata och därför kan modellen och data inte delas.
> För en liknande modell som använder offentligt tillgängliga data finns dock i det här exempelexperimentet i [Azure AI Gallery:](https://gallery.azure.ai/) [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Om du vill veta mer om hur du kan implementera en omsättningsanalysmodell med Cortana Intelligence Suite rekommenderar vi även [den här videon](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) av Senior Program Manager Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmer som används i prototypen
Vi använde följande fyra maskininlärningsalgoritmer för att bygga prototypen (ingen anpassning):  

1. Logistisk regression (LR)
2. Förstärkt beslutsträd (BT)
3. Genomsnittlig perceptron (AP)
4. Stöd vektor maskin (SVM)  

Följande diagram illustrerar en del av experimentdesignytan, vilket anger i vilken ordning modellerna skapades:  

![Skärmbild av en liten del av studioexperimentduken](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Bild 8: Skapa modeller i Machine Learning Studio (klassisk)*  

### <a name="scoring-methods"></a>Bedömningsmetoder
Vi gjorde de fyra modellerna med hjälp av en märkt utbildningsdatauppsättning.  

Vi skickade också poängdatauppsättningen till en jämförbar modell som byggts med hjälp av skrivbordsutgåvan av SAS Enterprise Miner 12. Vi mätte noggrannheten i SAS-modellen och alla fyra Machine Learning Studio (klassiska) modeller.  

## <a name="results"></a>Resultat
I det här avsnittet presenterar vi våra resultat om modellernas riktighet, baserat på bedömningsdatauppsättningen.  

### <a name="accuracy-and-precision-of-scoring"></a>Precision och precision i poängsättning
I allmänhet ligger implementeringen i Azure Machine Learning Studio (klassisk) bakom SAS i noggrannhet med ca 10-15% (Area Under Curve eller AUC).  

Men det viktigaste måttet i churn är felklassificeringshastigheten: det vill säga av de bästa N-churners som förutspåddes av klassificeraren, vilken av dem faktiskt **inte** churn, och ändå fått särskild behandling? I följande diagram jämförs den här felklassificeringsfrekvensen för alla modeller:  

![Område under kurvdiagrammet som jämför prestanda för 4 algoritmer](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Figur 9: Prototypområde passau under kurvan*

### <a name="using-auc-to-compare-results"></a>Använda AUC för att jämföra resultat
Area Under Curve (AUC) är ett mått som representerar ett globalt mått på *separabilitet* mellan fördelningen av poäng för positiva och negativa populationer. Det liknar den traditionella ROC-grafen (Receiver Operator Characteristic), men en viktig skillnad är att AUC-måttet inte kräver att du väljer ett tröskelvärde. I stället sammanfattas resultaten över **alla** möjliga val. Däremot visar det traditionella ROC-diagrammet den positiva hastigheten på den vertikala axeln och den falska positiva frekvensen på den horisontella axeln, och klassificeringströskeln varierar.   

AUC används som ett mått på värde för olika algoritmer (eller olika system) eftersom det gör att modeller kan jämföras med hjälp av deras AUC-värden. Detta är ett populärt tillvägagångssätt inom branscher som meteorologi och biovetenskap. AUC representerar således ett populärt verktyg för att bedöma klassificerarnas prestanda.  

### <a name="comparing-misclassification-rates"></a>Jämföra felklassificeringskurser
Vi jämförde felklassificeringsfrekvensen på datauppsättningen i fråga med hjälp av CRM-data för cirka 8 000 prenumerationer.  

* SAS felklassificeringsgrad var 10-15%.
* Machine Learning Studio (klassisk) felklassificering var 15-20% för de 200-300 churners.  

Inom telekommunikationsindustrin är det viktigt att endast vända sig till de kunder som har störst risk att spotta genom att erbjuda dem en concierge-tjänst eller annan särbehandling. I det avseendet uppnår Machine Learning Studio -implementeringen (klassisk) resultat i nivå med SAS-modellen.  

På samma sätt är noggrannhet viktigare än precision eftersom vi mest är intresserade av att korrekt klassificera potentiella churners.  

Följande diagram från Wikipedia visar förhållandet i en livlig, lättförståelig grafik:  

![Två mål. Ett mål visar träff märken löst grupperade men nära bulls-eye märkt "Låg noggrannhet: god verklighet, dålig precision. Ett annat mål tätt grupperade men långt från bulls-eye märkt "Låg noggrannhet: dålig verklighet, bra precision"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Figur 10: Avvägning mellan noggrannhet och precision*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Noggrannhet och precisionsresultat för förstärkt beslutsträdmodell
Följande diagram visar råresultaten från bedömning med hjälp av Machine Learning-prototypen för den förstärkta beslutsträdmodellen, som råkar vara den mest exakta bland de fyra modellerna:  

![Tabellutdrag som visar noggrannhet, precision, återkallande, F-poäng, AUC, genomsnittlig loggförlust och träningsloggförlust för fyra algoritmer](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Figur 11: Förstärkta egenskaper för beslutsträdsmodell*

## <a name="performance-comparison"></a>Prestandajämn
Vi jämförde hastigheten med vilken data gjordes med machine learning studio -modellerna (klassiska) och en jämförbar modell som skapats med hjälp av skrivbordsutgåvan av SAS Enterprise Miner 12.1.  

I följande tabell sammanfattas algoritmernas prestanda:  

*Tabell 1. Algoritmernas allmänna prestanda (noggrannhet)*

| LR | BT | AP | Svm |
| --- | --- | --- | --- |
| Genomsnittlig modell |Den bästa modellen |Underpresterande |Genomsnittlig modell |

Modellerna värd i Machine Learning Studio (klassisk) överträffade SAS med 15-25% för hastighet utförande, men noggrannhet var till stor del i paritet.  

## <a name="discussion-and-recommendations"></a>Diskussion och rekommendationer
Inom telekommunikationsindustrin har flera metoder dykt upp för att analysera omsättning, bland annat:  

* Härleda mått för fyra grundläggande kategorier:
  * **Entitet (till exempel en prenumeration)**. Etablera grundläggande information om prenumerationen och/eller kunden som är föremål för omsättning.
  * **Aktivitet**. Hämta all möjlig användningsinformation som är relaterad till entiteten, till exempel antalet inloggningar.
  * **Kundsupport**. Skördsinformation från kundsupportloggar för att ange om prenumerationen hade problem eller interaktioner med kundsupport.
  * **Konkurrenskraftiga data och affärsdata**. Få all information om kunden (kan till exempel vara otillgänglig eller svår att spåra).
* Använd betydelse för att driva funktionsval. Detta innebär att den förstärkta beslut trädmodellen är alltid en lovande strategi.  

Användningen av dessa fyra kategorier skapar en illusion av att en enkel *deterministisk* metod, baserad på index som bildas på rimliga faktorer per kategori, bör räcka för att identifiera kunder i riskzonen för omsättning. Tyvärr, även om detta begrepp verkar rimligt, är det en falsk förståelse. Anledningen är att churn är en tidsmässig effekt och de faktorer som bidrar till churn är oftast i övergående tillstånd. Vad som får en kund att överväga att lämna idag kan vara annorlunda i morgon, och det kommer säkert att vara annorlunda sex månader från nu. Därför är en *probabilistisk* modell en nödvändighet.  

Denna viktiga iakttagelse förbises ofta i näringslivet, som i allmänhet föredrar en business intelligence-inriktad inställning till analys, mest för att det är en enklare sälja och medger enkel automatisering.  

Löftet om självbetjäningsanalys genom att använda Machine Learning Studio (klassisk) är dock att de fyra kategorierna av information, graderade efter avdelning eller avdelning, blir en värdefull källa för maskininlärning om omsättning.  

En annan spännande funktion som kommer i Azure Machine Learning Studio (klassisk) är möjligheten att lägga till en anpassad modul i databasen med fördefinierade moduler som redan är tillgängliga. Den här funktionen skapar i huvudsak en möjlighet att välja bibliotek och skapa mallar för vertikala marknader. Det är en viktig differentiator av Azure Machine Learning Studio (klassisk) på marknaden.  

Vi hoppas kunna fortsätta detta ämne i framtiden, särskilt relaterade till stordataanalys.
  

## <a name="conclusion"></a>Slutsats
I detta dokument beskrivs ett förnuftigt tillvägagångssätt för att ta itu med det gemensamma problemet med kundomsättning med hjälp av ett generiskt ramverk. Vi övervägde en prototyp för bedömningsmodeller och implementerade den med hjälp av Azure Machine Learning Studio (klassisk). Slutligen bedömde vi prototyplösningens noggrannhet och prestanda med avseende på jämförbara algoritmer i SAS.  

 

## <a name="references"></a>Referenser
[1] Predictive Analytics: Beyond the Predictions, W. McKnight, Information Management, juli/augusti 2011, s.18-20.  

[2] Wikipedia artikel: [Noggrannhet och precision](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Steg-för-steg data mining guide](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: Engagera dina kunder mer effektivt och drive värde](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco-mall för omsättningsmodell](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) i [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Bilaga
![Ögonblicksbild av en presentation på churn prototyp](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Bild 12: Ögonblicksbild av en presentation om churn prototyp*
