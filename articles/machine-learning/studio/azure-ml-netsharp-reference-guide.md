---
title: "NET # Neurala nätverk specifikation språk Guide - Azure Machine Learning | Microsoft Docs"
description: "Syntaxen för den Net # neurala nätverk specifikationsspråk, tillsammans med exempel på hur du skapar en anpassad neurala nätverket-modell med Net #"
services: machine-learning
documentationcenter: 
author: jeannt
manager: cgronlun
editor: 
ms.assetid: cfd1454b-47df-4745-b064-ce5f9b3be303
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/01/2018
ms.author: jeannt
ms.openlocfilehash: a166b45e7e482092006ddad276986b6f8b0f378c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2018
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guide till Net # neurala nätverket specifikationsspråk för Azure Machine Learning

NET # är ett språk som har utvecklats av Microsoft som används för att definiera neural nätverksarkitekturer. Använda Net # för att definiera strukturen på neurala nätverk gör det möjligt att definiera komplexa datastrukturer som djupa neurala nätverk eller faltningar av godtycklig dimensioner som är kända för att förbättra inlärning på data, till exempel bild, ljud och video.

Du kan använda en Net # arkitektur specifikation i dessa sammanhang:

+ Alla neurala nätverket moduler i Microsoft Azure Machine Learning Studio: [Multiclass Neural Network](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [Two-Class Neural Network](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), och [Neurala nätverk Regression](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neurala nätverket funktioner i MicrosoftML: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) och [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)för R-språket och [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) för Python.


Den här artikeln beskriver de grundläggande koncept och syntax som krävs för att utveckla ett anpassat neurala nätverk med hjälp av Net #: 

+ Neural nätverkskrav och hur du definierar de primära komponenterna
+ Syntax och nyckelord för Net #-specifikationsspråk
+ Exempel på anpassade neurala nätverk som skapats med hjälp av Net # 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="neural-network-basics"></a>Grunderna i neurala nätverket

En neurala nätverk som består av noder som är ordnade i lager, och viktat anslutningar (eller kanter) mellan noderna. Anslutningarna är riktat och varje anslutning har en Källnoden och en målnoden.  

Varje trainable lager (en dold eller ett lager för utdata) har en eller flera **anslutning paket**. En anslutning bunt består av ett lager för källa och en specifikation av anslutningarna från källan lagret. Alla anslutningar i ett visst paket delar samma källa lager och samma mållagret. I Net # anses en anslutning paket som hör till i bunten mållagret.

NET # stöder olika typer av paket, som kan du anpassa hur indata mappas till dolda lager och mappas till utdata.

Standard- eller standard paket är en **fullständig paket**, i som varje nod i käll-lagret är ansluten till varje nod i mållagret.

Dessutom stöder Net # följande fyra typer av avancerad anslutning paket:

+ **Filtrerade paket**. Användaren kan definiera ett predikat för layer Källnoden och lager målnoden. Noder är anslutna när predikatet är True.

+ **Convolutional paket**. Användaren kan definiera små neighborhoods av noder i käll-lagret. Varje nod i mållagret är ansluten till nätverket som en av noderna i käll-lagret.

+ **Poolning paket** och **svar normalisering paket**. Dessa liknar convolutional paket i att användaren definierar små neighborhoods av noder i käll-lagret. Skillnaden är att vikten av kanter i dessa paket inte är trainable. I stället används en fördefinierad funktion på noden källvärden att fastställa värdet för mål-nod.


## <a name="supported-customizations"></a>Anpassningar som stöds

Arkitektur för neurala nätverket modeller som du skapar i Azure Machine Learning kan stor utsträckning anpassas med hjälp av Net #. Du kan:

+ Skapa dolda lager och styr antalet noder i varje lager.
+ Ange hur lager ska vara anslutna till varandra.
+ Definiera särskilda anslutningar, till exempel faltningar och vikt delning paket.
+ Ange olika aktivering funktioner.

Mer information om syntaxen specifikation språk finns [struktur specifikationen](#Structure-specifications).  

Exempel på definierar neurala nätverk för några vanliga maskininlärning aktiviteter från simplex till komplexa, finns [exempel](#Examples-of-Net#-usage).

## <a name="general-requirements"></a>Allmänna krav

+ Det måste finnas exakt en utgående lager, minst ett indata lager och noll eller flera dolda lager. 
+ Varje lager har ett fast antal noder, begreppsmässigt ordnade i en rektangulär matris av godtycklig dimensioner. 
+ Inkommande lager saknar associerade utbildade parametrar som representerar den punkt där instansens data kommer in i nätverket. 
+ Trainable lager (dolda och utdata lager) har associerade utbildade parametrar, kallas även eventuella fördomar och vikt. 
+ Käll- och noder måste vara i separata lager. 
+ Anslutningar måste vara acykliska; med andra ord kan det finnas en kedja av anslutningar som leder tillbaka till den ursprungliga noden.
+ Lagret utdata kan inte vara ett källa lager för ett paket för anslutningen.  

## <a name="structure-specifications"></a>Specifikationer för struktur

En neurala nätverket struktur specifikation består av tre delar: den **konstantdeklaration**, **layer deklaration**, **anslutning deklaration**. Det finns även en valfri **dela deklaration** avsnitt. Avsnitten kan anges i valfri ordning.

## <a name="constant-declaration"></a>Konstantdeklaration

En konstantdeklaration är valfritt. Det ger dig möjlighet att definiera värden används någon annanstans i definitionen av neurala nätverket. Instruktionen deklaration består av en identifierare som följt av ett likhetstecken och ett värdeuttryck.

Till exempel följande sats definierar en konstant `x`:  

`Const X = 28;`

Om du vill definiera två eller flera konstanter samtidigt omges klammerparenteser identifierarnamn och värden och avgränsa dem med semikolon. Exempel:

`Const { X = 28; Y = 4; }`

Till höger i varje tilldelning uttrycket kan vara ett heltal, ett verkligt tal, ett booleskt värde (True eller False) eller ett matematiska uttryck. Exempel:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklarationen för lager

Layer-deklaration krävs. Den definierar storlek och källa för lager, inklusive dess anslutning paket och attribut. Instruktionen deklaration börjar med namnet på lagret (indata, dolda eller utdata) följt av dimensionerna lagret (en tuppel positiva heltal). Exempel:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;  
```

+ Produkten av dimensionerna är antalet noder i lagret. I det här exemplet finns det två dimensioner [5,20], vilket innebär att det finns 100 noder i lagret.
+ Lagren kan deklareras i vilken ordning som helst, med ett undantag: om fler än ett inkommande lager definieras den ordning de deklareras måste matcha ordningen på funktioner i indata.

Ange antalet noder i ett lager fastställas automatiskt den `auto` nyckelord. Den `auto` nyckelordet har olika effekter, beroende på lagret:

+ I en inkommande layer-deklaration är antalet noder antal funktioner i indata.
+ I en deklaration som dolda lagret antalet noder är det tal som anges av parametervärdet för **antalet dolda noder**.
+ I en layer deklaration utdata är antalet noder 2 för tvåklass klassificering, 1 för regression och som är lika med antalet noder som utdata för multiklass-baserad klassificering.

Följande nätverksdefinitionen kan till exempel storleken på alla skikt bestäms automatiskt:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;  
```

En layer-deklaration för en trainable lager (dolda eller utdata-lager) kan du också inkludera utdata funktionen (kallas även en funktion för aktivering), där standardinställningen är **sigmoid** för klassificering modeller och  **linjär** för regression modeller. Även om du använder standard ange du uttryckligen funktionen aktivering, om så önskas för tydlighetens skull.

Följande utdata-funktioner stöds:

+ sigmoid
+ linjär
+ softmax
+ rlinear
+ Ruta
+ sqrt
+ srlinear
+ ABS
+ TANH
+ brlinear

Till exempel följande deklaration använder den **softmax** funktionen:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklarationen för anslutning

Du måste deklarera anslutningar mellan lager som du har definierat omedelbart när du har definierat trainable lagret. Anslutningen paket deklarationen börjar med nyckelordet `from`, följt av namnet på det paket källa lager och vilken typ av anslutning paket för att skapa.

För närvarande stöds fem typer av paket för anslutningen:

+ **Fullständig** paket, som anges med nyckelordet `all`
+ **Filtrerade** paket som anges av nyckelordet `where`, följt av ett predikat uttryck
+ **Convolutional** paket som anges av nyckelordet `convolve`, följt av Faltning attribut
+ **Poolning** paket som anges av nyckelorden **max poolen** eller **innebär pool**
+ **Svaret normalisering** paket som anges av nyckelordet **svar normen**

## <a name="full-bundles"></a>Fullständig paket

En fullständig anslutning bunt innehåller en anslutning från varje nod i lagret för källan till varje nod i mållagret. Detta är typ av nätverksanslutning standard.

## <a name="filtered-bundles"></a>Filtrerade paket

En filtrerad anslutning paket specifikation innehåller ett predikat, uttryckt syntaktiskt, mycket som ett C# lambda-uttryck. I följande exempel definierar två filtrerade paket:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  
```

+ I predikatet för `ByRow`, `s` är en parameter som representerar ett index i rektangulär matrisen noder i det inkommande lagret `Pixels`, och `d` är en parameter som representerar ett index i matrisen noder i det dolda lagret `ByRow`. Typ av både `s` och `d` är en tuppel med heltal med längden två. Begreppsmässigt `s` sträcker sig över alla par med heltal med `0 <= s[0] < 10` och `0 <= s[1] < 20`, och `d` sträcker sig över alla par med heltal, med `0 <= d[0] < 10` och `0 <= d[1] < 12`. 

+ Det finns ett villkor på höger sida av predikatuttrycket. I det här exemplet för varje värde i `s` och `d` så att villkoret är sant, det finns en kant från lager Källnoden till målnoden för lager. Därför filteruttrycket anger att paketet innehåller en anslutning från den nod som definierats av `s` till definieras av noden `d` i samtliga fall där s [0] är lika med d [0].

Du kan också kan du ange en uppsättning vikterna för ett filtrerade paket. Värdet för den **vikterna** attributet måste vara en tuppel av flytande punktvärden med en längd som stämmer med antalet anslutningar som definieras av paketet. Som standard genereras slumpmässigt vikter.

Värde som är grupperade efter nodindex mål. Som, om den första Målnoden är ansluten till K källa noder första `K` element i den **vikter** tuppel är vikterna för första målnoden i indexet källordning. Detsamma gäller för de återstående noderna i målet.

Det är möjligt att ange vikterna direkt som konstanta värden. Om du har lärt dig vikterna tidigare kan du exempelvis ange dem som konstanter med följande syntax:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Convolutional paket

När av att träningsinformationen har en homogen struktur, används convolutional anslutningar ofta mer avancerade funktioner för data. Till exempel i bild, ljud- och data, spatial eller temporal dimensionalitet kan vara ganska enhetlig.  

Convolutional paket använder rektangulär **kärnor** som slid via dimensionerna. I princip varje kernel definierar en uppsättning vikten som används i lokala neighborhoods kallas **kernel-program**. Varje kernel-program som motsvarar en nod i lagret källa, som kallas den **centrala nod**. Vikten av en kernel delas av många anslutningar. I ett convolutional paket varje kernel är rektangulär och alla kernel-program har samma storlek.  

Convolutional paket har stöd för följande attribut:

**InputShape** definierar dimensionalitet skiktets källa för den här convolutional paket. Värdet måste vara en tuppel med positivt heltal. Produkten av heltalen måste vara lika med antalet noder i lagret källan, men i annat fall det behöver inte matchar dimensionaliteten har deklarerats för käll-lagret. Längden på den här tuppeln blir den **aritet** värde för convolutional paket. Vanligtvis avser aritet antalet argument eller operander som en funktion kan vidta.

Använda attribut för att definiera form och platserna för kärnor, **KernelShape**, **Stride**, **utfyllnad**, **LowerPad**, och  **UpperPad**:   

+ **KernelShape**: (obligatoriskt) anger dimensionaliteten för varje kernel för convolutional paket. Värdet måste vara en tuppel med positiva heltal med en längd som är lika med ariteten för paketet. Varje komponent i den här tuppeln får inte vara större än den motsvarande komponenten i **InputShape**. 

+ **STRIDE**: (valfritt) definierar glidande steg storlekarna på Faltning (ett Stegstorlek för varje dimension), som är avståndet mellan de centrala noderna. Värdet måste vara en tuppel med positiva heltal med en längd som är ariteten för paketet. Varje komponent i den här tuppeln får inte vara större än den motsvarande komponenten i **KernelShape**. Standardvärdet är en tuppel med alla komponenter som är lika med ett. 

+ **Dela**: (valfritt) definierar vikten för varje dimension i Faltning. Värdet kan vara ett booleskt värde eller en tuppel med booleska värden med en längd som är ariteten för paketet. Ett booleskt värde har utökats för att vara en tuppel rätt längd med alla komponenter som är lika med det angivna värdet. Standardvärdet är en tuppel som består av alla värden som True. 

+ **MapCount**: (valfritt) anger antalet funktionen mappar för convolutional paket. Värdet kan vara ett enda positivt heltal eller en tuppel med positiva heltal med en längd som är ariteten för paketet. Ett enda heltal har utökats för att vara en tuppel med de första komponenterna som är lika med det angivna värdet rätt längd och alla återstående komponenter som är lika med ett. Standardvärdet är en. Det totala antalet funktionen maps är produkten av komponenterna i tuppeln. Factoring detta totala antal över komponenterna som bestämmer hur funktionen mappa värden grupperas i mål-noder. 

+ **Vikterna**: (valfritt) definierar inledande vikterna för paketet. Värdet måste vara en tuppel av flytande punktvärden med en längd som är antalet kärnor gånger antalet vikter per kernel, enligt nedan. Standardvikterna genereras slumpmässigt.  

Det finns två uppsättningar med egenskaper som styr utfyllnad, egenskaper som inte anges samtidigt:

+ **Utfyllnad**: (valfritt) avgör om indata ska bli utfyllt med hjälp av en **utfyllnad standardschema**. Värdet kan vara ett booleskt värde eller så kan vara en tuppel med booleska värden med en längd som är ariteten för paketet. 

    Ett booleskt värde har utökats för att vara en tuppel rätt längd med alla komponenter som är lika med det angivna värdet. 
    
    Om värdet för en dimension är True, utfyllnad källan logiskt i dimensionen med cellerna noll-värden till ytterligare kernel-program så att centrala noderna i de första och sista kärnor i den aktuella dimensionen är de första och sista noderna i som dimensionen i käll-lagret. Därför antalet ”falska” noder i varje dimension bestäms automatiskt, så att den passar exakt `(InputShape[d] - 1) / Stride[d] + 1` kärnor i lagret Vadderat källa. 
    
    Om värdet för en dimension är False, definieras de kernlar som är så att antalet noder på varje sida som lämnas ut är samma (upp till en skillnad på 1). Standardvärdet för det här attributet är en tuppel med alla komponenter som är lika med False.

+ **UpperPad** och **LowerPad**: (valfritt) Ange större kontroll över utfyllnad ska användas. **Viktigt:** attributen kan vara definierade om och bara om den **utfyllnad** egenskapen ovan är ***inte*** definieras. Värdena måste vara heltal enkelvärdesattribut tupplar med längd som är ariteten för paketet. När dessa attribut har angetts läggs ”falska” noder till lägre och övre parterna för varje inkommande lagret. Antalet noder som lagts till i de nedre och övre ends i varje dimension bestäms av **LowerPad**[i] och **UpperPad**[i] respektive. 

    För att säkerställa att kärnor överensstämmer endast ”verkliga” noder och inte ”falska” noder, måste följande villkor uppfyllas:
      - Varje komponent i **LowerPad** måste vara absolut mindre än `KernelShape[d]/2`. 
      - Varje komponent i **UpperPad** får inte vara större än `KernelShape[d]/2`. 
      - Standardvärdet för dessa attribut är en tuppel med alla komponenter som är lika med 0. 

    Inställningen **utfyllnad** = true tillåter så mycket utfyllnad som behövs för att hålla ”mittpunkt” kernel inuti den ”verkligt” indata. Math lite för datoranvändning storlek ändras. I allmänhet storlek *D* beräknas som `D = (I - K) / S + 1`, där `I` är inkommande storlek `K` kernel-storlek `S` är stride, och `/` är Heltalsdivision (avrunda mot noll ). Om du ställer in UpperPad = [1, 1] inkommande storleken `I` är i praktiken 29, och därmed `D = (29 - 5) / 2 + 1 = 13`. Men när **utfyllnad** = true, i stort sett `I` hämtar ökar av `K - 1`; därför `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Genom att ange värden för **UpperPad** och **LowerPad** du får mycket mer kontroll över utfyllnaden än om du bara ange **utfyllnad** = true.

Mer information om convolutional nätverk och deras program finns i de här artiklarna: 

+ [http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
+ [http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
+ [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Poolning paket

En **poolning paket** gäller geometri liknar convolutional anslutning, men den använder fördefinierade funktioner till noden källvärden för att härleda mål nodvärde. Därför har anslutningspoolen paket inga trainable tillstånd (vikterna eller eventuella fördomar). Anslutningspooler paket stöder alla convolutional attribut utom **delning**, **MapCount**, och **vikterna**.

Normalt kärnor sammanfattning av intilliggande anslutningspoolen enheter inte överlappar varandra. Om Stride [d] är lika med KernelShape [d] i varje dimension är lagret fick traditionella lokala anslutningspoolen lager, som ofta används i convolutional neurala nätverk. Varje målnoden beräknar maximalt eller medelvärdet av aktiviteter för dess kernel i käll-lagret.  

I följande exempel visas ett anslutningspoolen paket: 

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }  
```

+ Ariteten för paketet är 3: som är längden på tuppeln `InputShape`, `KernelShape`, och `Stride`. 
+ Antalet noder i käll-lagret är `5 * 24 * 24 = 2880`. 
+ Detta är ett vanligt lokala anslutningspoolen lager eftersom **KernelShape** och **Stride** är lika. 
+ Antalet noder i mållagret är `5 * 12 * 12 = 1440`.

Mer information om anslutningspoolen lager finns dessa artiklar:  

+ [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Avsnittet 3.4)
+ [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
+ [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Svaret normalisering paket

**Svaret normalisering** är en lokal normalisering schema som introducerades av Geoffrey Hinton et al i dokumentet [ImageNet Classiﬁcation med djupa Neurala nätverk för Convolutional](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). 

Svaret normalisering används för att underlätta generalisering i neural nät. När en neuron startar på en aktivering av mycket hög nivå, förhindrar ett lager för lokala svar normalisering aktivering andelen omgivande neurons. Detta görs med hjälp av tre parametrar (`α`, `β`, och `k`) och en convolutional struktur (eller nätverket form). Varje neuron i mållagret **y** motsvarar en neuron **x** i käll-lagret. Aktivering andelen **y** ges genom följande formel, där `f` är den aktivering i en neuron och `Nx` är kärnan (eller som innehåller neurons i nätverket för **x**), enligt följande convolutional struktur:  

![formeln för convolutional struktur](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Svaret normalisering paket stöder alla convolutional attribut utom **delning**, **MapCount**, och **vikterna**.  

+ Om kernel innehåller neurons i samma karta som ***x***, normalisering schemat kallas **samma mappa normalisering**. Definiera samma karta normalisering första koordinaten i **InputShape** måste ha värdet 1.

+ Om kernel innehåller neurons i samma spatial position som ***x***, men neurons finns i andra mappar, normalisering schemat kallas **tvärs över mappar normalisering**. Den här typen av svar normalisering implementerar en form av laterala inhibition inspirerat av typ av hittades i verkliga neurons skapar konkurrens om stora aktivering nivåer bland neuron utdata beräknad på olika maps. Om du vill definiera över maps normalisering första koordinaten måste vara ett heltal större än ett och inte större än antalet maps och resten av koordinaterna måste ha värdet 1.

Eftersom svaret normalisering paket gäller en fördefinierad funktion källvärden nod att fastställa värdet för mål-nod, har de inget trainable tillstånd (vikt eller eventuella fördomar).

> [!NOTE]
> Noderna i mållagret motsvarar neurons som är centrala noder i kärnor. Till exempel om `KernelShape[d]` är ojämnt, sedan `KernelShape[d]/2` motsvarar den centrala kernel-noden. Om `KernelShape[d]` är jämnt och den centrala noden på `KernelShape[d]/2 - 1`. Därför om `Padding[d]` är False, först och senaste `KernelShape[d]/2` noder har inte motsvarande noder i mållagret. Om du vill undvika detta kan du definiera **utfyllnad** som [true, true,..., true].

Utöver de fyra attributen som beskrivs ovan, stöd svar normalisering paket också för följande attribut:

+ **Alpha**: (obligatoriskt) anger ett värde som motsvarar `α` i föregående formel. 
+ **Beta**: (obligatoriskt) anger ett värde som motsvarar `β` i föregående formel. 
+ **Förskjutning**: (valfritt) anger ett värde som motsvarar `k` i föregående formel. Standardvärdet 1.

I följande exempel definierar ett svar normalisering paket med hjälp av dessa attribut:  

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }  
```

+ Käll-lagret innehåller fem kartor, med aof dimension 12 x 12 summering 1440 noder. 
+ Värdet för **KernelShape** anger att detta är en samma normalisering kartskiktet, där nätverket är en rektangel 3 x 3. 
+ Standardvärdet för **utfyllnad** är False, vilket mållagret har bara 10 noder i varje dimension. Om du vill inkludera en nod i mållagret som motsvarar varje nod i käll-lagret, lägger du till utfyllnad = [true, true, true]; och ändra storlek på RN1 [5, 12, 12].  

## <a name="share-declaration"></a>Dela förklaring

NET # stöder definiera flera paket med delade vikter. Vikten av alla två paket kan delas om deras strukturer är identiska. Följande syntax definierar paket med delade vikter:  

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

Till exempel anger följande resurs-deklaration lagernamn, som anger att både vikterna och eventuella fördomar ska delas:  

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases  
```

+ Funktionerna för inkommande delas upp i två lika storlek inkommande lager. 
+ Dolda lager för att beräkna högre nivå funktioner på två inkommande lager. 
+ Resurs-deklarationen anger att *H1* och *H2* måste beräknas på samma sätt från deras respektive indata.  

Du kan också anges detta med två olika resurs-deklarationer enligt följande:  

```Net#
share { Data1 => H1, Data2 => H2 } // share weights  
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases  
```

Du kan använda kort form endast när lagren innehåller ett paket. I allmänhet är delning möjligt endast när den aktuella strukturen är identiska, vilket innebär att de har samma storlek, samma convolutional geometri och så vidare.  

## <a name="examples-of-net-usage"></a>Exempel på användning av Net #

Det här avsnittet innehåller några exempel på hur du kan använda Net # definiera hur dolda lager interagera med andra lager, och skapa convolutional nätverk om du vill lägga till dolda lager.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definiera en enkel anpassade neurala nätverket: ”Hello World”-exempel

Det här enkla exemplet visar hur du skapar en neurala nätverket modell som har ett dolt lager.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;  
```

I exemplet vissa grundläggande kommandon på följande sätt:  

+ Den första raden definierar inkommande lagret (med namnet `Data`). När du använder den `auto` nyckelord, det neurala nätverket inkluderar automatiskt alla kolumner i funktionen i inkommande exemplen. 
+ Den andra raden skapar det dolda lagret. Namnet `H` tilldelas det dolda lagret som har 200 noder. Det här lagret är fullständigt ansluten till inkommande lagret.
+ Den tredje raden definierar utdata lagret (med namnet `O`), som innehåller 10 utdata-noder. Om det neurala nätverket används för klassificering, är det en utdata nod per klass. Nyckelordet **sigmoid** anger att funktionen utdata ska användas i output-lagret.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definiera flera dolda lager: datorn vision exempel

Exemplet nedan visar hur du definierar ett lite mer komplext neurala nätverk med flera anpassade dolda lager.  

```Net#
// Define the input layers 
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100] 
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]  
{
from Gather all;
from MetaData all;
}  
```

Det här exemplet visar flera funktioner från språket som specifikationen neurala nätverk:

+ Strukturen har två inkommande lager `Pixels` och `MetaData`.
+ Den `Pixels` layer är ett skikt som källa för två anslutning paket, med målet lager `ByRow` och `ByCol`.
+ Lagren `Gather` och `Result` är målet lager i flera paket för anslutningen.
+ Utdata-lagret `Result`, är en mållagret i två anslutning paket; en med andra nivån dolda lagret `Gather` som en mållagret och andra inkommande nivå `MetaData` som ett mål-lager.
+ Dolda lager `ByRow` och `ByCol`, ange filtrerad anslutning med hjälp av predikatuttryck. Mer exakt noden i `ByRow` på [x, y] är ansluten till noderna i `Pixels` att har det första indexet samordna lika med noden har första samordna x. På liknande sätt noden i `ByCol` på [x, y] är ansluten till noderna i `Pixels` som har andra indexet samordna inom någon av noden har andra samordna y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Ange ett convolutional nätverk för multiklass-baserad klassificering: siffra recognition exempel

Definitionen av följande nätverk är utformad för att identifiera siffror och det visar att vissa avancerade tekniker för att anpassa en neurala nätverket.  

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve 
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve 
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;  
```

+ Strukturen har ett enda inkommande lager `Image`.
+ Nyckelordet `convolve` betyder att lagren heter `Conv1` och `Conv2` är convolutional lager. Var och en av dessa lager-deklarationer följs av en lista över Faltning-attribut.
+ Net har en tredje dolda lagret, `Hid3`, som är anslutet till det andra dolda lagret `Conv2`.
+ Utdata-lagret `Digit`, ansluts bara till det tredje dolda lagret `Hid3`. Nyckelordet `all` anger att utdata lagret fullständigt är ansluten till `Hid3`.
+ Ariteten för Faltning är tre: längden på tuppeln `InputShape`, `KernelShape`, `Stride, and `delning ”. 
+ Antalet vikter per kernel är `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. Eller `26 * 50 = 1300`.
+ Du kan beräkna noder i varje dolda lagret på följande sätt:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5` `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Det totala antalet noder kan beräknas med hjälp av deklarerade dimensionaliteten för lagret [50, 5, 5], enligt följande: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Eftersom `Sharing[d]` är False endast för `d == 0`, antalet kärnor är `MapCount * NodeCount\[0] = 10 * 5 = 50`. 

## <a name="acknowledgements"></a>Bekräftelser

Net #-språk för att anpassa arkitekturen för neurala nätverk har utvecklats på Microsoft av Shon Katzenberger (systemarkitekt, Machine Learning) och Alexey Kamenev (programvara tekniker, Microsoft Research). Det används internt för maskininlärning projekt och program, från identifiering av avbildningen till text analytics. Mer information finns i [Neural nät i Azure ML - introduktion till Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
