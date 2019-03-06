---
title: Välja algoritmer
titleSuffix: Azure Machine Learning Studio
description: Så här väljer du Azure Machine Learning Studio-algoritmer för övervakad och oövervakad inlärning i kluster-, klassificerings- eller regressionsmodell experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: d75048cb53dd1a5ebaba6322228af6137d0f3b96
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456184"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Så här väljer du algoritmer för Azure Machine Learning Studio

Besvara frågan ”vad maskininlärningsalgoritmen ska jag använda”? är alltid ”det beror på”. Det beror på storleken, kvalitet och typen av uppgift. Det beror på vad du vill göra med svaret. Det beror på hur matematiska av algoritmen har översatts till anvisningar för den dator som du använder. Och det beror på hur lång tid som du har. Även de mest erfarna dataexperter kan inte se bästa utförs innan du försöker dem med vilken algoritm.

Machine Learning Studio tillhandahåller avancerade algoritmer som skalbara förstärkta beslutsträd, Bayesian Recommendation-system, djupa neurala nätverk och beslutsdjungler utvecklade av Microsoft Research. Skalbara maskininlärningspaket med öppen källkod, som Vowpal Wabbit, ingår också. Machine Learning Studio stöder maskininlärningsalgoritmer för multiklass-baserad och binär klassificering, regression och kluster. Se en fullständig lista över [Machine Learning-moduler](/azure/machine-learning/studio-module-reference/index).
Dokumentationen innehåller information om varje algoritm och hur du ställer in parametrar för att optimera algoritmen för din användning.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Lathund för Machine Learning-algoritm

Den **[Microsoft Azure Machine Learning Studio algoritmen facit blad](algorithm-cheat-sheet.md)** hjälper till att du väljer rätt machine learning-algoritm för dina lösningar för förutsägande analyser från Azure Machine Learning Studio-bibliotek med algoritmer.
Den här artikeln vägleder dig igenom hur du använder den här lathunden.

