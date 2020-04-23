---
title: Analysera kund omsättning
titleSuffix: ML Studio (classic) - Azure
description: Fallstudie om att utveckla en integrerad modell för att analysera och värdera kund omsättningen med hjälp av Azure Machine Learning Studio (klassisk).
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
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analysera kund omsättning med Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Översikt
Den här artikeln visar en referens implementering av ett analys projekt för kund omsättning som har skapats med hjälp av Azure Machine Learning Studio (klassisk). I den här artikeln diskuterar vi tillhör ande allmänna modeller för holistiskt lösa problemet med den industriella kund omsättningen. Vi mäter också noggrannheten i modeller som har skapats med hjälp av Machine Learning, och bedöma vägvisningar för ytterligare utveckling.  

### <a name="acknowledgements"></a>Bekräftelser
Det här experimentet utvecklades och testades av Serge Berger, huvudsakliga data expert hos Microsoft och Roger Bargas, tidigare produkt chef för Microsoft Azure Machine Learning Studio (klassisk). Azures dokumentations grupp gratefully bekräftar sina kunskaper och tackar för att dela den här white paper.

> [!NOTE]
> Data som används för det här experimentet är inte offentligt tillgängliga. Ett exempel på hur du skapar en Machine Learning-modell för omsättnings analys finns i: [mallen Retail omsättnings modell](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) i [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problemet med kund omsättning
Företag på konsument marknaden och i alla företags sektorer måste hantera omsättningen. Ibland är omsättningen för stora och påverkar princip beslut. Den traditionella lösningen är att förutse benägenhet-dataomsättningar och att de kan hantera sina behov via en concierge-tjänst, marknadsförings kampanjer eller genom att använda särskilda dispens. Dessa metoder kan variera mellan olika branscher. De kan till och med variera från ett visst konsument kluster till ett annat inom en bransch (till exempel telekommunikation).

Den gemensamma faktorn är att företag behöver minimera dessa särskilda insatser för kund kvarhållning. Därför skulle en naturlig metod vara att Poäng för varje kund med sannolikheten att omsättningen och adressen till de översta N. De främsta kunderna kan vara de mest lönsamma. I mer avancerade scenarier används till exempel en vinst funktion under valet av kandidater för särskild dispens. Dessa överväganden är dock bara en del av hela strategin för att hantera omsättningen. Företag måste också ta hänsyn till risk (och associerad risk tolerans), nivån och kostnaden för interventionen och plausible kund segmentering.  

## <a name="industry-outlook-and-approaches"></a>Bransch Outlook och metoder
Avancerad hantering av omsättning är ett tecken på en vuxen bransch. Det klassiska exemplet är telekommunikations branschen där prenumeranter brukar byta från en leverantör till en annan. Denna frivilliga omsättning är ett viktigt problem. Dessutom har leverantörer samlat in betydande kunskaper om *omsättnings driv rutiner*, vilka är de faktorer som gör att kunderna kan byta.

Till exempel är telefonlur eller enhets val en välkänd driv rutin för omsättning i mobil telefonens verksamhet. Därför är en populär policy att ta ut priset på en telefonlur för nya prenumeranter och debitera ett fullständigt pris på befintliga kunder för en uppgradering. Historiskt sett har den här principen lett till kundernas hoppande från en leverantör till en annan för att få en ny rabatt. Detta, i sin tur, har uppmanat leverantörer att förfina sina strategier.

Hög flyktiga i mobila erbjudanden är en faktor som snabbt invaliderar modeller av omsättning som baseras på aktuella mobila modeller. Mobila telefoner är dessutom inte bara telekommunikations enheter, de är också mode-instruktioner (Överväg iPhone). De här sociala fördikteringarna ligger utanför omfånget för vanliga kommunikations data uppsättningar.

Netto resultatet av modellering är att du inte kan utforma en ljud princip genom att eliminera kända orsaker till omsättningen. I själva verket är en strategi för kontinuerlig modellering, inklusive klassiska modeller som kvantifierar kategoriska-variabler (till exempel besluts träd) **obligatoriska**.

Med hjälp av stora data uppsättningar på sina kunder utför organisationer stor data analys (särskilt omsättnings identifiering baserat på Big data) som en effektiv metod för problemet. Du hittar mer information om den stora data metoden för problemet med omsättning i avsnittet rekommendationer i ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metod för att modellera kund omsättning
En vanlig problemlösnings process för att lösa kund omsättningen beskrivs i figurerna 1-3:  

1. Med en risk modell kan du överväga hur åtgärder påverkar sannolikheten och risken.
2. Med en interventions modell kan du överväga hur nivån på ingreppet kan påverka sannolikheten för omsättning och antalet kundernas livstids värde (CLV).
3. Den här analysen lånar sig själv till en kvalitativ analys som eskaleras till en proaktiv marknadsförings kampanj som är riktad mot kund segment för att leverera det bästa erbjudandet.  

![Diagram över hur risk tolerans plus besluts modeller ger till åtgärds bara insikter](./media/azure-ml-customer-churn-scenario/churn-1.png)

Den här framåtriktade metoden är det bästa sättet att behandla omsättning, men det har en komplexitet: vi måste utveckla ett archetype och spårnings beroenden i flera modeller mellan modellerna. Interaktionen mellan modeller kan kapslas in som du ser i följande diagram:  

![Interaktions diagram över omsättnings modell](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figur 4: enhetlig archetype för flera modeller*  

Interaktionen mellan modellerna är nyckel om vi ska leverera en helhets strategi för kund kvarhållning. Varje modell behöver försämras med tiden. arkitekturen är därför en implicit slinga (liknar den archetype som anges av den skarpa DM-standarden, [***3***]).  

Den övergripande cykeln för risk-och marknads segmentering är fortfarande en generaliserad struktur som gäller för många affärs problem. Omsättnings analys är helt enkelt en stark representant för den här gruppen med problem, eftersom den innehåller alla egenskaper för ett komplext verksamhets problem som inte tillåter en förenklad förutsägelse lösning. De sociala aspekterna av den moderna metoden till omsättning är inte särskilt markerade i metoden, men de sociala aspekterna är inkapslade i archetype för modellering som de skulle vara i vilken modell som helst.  

Ett intressant tillägg här är stor data analys. Dagens telekommunikationer och detalj handels företag samlar in uttömmande data om sina kunder, och vi kan enkelt förutse att behovet av anslutning till flera modeller blir en gemensam tendens, och ger nya trender som Sakernas Internet-och allmänt förekommande-enheter, vilket gör det möjligt för företag att använda smarta lösningar på flera lager.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementera modellerings archetype i Machine Learning Studio (klassisk)
På det sätt som beskrivs är det bästa sättet att implementera en integrerad modell och en bedömnings metod? I det här avsnittet visar vi hur vi har utfört detta genom att använda Azure Machine Learning Studio (klassisk).  

Metoden för flera modeller är en måste när man utformar en global archetype för omsättning. Även om poängen (förutsägelse) i metoden bör vara Multi-Model.  

Följande diagram visar den prototyp som vi har skapat, som använder fyra bedömnings algoritmer i Machine Learning Studio (klassisk) för att förutse omsättningen. Anledningen till att använda en metod med flera modeller är inte bara att skapa en ensemble-klassificerare för att öka noggrannheten, utan även för att skydda mot överanpassning och för att förbättra för funktions urvalet.  

![Skärm bild som illustrerar en komplex Studio (klassisk) arbets yta med många sammankopplade moduler](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figur 5: prototyp för en metod för omsättnings modellering*  

I följande avsnitt finns mer information om prototyp bedömnings modellen som vi implementerade med hjälp av Machine Learning Studio (klassisk).  

### <a name="data-selection-and-preparation"></a>Val och förberedelse av data
De data som används för att bygga modeller och Poäng kunder hämtades från en vertikal CRM-lösning med data fördunklade för att skydda kundens sekretess. Datan innehåller information om 8 000-prenumerationer i USA och kombinerar tre källor: etablerings data (metadata för prenumerationer), aktivitets data (användning av systemet) och kund support data. Data innehåller ingen affärsrelaterad information om kunderna. Det omfattar till exempel inte förmåns-metadata eller kredit poäng.  

För enkelhetens skull är ETL-och data rengörings processerna utanför räckvidden, eftersom vi antar att förberedelsen av data redan har utförts någon annan stans.

Funktions val för modellering baseras på preliminära signifikans-Poäng för uppsättningen av förutsägare, som ingår i den process som använder den slumpmässiga modulen för skogar. För implementeringen i Machine Learning Studio (klassisk) beräknade vi medelvärde, median och intervall för representativa funktioner. Vi har till exempel lagt till agg regeringar för kvalitativa data, till exempel lägsta och högsta värden för användar aktivitet.

Vi har också samlat in temporal information för de senaste sex månaderna. Vi har analyserat data i ett år och vi har fastställt att även om det var statistiskt betydande trender, minskas effekterna på omsättningen avsevärt efter sex månader.  

Den viktigaste punkten är att hela processen, inklusive ETL, funktions val och modellering implementerades i Machine Learning Studio (klassisk) med hjälp av data källor i Microsoft Azure.   

Följande diagram illustrerar de data som användes.  

![Skärm bild som visar ett exempel på data som används med RAW-värden](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Bild 6: utdrag från data källa (fördunklade)*  

![Skärm bild som visar statistiska funktioner som har extraherats från data källan](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Bild 7: funktioner som extraherats från data källan*
 

> Observera att dessa data är privata och därför att modellen och data inte kan delas.
> Men för en liknande modell som använder offentligt tillgängliga data, se det här exempel experimentet i [Azure AI Gallery](https://gallery.azure.ai/): [Telco kund omsättning](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Om du vill veta mer om hur du kan implementera en omsättnings analys modell med Cortana Intelligence Suite rekommenderar vi även [den här videon](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) av Wee Hyong tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmer som används i prototypen
Vi använde följande fyra Machine Learning-algoritmer för att skapa prototypen (ingen anpassning):  

1. Logistisk regression (LR)
2. Utökat besluts träd (BT)
3. Genomsnittlig Perceptron (AP)
4. Support Vector Machine (SVM)  

Följande diagram illustrerar en del av experiment design ytan, som anger i vilken ordning modellerna skapades:  

![Skärm bild av en liten del av experiment arbets ytan i Studio](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Figur 8: skapa modeller i Machine Learning Studio (klassisk)*  

### <a name="scoring-methods"></a>Bedömnings metoder
Vi har betecknat fyra modeller med hjälp av en etikettad tränings data uppsättning.  

Vi skickade även Poäng data uppsättningen till en jämförbar modell som skapats med hjälp av Desktop-versionen av SAS Enterprise Miner 12. Vi mätte precisionen för SAS-modellen och alla fyra Machine Learning Studios modeller (klassiska).  

## <a name="results"></a>Resultat
I det här avsnittet presenterar vi våra resultat om noggrannheten i modellerna, baserat på Poäng data uppsättningen.  

### <a name="accuracy-and-precision-of-scoring"></a>Noggrannhet och precision för Poäng
I allmänhet är implementeringen i Azure Machine Learning Studio (klassisk) bakom SAS med noggrannhet med cirka 10-15% (area under kurva eller AUC).  

Det viktigaste måttet i omsättningen är dock den fel klassificerings takt: det vill säga av de x främsta omsättningarna som förutsägs av klassificeraren, vilket av dem som faktiskt **inte** hade omsättningen, och som ännu fått särskild behandling? Följande diagram jämför den här felklassificeringen för alla modeller:  

![Område under kurv diagrammet som jämför prestandan för 4 algoritmer](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Bild 9: Passau prototyp area under kurva*

### <a name="using-auc-to-compare-results"></a>Använda AUC för att jämföra resultat
Area under kurva (AUC) är ett mått som representerar ett globalt mått på *separability* mellan fördelningarna av Poäng för positiva och negativa populationer. Det liknar ROC-grafen (traditionell receiver operator), men en viktig skillnad är att AUC-måttet inte kräver att du väljer ett tröskelvärde. I stället sammanfattas resultaten över **alla** möjliga val. Det traditionella ROC-diagrammet visar däremot den positiva hastigheten på den lodräta axeln och den falska positiva positiva frekvensen på den vågräta axeln, och klassificerings tröskeln varierar.   

AUC används som ett mått på värt för olika algoritmer (eller olika system) eftersom det gör att modeller kan jämföras med hjälp av sina AUC-värden. Detta är en populär metod i branscher som meteorology och Biosciences. Därför representerar AUC ett populärt verktyg för att utvärdera klassificerings prestanda.  

### <a name="comparing-misclassification-rates"></a>Jämföra pris nivåer för klassificering
Vi jämförde de felklassificerings taxan för data uppsättningen i fråga med hjälp av CRM-data för cirka 8 000 prenumerationer.  

* Den felklassificerings frekvensen för SAS var 10-15%.
* Den felklassificerings takten för Machine Learning Studio (klassisk) var 15-20% för de översta 200-300-datakärlen.  

I telekommunikations branschen är det viktigt att endast hantera de kunder som har den högsta risken för omsättning genom att erbjuda dem en concierge-tjänst eller annan speciell behandling. I detta hänseende uppnår Machine Learning Studio (klassisk) implementeringen resultat på parivärde med SAS-modellen.  

Med samma token är precisionen viktigare än precisionen eftersom vi främst är intresserade av att klassificera potentiella omsättnings bara på rätt sätt.  

Följande diagram från wikipedia visar förhållandet i en Lively, användarvänlig bild:  

![Två mål. Ett mål visar att markeringen är löst grupperad men nära tjurarna-ögonen markerat med låg exakthet: bra värde, låg precision. Ett annat mål som är nära grupperat, men långt från tjurar – ögon markerat med låg exakthet: låg exakthet, bra precision](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Bild 10: kompromisser mellan precision och precision*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Precisions-och precisions resultat för bättre besluts träd modell
Följande diagram visar de råa resultaten från en bedömning med hjälp av Machine Learning prototypen för modell modellen med högre beslut, som är den mest exakta bland de fyra modellerna:  

![Tabell kodfragment som visar precision, precision, återkallande, F-score, AUC, genomsnittlig logg förlust och inlärnings logg förlust för fyra algoritmer](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Bild 11: modell egenskaper för utökat besluts träd*

## <a name="performance-comparison"></a>Prestanda jämförelse
Vi jämförde den hastighet med vilken data beskrevs med hjälp av Machine Learning Studio (klassiska) modeller och en jämförbar modell som skapats med hjälp av Desktop-versionen av SAS Enterprise Miner 12,1.  

I följande tabell sammanfattas algoritmernas prestanda:  

*Tabell 1. Allmänna prestanda (noggrannhet) för algoritmerna*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Genomsnitts modell |Den bästa modellen |Underpresterar |Genomsnitts modell |

De modeller som finns i Machine Learning Studio (klassisk) utgjorde SAS med 15-25% för körnings hastighet, men noggrannheten var stor på parivärde.  

## <a name="discussion-and-recommendations"></a>Diskussion och rekommendationer
I telekommunikations branschen har flera metoder för att analysera omsättningen, inklusive:  

* Härled mått för fyra grundläggande kategorier:
  * **Entitet (till exempel en prenumeration)**. Etablera grundläggande information om den prenumeration och/eller kund som är föremål för omsättning.
  * **Aktivitet**. Hämta all eventuell användnings information som är relaterad till entiteten, till exempel antalet inloggningar.
  * **Kund support**. Skörd information från kund support loggar för att ange om prenumerationen har problem eller interaktioner med kund support.
  * **Konkurrens kraft och affärs data**. Få all information som är möjlig om kunden (till exempel kan vara otillgänglig eller svår att spåra).
* Använd prioritet för att välja enhet för funktioner. Detta innebär att den utökat besluts träd modellen alltid är en löftes metod.  

Användningen av dessa fyra kategorier skapar en illusion av att en enkel *deterministisk* Metod, baserat på index som bildas på rimliga faktorer per kategori, räcker för att identifiera kunder som riskerar att ta omsättning. Tyvärr, även om det här begreppet verkar plausible, är det en falsk förståelse. Orsaken är att omsättningen är en temporal inverkan och de faktorer som bidrar till omsättningen vanligt vis är i tillfälliga tillstånd. Det som gör det möjligt för en kund att lämna idag kan vara annorlunda i morgon, och det kommer att vara en annan i sex månader. Därför är en *Probabilistic* modell en nödvändighet.  

Den här viktiga observationen är ofta överblick i verksamheten, som vanligt vis föredrar en Business Intelligence-orienterad metod för analys, eftersom det är en enklare Sälj-och admits enkel automatisering.  

Löftet om självbetjänings analys med hjälp av Machine Learning Studio (klassisk) är dock att de fyra informations kategorierna, klassificeras efter avdelning eller avdelning, blir en värdefull källa för maskin inlärning om omsättning.  

En annan spännande funktion i Azure Machine Learning Studio (klassisk) är möjligheten att lägga till en anpassad modul i lagrings platsen för fördefinierade moduler som redan är tillgängliga. Den här funktionen innebär i själva verket att skapa en möjlighet att välja bibliotek och skapa mallar för vertikala marknader. Det är en viktig differentiering av Azure Machine Learning Studio (klassisk) på marknads platsen.  

Vi hoppas att fortsätta med det här avsnittet i framtiden, särskilt relaterat till Big data Analytics.
  

## <a name="conclusion"></a>Slutsats
I det här dokumentet beskrivs en lämpliga metod för att kunna hantera det vanliga problemet med kund omsättningen med hjälp av ett allmänt ramverk. Vi ansåg en prototyp för bedömnings modeller och implementerar den med hjälp av Azure Machine Learning Studio (klassisk). Slutligen bedömde vi precisionen och prestandan för prototyp lösningen med avseende på jämförbara algoritmer i SAS.  

 

## <a name="references"></a>Referenser
[1] förutsägelse analys: utöver förutsägelserna W. McKnight, informations hantering, juli/augusti 2011, s. 18-20.  

[2] Wikipedia-artikel: [precision och precision](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [skarp-DM 1,0: steg-för-steg-guide för Data utvinning](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [stor data marknadsföring: engagera dina kunder på ett mer effektivt sätt och enhets värde](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco omsättnings modell mall](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) i [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Bilaga
![Ögonblicks bild av en presentation på omsättnings prototyp](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Bild 12: ögonblicks bild av en presentation på omsättnings prototyp*
