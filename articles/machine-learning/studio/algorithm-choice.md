---
title: Så här väljer du algoritmer för maskininlärning | Microsoft Docs
description: Så här väljer du Azure Machine Learning algoritmer för övervakad och oövervakad inlärning i kluster-, klassificerings- eller regressionsmodell experiment.
services: machine-learning
documentationcenter: ''
author: pakalra
ms.author: pakalra
manager: cgronlun
editor: cgronlun
tags: ''
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/18/2017
ms.openlocfilehash: e1c7030a5f0c6e13653b302fcb48e7d4efa232c7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Så här väljer du algoritmer för Microsoft Azure Machine Learning
Besvara frågan ”vad maskininlärningsalgoritmen ska jag använda”? är alltid ”det beror”. Det beror på storleken, kvaliteten och typen av uppgift. Det beror på vad du vill göra med svaret. Det beror på hur matematiska av algoritmen översattes till instruktioner för den dator som du använder. Och det beror på hur lång tid som du har. Även de mest erfarna datavetare går inte att avgöra vilken algoritm utför bäst innan du försöker dem.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Machine Learning-algoritmen Cheat blad
Den **Microsoft Azure Machine Learning algoritmen Cheat blad** val höger datorn Inlärningsalgoritmen för förutsägelseanalyslösningar från Microsoft Azure Machine Learning-biblioteket med algoritmer.
Den här artikeln får du veta hur du använder den.