> [!NOTE]
> Ladda ner facit och följa den här artikeln genom att gå till [Machine learning-algoritmfacit för Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Den här lathunden har en mycket specifik målgrupp i åtanke: början dataexpert med gymnasiet på servernivå machine learning, försök välja en algoritm till att börja med i Azure Machine Learning Studio. Det innebär att det gör vissa generaliseringar och oversimplifications, men den pekar du i en säker riktning. Det innebär också att det finns många olika algoritmer som inte visas här.

De här rekommendationerna är kompilerade feedback och tips från många datatekniker och machine learning-experter. Vi kunde inte komma överens om allt, men vi har försökt att harmonisera våra åsikter i en ungefärlig konsensus. De flesta av rapporterna avvikelser som börjar med ”det beror...”

### <a name="how-to-use-the-cheat-sheet"></a>Hur du använder facit

Läsa sökvägen och algoritmen etiketterna i diagrammet som ”för  *&lt;sökväg etikett&gt;*, använda  *&lt;algoritmen&gt;*”. Till exempel ”för *hastighet*, använda *två klassen logistic regression*”. Ibland mer än en gren gäller.
Ibland är ingen av dem en perfekt passning. De är avsedda att vara regeln av USB-rekommendationer, så oroa dig inte om den är exakt.
Flera dataexperter vi talade med dessa som det enda säkra sättet att hitta den allra bästa algoritmen är att försöka dem alla.

Här är ett exempel från den [Azure AI-galleriet](http://gallery.azure.ai/) av ett experiment som försöker flera algoritmer mot samma data och jämför resultaten: [Jämför flera klassificerare: Enhetsbokstaven för](http://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> För att ladda ned en enkel att förstå informationsgrafiken översikt över grunderna i machine learning att lära dig om populära algoritmer som används för att besvara vanliga frågor för machine learning, [Machine learning-grunder med algoritmexempel](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Varianter av machine learning

### <a name="supervised"></a>Övervakat

Övervakad inlärning algoritmer göra förutsägelser utifrån en uppsättning exempel. Historiska aktiekurser kan till exempel användas för att se gissningar om priserna i framtiden. Varje exempel som används för träning är märkt med värdet intressanta – i det här fallet aktiekursen. En algoritm för övervakad inlärning söker efter mönster i värdet etiketterna. Den kan använda all information som kan vara relevanta – dagen i veckan, säsongen, företagets finansiella data, typ av bransch, förekomsten av geopolitiska avbrottshändelser, och varje algoritm ser ut för olika typer av mönster. När algoritmen har hittat bästa mönstret kan den, mönstret används för att göra förutsägelser för utan etikett testdata – morgondagens priser.

Övervakad inlärning är en populär och användbart typ av machine learning. Med ett undantag är alla moduler i Azure Machine Learning Studio övervakade algoritmer. Det finns flera specifika typer av övervakad inlärning som representeras i Azure Machine Learning Studio: klassificering, regression och avvikelseidentifiering.

* **Klassificering**. När data som används för att förutsäga en kategori, kallas även övervakad inlärning klassificering. Detta är fallet när du tilldelar en avbildning som en bild av en katt eller en hund. När det finns två alternativ, kallas **tvåklassförhöjt** eller **binominal klassificering**. När det finns fler kategorier som när förutse vinnare av NCAA March Madness-turneringen, det här problemet kallas **flera klassificering**.
* **Regression**. När ett värde som förväntas, precis som med aktiekurser, kallas regression med övervakad inlärning.
* **Avvikelseidentifiering**. Ibland är målet att identifiera datapunkter som är helt enkelt ovanliga. Upptäcka bedrägerier, till exempel alla sällsynta kreditkort utgiftsgränsen mönster är tveksam. Det finns så många möjliga varianter och utbildning exemplen så liten att det inte är möjligt att lära dig vilka bedrägliga aktivitet ser ut. Den metod som tar avvikelseidentifiering är att helt enkelt lära sig vilka normal aktivitet ut (med en historik över icke-olagliga transaktioner) och identifiera något som är avsevärt annorlunda.

### <a name="unsupervised"></a>Oövervakade

Oövervakad inlärning har datapunkterna inga etiketter. Målet med en oövervakad Inlärningsalgoritmen är istället att ordna data på något sätt eller för att beskriva dess struktur. Detta kan innebära att gruppera den i kluster eller hitta olika sätt att titta på komplexa data så att den visas enklare eller mer organiserad.

### <a name="reinforcement-learning"></a>Förstärkande inlärning

I förstärkande inlärning, hämtar algoritmen att välja en åtgärd som svar på varje datapunkt. Inlärningsalgoritmen får också en utmärkelse signal en kort tid senare, som anger hur bra beslutet var.
Baserat på den här ändrar algoritmen sin strategi för att uppnå högsta trafik. Det finns för närvarande inga förstärkt inlärningsmoduler algoritmen i Azure Machine Learning Studio. Förstärkande inlärning är vanligt i robotteknik, där uppsättningen sensoravläsningar vid en punkt i tiden är en datapunkt och algoritmen måste välja den robot nästa åtgärd. Det är också en fysisk som passar för sakernas Internet program.

## <a name="considerations-when-choosing-an-algorithm"></a>Att tänka på när du väljer en algoritm

### <a name="accuracy"></a>Tillförlitlighet

Hämta det mest korrekta svaret möjliga är inte alltid nödvändigt.
Ibland är ett approximativt värde lämplig, beroende på vad du vill använda den för. Om så är fallet kan du kunna klippa ut din bearbetningstid avsevärt genom fastna mer ungefärliga metoder. En annan fördel med mer ungefärliga metoder är att de naturligt tenderar att undvika overfitting.

### <a name="training-time"></a>Utbildningstid

Hur många minuter eller timmar som behövs för att träna en modell varierar mycket mellan algoritmer. Utbildning tid är ofta beroende av Precision – en vanligtvis medföljer den andra. Dessutom kan är vissa algoritmer mer känsliga för antalet datapunkter än andra.
När tid är begränsad hinner valet av algoritmen, särskilt när datauppsättningen är stor.

### <a name="linearity"></a>Linearitet

Se massor av machine learning-algoritmer använder linearitet. Linjär klassificering algoritmer förutsätter att klasser kan separeras med en rak linje (eller dess högre dimension analoga). Dessa inkluderar logistic regression och stöd för vektor-datorer (som implementeras i Azure Machine Learning Studio).
Linjär regression algoritmer förutsätter att datatrender följer en rak linje. Dessa antaganden är inte dåligt för vissa problem, men på andra de påverkar precision.

![Icke-linjära klass gräns](./media/algorithm-choice/image1.png)

***Icke-linjära klass gräns*** *-förlita sig på en linjär klassificeringsalgoritm skulle resultera i sämre Precision*

![Data med en icke-linjära trend](./media/algorithm-choice/image2.png)

***Data med en icke-linjära trend*** *-metoden linjär regression skulle generera mycket större fel än nödvändigt*

Trots sitt farorna är linjär algoritmer mycket populär som en första rad av angrepp. De brukar vara algoritmiskt enkelt och snabbt att träna.

### <a name="number-of-parameters"></a>Antalet parametrar

Parametrar är rattar inom data Science hämtar aktivera när du konfigurerar en algoritm. De är värden som påverkar den algoritmen fungerar, till exempel feltolerans eller antalet iterationer eller alternativ mellan varianter av hur algoritmen fungerar. Utbildning och korrektheten i algoritmen kan ibland vara ganska känsliga för att hämta bara rätt inställningar. Normalt kräver algoritmer med stort antal parametrar mest och annat försök att hitta en bra kombination.

Du kan också det finns en [parametern oinskränkt](algorithm-parameters-optimize.md) modulen block i Azure Machine Learning Studio som försöker automatiskt alla parameterkombinationer av med den precision som du väljer. Även om det är ett bra sätt att kontrollera att omfattas av området parametern ökar exponentiellt den tid som krävs för att träna en modell med parametrar.

Den upp och är att ha många parametrar vanligtvis anger att en algoritm har större flexibilitet. Det kan ofta få mycket bra Precision, förutsatt att du kan hitta rätt kombination av parameterinställningar för.

### <a name="number-of-features"></a>Antal funktioner

För vissa typer av data, antal funktioner kan vara mycket stora jämfört med antalet datapunkter. Det här är ofta fallet med genetik eller textdata. Det stora antalet funktioner kan göra ned vissa learning-algoritmer, vilket gör utbildning tid unfeasibly lång. Support Vector datorer är särskilt väl lämpade för att det här fallet (se nedan).

### <a name="special-cases"></a>Specialfall

Vissa learning-algoritmer göra viss antaganden om strukturen för data eller det önskade resultatet. Om du hittar en som passar dina behov kan den ge dig mer användbara resultat eller mer exakta förutsägelser snabbare till utbildning.

| **Algoritm** | **Precision** | **Utbildningstid** | **Linearitet** | **Parametrar** | **Anteckningar** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Två klassificering** | | | | | |
| [Logistic regression](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [beslutsskog](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [beslutet Djungel](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Låg minneskrav |
| [beslutsträd](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Stora minneskrav |
| [neuralt nätverk](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Ytterligare anpassning är möjligt](azure-ml-netsharp-reference-guide.md) |
| [Genomsnittlig perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [dator för vektorstöd](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Bra för stora funktionsuppsättningar |
| [lokalt djup dator för vektorstöd](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Bra för stora funktionsuppsättningar |
| [Bayes' datorn](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Flera klassificering** | | | | | |
| [Logistic regression](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [beslutsskog](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [beslutet Djungel](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Låg minneskrav |
| [neuralt nätverk](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Ytterligare anpassning är möjligt](azure-ml-netsharp-reference-guide.md) |
| [one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Visa egenskaper för metoden tvåklassförhöjt valt |
| **Regression** | | | | | |
| [linear](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Linjär Bayesian](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [beslutsskog](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [beslutsträd](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Stora minneskrav |
| [snabb skog quantile](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Distributioner i stället för punkt-förutsägelser |
| [neuralt nätverk](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Ytterligare anpassning är möjligt](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Tekniskt sett log-linjära. För att förutsäga antalet |
| [ordinal](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |För att förutsäga rangordning-axel |
| **Avvikelseidentifiering** | | | | | |
| [dator för vektorstöd](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Särskilt bra för stora funktionsuppsättningar |
| [PCA-baserad avvikelseidentifiering](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-means](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |En algoritm för klustring |

**Algoritmen egenskaper:**

**●** -visar hög precision, snabb utbildning gånger och användningen av linearitet

**○** -visar bra Precision och måttlig utbildning gånger

## <a name="algorithm-notes"></a>Algoritmen anteckningar

### <a name="linear-regression"></a>Linjär regression

Som nämnts tidigare [linjär regression](/azure/machine-learning/studio-module-reference/linear-regression) anpassar en rad (eller plan eller hyperplane) till datauppsättningen. Det är en bestämmer hög grad, enkelt och snabbt, men det kan vara alltför enkelt för vissa problem.

![Data med en linjär trend](./media/algorithm-choice/image3.png)

***Data med en linjär trend***

### <a name="logistic-regression"></a>Logistic regression

Även om det innehåller 'regression ”i namnet logistic regression är verkligen ett kraftfullt verktyg för [tvåklassförhöjt](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) och [multiclass](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) klassificering. Det är snabbt och enkelt. Det faktum att den använder en '-formade kurvan i stället för en rak linje gör det passar för att dela upp data i grupper. Logistic regression ger linjär klassgränser, så kontrollera när du använder den, att en linjär uppskattning är något du kan existera med.

![Logistic regression till två data med bara en funktion](./media/algorithm-choice/image4.png)

***En logistisk regression till två data med bara ett funktionen*** *-klassen gränsen är den punkt då logistic kurvan är bara så nära båda klasserna*

### <a name="trees-forests-and-jungles"></a>Träd och skogar jungles

Beslut skogar ([regression](/azure/machine-learning/studio-module-reference/decision-forest-regression), [tvåklassförhöjt](/azure/machine-learning/studio-module-reference/two-class-decision-forest), och [multiclass](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), beslut jungles ([tvåklassförhöjt](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) och [ multiclass](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)), och förstärkta beslutsträd ([regression](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) och [tvåklassförhöjt](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) baseras på beslutsträd, grundläggande machine learning-begrepp. Det finns många varianter av beslutsträd, men de kan göra samma sak – dela in funktionen utrymme i regioner med huvudsakligen samma etikett. Det kan vara regioner för konsekvent kategori eller konstant värde, beroende på om du gör klassificerings- eller regressionsmodell.

![Beslutsträd delar upp ett utrymme för funktionen](./media/algorithm-choice/image5.png)

***Ett beslutsträd delar upp ett utrymme för funktionen i regioner med ungefär uniform värden***

Eftersom funktionen kan kan delas in i godtyckligt små regioner, är det enkelt att föreställa sig dividera tillräckligt noggrant för att skapa en datapunkt per region. Det här är ett extrem exempel på overfitting. För att undvika detta skapas ett stort antal träd med särskilda matematiska försiktighet som vidtagits för att säkerställa träd inte kopplas ihop. Genomsnittet för det här ”beslutsskog” är ett träd som undviker overfitting. Beslutet skogar kan använda mycket minne. Beslutsdjungler är en variant som förbrukar mindre minne på bekostnad av en något längre utbildningstid.

Förbättrat beslutsträd undvika overfitting genom att begränsa hur många gånger som de kan du dela upp och hur datapunkter är tillåtna i varje region. Algoritmen skapar en sekvens av träd, som lär sig att kompensera för fel i trädet innan du har lämnat. Resultatet är en mycket noggrann learner som ofta använder mycket minne. Fullständig teknisk beskrivning finns i [Friedmans ursprungliga dokumentet](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Snabb skog quantile regression](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) är en variant av beslutsträd för särskilda fall där du vill veta det vanliga (median) värdet av data inom en region, utan även dess distribution i form av quantiles.

### <a name="neural-networks-and-perceptrons"></a>Neurala nätverk och perceptrons

Neurala nätverk är hjärna-inspirerat algoritmer som täcker [multiclass](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [tvåklassförhöjt](/azure/machine-learning/studio-module-reference/two-class-neural-network), och [regression](/azure/machine-learning/studio-module-reference/neural-network-regression) problem. De kommer in en rad, men neurala nätverk i Azure Machine Learning Studio är alla slags dirigerad acykliska diagram. Det innebär att indatafunktionerna skickas vidare (aldrig bakåt) via en sekvens av lager innan ha varit i utdata. I varje skikt är indata viktad i olika kombinationer, summeras och överföras till nästa lager. Den här kombinationen av enkla beräkningar innebär möjlighet att lära dig avancerade klass gränser och data trender, till synes genom magic. Många nivåer nätverk på den här typen utför den ”deep learning” som skapar förutsättningar för mycket teknisk reporting och science fiction.

Den här högpresterande gratis inte, men. Neurala nätverk kan ta lång tid att träna, särskilt för stora mängder data med många funktioner. De har också fler parametrar än de flesta algoritmer, vilket innebär att parametern oinskränkt expanderar utbildningstid ett bra pris.
Och för de overachievers som vill [ange sina egna nätverksstrukturen](azure-ml-netsharp-reference-guide.md), möjligheterna är inexhaustible.

![Gränser som upptäckts av neurala nätverk](./media/algorithm-choice/image6.png)

***De gränser som upptäckts av neurala nätverk kan vara komplexa och oregelbundna***

Den [tvåklassförhöjt i genomsnitt perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) är neurala nätverk svaret skjuter utbildning gånger. Den använder en nätverksinfrastruktur som ger linjär klassgränser. Det är nästan primitiva av dagens standarder, men den har lång erfarenhet av att arbeta kraftigare och är tillräckligt liten för att lära dig snabbt.

### <a name="svms"></a>SVMs

Support vector datorer (SVMs) hitta gränsen som avgränsar klasser av som en marginal som möjligt. När två klasser inte kan vara klart åtskilda, hitta algoritmerna bästa gräns som de kan. Som skrivits i Azure Machine Learning Studio, den [tvåklassförhöjt SVM](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) gör detta med en rak linje (i SVM talar, används en linjär kernel).
Eftersom det gör den här linjär uppskattning, är det att köra ganska snabbt. Där det är som bäst är med funktionen intensiv data, till exempel text eller stora uppsättningar genomiska data. I dessa fall kan SVMs att separera klasser snabbare och med mindre overfitting än de flesta andra algoritmer, förutom att begära mycket små mängden minne.

![Support vector machine klass gräns](./media/algorithm-choice/image7.png)

***En typisk support vector machine klass gräns maximerar marginal att ange två klasser***

En annan produkt av Microsoft Research, den [tvåklassförhöjt lokalt djup SVM](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) är en icke-linjära variant av SVM som behåller de flesta av den linjära versionen hastighet och minne effektivitet. Det är perfekt i de fall där den linjära metoden inte ger tillräckligt exakta svar på. Utvecklare sparas den snabbt genom att bryta ned problemet i flera mindre linjär SVM problem. Läs den [fullständig beskrivning](http://proceedings.mlr.press/v28/jose13.html) mer information om hur de hämtas av den här knep.

Med hjälp av en smarta förlängning av icke-linjära SVMs den [en klass SVM](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) ritar en gräns som tätt visar hela datauppsättningen. Det är användbart för identifiering av avvikelser. Alla nya datapunkter som inte omfattas av mycket gränsen är ovanlig vara anmärkningsvärda.

### <a name="bayesian-methods"></a>Bayesian metoder

Bayesian metoderna har en mycket önskvärt kvalitet: de undvika overfitting. De kan göra detta genom att göra några antaganden i förväg om sannolikt fördelningen av svaret. En annan byproduct med den här metoden är att de har mycket få parametrar. Azure Machine Learning Studio har Bayesian algoritmer för båda klassificering ([tvåklassförhöjt Bayes datorn](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) och regression ([Bayesian linjär regression](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Observera att dessa förutsätts att data kan dela eller ryms inom en rak linje.

På en historisk anteckning utvecklades 'Bayes point datorer på Microsoft Research. De har vissa presterar exceptionellt bra teoretisk work bakom dem. Intresserad av elevens dirigeras till den [ursprungliga artikeln i JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) och en [insiktsfulla blogg av Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Specialiserade algoritmer
Om du har ett mycket specifik mål du i så fall. Det finns algoritmer som specialiserar sig på i Azure Machine Learning Studio-samling:

- rangordnas förutsägelse ([ordningstal regression](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- Räkna förutsägelse ([Poisson regression](/azure/machine-learning/studio-module-reference/poisson-regression)),
- avvikelseidentifiering (en utifrån [huvudkomponenter analysis](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) och en baserat på [stöd vektor datorer](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- klustring ([K-means](/azure/machine-learning/studio-module-reference/k-means-clustering))

![PCA-baserad avvikelseidentifiering](./media/algorithm-choice/image8.png)

***PCA-baserad avvikelseidentifiering*** *-merparten av data hamnar i en stereotypical distribution; punkter kraftigt avviker från den distributionsplatsen är tveksam*

![Datauppsättning som är grupperade med K-means](./media/algorithm-choice/image9.png)

***En datauppsättning är grupperade i fem kluster med K-means***

Det finns också en ensemble [en v alla inom klassificerare](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), som skadar klassificeringsproblem N-klassen i N-1 tvåklassförhöjt klassificering problem. Den precision, utbildning och linearitet egenskaper bestäms av tvåklassförhöjt-klassificerare som används.

![Två klassificerare kombineras för att bilda en tre-class-klassificerare](./media/algorithm-choice/image10.png)

***Ett par med två klassificerare kan kombineras för att bilda en tre-class-klassificerare***

Azure Machine Learning Studio innehåller också åtkomst till ett kraftfullt machine learning-ramverk under namnet på [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW lekfull kategorisering här, eftersom det kan lära dig klassificerings- och regressionsmodeller problem och kan även lära sig från delvis omärkta data. Du kan konfigurera den att använda någon av flera olika algoritmer, förlust av funktioner och algoritmerna för prisoptimering. Det har utformats från grunden upp för att vara effektiv, parallella och mycket snabba. Den hanterar löjligt stort funktionsuppsättningar utan besvär tydligt.
Igång och leds av Microsoft Researchs egen John Langford, är VW en formel en post i ett fält av lager bil algoritmer. Inte alla problem som passar VW, men om den kan det vara värt din tag att klara inlärningskurvan i dess gränssnitt. Det är också tillgängliga som [fristående öppen källkod](https://github.com/JohnLangford/vowpal_wabbit) på flera språk.

## <a name="next-steps"></a>Nästa steg

* För att ladda ned en enkel att förstå informationsgrafiken översikt över grunderna i machine learning att lära dig om populära algoritmer som används för att besvara vanliga frågor för machine learning, [Machine learning-grunder med algoritmexempel](basics-infographic-with-algorithm-examples.md).

* En lista efter kategori av alla de tillgängliga maskininlärningsalgoritmer i Machine Learning Studio finns i [initiera modell](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) i Machine Learning Studio-algoritmen och modulen hjälpa.

* En fullständig alfabetisk lista över algoritmer och moduler i Machine Learning Studio finns i [A-Z-lista över Machine Learning Studio-moduler](/azure/machine-learning/studio-module-reference/a-z-module-list) i modulen hjälpen och Machine Learning Studio-algoritm.
