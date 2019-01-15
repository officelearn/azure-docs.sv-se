---
title: Analysera kundomsättning
titleSuffix: Azure Machine Learning Studio
description: Fallstudie av att utveckla en integrerad modell för att analysera och bedömning kundomsättningen med Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.component: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: dbe853172c0f0e49408bd4a2dd630a69659421d3
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260609"
---
# <a name="analyzing-customer-churn-using-azure-machine-learning-studio"></a>Analysera kundens omsättning med hjälp av Azure Machine Learning Studio
## <a name="overview"></a>Översikt
Den här artikeln beskriver vi en referensimplementering av ett kundomsättning analysis-projekt som skapats med hjälp av Azure Machine Learning. I den här artikeln diskuterar vi associerade allmän modeller för att lösa problemen med industriell kundomsättning holistiskt. Vi också mäta riktighet modeller som skapas med hjälp av Machine Learning och utvärdera anvisningarna för ytterligare utveckling.  

### <a name="acknowledgements"></a>Bekräftelser
Det här experimentet utvecklades och testats av Serge Berger huvudnamn dataexpert hos Microsoft och Roger Barga, tidigare Product Manager för Microsoft Azure Machine Learning. Azure-Dokumentationsteamet mycket bekräftar sin expertis och tack dem för att dela det här dokumentet.

