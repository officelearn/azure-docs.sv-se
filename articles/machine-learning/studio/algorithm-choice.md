---
title: Välja algoritmer
titleSuffix: ML Studio (classic) - Azure
description: Hur du väljer Azure Machine Learning Studio (klassiska) algoritmer för övervakad och oövervakad inlärning i klustring, klassificering eller Regressions experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: c69b4c15397dc13f36a707f932c2464a4ff94ca7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838471"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio-classic"></a>Så här väljer du algoritmer för Azure Machine Learning Studio (klassisk)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Svaret på frågan "vilken Machine Learning-algoritm ska jag använda?" är alltid "det är beroende av". Det beror på datans storlek, kvalitet och beskaffenhet. Det beror på vad du vill göra med svaret. Det beror på hur matematik algoritmen har översatts till instruktioner för den dator som du använder. Och det beror på hur lång tid du har. Även de mest erfarna data experterna kan inte avgöra vilken algoritm som fungerar bäst innan du provar dem.

Machine Learning Studio (klassisk) tillhandahåller avancerade algoritmer, till exempel skalbara, utökat besluts träd, Bayesian rekommendations system, djup neurala nätverk och beslut Beslutsdjungler som utvecklats på Microsoft Research. Skalbara maskininlärningspaket med öppen källkod, som Vowpal Wabbit, ingår också. Den klassiska versionen av Machine Learning Studio stöder Machine Learning-algoritmer för multiklass och binär klassificering, regression och klustring. Se den fullständiga listan med [Machine Learning moduler](/azure/machine-learning/studio-module-reference/index).
Dokumentationen innehåller viss information om varje algoritm och hur du kan finjustera parametrarna för att optimera algoritmen för din användning.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Lathund-bladet Machine Learning algorithm

I **[lathund-bladet Microsoft Azure Machine Learning algorithm](../algorithm-cheat-sheet.md)** kan du välja rätt Machine Learning-algoritm för dina förutsägelse analys lösningar från Azure Machine Learning bibliotek med algoritmer.
Den här artikeln beskriver hur du använder det här lathund-bladet.

> [!NOTE]
> Om du vill hämta lathund-bladet och följa anvisningarna i den här artikeln går du till [lathund-bladet Machine Learning-algoritm](../algorithm-cheat-sheet.md).
> 
> 

Dessa rekommendationer är kompilerade feedback och tips från många data experter och maskin inlärnings experter. Vi har inte samtyckt till allt, men vi har försökt att harmonisera våra åsikter till en grov enighet. De flesta av satserna i disöverenskommelsen börjar med "det är beroende..."


