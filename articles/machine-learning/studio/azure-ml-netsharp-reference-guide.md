---
title: 'NET # Neural Networks specifikationen språk guiden – Azure Machine Learning | Microsoft Docs'
description: 'Syntaxen för den Net # neural networks-språket, tillsammans med exempel på hur du skapar en modell för anpassade neurala nätverk med hjälp av Net #'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: cfd1454b-47df-4745-b064-ce5f9b3be303
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/01/2018
ms.openlocfilehash: dcc6d8c1ab5a5988a9d2cb33e038f364438724fa
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822984"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guide till Net # språket för neurala nätverk för Azure Machine Learning

NET # är ett språk som utvecklats av Microsoft och som används för att definiera nätverksarkitekturer för neurala. Med Net # för att definiera strukturen för ett neural network gör det möjligt att definiera komplexa strukturer som djupa neurala nätverk eller faltningar av godtycklig dimensioner, som är kända för att förbättra learning på data, till exempel bild, ljud eller video.

Du kan använda en Net # arkitektur specifikation dessa när det gäller:

+ Alla neurala nätverksmoduler i Microsoft Azure Machine Learning Studio: [Multiclass Neural Network](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [Tvåklassförhöjt Neural Network](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), och [Neurala nätverk Regression](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neuralt nätverksfunktioner i MicrosoftML: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) och [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)för R-språket och [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) för Python.


Den här artikeln beskriver grundläggande begrepp och syntax som krävs för att utveckla ett anpassat neurala nätverk med hjälp av Net #: 

+ Nätverkskrav för neurala och definiera de primära komponenterna
+ Syntaxen och nyckelorden i Net #-specifikationsspråk
+ Exempel på anpassade neurala nätverk som skapats med hjälp av Net # 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="neural-network-basics"></a>Grunderna i neuralt nätverk

Ett neuralt nätverk som består av noder som är ordnade i lager, och viktad anslutningar (eller kanter) mellan noderna. Anslutningarna är riktad och varje anslutning har en Källnoden och en målnoden.  

Varje trainable lager (en dold eller ett lager för utdata) har ett eller flera **anslutning paket**. Ett paket med anslutning består av ett lager för källa och en specifikation av anslutningarna från en källa skiktet. Alla anslutningar i ett visst paket delar samma lager för källa och samma mållagret. I Net # är ett paket med anslutning anses tillhöra i bunten mållagret.

NET # stöder olika typer av paket, som kan du anpassa sätt indata är mappad till dolda lager och mappas till utdata.

Standard- eller standard paket är en **fullständig paket**, i som varje nod i käll-lagret är ansluten till varje nod i mållagret.

Dessutom stöder Net # följande fyra typer av avancerade anslutning paket:

+ **Filtrerade paket**. Du kan definiera ett predikat med hjälp av platserna för layer Källnoden och layer målnoden. Noderna ansluts när predikatet är sant.

+ **Convolutional paket**. Du kan definiera små neighborhoods noder i käll-lagret. Varje nod i mållagret är ansluten till en stadsdel av noder i käll-lagret.

+ **Poolning paket** och **svar normalisering paket**. Dessa liknar convolutional paket i den användaren definierar små neighborhoods noder i käll-lagret. Skillnaden är att vikterna kanter i dessa paket inte är trainable. I stället används en fördefinierad funktion på noden källvärdena mål noden värdet.


## <a name="supported-customizations"></a>Anpassningar som stöds

Arkitekturen i modeller av neurala nätverk som du skapar i Azure Machine Learning kan anpassas med hjälp av Net # stor utsträckning. Du kan:

+ Skapa dolda lager och kontrollera antalet noder i varje lager.
+ Ange hur lager ska anslutas till varandra.
+ Definiera särskilda anslutningar, till exempel faltningar och vikt delning paket.
+ Ange olika aktivering funktioner.

Information om syntaxen för specifikation finns [struktur specifikationen](#Structure-specifications).  

Exempel på Definiera neurala nätverk för vissa vanliga machine learning-uppgifter från simplex till fler, komplexa finns [exempel](#Examples-of-Net#-usage).

## <a name="general-requirements"></a>Allmänna krav

+ Det måste finnas exakt en utdata-lager, minst ett indata lager och noll eller flera dolda lager. 
+ Varje lager har ett fast antal noder, begreppsmässigt ordnade i en rektangulär matris med godtyckliga dimensioner. 
+ Indata-lager har inga associerade tränade parametrar och representerar den punkt där instansens data kommer in i nätverket. 
+ Trainable lager (dolda och utdata-lager) ha associerade tränade parametrar, kallat vikterna och eventuella fördomar. 
+ Käll- och målnoder måste finnas i separata lager. 
+ Anslutningar måste vara acykliska; Det får inte med andra ord vara en kedja av anslutningar som leder tillbaka till den ursprungliga noden.
+ Utdata-lagret får inte vara ett käll-lager för ett paket för anslutningen.  

## <a name="structure-specifications"></a>Strukturen specifikationer

En neuralt nätverk struktur specifikation består av tre avsnitt: den **konstant deklarationen**, **layer deklarationen**, **anslutning deklarationen**. Det finns också en valfri **dela deklarationen** avsnittet. Avsnitten kan anges i valfri ordning.

## <a name="constant-declaration"></a>Konstant deklaration

En konstant deklaration är valfritt. Det ger dig möjlighet att definiera värden används någon annanstans i definitionen av neurala nätverket. Instruktionen deklarationen består av en identifierare som följt av ett likhetstecken och ett uttryck.

Till exempel följande instruktion definierar en konstant `x`:  

`Const X = 28;`

Om du vill definiera två eller flera konstanter samtidigt identifierarnamn och värden omges av klammerparenteser, och avgränsade med semikolon. Exempel:

`Const { X = 28; Y = 4; }`

Till höger i varje tilldelning uttryck kan vara ett heltal, ett reellt tal, ett booleskt värde (True eller False) eller ett matematiska uttryck. Exempel:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Layer-deklaration

Layer-deklaration krävs. Den definierar storlek och källa för lager, inklusive dess anslutning paket och attribut. Instruktionen deklarationen börjar med namnet på lagret (Ange, dolda eller utdata), följt av dimensioner för layer (en tuppel positiva heltal). Exempel:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;  
```

+ Produkten av dimensionerna är antalet noder i lagret. I det här exemplet finns två dimensioner [5,20], vilket innebär att det finns 100 noder i lagret.
+ Lagren kan deklareras i valfri ordning, med ett undantag: Om flera inkommande lager har definierats kan den ordning de deklareras måste matcha ordningen på funktioner i indata.

Om du vill att antalet noder i ett lager fastställas automatiskt använder den `auto` nyckelord. Den `auto` nyckelordet har olika effekter, beroende på lagret:

+ I ett inkommande layer-deklarationen är antalet noder antalet funktioner i indata.
+ I en deklaration som dolda lagret är antalet noder talet som anges av parametervärdet för **antalet dolda noder**.
+ I ett lager utdatadeklarationen är antalet noder 2 för två klassificering, 1 för regression och är lika med antalet noder som utdata för multiklass-baserad klassificering.

Följande definition för nätverk kan till exempel storleken på alla skikt fastställas automatiskt:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;  
```

En layer-deklarationen för ett trainable lager (dolda eller utdata-lager) kan du också inkludera utdata funktionen (kallas även en aktivering-funktion), där standardinställningen är **sigmoid** för klassificering modeller och  **linjär** för regressionsmodeller. Även om du använder standard kan du uttryckligen ange aktivering-funktionen om du vill för tydlighetens skull.

Följande utdata-funktioner stöds:

+ sigmoid
+ Linjär
+ softmax
+ rlinear
+ Ruta
+ SQRT
+ srlinear
+ ABS
+ TANH
+ brlinear

Till exempel följande deklaration använder den **softmax** funktionen:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklarationen för anslutning

Omedelbart när du har definierat trainable lagret, måste du deklarera anslutningar mellan lager som du har definierat. Paket-deklarationen anslutning börjar med nyckelordet `from`, följt av namnet på det paket källa layer och vilken typ av anslutning paket för att skapa.

Fem typer av anslutning paket stöds för närvarande:

+ **Fullständig** paket, som anges med nyckelordet `all`
+ **Filtrerade** paket, som anges med nyckelordet `where`, följt av en predikatuttryck
+ **Convolutional** paket, som anges med nyckelordet `convolve`, följt av Faltning-attribut
+ **Poolning** paket, som anges med nyckelord **max pool** eller **innebära pool**
+ **Svaret normalisering** paket, som anges med nyckelordet **svar normen**

## <a name="full-bundles"></a>Fullständig paket

En fullständig anslutning paketet ingår en anslutning från varje nod i lagret källa till varje nod i mållagret. Det här är typ av nätverksanslutning standard.

## <a name="filtered-bundles"></a>Filtrerade paket

En filtrerad anslutning paket specifikation innehåller en predikat, uttryckt syntaktiskt, mycket som en C# lambda-uttryck. I följande exempel definierar två filtrerade paket:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  
```

+ I predikatet för `ByRow`, `s` är en parameter som representerar ett index i rektangulär matrisen av noder med indata-lagret, `Pixels`, och `d` är en parameter som representerar ett index i matrisen av noder med det dolda lagret `ByRow`. Vilken typ av både `s` och `d` är en tuppel med heltal med längden två. Den övergripande `s` intervall över alla par med heltal med `0 <= s[0] < 10` och `0 <= s[1] < 20`, och `d` över alla par med heltal,-intervall med `0 <= d[0] < 10` och `0 <= d[1] < 12`. 

+ På höger sida av predikatuttryck finns det ett villkor. I det här exemplet för varje värde i `s` och `d` så att villkoret är sant, det finns en gräns från lagret Källnoden till målnoden för lager. Därför den här filteruttrycket anger att paketet innehåller en anslutning från den nod som definierats av `s` till den nod som definieras av `d` i samtliga fall där s [0] är lika d [0].

Alternativt kan du ange en uppsättning vikterna för ett filtrerade paket. Värdet för den **vikterna** attributet måste vara en tuppel av flytande punktvärden med en längd som matchar antalet anslutningar som definieras av paketet. Som standard skapas slumpmässigt vikterna.

Viktningsvärden är grupperade efter index för mål-nod. Det vill säga, om den första Målnoden är ansluten till källnoderna för K, först `K` element i den **vikterna** tuppel är vikterna för första målnoden i källordning för indexet. Detsamma gäller för de återstående noderna för målet.

Det är möjligt att ange vikterna direkt som konstanta värden. Om du har lärt dig vikterna tidigare kan du till exempel ange dem som konstanter med följande syntax:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Convolutional paket

När träningsdata har en homogena struktur, används ofta convolutional anslutningar mer avancerade funktionerna i data. Till exempel i bild, ljud- eller data, spatial eller temporala dimensionalitet kan vara ganska uniform.  

Convolutional buntar använder rektangulär **kernlar** som är slid via dimensioner. I princip varje kernel definierar en uppsättning vikterna tillämpas i lokala neighborhoods kallas **kernel-program**. Varje kernel-program som motsvarar en nod i käll-lager, som kallas den **central nod**. Vikten av en kernel som delas mellan många anslutningar. I ett convolutional paket varje kerneln är rektangulär och alla kernel-program har samma storlek.  

Convolutional paket har stöd för följande attribut:

**InputShape** definierar dimensionaliteten för käll-lagret för det här convolutional paketet. Värdet måste vara en tuppel av positiva heltal. Produkten av heltal måste vara lika med antalet noder i käll-lagret, men annars det behöver inte matchar dimensionaliteten deklarerats för käll-lager. Längden på den här tuppel blir den **specifikaci** värde för convolutional paketet. Specifikaci avser vanligtvis antalet argument eller operander som en funktion kan vidta.

Använda attribut för att definiera form och platserna för dessa kernlar, **KernelShape**, **Stride**, **utfyllnad**, **LowerPad**, och  **UpperPad**:   

+ **KernelShape**: (obligatoriskt) definierar dimensionaliteten för varje kernel för convolutional paket. Värdet måste vara en tuppel av positiva heltal med en längd som är lika med specifikaci för paketet. Varje komponent i den här tuppeln får inte vara större än den motsvarande komponenten i **InputShape**. 

+ **STRIDE**: (valfritt) definierar de glidande steg storlekarna på Faltning (ett Stegstorlek för varje dimension), som är avståndet mellan de centrala noderna. Värdet måste vara en tuppel av positiva heltal med en längd som är specifikaci för paketet. Varje komponent i den här tuppeln får inte vara större än den motsvarande komponenten i **KernelShape**. Standardvärdet är en tuppel med alla komponenter som är lika med ett. 

+ **Dela**: (valfritt) definierar vikten för varje dimension av Faltning. Värdet kan vara ett booleskt värde eller en tuppel för booleska värden med en längd som är specifikaci för paketet. Ett booleskt värde utökas för att vara en tuppel rätt längd med alla komponenter som är lika med det angivna värdet. Standardvärdet är en tuppel som består av alla värden som True. 

+ **MapCount**: (valfritt) anger antalet funktionen mappar för convolutional paket. Värdet kan vara ett positivt heltal som är enkel eller en tuppel av positiva heltal med en längd som är specifikaci för paketet. Ett enda heltal utökas för att vara en tuppel rätt längd med de första komponenterna som är lika med det angivna värdet och alla återstående komponenter som är lika med en. Standardvärdet är en. Det totala antalet funktionen maps är produkten av komponenterna i tuppeln. Och detta totala antal väger över komponenter avgör hur funktionen mappa värden grupperas i mål-noder. 

+ **Vikterna**: (valfritt) definierar inledande vikterna för paketet. Värdet måste vara en tuppel av flytande punktvärden med en längd som är antalet gånger som kernlar som är antalet vikterna per kerneln, enligt definitionen nedan. Standardvärden är slumpmässigt genererat.  

Det finns två uppsättningar med egenskaper som styr utfyllnad, egenskaper som inte anges samtidigt:

+ **Utfyllnad**: (valfritt) avgör om indata ska bli utfyllt med hjälp av en **utfyllnad standardschema**. Värdet kan vara ett booleskt värde eller det kan vara en tuppel för booleska värden med en längd som är specifikaci för paketet. 

    Ett booleskt värde utökas för att vara en tuppel rätt längd med alla komponenter som är lika med det angivna värdet. 
    
    Om värdet för en dimension är True är källan logiskt fylls ut i dimensionen med cellerna med noll-värden för ytterligare kernel-program, så att de centrala noderna i de första och sista kernlar i dimensionen är de första och sista noderna i som dimensionen i käll-lagret. Därför antalet ”dummy” noder i varje dimension fastställs automatiskt, så att de passar exakt `(InputShape[d] - 1) / Stride[d] + 1` kernlar till Vadderat käll-lager. 
    
    Om värdet för en dimension är FALSKT, definieras kernlar som är så att antalet noder på varje sida som lämnas ut är samma (upp till en skillnad på 1). Standardvärdet för det här attributet är en tuppel med alla komponenter som är lika med False.

+ **UpperPad** och **LowerPad**: (valfritt) Ange större kontroll över utfyllnad att använda. **Viktigt:** dessa attribut kan vara definierad om och bara om den **utfyllnad** egenskapen ovan är ***inte*** definierats. Värdena måste vara integer-värden tupplar med längder som är specifikaci för paketet. När dessa attribut anges, läggs ”dummy” noder på lägre och övre kanten av varje dimension skiktets indata. Antalet noder som har lagts till i det lägre och övre upphört att gälla i varje dimension bestäms av **LowerPad**[i] och **UpperPad**[i] respektive. 

    För att säkerställa att kernlar motsvara endast ”verklig” noder och inte till ”dummy” noder, måste följande villkor vara uppfyllda:
      - Varje komponent i **LowerPad** måste vara strikt mindre än `KernelShape[d]/2`. 
      - Varje komponent i **UpperPad** får inte vara större än `KernelShape[d]/2`. 
      - Standardvärdet för dessa attribut är en tuppel med alla komponenter som är lika med 0. 

    Inställningen **utfyllnad** = true tillåter så mycket utfyllnad som behövs för att hålla ”mittpunkt” kerneln i ”verkliga” indata. Detta ändrar matematiska lite för databehandling storlek. I allmänhet utdatastorlek *D* beräknas som `D = (I - K) / S + 1`, där `I` har inkommande storlek, `K` har kernel-storlek, `S` är på väg och `/` är Heltalsdivision (avrundas mot noll ). Om du ställer in UpperPad = [1, 1], Indatastorleken `I` är effektivt 29, och därmed `D = (29 - 5) / 2 + 1 = 13`. Men när **utfyllnad** = true, i stort sett `I` hämtar ökar av `K - 1`; därför `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Genom att ange värden för **UpperPad** och **LowerPad** du får mycket större kontroll över utfyllnaden än om du bara ange **utfyllnad** = true.

Mer information om convolutional nätverk och deras program finns i följande artiklar: 

+ [http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
+ [http://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf) 
+ [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Poolning paket

En **poolning paket** gäller geometri liknar convolutional anslutning, men den använder fördefinierade funktioner till noden källvärdena för att härleda mål nodvärde. En pool paket har därför inget trainable tillstånd (vikterna eller eventuella fördomar). Anslutningspooler paket stöder alla convolutional attribut utom **delning**, **MapCount**, och **vikterna**.

Vanligtvis dessa kernlar sammanställt efter intilliggande anslutningspoolen enheter inte överlappar varandra. Om Stride [d] är lika KernelShape [d] i varje dimension är lagret fick traditionella lokala anslutningspoolen lager, som används ofta i convolutional neurala nätverk. Varje målnoden beräknar max eller medelvärdet av aktiviteter för dess kernel i käll-lagret.  

I följande exempel visas ett paket med en pool: 

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }  
```

+ Specifikaci av paketet är 3: det vill säga hur lång tuppeln `InputShape`, `KernelShape`, och `Stride`. 
+ Antalet noder i käll-lagret är `5 * 24 * 24 = 2880`. 
+ Det här är en traditionell lokal anslutningspoolen layer eftersom **KernelShape** och **Stride** är lika. 
+ Antalet noder i mållagret är `5 * 12 * 12 = 1440`.

Mer information om en pool lager finns i följande artiklar:  

+ [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Punkt 3.4)
+ [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
+ [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Svaret normalisering paket

**Svaret normalisering** är ett schema för lokala normalisering som introducerades av Geoffrey Hinton et al. i dokumentet [ImageNet Classiﬁcation med djupgående Convolutional Neural Networks](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). 

Svaret normalisering används för att underlätta generalisering i neurala nätverk. När en neuron håller på att utlösas på en mycket hög aktivering-nivå, Undertrycker en lokal svar normalisering layer aktivering andelen omgivande neurons. Detta görs med hjälp av tre parametrarna (`α`, `β`, och `k`) och en convolutional struktur (eller stadsdel form). Varje neuron i mållagret **y** motsvarar en neuron **x** i käll-lagret. Aktivering andelen **y** ges genom följande formel, där `f` är den aktivering i en neuron och `Nx` är kärnan (eller uppsättning som innehåller neurons i nätverket av **x**), som definieras av följande convolutional struktur:  

![formeln för convolutional struktur](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Svaret normalisering paket stöder alla convolutional attribut utom **delning**, **MapCount**, och **vikterna**.  

+ Om kernel innehåller neurons i samma kartan som ***x***, normalisering schemat kallas **samma mappa normalisering**. Definiera samma karta normalisering, första koordinaten i **InputShape** måste ha värdet 1.

+ Om kernel innehåller neurons i samma spatial position som ***x***, men neurons finns i andra mappningar, normalisering schemat kallas **över mappar normalisering**. Den här typen av svar normalisering implementerar en form av lateral hämning INSPIRERAD av typen finns i verkliga neurons skapar konkurrens om big aktivering nivåer av neuron utdata beräknas på olika kartor. Om du vill definiera över maps normalisering första koordinaten måste vara ett heltal större än ett och inte större än antalet maps och resten av koordinaterna måste ha värdet 1.

Eftersom svaret normalisering paket gäller en fördefinierad funktion för noden källvärdena mål noden värdet, har de inga trainable tillstånd (vikterna eller eventuella fördomar).

> [!NOTE]
> Noderna i mållagret motsvarar neurons som är centrala noder i dessa kernlar. Till exempel om `KernelShape[d]` är udda, sedan `KernelShape[d]/2` motsvarar den centrala kernel-noden. Om `KernelShape[d]` är jämnt och centrala noden är på `KernelShape[d]/2 - 1`. Därför om `Padding[d]` är FALSKT, först och senaste `KernelShape[d]/2` noder har inte motsvarande noder i mållagret. Om du vill undvika detta definierar **utfyllnad** som [true, true, …, true].

Förutom de fyra attributen som beskrivs ovan, stöd svar normalisering paket också för följande attribut:

+ **Alpha**: (obligatoriskt) anger ett värde som motsvarar `α` i den föregående formeln. 
+ **Beta**: (obligatoriskt) anger ett värde som motsvarar `β` i den föregående formeln. 
+ **Förskjutning**: (valfritt) anger ett värde som motsvarar `k` i den föregående formeln. Den standardvärdet 1.

I följande exempel definierar ett paket för normalisering av svaret med hjälp av dessa attribut:  

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }  
```

+ Käll-lagret innehåller fem kartor, var och en med aof dimension av 12 x 12, sammanräkning i 1440 noder. 
+ Värdet för **KernelShape** anger att detta är ett samma normalisering kartskikt, där området är en rektangel 3 x 3. 
+ Standardvärdet för **utfyllnad** är False, vilket mållagret har endast 10 noder i varje dimension. För att inkludera en nod i mållagret som motsvarar varje nod i käll-lagret, Lägg till utfyllnad = [true, true, true]; och ändra storleken på RN1 till [5, 12, 12].  

## <a name="share-declaration"></a>Dela deklaration

NET # stöder eventuellt definiera flera paket med delade vikterna. Vikten av alla två paket kan delas om deras strukturer är samma. Följande syntax definierar paket med delade vikterna:  

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

+ Indatafunktionerna delas upp i två lika stora inkommande lager. 
+ Dolda lager för att beräkna högre nivån funktioner på två inkommande lager. 
+ Resurs-deklarationen anger att *H1* och *H2* måste beräknas på samma sätt från deras respektive indata.  

Du kan också anges detta med två olika resurs-deklarationer på följande sätt:  

```Net#
share { Data1 => H1, Data2 => H2 } // share weights  
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases  
```

Du kan använda kortformen endast när lagren innehåller ett enda paket. I allmänhet är delning endast möjligt när den relevanta strukturen är identiska, vilket innebär att de har samma storlek, samma convolutional geometri och så vidare.  

## <a name="examples-of-net-usage"></a>Exempel på Net # användning

Det här avsnittet innehåller några exempel på hur du kan använda Net # definiera hur dolda lager interagera med andra lager, och skapa convolutional nätverk om du vill lägga till dolda lager.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definiera ett enkelt anpassade neurala nätverk: ”Hello World”-exempel

Det här enkla exemplet visar hur du skapar en modell för neurala nätverk som har ett enda dolda lager.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;  
```

Exemplet illustrerar några grundläggande kommandon på följande sätt:  

+ Den första raden definierar inkommande layer (med namnet `Data`). När du använder den `auto` nyckelord, neuralt nätverk automatiskt innehåller alla kolumner i funktionen i indata exemplen. 
+ Den andra raden skapar det dolda lagret. Namnet `H` tilldelas det dolda lagret som har 200 noder. Det här lagret är helt ansluten till inkommande lagret.
+ Den tredje raden definierar utdata-lagret (med namnet `O`), som innehåller 10 utdata-noder. Om det neurala nätverket används för klassificering, finns det en utdata-nod per klass. Nyckelordet **sigmoid** anger att funktionen utdata tillämpas i utdata-lagret.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definiera flera dolda lager: datorn vision exempel

I följande exempel visar hur du definierar ett lite mer komplext neurala nätverk med flera anpassade dolda lager.  

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

Detta exempel visar flera funktioner i språket för neurala nätverk:

+ Den har två inkommande lager `Pixels` och `MetaData`.
+ Den `Pixels` lagret är en källa lager för två anslutning paket, med målet lager `ByRow` och `ByCol`.
+ Lagren `Gather` och `Result` är målet lager i flera paket för anslutningen.
+ Utdata-lagret `Result`, är en mållagret i två anslutning paket, en med det andra nivån dolda lagret `Gather` som en mållagret och den andra med inkommande layer `MetaData` som ett mål-lager.
+ Dolda lager `ByRow` och `ByCol`, ange filtrerad anslutning med hjälp av predikatuttryck. Mer exakt, noden i `ByRow` på [x, y] är ansluten till noderna i `Pixels` att har det första indexet samordna lika noden användarens första koordinat x. På samma sätt kan du på noden i `ByCol` på [x, y] är ansluten till noderna i `Pixels` att ha andra indexet samordna inom någon av noden är andra koordinat y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definiera ett convolutional network för multiklass-baserad klassificering: siffra erkännande exempel

Definitionen av följande nätverk är utformad för att känna igen tal och det visar några avancerade metoder för att anpassa ett neurala nätverk.  

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
+ Nyckelordet `convolve` betyder att lagren heter `Conv1` och `Conv2` är convolutional lager. Var och en av dessa lager deklarationer följs av en lista över Faltning attribut.
+ Net har en tredje dolda lager, `Hid3`, som är anslutet till andra dolda lager, `Conv2`.
+ Utdata-lagret `Digit`, ansluts bara till det tredje dolda skiktet `Hid3`. Nyckelordet `all` anger att utdata-lagret är ansluten till fullständigt `Hid3`.
+ Specifikaci av Faltning är tre: längden på tuppeln `InputShape`, `KernelShape`, `Stride, and `delning ”. 
+ Vikterna per kernel får `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. Eller `26 * 50 = 1300`.
+ Du kan beräkna noder i varje dolda lagret på följande sätt:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Det totala antalet noder kan beräknas med hjälp av den deklarerade dimensionaliteten för lagret [50, 5, 5], enligt följande: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Eftersom `Sharing[d]` är False endast för `d == 0`, antal kärnor är `MapCount * NodeCount\[0] = 10 * 5 = 50`. 

## <a name="acknowledgements"></a>Bekräftelser

Net #-språket för anpassning av arkitekturen för neurala nätverk har utvecklats på Microsoft av Shon Katzenberger (Architect, Machine Learning) och Alexey Kamenev (programvaruutvecklare, Microsoft Research). Den används internt för machine learning-projekt och program som sträcker sig från bilden identifiering till textanalys. Mer information finns i [Neurala nätverk i Azure ML - introduktion till Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
