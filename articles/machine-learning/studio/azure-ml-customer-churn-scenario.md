---
title: Analysera kunden Omsättningsuppdateringar med Machine Learning | Microsoft Docs
description: Fallstudie för att utveckla en integrerad modell för att analysera och bedömningen kunden omsättning
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 1beba951a6785aa90eef22a63a8064e9da1bb27f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835125"
---
# <a name="analyzing-customer-churn-using-azure-machine-learning"></a>Analysera kunden Omsättningsuppdateringar med hjälp av Azure Machine Learning
## <a name="overview"></a>Översikt
Den här artikeln beskriver en för referensimplementering av en omsättning analys kundprojekt som skapats med hjälp av Azure Machine Learning. I den här artikeln tar vi upp associerade allmänna modeller för att lösa problemet med industriella kunden omsättning helhetsmässigt. Vi också mäta riktighet modeller som har skapats med hjälp av Machine Learning och utvärdera anvisningarna för ytterligare utveckling.  

### <a name="acknowledgements"></a>Bekräftelser
Experimentet har utvecklats och testats av Serge Berger och huvudnamn Data forskare på Microsoft Roger Barga, tidigare produkten Manager för Microsoft Azure Machine Learning. Dokumentation för Azure-teamet om sina kunskaper mycket och tack dem för att dela den här rapporten.