> [!TIP]
> För att ladda ned en lättanvänd informations grafiken-översikt över grunderna i Machine Learning och lär dig mer om populära algoritmer som används för att besvara vanliga Machine Learning-frågor, se [grunderna för maskin inlärning med algoritm exempel](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Varianter av Machine Learning

### <a name="supervised"></a>Övervakat

Övervakade Learning-algoritmer gör förutsägelser baserade på en uppsättning exempel. Historiska aktie kurser kan till exempel användas för att gissa sig till framtida priser. Varje exempel som används för utbildning är märkt med värdet av intresse, i det här fallet aktie kursen. En övervakad Learning-algoritm söker efter mönster i dessa värde etiketter. Den kan använda all information som kan vara relevant – dag i veckan, säsongs perioden, företagets finansiella data, typ av bransch, förekomsten av störande företagsspecifika politiska händelser, och varje algoritm söker efter olika typer av mönster. När algoritmen har hittat det bästa mönstret kan den använda det mönstret för att göra förutsägelser för omärkta test data – morgon priset.

Övervakad inlärning är en populär och användbar typ av maskin inlärning. Med ett undantag är alla moduler i den klassiska versionen av Azure Machine Learning Studio övervakade Learning-algoritmer. Det finns flera olika typer av övervakade utbildningar som representeras i Azure Machine Learning Studio (klassisk): klassificering, regression och avvikelse identifiering.

* **Klassificering**. När data används för att förutsäga en kategori kallas övervakad inlärning också klassificering. Detta är fallet när du tilldelar en bild som en bild av antingen en "katt" eller "hund". När det bara finns två alternativ kallas det **två klasser** eller **binomialfördelningen**. När det finns fler kategorier, som när du förutsäger vinnare av NCAA mars Madness turneringen, kallas det här problemet för klassificering av **flera klasser**.
* **Regression**. När ett värde förutsägs, precis som med aktie kurser, kallas övervakad inlärning regression.
* **Avvikelse identifiering**. Ibland är målet att identifiera data punkter som bara är ovanliga. På grund av bedrägerier är ett ovanligt stort kredit kort utgifts mönster misstänkt. De möjliga variationerna är så många och inlärnings exemplen så få, att det inte är möjligt att lära sig vilken bedräglig aktivitet som ser ut. Den metod som används för avvikelse identifiering är att helt enkelt lära dig om vilken normal aktivitet som ser ut (med en historik över icke-bedrägliga transaktioner) och identifiera allt som är mycket annorlunda.

### <a name="unsupervised"></a>Oövervakade

I en obevakad inlärning har data punkter inga etiketter kopplade till sig. Målet för en ej övervakad utbildningskurs är i stället att organisera data på ett visst sätt eller för att beskriva dess struktur. Detta kan betyda att gruppera dem i kluster eller hitta olika sätt att titta på komplexa data så att de visas enklare eller mer strukturerad.

### <a name="reinforcement-learning"></a>Kunskapsförmedling

I förstärknings inlärningen hämtar algoritmen för att välja en åtgärd som svar på varje data punkt. Learning-algoritmen får också en belönings signal en kort tid senare, vilket anger hur väl beslutet var.
Utifrån detta ändrar algoritmen strategin för att uppnå den högsta belöningen. Det finns för närvarande inga moduler för förstärkning av inlärnings algoritm i Azure Machine Learning Studio (klassisk). Förstärknings inlärning är vanligt i Robotics, där uppsättningen sensor läsningar vid en tidpunkt är en data punkt och algoritmen måste välja nästa åtgärd för roboten. Det är också en naturlig anpassning för Sakernas Internet program.

## <a name="considerations-when-choosing-an-algorithm"></a>Att tänka på när du väljer en algoritm

### <a name="accuracy"></a>Beräkning

Det är inte alltid nödvändigt att få det mest exakta svaret.
Ibland är en uppskattning tillräckligt, beroende på vad du vill använda det för. Om så är fallet kanske du kan minska bearbetnings tiden dramatiskt genom att hålla mer ungefärliga metoder. En annan fördel med mer ungefärliga metoder är att de är naturligt att undvika överanpassning.

### <a name="training-time"></a>Utbildnings tid

Antalet minuter eller timmar som krävs för att träna en modell är ett stort antal olika algoritmer. Inlärnings tiden är ofta nära knuten till noggrannhet – ett som vanligt vis medföljer det andra. Dessutom är vissa algoritmer mer känsliga för antalet data punkter än andra.
När tiden är begränsad kan det driva valet av algoritm, särskilt när data uppsättningen är stor.

### <a name="linearity"></a>Linearitet

Många Machine Learning-algoritmer använder linjäritet. Algoritmer för linjär klassificering förutsätter att klasser kan avgränsas med en rak linje (eller dess högre dimensionella analoga). Detta inkluderar logistik regression och stöd för Vector-datorer (som implementeras i Azure Machine Learning Studio (klassisk)).
Algoritmer för linjär regression förutsätter att data trender följer en rak linje. Dessa antaganden är inte dåliga för vissa problem, men för andra är de bättre.

![Icke-linjär klass gränser](./media/algorithm-choice/image1.png)

***Icke-linjär klass gränser*** *-förlitande på en algoritm för linjär klassificering skulle resultera i låg exakthet*

![Data med en linjär trend](./media/algorithm-choice/image2.png)

***Data med en linjär trend, som*** *använder en linjär Regressions metod, genererar mycket större fel än vad som behövs*

Trots deras faror är linjära algoritmer mycket populära som en första rad angrepp. De brukar vara algorithmically enkla och snabba att träna.

### <a name="number-of-parameters"></a>Antal parametrar

Parametrar är de rattar som en data expert kommer att sätta igång när en algoritm konfigureras. De är tal som påverkar algoritmens beteende, t. ex. fel tolerans eller antal iterationer eller alternativ mellan varianter av hur algoritmen fungerar. Algoritmens inlärnings tid och exakthet kan ibland vara ganska känsliga för att bara få rätt inställningar. Vanligt vis kräver algoritmer med ett stort antal parametrar den vanligaste utvärderings versionen och fel för att hitta en lämplig kombination.

Det kan också finnas en [parameter rensning](algorithm-parameters-optimize.md) av module-block i den klassiska versionen av Azure Machine Learning Studio som automatiskt försöker med alla parameter kombinationer vid vilken kornig het du väljer. Även om det är ett bra sätt att se till att du har använt parameter utrymmet, ökar den tid som krävs för att träna en modell exponentiellt med antalet parametrar.

Det finns många parametrar som vanligt vis indikerar att en algoritm har större flexibilitet. Det kan ofta uppnå mycket bra precision, förutsatt att du kan hitta rätt kombination av parameter inställningar.

### <a name="number-of-features"></a>Antal funktioner

För vissa typer av data kan antalet funktioner vara mycket stort jämfört med antalet data punkter. Detta är ofta fallet med genetiska data eller text data. Det stora antalet funktioner kan bli rörigt ned vissa Learning-algoritmer, vilket gör utbildnings tiden unfeasibly lång. Support vektor datorer är särskilt lämpade för det här fallet (se nedan).

### <a name="special-cases"></a>Särskilda fall

Vissa Learning-algoritmer gör särskilda antaganden om data strukturen eller önskade resultat. Om du kan hitta en som passar dina behov kan du ge dig mer användbara resultat, mer exakta förutsägelser eller snabbare inlärnings tider.

| **Algoritm** | **Beräkning** | **Utbildnings tid** | **Linearitet** | **Parametrar** | **Anteckningar** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klassificering i två klasser** | | | | | |
| [Logistisk regression](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [besluts skog](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [beslut Jungle](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Ont om minne |
| [utökat besluts träd](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Stor minnes storlek |
| [neurala-nätverk](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Ytterligare anpassning är möjlig](azure-ml-netsharp-reference-guide.md) |
| [genomsnittlig Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [Support Vector Machine](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Lämpligt för stora funktions uppsättningar |
| [lokal support Vector Machine](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Lämpligt för stora funktions uppsättningar |
| [Bayes ' PT Machine](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Klassificering i flera klasser** | | | | | |
| [Logistisk regression](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [besluts skog](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [beslut Jungle](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Ont om minne |
| [neurala-nätverk](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Ytterligare anpassning är möjlig](azure-ml-netsharp-reference-guide.md) |
| [en-v – alla](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Se egenskaperna för den två klass metoden som valts |
| **Regression** | | | | | |
| [Line](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Linjär Bayesian](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [besluts skog](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [utökat besluts träd](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Stor minnes storlek |
| [snabb skogs quantile](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Distributioner snarare än Poäng förutsägelser |
| [neurala-nätverk](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Ytterligare anpassning är möjlig](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Tekniskt logg-linjärt. För förutsägelse antal |
| [numret](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |För förutsägelse av rang-order |
| **Avvikelseidentifiering** | | | | | |
| [Support Vector Machine](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Särskilt bra för stora funktions uppsättningar |
| [PCA-baserad avvikelse identifiering](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-betyder](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |En klustrad algoritm |

**Egenskaper för algoritm:**

**●** – visar utmärkt precision, snabba inlärnings tider och användning av linearitet

**○** – visar bästa precision och måttliga utbildnings tider

## <a name="algorithm-notes"></a>Algoritm anteckningar

### <a name="linear-regression"></a>Linjär regression

Som tidigare nämnts passar den [linjära regressionen](/azure/machine-learning/studio-module-reference/linear-regression) en linje (eller ett plan eller en plan) till data uppsättningen. Det är en workhorse, enkel och snabb, men det kan vara förenkladt för vissa problem.

![Data med en linjär trend](./media/algorithm-choice/image3.png)

***Data med en linjär trend***

### <a name="logistic-regression"></a>Logistisk regression

Även om det innehåller "regression" i namnet är Logistisk regression i själva verket ett kraftfullt verktyg för klassificering med [två klasser](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) och [multiklasser](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) . Det går snabbt och enkelt. Det faktum att den använder en "-formad kurva" i stället för en rak linje gör att den passar för att dela upp data i grupper. Logistisk regression ger linjära klass gränser, så när du använder den bör du se till att en linjär uppskattning är något som du kan leva med.

![Logistisk regression till data i två klasser med bara en funktion](./media/algorithm-choice/image4.png)

***En logistik regression till data i två klasser med bara en funktion*** *– klass gränsen är den punkt där logistik kurvan är precis så nära båda klasserna*

### <a name="trees-forests-and-jungles"></a>Träd, skogar och beslutsdjungler

Besluts skogar ([regression](/azure/machine-learning/studio-module-reference/decision-forest-regression), [två klasser](/azure/machine-learning/studio-module-reference/two-class-decision-forest)och [multiklasser](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), beslut beslutsdjungler ([två klasser](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) och [multiklasser](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)) och utökat besluts träd ([regression](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) och [två klasser](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) är baserade på beslut träd, ett grundläggande begrepp för Machine Learning. Det finns många varianter av besluts träd, men alla gör samma sak – dela upp funktions utrymmet i flera regioner med samma etikett. Dessa kan vara regioner med konsekvent kategori eller konstant värde, beroende på om du gör klassificering eller regression.

![Besluts träd subdividerar ett funktions område](./media/algorithm-choice/image5.png)

***Ett besluts träd delar upp ett funktions utrymme i regioner med ungefär samma värden***

Eftersom ett funktions utrymme kan delas upp i godtyckligt små regioner är det enkelt att tänka på att dela upp det tillräckligt för att ha en data punkt per region. Detta är ett extrema exempel på överanpassning. För att undvika detta, konstrueras en stor uppsättning träd med särskilda matematiska vård åtgärder för att se till att träden inte korreleras. Genomsnittet av denna "besluts skog" är ett träd som undviker överanpassning. Besluts skogar kan använda mycket minne. Beslut beslutsdjungler är en variant som förbrukar mindre minne vid kostnad av en något längre utbildnings tid.

Utökat besluts träd undviker överanpassning genom att begränsa hur många gånger de kan dela upp och hur många data punkter som tillåts i varje region. Algoritmen skapar en sekvens av träd, som var och en lär sig att kompensera för felet som lämnats av trädet före. Resultatet är en mycket exakt elev som tenderar att använda mycket minne. En fullständig teknisk beskrivning finns i [Friedman original Paper](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Snabb skogs quantile regression](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) är en variant av besluts träd för det särskilda fallet där du vill veta att inte bara det typiska (median) värdet för data i en region, utan även dess distribution i form av quantiles.

### <a name="neural-networks-and-perceptrons"></a>Neurala nätverk och perceptrons

Neurala-nätverk är hjärna-inspirerade Learning-algoritmer som täcker [multiklass](/azure/machine-learning/studio-module-reference/multiclass-neural-network)-, [två-](/azure/machine-learning/studio-module-reference/two-class-neural-network)och [Regressions](/azure/machine-learning/studio-module-reference/neural-network-regression) problem. De kommer in i en oändlig sort, men neurala-nätverken i den klassiska versionen av Azure Machine Learning Studio är alla former av riktade och acykliska grafer. Det innebär att inmatnings funktioner överförs framåt (aldrig bakåt) genom en sekvens av lager innan de aktive ras i utdata. I varje lager viktas indata i olika kombinationer, summerade och skickas vidare till nästa lager. Den här kombinationen av enkla beräkningar ger möjlighet att lära sig sofistikerade klass gränser och data trender. Många lager nätverk i den här sorteringen utför "djupgående inlärning" som bränslen så mycket teknisk rapportering och vetenskaps medveten.

Den här höga prestandan är inte kostnads fri, men. Neurala-nätverk kan ta lång tid att träna, särskilt för stora data uppsättningar med massor av funktioner. De har också fler parametrar än de flesta algoritmer, vilket innebär att parameter rensningen utökar inlärnings tiden till ett bra erbjudande.
Och för de överutnyttjare som vill [ange sin egen nätverks struktur](azure-ml-netsharp-reference-guide.md)är möjligheterna inexhaustible.

![Gränser som har lärts av neurala-nätverk](./media/algorithm-choice/image6.png)

***De gränser som har lärts av neurala-nätverk kan vara komplexa och oregelbundna***

Den [genomsnittliga Perceptron i två klasser](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) är neurala Networks svar på skyrocketing inlärnings tider. Den använder en nätverks struktur som ger linjära klass gränser. Den är nästan primitiv med dagens standarder, men har en lång historik som arbetar robust och är tillräckligt liten för att kunna lära sig snabbt.

### <a name="svms"></a>SVMs

Support Vector Machines (SVMs) hittar den gräns som avgränsar klasser med så stor marginal som möjligt. När de två klasserna inte är tydligt åtskilda, hittar algoritmerna den mest gräns de kan. Som skrivet i Azure Machine Learning Studio (klassisk) gör [SVM i två klasser](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) detta med enbart en rak linje (i SVM-Speak används en linjär kernel).
Eftersom det gör den här linjära uppskattningen kan den köras ganska snabbt. Var det verkligen är med funktions intensiva data, t. ex. text-eller genomiks data. I dessa fall kan SVMs separera klasser snabbare och med mindre överförbrukning än de flesta andra algoritmer, förutom att bara kräva en liten mängd minne.

![Stöd för Vectors dator klass gränser](./media/algorithm-choice/image7.png)

***En typisk support Vectors dator klass gräns maximerar marginalen mellan två klasser***

En annan produkt av Microsoft Research är den [SVM i två klasser](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) som är en icke-linjär variant av SVM som behåller det mesta av hastigheten och minnes effektiviteten för den linjära versionen. Det är idealiskt för fall där den linjära metoden inte ger korrekta tillräckligt många svar. Utvecklarna förvarar det snabbt genom att bryta ned problemet till ett antal små linjära SVM-problem. Läs den [fullständiga beskrivningen](http://proceedings.mlr.press/v28/jose13.html) för information om hur de avvisade det här sticket.

Med hjälp av ett smarta-tillägg av dislines SVMs, ritar en [SVM](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) en gräns som tätt visar hela data uppsättningen. Det är användbart för avvikelse identifiering. Alla nya data punkter som ligger utanför den gränserna är ovanliga nog för att uppmärksammas.

### <a name="bayesian-methods"></a>Bayesian-metoder

Bayesian-metoder har hög önskvärd kvalitet: de undviker överanpassning. De gör detta genom att göra vissa antaganden i förväg om den sannolika fördelningen av svaret. En annan byproduct av den här metoden är att de har väldigt få parametrar. Den klassiska versionen av Azure Machine Learning Studio har Bayesian-algoritmer för båda klassificeringarna ([Bayes punkt maskin med två klasser](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) och regression ([Bayesian linjär regression](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Observera att dessa förutsätter att data kan delas upp eller anpassas med en rak linje.

På en historisk anteckning har Bayes-datorer utvecklats på Microsoft Research. De har vissa undantags bara teoretiskt teoretiskt arbete bakom dem. Den intresserade studenten dirigeras till den [ursprungliga artikeln i JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) och en [inblickad blogg av Chris Svensson](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Specialiserade algoritmer
Om du har ett särskilt mål kan du vara i tur. I den Azure Machine Learning Studio (klassiska) samlingen finns algoritmer som specialiserar sig på:

- rang förutsägelse ([ordnings regression](/azure/machine-learning/studio-module-reference/ordinal-regression))
- räkna förutsägelse ([Poisson regression](/azure/machine-learning/studio-module-reference/poisson-regression))
- avvikelse identifiering (en baserad på [huvud komponenter analys](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) och en baserad på [support Vector-datorer](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- klustring ([K-medel](/azure/machine-learning/studio-module-reference/k-means-clustering))

![PCA-baserad avvikelse identifiering](./media/algorithm-choice/image8.png)

***PCA-baserad avvikelse identifiering*** *– den stora delen av data hamnar i en Stereotypical-distribution. punkter som deviating dramatiskt från fördelningen är misstänkta*

![Data uppsättning grupperad med K--medel](./media/algorithm-choice/image9.png)

***En data uppsättning är grupperad i fem kluster med hjälp av K-:***

Det finns också en ensemble [1 – v – alla klassificerare med flera klasser](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), vilket bryter mot klassificerings problemet i n-1 2-klass. Egenskaperna exakthet, Training Time och linearitet bestäms av de dubbelriktade klassificeraren som används.

![Klassificerare i två klasser tillsammans för att bilda en klassificerare med tre klasser](./media/algorithm-choice/image10.png)

***Ett par med klassificerare i två klasser kombinerar för att bilda en klassificerare med tre klasser***

Den klassiska versionen av Azure Machine Learning Studio omfattar även åtkomst till ett kraftfullt ramverk för maskin inlärning under rubriken för [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW Defies kategorisering här, eftersom den kan lära sig både klassificerings-och Regressions problem och kan till och med lära sig från delvis omärkta data. Du kan konfigurera den att använda något av flera olika inlärnings algoritmer, förlust funktioner och optimerings algoritmer. Den utformades från grunden för att vara effektiv, parallell och mycket snabb. Den hanterar löjligt stora funktions uppsättningar med lite uppenbar ansträngning.
Igång och ledde till Microsofts forskning för John Langford, VW är en formel en post i ett fält med algoritmer för bilar. Det är inte alla problem som passar för VW, men om du gör det kan det vara värt att använda inlärnings kurvan på sitt gränssnitt. Den är också tillgänglig som [fristående öppen källkod](https://github.com/JohnLangford/vowpal_wabbit) på flera olika språk.

## <a name="next-steps"></a>Nästa steg

* För att ladda ned en lättanvänd informations grafiken-översikt över grunderna i Machine Learning och lär dig mer om populära algoritmer som används för att besvara vanliga Machine Learning-frågor, se [grunderna för maskin inlärning med algoritm exempel](basics-infographic-with-algorithm-examples.md).

* En lista efter kategori för alla Machine Learning-algoritmer som är tillgängliga i Machine Learning Studio (klassisk) finns i [initiera modellen](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) i Machine Learning Studio (klassisk) algoritm och modul-hjälp.

* En fullständig alfabetisk lista över algoritmer och moduler i den klassiska versionen av Machine Learning Studio finns i [en-ö-lista över Machine Learning Studio (klassiska) moduler](/azure/machine-learning/studio-module-reference/a-z-module-list) i Machine Learning Studio (klassisk) algoritm och modul-hjälp.