> [!NOTE]
> Om du vill hämta fusklapp och följa den här artikeln, gå till [maskin learning algoritmen fusklapp för Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Den här fusklapp har en särskild målgrupp i åtanke: en början data forskare med undergraduate nivå machine learning som försöker välja börja med en algoritm i Azure Machine Learning Studio. Det innebär att den gör vissa generaliseringar och oversimplifications, men den pekar du i en säker riktning. Det innebär också att det finns många av algoritmer som inte visas här. När Azure Machine Learning växer och omfattar en fullständig uppsättning tillgängliga metoder kan vi lägger till dem.

De här rekommendationerna är kompilerade feedback och tips från många dataanalytiker och machine learning experter. Vi har inte accepterar allt, men jag har försökt harmonisera våra åsikter i en grov konsensus. De flesta av instruktionerna avvikelser som börjar med ”det beror...”

### <a name="how-to-use-the-cheat-sheet"></a>Hur du använder fusklapp
Läsa sökvägen och algoritmen etiketterna i diagrammet som ”för  *&lt;sökväg etikett&gt;*, använda  *&lt;algoritmen&gt;*”. Till exempel ”för *hastighet*, använda *två klassen logistic regression*”. Ibland mer än en gren gäller.
Ibland är ingen av dem en perfekt passning. De är avsedda att regeln för USB rekommendationer så oroa dig inte om det är exakt.
Flera datavetare jag pratade med dessa som det enda säkra sättet att hitta algoritmen mycket bästa är att prova alla.

Här är ett exempel från den [Azure AI-galleriet](http://gallery.cortanaintelligence.com/) av ett experiment som försöker flera algoritmer mot samma data och jämför resultaten: [jämför flera klassen klassificerare: enhetsbokstaven recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Se [Översiktsdiagram över funktionerna i Azure Machine Learning Studio](studio-overview-diagram.md) om du vill ladda ned och skriva ut ett diagram med en översikt över funktionerna i Machine Learning Studio.
> 
> 

## <a name="flavors-of-machine-learning"></a>Varianter av machine learning
### <a name="supervised"></a>Övervakad
Övervakad inlärning algoritmer göra förutsägelser baserat på en uppsättning exempel. Historiska lager priser kan till exempel användas för att risk gissningar i framtida priser. Varje exempel som används för träning är märkt med värdet för intresse – i det här fallet lager priset. En övervakad inlärningsalgoritm söker efter mönster i dessa värdeetiketter. Det kan använda all information som kan vara relevanta – dag i veckan, säsongen, företagets ekonomiska data, typ av bransch, förekomst av geopolitiska avbrott, och varje algoritm ser ut för olika typer av mönster. När algoritmen har hittat bästa mönstret den kan, mönstret används för att göra förutsägelser för namnlösa tester data – framtidens priser.

Övervakad inlärning är ett populärt och praktiskt typ av maskininlärning. Med ett undantag är alla moduler i Azure Machine Learning övervakad inlärning algoritmer. Det finns flera specifika typer av övervakad inlärning som representeras i Azure Machine Learning: klassificering, regression och avvikelseidentifiering identifiering.

* **Klassificering**. När data används för att förutsäga en kategori, kallas även övervakad inlärning klassificering. Detta är fallet när du tilldelar en avbildning som en bild av en katt eller en hund. När det finns två alternativ, kallas **tvåklass** eller **binomial klassificering**. När det finns flera kategorier som när förutsäga vinnaren av NCAA mars Madness turnering, problemet kallas **flera klassen klassificering**.
* **Regression**. När ett värde är att förutsade, precis som med lagrets priser, kallas övervakad inlärning regression.
* **Avvikelseidentifiering**. Ibland är målet att identifiera datapunkter som är helt enkelt ovanliga. Att upptäcka bedrägerier, till exempel alla sällsynt kreditkort utgiftsgränsen mönstren är tveksam. Möjliga varianter är så många och utbildning exemplen så några att det inte är möjligt att veta vilka bedrägliga aktiviteten ser ut. Den metod som tar avvikelseidentifiering är att bara veta vilken normal aktivitet ser ut som (med en tidigare icke-olagliga transaktioner) och identifiera något som skiljer sig avsevärt.

### <a name="unsupervised"></a>Oövervakad
I oövervakade learning har datapunkterna inga etiketter. Målet med en algoritm för oövervakad inlärning är i stället att ordna data på något sätt eller för att beskriva strukturen. Detta kan innebära att gruppera i kluster eller för att hitta olika sätt att granska komplexa data så att den visas enklare och mer organiserad.

### <a name="reinforcement-learning"></a>Förstärkning learning
I förstärkning learning, hämtar algoritmen att välja en åtgärd som svar på varje datapunkt. Inlärningsalgoritmen får också en ersättning signal en kort stund senare, som anger hur bra beslutet.
Baserat på den här ändrar algoritmen sin strategi för att uppnå högsta ersättning. Det finns inga förstärkning learning algoritmen moduler i Azure Machine Learning. Förstärkning learning är vanligt i robotics, där en uppsättning sensoravläsningar vid en punkt i tiden är en datapunkt och algoritmen måste välja av roboten nästa åtgärd. Det är också en fysisk plats för sakernas Internet program.

## <a name="considerations-when-choosing-an-algorithm"></a>Att tänka på när du väljer en algoritm
### <a name="accuracy"></a>Noggrannhet
Hämtar det mest korrekta svaret möjliga är inte alltid nödvändigt.
Ibland är en uppskattning lämplig, beroende på vad du vill använda den för. Om så är fallet kan du klippa ut din bearbetningstid kraftigt av skulle fastna för fler ungefärliga metoder. En annan fördel med flera ungefärliga metoder är att de naturligt tenderar att undvika [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Utbildning
Hur många minuter eller timmar som krävs för att träna en modell varierar mycket mellan algoritmer. Utbildning tid är ofta beroende av noggrannhet – en vanligtvis medföljer den andra. Dessutom kan är vissa algoritmer mer känslig för antalet datapunkter än andra.
När tiden begränsas hinner valet av algoritmen, särskilt när de uppgifter som är stor.

### <a name="linearity"></a>Linearitet
Många maskininlärningsalgoritmer Se användning av linearitet. Linjär klassificering algoritmer förutsätter att klasser kan avgränsas med rakt (eller dess högre dimension analoga). Dessa inkluderar logistic regression och stöd för vector datorer (som implementeras i Azure Machine Learning).
Linjär regression algoritmer förutsätter att datatrender följer rakt. Dessa förutsättningar är inte bra för vissa problem, men på andra de avslutar noggrannhet.

![Icke-linjära klassen gräns][1]

***Icke-linjära klassen gräns*** *-förlita dig på en linjär klassificeringsalgoritm skulle resultera i sämre Precision*

![Data med en icke-linjär trend][2]

***Data med en icke-linjär trend*** *-metoden linjär regression skulle generera mycket större fel än nödvändigt*

Trots sina farorna är linjär algoritmer mycket populär första raden för angrepp. De brukar vara algoritmiskt snabbt och enkelt att träna.

### <a name="number-of-parameters"></a>Antalet parametrar
Parametrarna är rattar en data-forskare hämtar aktivera när du konfigurerar en algoritm. De är siffror som påverkar den algoritm beteende, till exempel feltolerans eller antal iterationer eller alternativ mellan varianter av hur algoritmen fungerar. Utbildning och korrektheten i algoritmen kan ibland vara ganska känslig för hämtning av precis rätt inställningar. Vanligtvis krävs algoritmer med många parametrar i de flesta och annat försök att hitta en bra kombination.

Du kan också finns en [parametern omfattande](algorithm-parameters-optimize.md) modulen block i Azure Machine Learning som försöker automatiskt alla parameterkombinationer på oavsett granularitet som du väljer. Detta är ett bra sätt att kontrollera omfattas av parameter-utrymme, ökar den tid som krävs för att träna en modell exponentiellt med antalet parametrar.

Den upp och är att med många parametrar vanligtvis anger att en algoritm har större flexibilitet. Det kan ofta uppnå mycket bra precision. Du kan hitta rätt kombination av parameterinställningar för har angetts.

### <a name="number-of-features"></a>Antal funktioner
För vissa typer av data, antal funktioner kan vara mycket stora jämfört med antal datapunkter. Detta är ofta fallet med genetics eller textdata. Ett stort antal funktioner kan bog ned vissa algoritmer för maskininlärning gör utbildning tid unfeasibly lång. Support Vector datorer är särskilt väl lämpade för det här fallet (se nedan).

### <a name="special-cases"></a>Särskilda fall
Vissa algoritmer för maskininlärning gör viss antaganden om strukturen för data eller önskat resultat. Om du hittar en som passar dina behov, får den du bättre resultat, mer korrekta förutsägelser eller snabbare utbildning.

| **Algoritm** | **Noggrannhet** | **Utbildning** | **Linearitet** | **Parametrar** | **Anteckningar** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Tvåklass klassificering** | | | | | |
| [Logistic regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [beslut skog](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [beslut Djungel](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Låg minneskrav |
| [tvåklassförhöjda beslutsträdet](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Stora minneskrav |
| [neurala nätverket](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[Ytterligare anpassning är möjligt](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [Genomsnittlig perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [support vector machine](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Bra för stora funktionsuppsättningar |
| [lokalt djup support vector machine](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Bra för stora funktionsuppsättningar |
| ['Bayes point-dator](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Flera klassen klassificering** | | | | | |
| [Logistic regression](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [beslut skog](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [beslut Djungel ](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Låg minneskrav |
| [neurala nätverket](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[Ytterligare anpassning är möjligt](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [ett-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Visa egenskaper för vald metod två-klass |
| **Regression** | | | | | |
| [linjär](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Linjär Bayesian](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [beslut skog](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [tvåklassförhöjda beslutsträdet](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Stora minneskrav |
| [snabb skog quantile](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Distributioner i stället för punkt förutsägelser |
| [neurala nätverket](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[Ytterligare anpassning är möjligt](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Tekniskt sett log-linjär. För att förutsäga antalet |
| [ordningstalet](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |För att förutsäga rang ordning |
| **Avvikelseidentifiering** | | | | | |
| [support vector machine](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Särskilt bra för stora funktionsuppsättningar |
| [PCA-baserad avvikelseidentifiering](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |En algoritm för kluster |

**Algoritmen egenskaper:**

**●** -visar hög precision, snabb utbildning gånger och användning av linearitet

**○** -visar bra Precision och måttlig utbildning gånger

## <a name="algorithm-notes"></a>Algoritmen anteckningar
### <a name="linear-regression"></a>Linjär regression
Som nämnts tidigare [linjär regression](https://msdn.microsoft.com/library/azure/dn905978.aspx) passar en rad (eller plan eller hyperplane) i datauppsättningen. Det är en bestämmer hög grad, snabbt och enkelt, men det kan vara alltför simplistic för vissa problem.
Här en [linjär regression kursen](linear-regression-in-azure.md).

![Data med en linjär trend][3]

***Data med en linjär trend***

### <a name="logistic-regression"></a>Logistic regression
Även om den innehåller förvirrande 'regression ”i namnet, logistic regression är faktiskt ett kraftfullt verktyg för [tvåklass](https://msdn.microsoft.com/library/azure/dn905994.aspx) och [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) klassificering. Det går snabbt och enkelt. Det faktum att den använder en '-formad kurva i stället för rakt är det en naturlig anpassning för att dela data i grupper. Logistic regression ger linjär klassgränser, så se när du använder den, till en linjär uppskattning är något som du kan live med.

![Logistic regression till tvåklass data med just en funktion][4]

***En logistic regression tvåklass data med ett enda funktionen*** *-klassen gräns är den punkt då logistic kurvan är precis som nära båda klasserna*

### <a name="trees-forests-and-jungles"></a>Träd och skogar jungles
Decision skogar ([regression](https://msdn.microsoft.com/library/azure/dn905862.aspx), [tvåklass](https://msdn.microsoft.com/library/azure/dn906008.aspx), och [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)), beslut jungles ([tvåklass](https://msdn.microsoft.com/library/azure/dn905976.aspx) och [ multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)), och förstärkta beslutsträd ([regression](https://msdn.microsoft.com/library/azure/dn905801.aspx) och [tvåklass](https://msdn.microsoft.com/library/azure/dn906025.aspx)) baseras på beslutsträd, en grundläggande koncept för maskininlärning. Det finns många varianter av beslutsträd, men alla gör samma sak, dela in funktionen utrymme i regioner med mestadels samma etikett. Dessa kan vara områden av konsekvent kategori eller konstant värde, beroende på om du gör klassificerings- eller regressionsmodell.

![Beslutsträdet delar upp en funktion utrymme][5]

***Ett beslutsträd delar upp kan funktionen i områden av ungefär uniform värden***

Eftersom funktionen kan kan delas in i godtyckligt små områden, är det enkelt att tro att dividera tillräckligt buffertstorleken för att ha en datapunkt per region. Detta är ett ytterst exempel på overfitting. För att undvika detta, skapas en stor mängd träd med matematiska vidtas att träd inte korrelerade. Medelvärde för den här ”beslut skogen” är ett träd som undviker overfitting. Beslut skogar kan använda mycket minne. Beslutsdjungler är en variant som förbrukar mindre minne på bekostnad av en längre utbildningstid.

Förstärkta beslutsträd undvika overfitting genom att begränsa hur många gånger som de kan du dela upp och hur många datapunkter är tillåtna i varje region. Algoritmen skapar en sekvens med träd, där varje lär sig att kompensera för fel som har lämnat trädet innan. Resultatet är en mycket noggrann deltagaren som tenderar att använda mycket minne. Fullständig teknisk beskrivning kolla [Friedmans ursprungliga dokumentet](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Snabb skog quantile regression](https://msdn.microsoft.com/library/azure/dn913093.aspx) är en variation av beslutsträd för särskilda fall där du vill veta inte bara vanliga () medianvärdet för data i en region men dess distributionsplatsen i form av quantiles.

### <a name="neural-networks-and-perceptrons"></a>Neurala nätverk och perceptrons
Neurala nätverk är hjärna-inspirerat learning algoritmer som täcker [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx), [tvåklass](https://msdn.microsoft.com/library/azure/dn905947.aspx), och [regression](https://msdn.microsoft.com/library/azure/dn905924.aspx) problem. De kommer i en rad, men neurala nätverk i Azure Machine Learning är någon form av riktat acykliskt diagram. Det innebär att inkommande funktioner skickas framåt (aldrig bakåt) via en sekvens av lager innan aktiveras i utdata. I varje lager är indata viktas i olika kombinationer summeras och skickas till nästa säkerhetslager. Den här kombinationen av enkla beräkningar ger möjlighet att få mer avancerade klassen gränser och data trender till synes genom Magiskt tal för. Många lager nätverk för den här typen utföra i ”djupt learning” som bränslen så mycket rapportering av teknisk och vetenskaplig fiktion.

Den här högpresterande finns inte gratis men. Neurala nätverk kan ta lång tid att träna, särskilt för stora datamängder med många funktioner. De kan också ha fler parametrar än de flesta algoritmer, vilket innebär att parametern omfattande expanderar utbildningstid en hel del.
Och för de overachievers som vill [ange sina egna nätverksstrukturen](http://go.microsoft.com/fwlink/?LinkId=402867), du kan använda inexhaustible.

![Gränser som upptäckts av neurala nätverk][6]
***gränser som upptäckts av neurala nätverk kan vara komplicerat och oregelbundna***

Den [tvåklass var i genomsnitt perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) är neurala nätverk svaret skjuter utbildning gånger. Den använder en nätverksinfrastruktur som ger linjär klassgränser. Det är nästan primitiva dagens standarder, men den har en lång historik över fungerar robustly och är tillräckligt små för att lära dig snabbt.

### <a name="svms"></a>SVMs
Support vector datorer (SVMs) hitta gräns som avgränsar klasser av som bred en marginal som möjligt. När två klasser inte kan vara klart åtskilda, hitta algoritmerna bästa gräns som de kan. Som skrivits i Azure Machine Learning i [tvåklass SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) gör detta med en rak linje. (I SVM tala används en linjär kernel.) Eftersom det gör det här linjär uppskattning, går det att köra ganska snabbt. Där det sin verkliga styrka är med funktionen intensiva data, t.ex. text eller genom. I dessa fall kan SVMs separata klasser snabbare och med mindre overfitting än de flesta andra algoritmer, förutom kräver bara en liten mängd minne.

![Support vector machine klassen gräns][7]

***En typisk support vector machine klass gräns maximerar marginalen avgränsa två klasser***

En annan produkt från Microsoft Research den [tvåklass lokalt djup SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx) är en icke-linjär variant av SVM som behåller de flesta av den linjära versionen hastighet och minne effektivitet. Det är perfekt för fall där den linjära metoden inte får tillräckligt aktuell svar. Utvecklare sparas det snabbt genom att bryta ned problemet till flera mindre linjär SVM problem. Läs den [fullständig beskrivning](http://proceedings.mlr.press/v28/jose13.html) information om hur de hämtas av den här lura.

Med en smarta i linjära SVMs den [en klass SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) ritar en gräns som beskriver nära hela datauppsättningen. Det är användbart för identifiering av avvikelse. Alla nya datapunkter som långt hamnar utanför gränsen är ovanligt att anmärkningsvärda.

### <a name="bayesian-methods"></a>Bayesian metoder
Bayesian metoder har en hög önskvärt kvalitet: de undvika overfitting. De kan göra detta genom att göra några antaganden i förväg om sannolikt fördelningen av svaret. En annan byproduct med den här metoden är att de har mycket några parametrar. Azure Machine Learning har båda Bayesian algoritmer för båda klassificering ([Two-class Bayes point-dator](https://msdn.microsoft.com/library/azure/dn905930.aspx)) och regression ([Bayesian linjär regression](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Observera att detta förutsätter att data kan dela eller ryms inom rakt.

På en historisk anteckning utvecklades Bayes' point datorer Microsoft Research. De har vissa undantagsfall snygg teoretisk arbete bakomliggande. Den berörda studenten omdirigeras till den [ursprungliga artikeln i JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) och en [insiktsfulla blogg av Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Särskilda algoritmer
Om du har en särskild målet kanske tur. I Azure Machine Learning-samlingen finns algoritmer som specialiserade på:

- rangordnas förutsägelse ([ordningstal regression](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- Räkna förutsägelse ([Poisson regression](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- avvikelseidentifiering (en baserat på [huvudkomponenter analysis](https://msdn.microsoft.com/library/azure/dn913102.aspx) och baserat på ett [support vector machine](https://msdn.microsoft.com/library/azure/dn913103.aspx)s)
- kluster ([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![PCA-baserad avvikelseidentifiering][8]

***PCA-baserad avvikelseidentifiering*** *-majoriteten av data hamnar i en stereotypical distributionsplats; punkter kraftigt avviker från att distributionsplatsen är tveksam*

![Datauppsättning grupperas med K-means][9]

***En datauppsättning är grupperade i fem kluster med hjälp av K-means***

Det finns också en ensemble [en v alla multiklass-baserad klassificerare](https://msdn.microsoft.com/library/azure/dn905887.aspx), vilket bryter klassificeringsproblem N-klass N-1 tvåklass klassificering problem. Noggrannhet, utbildning och linearitet egenskaper bestäms av tvåklass-klassificerare som används.

![Två-klass, klassificerare kombineras för att bilda en klassificerare tre-klass][10]

***Ett par med två-klass, klassificerare kombineras till en klassificerare tre-klass***

Azure Machine Learning även åtkomst till en kraftfull maskininlärning framework under namnet på [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW lekfull kategorisering här, eftersom den kan lära dig klassificerings- och regression problem och kan även lära sig från delvis omärkta data. Du kan konfigurera den att använda någon av ett antal learning, förlust funktioner och optimering algoritmer. Det har utformats från grunden in vara effektiva, parallella och mycket snabba. Den hanterar funktionen löjligt stora mängder med mycket tydligt ansträngning.
Igång och lett av Microsoft Researchs egna John Langford är VW en formel en post i ett fält av börs bil algoritmer. Inte alla problem som passar VW, men om den det kan vara värt att din stund att klara inlärningskurvan på dess gränssnitt. Det är också tillgängliga som [fristående öppen källkod](https://github.com/JohnLangford/vowpal_wabbit) på flera språk.

## <a name="more-help-with-algorithms"></a>Mer hjälp med algoritmer
* En nedladdningsbar infographic som beskriver algoritmer och exempel finns [nedladdningsbara Infographic: maskin lär du dig grunderna med algoritmen exempel](basics-infographic-with-algorithm-examples.md).
* En lista efter kategori av alla de tillgängliga maskininlärningsalgoritmer i Azure Machine Learning Studio finns [initiera modell] [ initialize-model] i Machine Learning Studio algoritmen modulen hjälpen och.
* En fullständig alfabetisk lista över algoritmer och moduler i Azure Machine Learning Studio finns [A-Ö-listan över Machine Learning Studio moduler] [ a-z-list] i Machine Learning Studio algoritmen och hjälpa till att modulen.
* Om du vill hämta och skriva ut ett diagram som ger en översikt över funktionerna i Azure Machine Learning Studio finns [Översiktsdiagram över funktioner i Azure Machine Learning Studio i](studio-overview-diagram.md).


<!-- Reference links -->
[initialize-model]: https://msdn.microsoft.com/library/azure/dn905812.aspx
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx

<!-- Media -->

[1]: ./media/algorithm-choice/image1.png
[2]: ./media/algorithm-choice/image2.png
[3]: ./media/algorithm-choice/image3.png
[4]: ./media/algorithm-choice/image4.png
[5]: ./media/algorithm-choice/image5.png
[6]: ./media/algorithm-choice/image6.png
[7]: ./media/algorithm-choice/image7.png
[8]: ./media/algorithm-choice/image8.png
[9]: ./media/algorithm-choice/image9.png
[10]: ./media/algorithm-choice/image10.png