> [!NOTE]
> Data som används för experimentet är inte tillgänglig. Ett exempel på hur du skapar en maskininlärningsmodell för omsättning analys finns: [Retail omsättningsuppdateringar modellen mallen](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) i [Azure AI-galleriet](http://gallery.cortanaintelligence.com/)
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>Problemet med kunden omsättning
Företag i konsumentmarknaden och i alla enterprise sektorer måste hantera omsättning. Ibland omsättningen är för lång tid och påverkar förpolicybeslut. Traditionell lösning är att förutsäga hög benägenheten churners och deras behov via en concierge tjänst, marknadsföringskampanjer, eller genom att tillämpa särskilda dispens. Dessa metoder kan variera från bransch till bransch. De kan även variera från ett visst konsumenten kluster till ett annat inom samma bransch (till exempel telekommunikation).

Vanliga faktorn är att företag behöver för att minimera dessa ansträngningar för kvarhållning av särskilda kunden. Därför är en fysisk metod att poängsätta varje kund med sannolikheten för omsättning och åtgärda de översta N de. De främsta kunderna kanske de mest lönsamma. En vinst-funktion är till exempel anställd under valet av kandidater för särskilda dispens i mer avancerade scenarier. Dessa överväganden är dock endast en del av fullständig strategin för att hantera omsättning. Företag måste också ta risken för kontot (och associerade risktolerans) till nivå och kostnaden för åtgärder och rimligt kunden segmentering.  

## <a name="industry-outlook-and-approaches"></a>Branschen outlook och metoder
Avancerad hantering av omsättning är ett tecken på en mogen bransch. Klassiska exemplet är telekommunikation bransch där prenumeranter kallas ofta växla från en leverantör till en annan. Den här frivilliga omsättning är ett särskilda problem. Dessutom providers skett betydande kunskap om *omsättningsuppdateringar drivrutiner*, som är de faktorer som enheten kunder att växla.

Till exempel är luren eller enhet valet en välkänd kärnan i mobiltelefon business-drivrutin. Därför är en populär princip att subventionera priset på en luren om nya prenumeranter och debiterar ett fullständig pris till befintliga kunder för en uppgradering. Den här principen har tidigare lett till kunder hopping från en leverantör till en annan för att få en ny rabatt. Det har i sin tur uppmanas leverantörer att förfina sina strategier.

Hög volatil i luren erbjudanden är en faktor som snabbt upphäver modeller av omsättning som baseras på den aktuella luren modeller. Dessutom mobiltelefoner är inte bara telekommunikation enheter, de är också sätt rapporter (Överväg att iPhone). Dessa sociala predictors ligger utanför omfånget för vanliga telekommunikation datauppsättningar.

Resultatet för modellering är att du kan utforma en sund princip genom att eliminera kända orsaker till omsättning. Faktum är en kontinuerlig modellering strategi, inklusive klassiska modeller som kvantifiera kategoriska variabler (till exempel beslutsträd) är **obligatoriska**.

Med stora datauppsättningar på sina kunder, utför organisationer analyser av stordata (särskilt omsättning identifiering baserat på stordata) som ett effektivt sätt att problemet. Du kan läsa mer om stordata metod för problemet med omsättning i rekommendationer om ETL-avsnittet.  

## <a name="methodology-to-model-customer-churn"></a>Metoder att modellen kunden omsättning
En gemensam problemlösning process lösa kunden omsättning visas i figur 1-3:  

1. En modell för risk kan du överväga hur åtgärder som påverkar sannolikhet och risker.
2. En åtgärd modell kan du överväga hur andelen åtgärd kan påverka omsättningen och mängden kunden värde för livslängd (CLV).
3. Den här analysen lämpar sig för en kvalitativ analys eskaleras till en proaktiv marknadsföringskampanj som riktar sig till kundsegment att ge optimala erbjudandet.  

![][1]

Den här framåt söker metoden är det bästa sättet att behandla omsättning, men det ingår komplexitet: Vi har att utveckla en modell med flera archetype och spåra beroenden mellan modeller. Vara kan inkapslade samspelet mellan modeller som visas i följande diagram:  

![][2]

*Bild 4: Enhetlig flera modellen archetype*  

Interaktionen mellan modeller är nyckeln om vi levererar en heltäckande metod till kunden kvarhållning. Varje modell försämras nödvändigtvis med tiden. arkitekturen är därför en implicit loop (liknar archetype som angetts av SKARPA DM data utvinningsmodellen standard [***3***]).  

Den övergripande cykeln av risk-beslut-marknadsföring segmentering/uppdelning är fortfarande en generaliserad struktur som gäller för många affärsproblem. Omsättning analys är en starkt representant i den här gruppen av problem eftersom den ger egenskaper som komplexa affärsproblem som inte tillåter att en förenklad förutsägelselösning. Sociala aspekter för det moderna tillvägagångssättet för omsättningsuppdateringar särskilt markeras inte i metoden, men sociala aspekter som är inkapslade i modellering-archetype som de skulle göra i någon modell.  

Ett intressant tillägg är stordata. Samla in fullständig information om sina kunder dagens telekommunikation och retail företag och vi kan enkelt förutser behovet av att flera modellen anslutningen blir en gemensam trend angivna nya trender, till exempel Internet of Things och överallt enheter som gör att företag att använda smarta lösningar på flera lager.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementera modellering archetype i Machine Learning Studio
Angivna problemet som beskrivs bara vad är det bästa sättet att implementera en integrerad modellering och bedömningen metoden? I det här avsnittet visar vi hur vi göra detta med Azure Machine Learning Studio.  

Flera modellen metoden är ett måste när du skapar en global archetype för omsättning. Även den bedömningsprofil (förutsägande) en del av metoden som ska vara flera modellen.  

Följande diagram visar typen som vi skapade som använder fyra bedömningsprofil algoritmer i Machine Learning Studio för att förutsäga omsättning. Orsak till att använda flera olika modeller metoden är inte bara för att skapa en ensemble klassificerare att öka tillförlitligheten, men också att skydda mot över passning och förbättra normativ Funktionsurval.  

![][3]

*Bild 5: Prototyp av en omsättning modeling metod*  

Följande avsnitt innehåller mer information om prototyp poängsättning av modellen som vi har implementerat med hjälp av Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Dataurval och förberedelse
Data som används för att skapa modeller och poäng kunder har hämtats från en lodrät CRM-lösning med de data som har dolts för att skydda kundens integritet. Data innehåller information om 8 000 prenumerationer i USA och det kombinerar tre källor: etablering data (prenumeration metadata), aktivitetsdata (användning av systemet) och kundinformation för support. Data innehåller inte alla företag relaterad information om kunder. till exempel innehåller den inte förmåner metadata eller kredit resultat.  

För enkelhetens skull är ETL och rensa processer data utanför omfånget eftersom vi antar att förberedelse av data har redan gjorts på en annan plats.   

Val av funktioner för modellering baseras på preliminär signifikans poängsättning av en uppsättning predictors som ingår i den process som använder modulen slumpmässiga skog. För att genomföra i Machine Learning Studio beräkna vi medelvärde, median och intervallen för representativt funktioner. Vi lagt till exempel mängder för kvalitativa data, till exempel lägsta och högsta värden för användaraktivitet.    

Vi också avbildas temporal information under de senaste sex månaderna. Vi analyserade data för ett år och det fastställs att även om det fanns statistiskt viktiga trender, minskat effekten på omsättning avsevärt efter sex månader.  

Den viktigaste är att hela processen, inklusive ETL, val av funktioner och modellering implementerades i Machine Learning Studio, använda datakällor i Microsoft Azure.   

Följande diagram visar de data som har använts.  

![][4]

*Bild 6: Utdrag ur datakälla (dolt)*  

![][5]

*Bild 7: Funktioner som extraheras från datakällan*
 

> Observera att dessa data är privat och därför modellen och data kan inte delas.
> Men en liknande modell med offentligt tillgängliga data, finns i det här exemplet experiment i den [Azure AI-galleriet](http://gallery.cortanaintelligence.com/): [anger kunden Omsättningsuppdateringar](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Mer information om hur du kan implementera en modell för analys av omsättning med hjälp av Cortana Intelligence Suite vi rekommenderar också att [den här videon](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) av programchef Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmer som används i typen
Vi används följande fyra maskininlärningsalgoritmer för att skapa prototyp (Ingen anpassning):  

1. Logistic regression (LR)
2. Tvåklassförhöjda beslutsträdet (BT)
3. Genomsnittlig perceptron (AP)
4. Support vector machine (SVM)  

Följande diagram illustrerar en del av designytan experiment som anger den sekvens som modeller har skapats:  

![][6]  

*Bild 8: Skapa modeller i Machine Learning Studio*  

### <a name="scoring-methods"></a>Bedömningsprofil metoder
Vi bedömas fyra modeller med hjälp av en märkt utbildning dataset.  

Vi skickade också bedömningsprofil dataset till en jämförbar modell som skapas med hjälp av fjärrskrivbord utgåvan av SAS Enterprise Miner 12. Vi mätt riktighet SAS-modellen och alla fyra Machine Learning Studio-modeller.  

## <a name="results"></a>Resultat
I det här avsnittet presenterar vi vårt resultat om riktighet modeller, baserat på bedömningsprofil dataset.  

### <a name="accuracy-and-precision-of-scoring"></a>Noggrannhet och precision av bedömningen
Implementering i Azure Machine Learning är oftast bakom SAS Precision med cirka 10 – 15% (område Under kurvan eller AUC).  

Den viktigaste måtten i omsättning är dock felklassificering hastighet: som är av de främsta churners som förväntade av klassificeraren vilken av dem gjorde **inte** omsättningsuppdateringar och har fått någon särskild behandling? Följande diagram jämförs felklassificering hastigheten för alla modeller:  

![][7]

*Bild 9: Passau prototyp området under kurva*

### <a name="using-auc-to-compare-results"></a>Använda AUC för att jämföra resultatet
Området Under kurvan (AUC) är ett mått som representerar en global mått på *Avskiljbarhet* mellan distributioner av poängen för positiva och negativa population. Den liknar traditionella mottagaren operatorn egenskap (ROC) diagrammet, men en viktig skillnad är att AUC mått inte måste du välja ett tröskelvärde. I stället det sammanfattar resultaten över **alla** möjliga alternativ. Däremot traditionella ROC diagrammet visar positivt frekvensen på den lodräta axeln och falska positiva på den horisontala axeln och klassificering tröskelvärde varierar.   

AUC används vanligtvis som en åtgärd för värd för olika algoritmer (eller olika system) eftersom den tillåter modeller som ska jämföras med hjälp av deras AUC värden. Det här är en populär metod i branscher som meteorologi och biosciences. Därför representerar AUC ett populära verktyg för att bedöma klassificerare prestanda.  

### <a name="comparing-misclassification-rates"></a>Jämföra felklassificering priser
Vi jämfört med felklassificering priser för datauppsättningen i fråga med hjälp av CRM-data över ca 8 000 prenumerationer.  

* Hastigheten med SAS felklassificering var 10 – 15%.
* Hastigheten med Machine Learning Studio felklassificering var 15-20% för översta 200 300 churners.  

Det är viktigt att åtgärda de kunder som har den högsta risken för omsättningsuppdateringar genom att erbjuda dem en concierge tjänst eller andra särskilda behandling i branschen telekommunikation. I detta avseende uppnår Machine Learning Studio-implementering resultat likvärdiga dem SAS-modellen.  

På samma sätt, är noggrannhet viktigare än precisionen eftersom vi främst är intresserad av att korrekt klassificera potentiella churners.  

I följande diagram från Wikipedia visar relationen grafiskt livlig, enkelt att förstå:  

![][8]

*Figur 10: Förhållandet mellan noggrannhet och precision*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Noggrannhet och precision resultat för förstärkta trädet modellen
Följande diagram visar rådata resultaten från bedömningen med Machine Learning-prototyp den förstärkta trädet modellen du den mest korrekta bland de fyra modellerna:  

![][9]

*Figur 11: Förstärkta trädet modellen egenskaper*

## <a name="performance-comparison"></a>Jämförelse av prestanda
Vi jämfört med hastigheten som data har lyckats med hjälp av Machine Learning Studio-modeller och en jämförbar modell som skapats med hjälp av SAS Enterprise Miner 12.1 skrivbord utgåvan.  

I följande tabell sammanfattas prestanda för algoritmer:  

*Tabell 1. Allmänna prestanda (noggrannhet) algoritmer*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Genomsnittlig modellen |Bästa modellen |Presterar som förväntat |Genomsnittlig modellen |

Modeller finns i Machine Learning Studio gick bättre än förväntat SAS med 15 25% för hastigheten för körning, men noggrannhet utfördes i stort sett par.  

## <a name="discussion-and-recommendations"></a>Beskrivning och rekommendationer
I branschen telekommunikation flera metoder har vuxit fram för att analysera omsättning, inklusive:  

* Härledd mätvärden för fyra grundläggande kategorier:
  * **Entitet (till exempel en prenumeration)**. Etablera grundläggande information om prenumerationen och/eller kund som omfattas av omsättning.
  * **Aktivitet**. Hämta alla möjliga användningsinformation som är relaterad till entitet, till exempel antal inloggningar.
  * **Teknisk support**. Samla information från kunden stöd loggar för att indikera om prenumerationen har problem eller interaktioner med teknisk support.
  * **Konkurrenskraftiga-och**. Hämta möjliga information om kunden (till exempel kan vara otillgänglig eller svårt att spåra).
* Använd betydelse för val av funktioner för enheten. Detta innebär att den förstärkta träd modellen alltid är en Orderlöfte metod.  

Användning av dessa fyra kategorier skapar illusionen som en enkel *deterministisk* metod, baserat på index format på rimliga faktorer per kategori, bör vara tillräckligt för att identifiera kunder för risk för omsättning. Tyvärr kan är detta begrepp verkar rimligt, men false förstå. Det beror på att omsättning är en temporal och faktorer för omsättningsuppdateringar är vanligtvis tillfälligt tillstånd. Vad leder en kund till bör du låta idag skilja imorgon och det verkligen är olika sex månader från nu. Därför kan en *probabilistic* modell är nödvändigt.  

Den här viktiga observationer förbises ofta i företag, som vanligtvis föredrar en business intelligence-inriktade metod till analytics, främst eftersom det är en enklare sälja och ger enkla automation.  

Molnapparnas självserviceanalys med hjälp av Machine Learning Studio är dock att de fyra informationskategorierna klassificerats efter avdelning blir en viktig källa för machine learning om omsättning.  

En spännande funktion kommer i Azure Machine Learning är möjligheten att lägga till en anpassad modul i databasen med fördefinierade moduler som redan är tillgängliga. Den här funktionen skapas, en möjlighet att välja bibliotek och skapa mallar för vertikala marknader. Det är en viktiga fördelar med Azure Machine Learning på marknaden plats.  

Vi hoppas att fortsätta det här avsnittet i framtiden, särskilt rör analyser av stordata.
  

## <a name="conclusion"></a>Sammanfattning
Det här dokumentet beskriver en sensible metod för att lösa vanliga problem med kunden omsättning med hjälp av ett allmänt ramverk. Vi anses vara en prototyp för resultatfunktioner modeller och implementeras med hjälp av Azure Machine Learning. Slutligen vi för att utvärdera prestanda prototyp lösningens avseende jämförbara algoritmer i SAS.  

 

## <a name="references"></a>Referenser
[1] Förutsägelseanalyser: utöver förutsägelser västra McKnight informationshantering, juli/augusti 2011, s.18 20.  

[2] Wikipedia artikel: [noggrannhet och precision](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [SKARPA-DM 1.0: stegvisa Data Utvinningsmodellen Guide](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Stordata marknadsföring: engagera kunderna mer effektivt och enheten värde](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [anger omsättningsuppdateringar modellen mallen](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) i [Azure AI-galleriet](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Bilaga
![][10]

*Figur 12: Ögonblicksbild av en presentation på omsättning prototyp*

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