> [!NOTE]
> Data som används för det här experimentet är inte allmänt tillgängliga. Ett exempel på hur du skapar en maskininlärningsmodell för omsättning analys finns: [Detaljhandel omsättningen modellen mallen](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) i [Azure AI-galleriet](http://gallery.cortanaintelligence.com/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problemet med kundomsättning
Företag i konsumentmarknaden och i alla enterprise sektorer måste hantera omsättning. Ibland omsättning är för lång tid och påverkar policybeslut. Traditionell lösning är att förutsäga hög benägenhet churners och deras behov via en concierge-tjänst, marknadsföring, eller genom att tillämpa särskilda dispens. Dessa metoder kan variera från bransch till bransch. De kan även variera från ett visst konsument-kluster till ett annat inom en bransch (till exempel telekommunikation).

Vanliga faktor är att företag behöver för att minimera dessa särskilda kunden besökslängd. Därför är en naturlig metod att bedöma alla kunder med sannolikheten för omsättning och åtgärda de översta N ettor. Främsta kunder kan vara de mest lönsamma. En vinst-funktion är exempelvis anställd under valet av kandidater för särskilda dispens i mer avancerade scenarier. Detta är dock endast en del av komplett strategi för att hantera omsättning. Företag måste också beakta konto risk (och associerade risktolerans) och i kostnaden för åtgärder och rimligt kundsegmentering.  

## <a name="industry-outlook-and-approaches"></a>Branschens outlook och metoder
Avancerad hantering av omsättning är ett tecken på en mogen bransch. Det klassiska exemplet är telekommunikation branschen där prenumeranter är kända för att växla ofta från en leverantör till en annan. Frivillig dataomsättningen är särskilda viktig. Dessutom providers skett betydande kunskap om *omsättningen drivrutiner*, som är de faktorer som driver kunder att växla.

Exempelvis är luren eller enhet valet en välkänd kärnan i verksamheten mobiltelefon-drivrutin. Därför är en populär princip att subventionera priset för en luren för nya prenumeranter och debiterar en fullt pris för befintliga kunder för en uppgradering. Historiskt sett har har den här principen lett till kunder som hoppar från en leverantör till en annan för att få en ny rabatt. Detta har i sin tur uppmanas att förfina sina strategier.

Hög volatil luren erbjudanden är en faktor som snabbt upphäver modeller av omsättning som baseras på den aktuella luren modeller. Dessutom mobiltelefoner är inte endast telekommunikation enheter, de är också sätt-uttryck (Överväg iPhone). Dessa sociala förutsägelserna ligger utanför omfånget för regelbundna telekommunikation datauppsättningar.

Resultatet för modellering är att du inte kan utforma en bra princip genom att eliminera kända orsaker till omsättning. I själva verket en strategi för kontinuerlig modellering, inklusive klassiska modeller som kvantifiera kategoriska variabler (till exempel beslutsträd) är **obligatoriska**.

Använder stordata-uppsättningar på sina kunder kan utför organisationer analyser av stordata (särskilt omsättning identifiering utifrån stordata) som en effektiv strategi på problemet. Du kan hitta mer information om stordata-metod för att problemet med kärnan i rekommendationer om ETL-avsnittet.  

## <a name="methodology-to-model-customer-churn"></a>Metoder för att modellen kundomsättning
En gemensam problemlösning process för att lösa kundomsättning illustreras i bild 1 – 3:  

1. En modell för risk kan du överväga hur åtgärder som påverkar sannolikheten och risk.
2. En åtgärd från modell kan du överväga hur andelen åtgärder kan påverka sannolikheten för omsättning och mängden kunden livstidsvärde (CLV).
3. Den här analysen lämpar sig för en kvalitativ analys som har skickats vidare till en proaktiv marknadsföringskampanj som riktar sig mot kundsegment för att ge optimala erbjudandet.  

![][1]

Den här framåt söker metoden är det bästa sättet att behandla omsättning, men medföljer komplexiteten: Vi har att utveckla en flermodells archetype och spåra beroenden mellan modellerna. Vara kan inkapslade samspelet mellan modeller som du ser i följande diagram:  

![][2]

*Bild 4: Enhetlig flermodells archetype*  

Interaktion mellan modellerna är nyckeln om vi att leverera holistiska approachen till kunder. Varje modell försämras nödvändigtvis med tiden. arkitekturen är därför en implicit loop (liknar archetype som angetts av SKARPA-DM data-utvinning standard [***3***]).  

Den övergripande cykeln av risk-beslut-marketing segmentering/uppdelning är fortfarande en generaliserad struktur som gäller för många affärsproblem. Analys av omsättning är helt enkelt en stark företrädare för den här gruppen av problem eftersom den ger egenskaperna som ett komplexa verksamhetsproblem som inte tillåter att en förenklad förutsägelselösning. Sociala aspekter av den moderna metoden att omsättning speciellt markeras inte metoden med, men de sociala aspekterna som är inkapslade i modellering-archetype som de skulle göra i alla modeller.  

Ett intressant tillägg är analys av stordata. Samla in fullständig information om sina kunder dagens telekommunikation och detaljhandel företag och vi kan enkelt förutser behovet av flera modeller anslutningen blir en gemensam trend angivna kommande trender, som Sakernas Internet och allt vanligare enheter som gör att företag att använda smarta lösningar på flera nivåer.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementera modellering archetype i Machine Learning Studio
Beroende problemet som beskrivs vad är det bästa sättet att implementera en integrerad modellering och bedömning metoden? I det här avsnittet visar vi hur vi gör detta med hjälp av Azure Machine Learning Studio.  

Flera datamodeller metoden är ett måste när du utformar en global archetype för omsättning. Även den bedömnings (förutsägande) delen av metoden som ska vara flera modeller.  

Följande diagram visar prototypen vi skapade, som använder fyra bedömnings algoritmer i Machine Learning Studio för att förutsäga omsättning. Orsak till att använda en metod för flera modeller är inte bara för att skapa en ensemble klassificerare att öka tillförlitligheten, men också att skydda mot över montering och förbättra val av förebyggande funktioner.  

![][3]

*Bild 5: Skapa prototyper för en omsättning modellering metod*  

Följande avsnitt innehåller mer information om prototypen bedömningsmodell som vi har implementerat med hjälp av Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Dataurval och förberedelse
Data som används för att skapa modeller och poängsätta kunder har hämtats från en lodrät CRM-lösning med de data som har dolts för att skydda kundernas integritet. Data innehåller information om 8 000 prenumerationer i USA och den kombinerar tre källor: etablering data (prenumeration metadata), aktivitetsdata (användning av systemet) och stöd för kunddata. Data innehåller inte alla företag relaterad information om kunder; Exempelvis kan innehåller den inte lojalitet metadata eller kredit poäng.  

För enkelhetens skull är ETL och processer för Datarensning utanför omfånget eftersom vi antar att förberedelse av data har redan gjorts någon annanstans.   

Val av funktioner för modellering baseras på preliminära multipel bedömning av uppsättning förutsägelserna som ingår i den process som använder slumpmässig skog-modulen. För att genomföra i Machine Learning Studio beräknat vi medelvärde, median och intervall för representativa funktioner. Exempelvis kan vi lagt till mängder för kvalitativ data, till exempel lägsta och högsta värden för användaraktivitet.    

Vi har också avbildas temporala information för de senaste sex månaderna. Vi analyserade data i ett år och vi upprättat att även om det fanns statistiskt signifikanta trender, minskat effekten på omsättning avsevärt efter sex månader.  

Det viktigaste är att hela processen, inklusive ETL, val av funktioner och modellering har implementerats i Machine Learning Studio, med hjälp av datakällor i Microsoft Azure.   

Följande diagram visar de data som har använts.  

![][4]

*Bild 6: Utdrag ur datakällan (dold)*  

![][5]

*Bild 7: Funktioner som extraherats från datakällan*
 

> Observera att dessa data är privat och därför modell och data kan inte delas.
> Dock finns i det här exemplet experimentera i en liknande modell med offentligt tillgängliga data, den [Azure AI-galleriet](http://gallery.cortanaintelligence.com/): [Telco Konsumentförlust](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Om du vill veta mer om hur du kan implementera en modell för analys av omsättning med Cortana Intelligence Suite kan vi rekommenderar också [videon](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) av programchef Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmer som används i prototypen
Vi använde följande fyra maskininlärningsalgoritmer för att skapa prototyper (Ingen anpassning):  

1. Logistic regression (LR)
2. Beslutsträd (BT)
3. Genomsnittlig perceptron (AP)
4. Dator för vektorstöd (SVM)  

Följande diagram illustrerar en del av designyta experiment som anger vilken ordning som modeller har skapats:  

![][6]  

*Bild 8: Skapa modeller i Machine Learning Studio*  

### <a name="scoring-methods"></a>Bedömning metoder
Vi poängsätts fyra modeller med hjälp av en datauppsättning med märkta utbildning.  

Vi har också har skickats bedömnings datauppsättningen till en jämförbar modellen som skapats med hjälp av fjärrskrivbord utgåva av SAS Enterprise Miner 12. Vi mätt riktighet SAS-modellen och alla fyra modeller i Machine Learning Studio.  

## <a name="results"></a>Resultat
I det här avsnittet presentera vi våra resultat om det arbete du utfört modeller, baserat på bedömnings datauppsättningen.  

### <a name="accuracy-and-precision-of-scoring"></a>Precision och precisionen för bedömning
Implementering i Azure Machine Learning är oftast bakom SAS i Precision med cirka 10 – 15% (området Under kurvan eller AUC).  

Omsättning viktigaste måttet är dock felklassificering frekvensen: det vill säga för högsta N-churners som förväntade av klassificeraren vilken av dem gjorde **inte** omsättning och har fått någon särskild behandling? Diagrammet nedan jämförs felklassificering priset för varje modell:  

![][7]

*Bild 9: Passau prototyp området under kurva*

### <a name="using-auc-to-compare-results"></a>Med hjälp av AUC för att jämföra resultaten
Området Under kurvan (AUC) är ett mått som representerar ett globala mått på *Avskiljbarhet* mellan distributioner av poäng för positiva och negativa. Den liknar traditionella mottagare operatorn egenskap (ROC) diagrammet, men en viktig skillnad är att AUC mått inte måste du välja ett tröskelvärde. I stället det sammanfattar resultaten över **alla** möjliga alternativ. Däremot i traditionella ROC-diagrammet visas positiva identifieringar på den lodräta axeln och andel falska positiva identifieringar på den horisontala axeln och klassificering tröskelvärde varierar.   

AUC används vanligtvis i form av ett mått är värd för olika algoritmer (eller olika system) eftersom den tillåter modeller som ska jämföras med hjälp av deras AUC värden. Det här är en populär metod i olika branscher, till exempel väderförhållanden samt biosciences. Därför representerar AUC ett populärt verktyg för utvärdering av klassificerare prestanda.  

### <a name="comparing-misclassification-rates"></a>Jämföra felklassificering priser
Vi jämfört med priserna för felklassificering på datauppsättningen i fråga med hjälp av CRM-data av cirka 8 000 prenumerationer.  

* SAS felklassificering frekvensen var 10 – 15%.
* Machine Learning Studio felklassificering frekvensen var 15-20% för de översta 200 300 churners.  

Det är viktigt att åtgärda de kunder som har högsta risk att omsättningen genom att erbjuda dem en concierge-tjänst eller andra särskild behandling för företagsförsäljning telekommunikation. I detta avseende uppnår Machine Learning Studio-implementeringen resultat likvärdig med den hos SAS-modellen.  

På samma sätt, är viktigare än precisionen eftersom det är mest intresserad av att klassificera potentiella churners korrekt.  

Följande diagram från Wikipedia visar relationen i en livlig, enkelt att förstå bild:  

![][8]

*Bild 10: Förhållandet mellan noggrannhet och precision*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Noggrannhet och precision resultat för beslutsträd trädet modell
Följande diagram visar rådata resultaten från bedömning med Machine Learning-prototyp för beslutsträd trädet modellen råkar vara det mest korrekta bland fyra modeller:  

![][9]

*Bild 11: Beslutsträd trädet modellen egenskaper*

## <a name="performance-comparison"></a>Jämförelse av prestanda
Vi jämfört med den hastighet med vilken data har poängsätts med hjälp av Machine Learning Studio-modeller och en jämförbar modellen som skapats med hjälp av fjärrskrivbord utgåva av SAS Enterprise Miner 12.1.  

I följande tabell sammanfattas algoritmerna prestanda:  

*Tabell 1. Allmänna prestanda (precision) av algoritmerna*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Genomsnittlig modell |Den bästa modellen |Presterar som förväntat |Genomsnittlig modell |

Modeller som finns i Machine Learning Studio – bättre än SAS med 15-25% för hastighet för körning, men Precision har till stor del på par.  

## <a name="discussion-and-recommendations"></a>Diskussioner och rekommendationer
I branschen telekommunikation, flera metoder har vuxit fram för att analysera omsättningen, inklusive:  

* Härled mätvärden för fyra grundläggande kategorier:
  * **Entiteten (till exempel en prenumeration)**. Etablera grundläggande information om den prenumeration och/eller kund som omfattas av omsättning.
  * **Aktivitet**. Hämta alla möjliga användningsinformation som är relaterade till entiteten, till exempel antalet inloggningar.
  * **Kundsupport**. Samla information från customer support loggar att indikera om prenumerationen har problem eller interaktioner med kundsupport.
  * **Data konkurrenskraftiga och företagsdata**. Få all information som möjligt om kunden (till exempel kan vara otillgänglig eller svårt att spåra).
* Använd betydelse för val av funktioner för enheten. Detta innebär att modellen beslutsträd trädet är alltid en lovande metod.  

Användningen av dessa fyra kategorier skapar illusionen som en enkel *deterministisk* metod, baserat på index som skapats på rätt sätt på rimlig faktorer per kategori, bör vara tillräckligt för att identifiera kunder risk för omsättning. Tyvärr även om detta begrepp verkar rimligt, är det en falsk förståelse. Det beror på att omsättning är en temporal effekt och faktorer som påverkar återställningstiden omsättningen är vanligtvis i ett tillfälligt tillstånd. Vad leder en kund att lämna idag kan skilja sig morgon och det däremot påverkas sex månader från nu. Därför kan en *avsnittet om sannolikhetsbunden* modellen är nödvändigt.  

Den här viktiga observationer förbises ofta i företag, vilket vanligtvis föredrar en business intelligence-orienterade metod till analytics, främst eftersom det är en enklare sälja och ger enkel automation.  

Löftet om självbetjäningsanalys med hjälp av Machine Learning Studio är dock att de fyra informationskategorierna klassificerats efter avdelning blir en värdefull källa för machine learning om omsättning.  

En annan viktig funktion i Azure Machine Learning är möjligheten att lägga till en anpassad modul till lagringsplatsen för fördefinierade moduler som redan är tillgängliga. Den här funktionen skapar i princip en möjlighet att ange bibliotek och mallar för vertikala marknader. Det är ett viktigt skillnaden av Azure Machine Learning på marknaden plats.  

Vi hoppas att fortsätta det här avsnittet i framtiden, särskilt rör analyser av stordata.
  

## <a name="conclusion"></a>Sammanfattning
Det här dokumentet beskriver en metod som är känsliga för att lösa vanliga problem med kundomsättning med hjälp av ett allmänt ramverk. Vi anses vara en prototyp för bedömning modeller och implementeras med hjälp av Azure Machine Learning. Slutligen kan utvärderat vi prestanda av lösningen prototyp avseende jämförbara algoritmer i SAS.  

 

## <a name="references"></a>Referenser
[1] förutsägande analys: Utöver de förutsägelser västra McKnight informationshantering, juli/augusti 2011, s.18-20.  

[2] Wikipedia-artikel: [Noggrannhet och precision](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Stegvisa Data-utvinning Guide](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Stordata marknadsföring: Engagera dina kunder mer effektivt och Driv värde](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco omsättningen modellen mallen](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) i [Azure AI-galleriet](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Bilaga
![][10]

*Bild 12: Ögonblicksbild av en presentation i omsättning prototyp*

[1]: ./media/azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/azure-ml-customer-churn-scenario/churn-10.png
