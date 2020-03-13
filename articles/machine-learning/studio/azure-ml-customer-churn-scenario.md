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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204536"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analysera kund omsättning med Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Översikt
Den här artikeln visar en referens implementering av ett analys projekt för kund omsättning som har skapats med hjälp av Azure Machine Learning Studio (klassisk). I den här artikeln diskuterar vi associerade allmän modeller för att lösa problemen med industriell kundomsättning holistiskt. Vi också mäta riktighet modeller som skapas med hjälp av Machine Learning och utvärdera anvisningarna för ytterligare utveckling.  

### <a name="acknowledgements"></a>Erkännanden
Det här experimentet utvecklades och testades av Serge Berger, huvudsakliga data expert hos Microsoft och Roger Bargas, tidigare produkt chef för Microsoft Azure Machine Learning Studio (klassisk). Azure-Dokumentationsteamet mycket bekräftar sin expertis och tack dem för att dela det här dokumentet.

> [!NOTE]
> Data som används för det här experimentet är inte allmänt tillgängliga. Ett exempel på hur du skapar en Machine Learning-modell för omsättnings analys finns i: [mallen Retail omsättnings modell](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) i [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problemet med kundomsättning
Företag i konsumentmarknaden och i alla enterprise sektorer måste hantera omsättning. Ibland omsättning är för lång tid och påverkar policybeslut. Traditionell lösning är att förutsäga hög benägenhet churners och deras behov via en concierge-tjänst, marknadsföring, eller genom att tillämpa särskilda dispens. Dessa metoder kan variera från bransch till bransch. De kan även variera från ett visst konsument-kluster till ett annat inom en bransch (till exempel telekommunikation).

Vanliga faktor är att företag behöver för att minimera dessa särskilda kunden besökslängd. Därför är en naturlig metod att bedöma alla kunder med sannolikheten för omsättning och åtgärda de översta N ettor. Främsta kunder kan vara de mest lönsamma. En vinst-funktion är exempelvis anställd under valet av kandidater för särskilda dispens i mer avancerade scenarier. Detta är dock endast en del av komplett strategi för att hantera omsättning. Företag måste också beakta konto risk (och associerade risktolerans) och i kostnaden för åtgärder och rimligt kundsegmentering.  

## <a name="industry-outlook-and-approaches"></a>Branschens outlook och metoder
Avancerad hantering av omsättning är ett tecken på en mogen bransch. Det klassiska exemplet är telekommunikation branschen där prenumeranter är kända för att växla ofta från en leverantör till en annan. Frivillig dataomsättningen är särskilda viktig. Dessutom har leverantörer samlat in betydande kunskaper om *omsättnings driv rutiner*, vilka är de faktorer som gör att kunderna kan byta.

Exempelvis är luren eller enhet valet en välkänd kärnan i verksamheten mobiltelefon-drivrutin. Därför är en populär princip att subventionera priset för en luren för nya prenumeranter och debiterar en fullt pris för befintliga kunder för en uppgradering. Historiskt sett har har den här principen lett till kunder som hoppar från en leverantör till en annan för att få en ny rabatt. Detta har i sin tur uppmanas att förfina sina strategier.

Hög volatil luren erbjudanden är en faktor som snabbt upphäver modeller av omsättning som baseras på den aktuella luren modeller. Dessutom mobiltelefoner är inte endast telekommunikation enheter, de är också sätt-uttryck (Överväg iPhone). Dessa sociala förutsägelserna ligger utanför omfånget för regelbundna telekommunikation datauppsättningar.

Resultatet för modellering är att du inte kan utforma en bra princip genom att eliminera kända orsaker till omsättning. I själva verket är en strategi för kontinuerlig modellering, inklusive klassiska modeller som kvantifierar kategoriska-variabler (till exempel besluts träd) **obligatoriska**.

Använder stordata-uppsättningar på sina kunder kan utför organisationer analyser av stordata (särskilt omsättning identifiering utifrån stordata) som en effektiv strategi på problemet. Du kan hitta mer information om stordata-metod för att problemet med kärnan i rekommendationer om ETL-avsnittet.  

## <a name="methodology-to-model-customer-churn"></a>Metoder för att modellen kundomsättning
En gemensam problemlösning process för att lösa kundomsättning illustreras i bild 1 – 3:  

1. En modell för risk kan du överväga hur åtgärder som påverkar sannolikheten och risk.
2. En åtgärd från modell kan du överväga hur andelen åtgärder kan påverka sannolikheten för omsättning och mängden kunden livstidsvärde (CLV).
3. Den här analysen lämpar sig för en kvalitativ analys som har skickats vidare till en proaktiv marknadsföringskampanj som riktar sig mot kundsegment för att ge optimala erbjudandet.  

![Diagram över hur risk tolerans plus besluts modeller ger till åtgärds bara insikter](./media/azure-ml-customer-churn-scenario/churn-1.png)

Den här framåt söker metoden är det bästa sättet att behandla omsättning, men medföljer komplexiteten: Vi har att utveckla en flermodells archetype och spåra beroenden mellan modellerna. Vara kan inkapslade samspelet mellan modeller som du ser i följande diagram:  

![Interaktions diagram över omsättnings modell](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figur 4: enhetlig archetype för flera modeller*  

Interaktion mellan modellerna är nyckeln om vi att leverera holistiska approachen till kunder. Varje modell behöver försämras med tiden. arkitekturen är därför en implicit slinga (liknar den archetype som anges av den skarpa DM-standarden, [***3***]).  

Den övergripande cykeln av risk-beslut-marketing segmentering/uppdelning är fortfarande en generaliserad struktur som gäller för många affärsproblem. Analys av omsättning är helt enkelt en stark företrädare för den här gruppen av problem eftersom den ger egenskaperna som ett komplexa verksamhetsproblem som inte tillåter att en förenklad förutsägelselösning. Sociala aspekter av den moderna metoden att omsättning speciellt markeras inte metoden med, men de sociala aspekterna som är inkapslade i modellering-archetype som de skulle göra i alla modeller.  

Ett intressant tillägg är analys av stordata. Samla in fullständig information om sina kunder dagens telekommunikation och detaljhandel företag och vi kan enkelt förutser behovet av flera modeller anslutningen blir en gemensam trend angivna kommande trender, som Sakernas Internet och allt vanligare enheter som gör att företag att använda smarta lösningar på flera nivåer.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementera modellerings archetype i Machine Learning Studio (klassisk)
På det sätt som beskrivs är det bästa sättet att implementera en integrerad modell och en bedömnings metod? I det här avsnittet visar vi hur vi har utfört detta genom att använda Azure Machine Learning Studio (klassisk).  

Flera datamodeller metoden är ett måste när du utformar en global archetype för omsättning. Även den bedömnings (förutsägande) delen av metoden som ska vara flera modeller.  

Följande diagram visar den prototyp som vi har skapat, som använder fyra bedömnings algoritmer i Machine Learning Studio (klassisk) för att förutse omsättningen. Orsak till att använda en metod för flera modeller är inte bara för att skapa en ensemble klassificerare att öka tillförlitligheten, men också att skydda mot över montering och förbättra val av förebyggande funktioner.  

![Skärm bild som illustrerar en komplex Studio (klassisk) arbets yta med många sammankopplade moduler](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figur 5: prototyp för en metod för omsättnings modellering*  

I följande avsnitt finns mer information om prototyp bedömnings modellen som vi implementerade med hjälp av Machine Learning Studio (klassisk).  

### <a name="data-selection-and-preparation"></a>Dataurval och förberedelse
Data som används för att skapa modeller och poängsätta kunder har hämtats från en lodrät CRM-lösning med de data som har dolts för att skydda kundernas integritet. Data innehåller information om 8 000 prenumerationer i USA och den kombinerar tre källor: etablering data (prenumeration metadata), aktivitetsdata (användning av systemet) och stöd för kunddata. Data innehåller ingen affärsrelaterad information om kunderna. Det omfattar till exempel inte förmåns-metadata eller kredit poäng.  

För enkelhetens skull är ETL och processer för Datarensning utanför omfånget eftersom vi antar att förberedelse av data har redan gjorts någon annanstans.

Val av funktioner för modellering baseras på preliminära multipel bedömning av uppsättning förutsägelserna som ingår i den process som använder slumpmässig skog-modulen. För implementeringen i Machine Learning Studio (klassisk) beräknade vi medelvärde, median och intervall för representativa funktioner. Exempelvis kan vi lagt till mängder för kvalitativ data, till exempel lägsta och högsta värden för användaraktivitet.

Vi har också avbildas temporala information för de senaste sex månaderna. Vi analyserade data i ett år och vi upprättat att även om det fanns statistiskt signifikanta trender, minskat effekten på omsättning avsevärt efter sex månader.  

Den viktigaste punkten är att hela processen, inklusive ETL, funktions val och modellering implementerades i Machine Learning Studio (klassisk) med hjälp av data källor i Microsoft Azure.   

Följande diagram visar de data som har använts.  

![Skärm bild som visar ett exempel på data som används med RAW-värden](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Bild 6: utdrag från data källa (fördunklade)*  

![Skärm bild som visar statistiska funktioner som har extraherats från data källan](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Bild 7: funktioner som extraherats från data källan*
 

> Observera att dessa data är privat och därför modell och data kan inte delas.
> Men för en liknande modell som använder offentligt tillgängliga data, se det här exempel experimentet i [Azure AI Gallery](https://gallery.azure.ai/): [Telco kund omsättning](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Om du vill veta mer om hur du kan implementera en omsättnings analys modell med Cortana Intelligence Suite rekommenderar vi även [den här videon](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) av Wee Hyong tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmer som används i prototypen
Vi använde följande fyra maskininlärningsalgoritmer för att skapa prototyper (Ingen anpassning):  

1. Logistic regression (LR)
2. Beslutsträd (BT)
3. Genomsnittlig perceptron (AP)
4. Dator för vektorstöd (SVM)  

Följande diagram illustrerar en del av designyta experiment som anger vilken ordning som modeller har skapats:  

![Skärm bild av en liten del av experiment arbets ytan i Studio](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Figur 8: skapa modeller i Machine Learning Studio (klassisk)*  

### <a name="scoring-methods"></a>Bedömning metoder
Vi poängsätts fyra modeller med hjälp av en datauppsättning med märkta utbildning.  

Vi har också har skickats bedömnings datauppsättningen till en jämförbar modellen som skapats med hjälp av fjärrskrivbord utgåva av SAS Enterprise Miner 12. Vi mätte precisionen för SAS-modellen och alla fyra Machine Learning Studios modeller (klassiska).  

## <a name="results"></a>Resultat
I det här avsnittet presentera vi våra resultat om det arbete du utfört modeller, baserat på bedömnings datauppsättningen.  

### <a name="accuracy-and-precision-of-scoring"></a>Precision och precisionen för bedömning
I allmänhet är implementeringen i Azure Machine Learning Studio (klassisk) bakom SAS med noggrannhet med cirka 10-15% (area under kurva eller AUC).  

Det viktigaste måttet i omsättningen är dock den fel klassificerings takt: det vill säga av de x främsta omsättningarna som förutsägs av klassificeraren, vilket av dem som faktiskt **inte** hade omsättningen, och som ännu fått särskild behandling? Diagrammet nedan jämförs felklassificering priset för varje modell:  

![Område under kurv diagrammet som jämför prestandan för 4 algoritmer](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Bild 9: Passau prototyp area under kurva*

### <a name="using-auc-to-compare-results"></a>Med hjälp av AUC för att jämföra resultaten
Area under kurva (AUC) är ett mått som representerar ett globalt mått på *separability* mellan fördelningarna av Poäng för positiva och negativa populationer. Den liknar traditionella mottagare operatorn egenskap (ROC) diagrammet, men en viktig skillnad är att AUC mått inte måste du välja ett tröskelvärde. I stället sammanfattas resultaten över **alla** möjliga val. Däremot i traditionella ROC-diagrammet visas positiva identifieringar på den lodräta axeln och andel falska positiva identifieringar på den horisontala axeln och klassificering tröskelvärde varierar.   

AUC används som ett mått på värt för olika algoritmer (eller olika system) eftersom det gör att modeller kan jämföras med hjälp av sina AUC-värden. Det här är en populär metod i olika branscher, till exempel väderförhållanden samt biosciences. Därför representerar AUC ett populärt verktyg för utvärdering av klassificerare prestanda.  

### <a name="comparing-misclassification-rates"></a>Jämföra felklassificering priser
Vi jämfört med priserna för felklassificering på datauppsättningen i fråga med hjälp av CRM-data av cirka 8 000 prenumerationer.  

* SAS felklassificering frekvensen var 10 – 15%.
* Den felklassificerings takten för Machine Learning Studio (klassisk) var 15-20% för de översta 200-300-datakärlen.  

Det är viktigt att åtgärda de kunder som har högsta risk att omsättningen genom att erbjuda dem en concierge-tjänst eller andra särskild behandling för företagsförsäljning telekommunikation. I detta hänseende uppnår Machine Learning Studio (klassisk) implementeringen resultat på parivärde med SAS-modellen.  

På samma sätt, är viktigare än precisionen eftersom det är mest intresserad av att klassificera potentiella churners korrekt.  

Följande diagram från Wikipedia visar relationen i en livlig, enkelt att förstå bild:  

![Två mål. Ett mål visar att markeringen är löst grupperad men nära tjurarna-ögonen markerat med låg exakthet: bra värde, låg precision. Ett annat mål som är nära grupperat, men långt från tjurar – ögon markerat med låg exakthet: låg exakthet, bra precision](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Bild 10: kompromisser mellan precision och precision*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Noggrannhet och precision resultat för beslutsträd trädet modell
Följande diagram visar rådata resultaten från bedömning med Machine Learning-prototyp för beslutsträd trädet modellen råkar vara det mest korrekta bland fyra modeller:  

![Tabell kodfragment som visar precision, precision, återkallande, F-score, AUC, genomsnittlig logg förlust och inlärnings logg förlust för fyra algoritmer](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Bild 11: modell egenskaper för utökat besluts träd*

## <a name="performance-comparison"></a>Jämförelse av prestanda
Vi jämförde den hastighet med vilken data beskrevs med hjälp av Machine Learning Studio (klassiska) modeller och en jämförbar modell som skapats med hjälp av Desktop-versionen av SAS Enterprise Miner 12,1.  

I följande tabell sammanfattas algoritmerna prestanda:  

*Tabell 1. Allmänna prestanda (noggrannhet) för algoritmerna*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Genomsnittlig modell |Den bästa modellen |Presterar som förväntat |Genomsnittlig modell |

De modeller som finns i Machine Learning Studio (klassisk) utgjorde SAS med 15-25% för körnings hastighet, men noggrannheten var stor på parivärde.  

## <a name="discussion-and-recommendations"></a>Diskussioner och rekommendationer
I branschen telekommunikation, flera metoder har vuxit fram för att analysera omsättningen, inklusive:  

* Härled mätvärden för fyra grundläggande kategorier:
  * **Entitet (till exempel en prenumeration)** . Etablera grundläggande information om den prenumeration och/eller kund som omfattas av omsättning.
  * **Aktivitet**. Hämta alla möjliga användningsinformation som är relaterade till entiteten, till exempel antalet inloggningar.
  * **Kund support**. Samla information från customer support loggar att indikera om prenumerationen har problem eller interaktioner med kundsupport.
  * **Konkurrens kraft och affärs data**. Få all information som möjligt om kunden (till exempel kan vara otillgänglig eller svårt att spåra).
* Använd betydelse för val av funktioner för enheten. Detta innebär att modellen beslutsträd trädet är alltid en lovande metod.  

Användningen av dessa fyra kategorier skapar en illusion av att en enkel *deterministisk* Metod, baserat på index som bildas på rimliga faktorer per kategori, räcker för att identifiera kunder som riskerar att ta omsättning. Tyvärr även om detta begrepp verkar rimligt, är det en falsk förståelse. Det beror på att omsättning är en temporal effekt och faktorer som påverkar återställningstiden omsättningen är vanligtvis i ett tillfälligt tillstånd. Vad leder en kund att lämna idag kan skilja sig morgon och det däremot påverkas sex månader från nu. Därför är en *Probabilistic* modell en nödvändighet.  

Den här viktiga observationer förbises ofta i företag, vilket vanligtvis föredrar en business intelligence-orienterade metod till analytics, främst eftersom det är en enklare sälja och ger enkel automation.  

Löftet om självbetjänings analys med hjälp av Machine Learning Studio (klassisk) är dock att de fyra informations kategorierna, klassificeras efter avdelning eller avdelning, blir en värdefull källa för maskin inlärning om omsättning.  

En annan spännande funktion i Azure Machine Learning Studio (klassisk) är möjligheten att lägga till en anpassad modul i lagrings platsen för fördefinierade moduler som redan är tillgängliga. Den här funktionen skapar i princip en möjlighet att ange bibliotek och mallar för vertikala marknader. Det är en viktig differentiering av Azure Machine Learning Studio (klassisk) på marknads platsen.  

Vi hoppas att fortsätta det här avsnittet i framtiden, särskilt rör analyser av stordata.
  

## <a name="conclusion"></a>Sammanfattning
Det här dokumentet beskriver en metod som är känsliga för att lösa vanliga problem med kundomsättning med hjälp av ett allmänt ramverk. Vi ansåg en prototyp för bedömnings modeller och implementerar den med hjälp av Azure Machine Learning Studio (klassisk). Slutligen kan utvärderat vi prestanda av lösningen prototyp avseende jämförbara algoritmer i SAS.  

 

## <a name="references"></a>Referenser
[1] förutsägelse analys: utöver förutsägelserna W. McKnight, informations hantering, juli/augusti 2011, s. 18-20.  

[2] Wikipedia-artikel: [precision och precision](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [skarp-DM 1,0: steg-för-steg-guide för Data utvinning](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [stor data marknadsföring: engagera dina kunder på ett mer effektivt sätt och enhets värde](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco omsättnings modell mall](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) i [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Bilaga
![Ögonblicks bild av en presentation på omsättnings prototyp](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Bild 12: ögonblicks bild av en presentation på omsättnings prototyp*
