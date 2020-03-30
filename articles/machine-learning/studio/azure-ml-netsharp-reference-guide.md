---
title: 'Net # anpassade neurala nätverk'
titleSuffix: ML Studio (classic) - Azure
description: Syntaxguide för referensspråket Net# neurala nätverk. Lär dig hur du skapar anpassade neurala nätverksmodeller i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: c1912e670a9cf1c178b58cefbd33171f15be2483
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218249"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio-classic"></a>Guide till net# neurala nätverk specifikation språk för Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Net# är ett språk som utvecklats av Microsoft och som används för att definiera komplexa neurala nätverksarkitekturer som djupa neurala nätverk eller faltningar av godtyckliga dimensioner. Du kan använda komplexa strukturer för att förbättra inlärningen av data som bild, video eller ljud.

Du kan använda en Net# arkitekturspecifikation i följande sammanhang:

+ Alla neurala nätverksmoduler i Microsoft Azure Machine Learning Studio (klassisk): [Multiclass Neural Network,](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network) [Two-Class Neural Network](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network)och [Neural Network Regression](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neurala nätverksfunktioner i Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) och [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)för R-språket och [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) för Python.


I den här artikeln beskrivs de grundläggande begrepp och syntax som behövs för att utveckla ett anpassat neuralt nätverk med Net#:

+ Krav på neurala nätverk och hur man definierar de primära komponenterna
+ Syntaxen och nyckelorden för net#-specifikationsspråket
+ Exempel på anpassade neurala nätverk som skapats med net #



## <a name="neural-network-basics"></a>Grunderna i neurala nätverk

En neural nätverksstruktur består av noder som är ordnade i lager och viktade anslutningar (eller kanter) mellan noderna. Anslutningarna är riktade och varje anslutning har en källnod och en målnod.

Varje tågställe (ett dolt eller utdatalager) har ett eller flera **anslutningspaket**. Ett anslutningspaket består av ett källlager och en specifikation av anslutningarna från det källlagret. Alla anslutningar i ett visst paket delar käll- och mållager. I Net# anses ett anslutningspaket tillhöra paketets mållager.

Net# stöder olika typer av anslutningspaket, vilket gör att du kan anpassa hur indata mappas till dolda lager och mappas till utgångarna.

Standard- eller standardpaketet är ett **komplett paket**där varje nod i källlagret är ansluten till varje nod i mållagret.

Dessutom stöder Net# följande fyra typer av avancerade anslutningspaket:

+ **Filtrerade buntar**. Du kan definiera ett predikat med hjälp av platserna för källlagernoden och mållagernoden. Noder är anslutna när predikatet är Sant.

+ **Faltningspaket**. Du kan definiera små stadsdelar med noder i källlagret. Varje nod i mållagret är ansluten till ett område med noder i källlagret.

+ **Samla buntar** och **svarsnormaliseringspaket**. Dessa liknar faltningspaket genom att användaren definierar små stadsdelar av noder i källlagret. Skillnaden är att kanternas vikter i dessa buntar inte är tågbara. I stället används en fördefinierad funktion på källnodvärdena för att bestämma målnodvärdet.


## <a name="supported-customizations"></a>Anpassningar som stöds

Arkitekturen för neurala nätverksmodeller som du skapar i Azure Machine Learning Studio (klassisk) kan anpassas i stor utsträckning med net#. Du kan:

+ Skapa dolda lager och kontrollera antalet noder i varje lager.
+ Ange hur lager ska kopplas till varandra.
+ Definiera särskilda anslutningsstrukturer, till exempel faltningar och viktdelningspaket.
+ Ange olika aktiveringsfunktioner.

Mer information om syntaxen för specifikationsspråk finns i [Strukturspecifikation](#structure-specifications).

Exempel på att definiera neurala nätverk för vissa vanliga maskininlärningsuppgifter, från simplex till komplex, finns [i Exempel](#examples-of-net-usage).

## <a name="general-requirements"></a>Allmänna krav

+ Det måste finnas exakt ett utdatalager, minst ett indatalager och noll eller fler dolda lager.
+ Varje lager har ett fast antal noder, begreppsmässigt ordnade i en rektangulär matris med godtyckliga dimensioner.
+ Indatalager har inga associerade tränade parametrar och representerar den punkt där instansdata kommer in i nätverket.
+ Tågbara lager (dolda och utdatalager) har associerade tränade parametrar, så kallade vikter och fördomar.
+ Käll- och målnoderna måste finnas i separata lager.
+ Anslutningarna måste vara acykliska. Med andra ord kan det inte finnas en kedja av anslutningar som leder tillbaka till den ursprungliga källnoden.
+ Utdatalagret kan inte vara ett källlager i ett anslutningspaket.

## <a name="structure-specifications"></a>Specifikationer för struktur

En specifikation för neural nätverksstruktur består av tre avsnitt: **konstantdeklarationen**, **lagerdeklarationen**, **anslutningsdeklarationen**. Det finns också ett valfritt **avsnitt om delningsdeklaration.** Avsnitten kan anges i valfri ordning.

## <a name="constant-declaration"></a>Konstant deklaration

En konstant deklaration är frivillig. Det ger ett sätt att definiera värden som används någon annanstans i neurala nätverk definitionen. Deklarationssatsen består av en identifierare följt av ett likhetstecken och ett värdeuttryck.

Följande sats definierar till `x`exempel en konstant:

`Const X = 28;`

Om du vill definiera två eller flera konstanter samtidigt omsluter du identifierarens namn och värden i klammerparenteser och separerar dem med semikolon. Ett exempel:

`Const { X = 28; Y = 4; }`

Höger sida av varje tilldelningsuttryck kan vara ett heltal, ett verkligt tal, ett booleskt värde (Sant eller Falskt) eller ett matematiskt uttryck. Ett exempel:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Lagerdeklaration

Lagerdeklarationen krävs. Den definierar storleken och källan för lagret, inklusive dess anslutningspaket och attribut. Deklarationssatsen börjar med namnet på lagret (indata, dold eller utdata), följt av lagrets dimensioner (en tuppel med positiva heltal). Ett exempel:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Produkten av dimensionerna är antalet noder i lagret. I det här exemplet finns det två dimensioner [5,20], vilket innebär att det finns 100 noder i lagret.
+ Lagren kan deklareras i valfri ordning, med ett undantag: Om mer än ett indatalager definieras måste den ordning i vilken de deklareras matcha ordningen på funktionerna i indata.

Om du vill ange att antalet noder i ett `auto` lager ska bestämmas automatiskt använder du nyckelordet. Nyckelordet `auto` har olika effekter, beroende på lagret:

+ I en deklaration för indatalager är antalet noder antalet funktioner i indata.
+ I en dold lagerdeklaration är antalet noder det nummer som anges av parametervärdet för **Antal dolda noder**.
+ I en utdatalagerdeklaration är antalet noder 2 för tvåklassklassificering, 1 för regression och lika med antalet utdatanoder för klassificering av flera klasser.

Med följande nätverksdefinition kan du till exempel bestämma storleken på alla lager automatiskt:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

En lagerdeklaration för ett tåglägesbart lager (de dolda eller utdatalagren) kan eventuellt inkludera utdatafunktionen (kallas även aktiveringsfunktion), som som standard **sigmoid** för klassificeringsmodeller och **linjär** för regressionsmodeller. Även om du använder standardinställningen kan du uttryckligen ange aktiveringsfunktionen, om så önskas för tydlighetens skull.

Följande utdatafunktioner stöds:

+ Sigmoid
+ Linjär
+ softmax (softmax)
+ rlinear (rlinear)
+ Square
+ Rot
+ srlinear (srlinar)
+ Abs
+ Tanh
+ brlinear (brlinear)

I följande deklaration används till exempel **funktionen softmax:**

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Anslutningsdeklaration

Omedelbart efter att du har definierat det tågbara lagret måste du deklarera anslutningar mellan de lager som du har definierat. Anslutningspaketdeklarationen börjar `from`med nyckelordet , följt av namnet på paketets källlager och vilken typ av anslutningspaket som ska skapas.

För närvarande stöds fem typer av anslutningspaket:

+ **Hela** buntar, som indikeras av nyckelordet`all`
+ **Filtrerade** buntar, som indikeras av nyckelordet `where`, följt av ett predikatuttryck
+ **Faltningspaket,** som anges `convolve`av nyckelordet , följt av faltningsattributen
+ **Poolning** buntar, som anges av sökorden **max pool** eller **genomsnittlig pool**
+ **Svarsnormaliseringspaket,** indikeras av **nyckelordssvarsnormen**

## <a name="full-bundles"></a>Hela buntar

Ett fullständigt anslutningspaket innehåller en anslutning från varje nod i källlagret till varje nod i mållagret. Det här är standardtypen för nätverksanslutning.

## <a name="filtered-bundles"></a>Filtrerade paket

En filtrerad anslutningspaketspecifikation innehåller ett predikat, uttryckt syntaktiskt, ungefär som ett C# lambda-uttryck. I följande exempel definieras två filtrerade paket:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ I predikatet `ByRow`för `s` är en parameter som representerar ett index till den `Pixels`rektangulära matrisen för noderna i indatalagret, och `d` är en parameter som representerar ett index i matrisen med noder i det dolda lagret. `ByRow` Typ av `s` båda `d` och är en tuppel av heltal av längd två. Begreppsmässigt `s` varierar allt par heltal `0 <= s[0] < 10` med `0 <= s[1] < 20`och `d` , och sträcker sig över alla `0 <= d[0] < 10` `0 <= d[1] < 12`par av heltal, med och .

+ På höger sida av predikatuttrycket finns ett villkor. I det här exemplet, `s` `d` för varje värde av och sådant att villkoret är Sant, finns det en kant från källlagernoden till mållagernoden. Det här filteruttrycket anger således att paketet innehåller en `s` anslutning från noden som definieras av till noden som definieras av `d` i alla fall där s[0] är lika med d[0].

Du kan också ange en uppsättning vikter för ett filtrerat paket. Värdet för **attributet Vikter** måste vara en tuppning med flyttalsvärden med en längd som matchar antalet anslutningar som definieras av paketet. Som standard genereras vikter slumpmässigt.

Viktvärden grupperas efter målnodsindexet. Det vill än om den första målnoden är ansluten till K-källnoder är de första `K` elementen i Tuppeln **Vikter** vikterna för den första målnoden, i källindexordning. Detsamma gäller för de återstående målnoderna.

Det är möjligt att ange vikter direkt som konstanta värden. Om du till exempel har lärt dig vikterna tidigare kan du ange dem som konstanter med den här syntaxen:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Faltningspaket

När träningsdata har en homogen struktur används ofta faltningsanslutningar för att lära sig datafunktioner på hög nivå. Till exempel i bild-, ljud- eller videodata kan rumslig eller temporal dimensionitet vara ganska enhetlig.

Faltningspaket använder rektangulära kärnor som **skjuts** genom dimensionerna. I huvudsak definierar varje kärna en uppsättning vikter som tillämpas i lokala stadsdelar, så kallade **kärnprogram**. Varje kernel-program motsvarar en nod i källlagret, som kallas den **centrala noden**. Vikten på en kärna delas mellan många anslutningar. I ett faltningspaket är varje kärna rektangulär och alla kärnprogram har samma storlek.

Faltningspaket stöder följande attribut:

**InputShape** definierar måttet på källlagret för den här faltningspaketet. Värdet måste vara en tuppel av positiva heltal. Produkten av heltalen måste vara lika med antalet noder i källlagret, men annars behöver den inte matcha den dimensionalitet som deklarerats för källlagret. Längden på denna tuppel blir **arityvärdet** för faltningsbunten. Vanligtvis refererar arity till antalet argument eller operanden som en funktion kan ta.

Om du vill definiera formen och platserna för kärnorna använder du attributen **KernelShape**, **Stride**, **Utfyllnad,** **LowerPad**och **UpperPad:**

+ **KernelShape**: (obligatoriskt) Definierar dimensionaliteten för varje kärna för faltningspaketet. Värdet måste vara en tuppel av positiva heltal med en längd som är lika med buntens aritet. Varje komponent i tuppeln får inte vara större än motsvarande komponent **i InputShape**.

+ **Steg:**(valfritt) Definierar faltningsstegets glidande stegstorlek (en stegstorlek för varje dimension), det vill säga avståndet mellan de centrala noderna. Värdet måste vara en tuppel av positiva heltal med en längd som är buntens aritet. Varje komponent i denna tuppel får inte vara större än motsvarande komponent **i KernelShape**. Standardvärdet är en tuppel med alla komponenter som är lika med en.

+ **Delning:**(valfritt) Definierar viktdelningen för varje dimension av faltningen. Värdet kan vara ett enda booleskt värde eller en tuppel av booleska värden med en längd som är buntens aritet. Ett enda booleskt värde utökas till att vara en tuppel av rätt längd med alla komponenter som är lika med det angivna värdet. Standardvärdet är en tuppel som består av alla sanna värden.

+ **MapCount**: (valfritt) Definierar antalet funktionskartor för faltningspaketet. Värdet kan vara ett enda positivt heltal eller en tuppel av positiva heltal med en längd som är ariteten för paketet. Ett enda heltalsvärde utökas till att vara en tuppel av rätt längd med de första komponenterna som är lika med det angivna värdet och alla återstående komponenter som är lika med en. Standardvärdet är ett. Det totala antalet funktionskartor är produkten av tuppelns komponenter. Factoring av det totala antalet mellan komponenterna avgör hur funktionskartvärdena grupperas i målnoderna.

+ **Vikter**: (valfritt) Definierar de ursprungliga vikterna för paketet. Värdet måste vara en tuppel av flyttalsvärden med en längd som är antalet kärnor gånger antalet vikter per kärna, enligt definitionen senare i den här artikeln. Standardvikterna genereras slumpmässigt.

Det finns två uppsättningar egenskaper som styr utfyllnad, egenskaperna är ömsesidigt uteslutande:

+ **Utfyllnad:**(valfritt) Avgör om indata ska vara vadderad med hjälp av ett **standardutfyllnadsschema**. Värdet kan vara ett enda booleskt värde, eller så kan det vara en tuppel av booleska värden med en längd som är buntens aritet.

    Ett enda booleskt värde utökas till att vara en tuppel av rätt längd med alla komponenter som är lika med det angivna värdet.

    Om värdet för en dimension är Sant, är källan logiskt vadderad i den dimensionen med nollvärderade celler för att stödja ytterligare kärnprogram, så att de centrala noderna för de första och sista kärnorna i den dimensionen är de första och sista noderna i den dimensionen. dimensionen i källlagret. Således bestäms antalet "dummy" noder i varje dimension automatiskt, `(InputShape[d] - 1) / Stride[d] + 1` för att passa exakt kärnor i det vadderade källlagret.

    Om värdet för en dimension är Falskt definieras kärnorna så att antalet noder på varje sida som utelämnas är detsamma (upp till en skillnad på 1). Standardvärdet för det här attributet är en tuppel med alla komponenter som är lika med False.

+ **UpperPad** och **LowerPad:**(tillval) Ge större kontroll över mängden utfyllnad som ska användas. **Viktigt:** Dessa attribut kan definieras om och endast om **utfyllnadsegenskapen** ovan ***inte*** har definierats. Värdena ska vara heltalsvärda tupplar med längder som är buntens aritet. När dessa attribut anges läggs "dummy"-noder till i de nedre och övre ändarna av varje dimension i indatalagret. Antalet noder som läggs till i de nedre och övre ändarna i varje dimension bestäms av **LowerPad**[i] respektive **UpperPad**[i] .

    För att säkerställa att kärnor endast motsvarar "riktiga" noder och inte "dummy"-noder måste följande villkor vara uppfyllda:
  - Varje komponent **i LowerPad** måste `KernelShape[d]/2`vara strikt mindre än .
  - Varje komponent **i UpperPad** får `KernelShape[d]/2`inte vara större än .
  - Standardvärdet för dessa attribut är en tuppel med alla komponenter som är lika med 0.

    Inställningen **Utfyllnad** = sant tillåter så mycket utfyllnad som behövs för att hålla "mitten" av kärnan inuti den "riktiga" ingången. Detta ändrar matematik lite för beräkning av utdatastorleken. I allmänhet beräknas *D* utdatastorleken `D = (I - K) / S + 1`D `I` som , `K` var är indatastorleken, är kärnstorleken, `S` är steg- och `/` är heltalsdelning (runda mot noll). Om du ställer in UpperPad = [1, 1] är `I` `D = (29 - 5) / 2 + 1 = 13`ingångsstorleken effektivt 29, och därmed . Men när **Stoppning** = sant, i `I` huvudsak `K - 1`blir stötte upp av ; därav `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Genom att ange värden för **UpperPad** och **LowerPad** får du mycket mer kontroll över utfyllnad än om du bara ställer in **Utfyllnad** = sant.

Mer information om faltningsnätverk och deras tillämpningar finns i följande artiklar:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Samla buntar

Ett **poolningspaket** tillämpar geometri som liknar faltningsanslutning, men används fördefinierade funktioner för att köpa nodvärden för att härleda målnodvärdet. Därför har poolning buntar inget trainable tillstånd (vikter eller fördomar). Poolningspaket stöder alla faltningsattribut utom **Delning,** **MapCount**och **Vikter**.

Vanligtvis överlappar kärnorna som sammanfattas av intilliggande poolenheter inte. Om Stride[d] är lika med KernelShape [d] i varje dimension, är det erhållna lagret det traditionella lokala poolningslagret, som vanligen används i faltnings neurala nätverk. Varje målnod beräknar det maximala eller medelvärdet av aktiviteterna för dess kärna i källlagret.

Följande exempel illustrerar ett poolningspaket:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Buntens aritet är 3: det vill än tupplar , `InputShape` `KernelShape`och `Stride`.
+ Antalet noder i källlagret `5 * 24 * 24 = 2880`är .
+ Detta är ett traditionellt lokalt poolningslag på grund av **att KernelShape** och **Stride** är lika.
+ Antalet noder i mållagret `5 * 12 * 12 = 1440`är .

Mer information om hur du samlar lager finns i följande artiklar:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)(Avsnitt 3.4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Paket med svarsnormalisering

**Svar normalisering** är en lokal normalisering system som först infördes av Geoffrey Hinton, et al, i papperet [ImageNet Klassificering med Deep Convolutional Neural Networks](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Svarsnormalisering används för att underlätta generalisering i neurala nät. När en neuron skjuter på en mycket hög aktiveringsnivå, en lokal svar normalisering lager undertrycker aktiveringsnivån av de omgivande nervceller. Detta görs med hjälp`α`av `β`tre `k`parametrar ( , , och ) och en faltningsstruktur (eller grannskapsform). Varje neuron i destinationen lager **y** motsvarar en neuron **x** i källskiktet. Aktiveringsnivån för **y** ges av `f` följande formel, var är `Nx` aktiveringsnivån för en neuron, och är kärnan (eller den uppsättning som innehåller nervceller i närheten av **x**), enligt definitionen i följande faltningsstruktur:

![formel för faltningsstruktur](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Svarsnormaliseringspaket stöder alla faltningsattribut utom **Delning,** **MapCount**och **Vikter**.

+ Om kärnan innehåller nervceller på samma karta som ***x***kallas normaliseringsschemat **för samma kartnormalisering**. Om du vill definiera samma kartnormalisering måste den första koordinaten i **InputShape** ha värdet 1.

+ Om kärnan innehåller nervceller i samma rumsliga position som ***x***, men nervcellerna finns i andra kartor, kallas normaliseringsschemat **över kartor normalisering**. Denna typ av svar normalisering genomför en form av lateral hämning inspirerad av den typ som finns i verkliga nervceller, skapa konkurrens om stora aktiveringsnivåer bland neuron utgångar beräknas på olika kartor. Om du vill definiera över kartors normalisering måste den första koordinaten vara ett heltal som är större än ett och inte större än antalet kartor, och resten av koordinaterna måste ha värdet 1.

Eftersom svarsnormaliseringspaket använder en fördefinierad funktion på källnodvärden för att bestämma målnodvärdet, har de inget trainable-tillstånd (vikter eller fördomar).

> [!NOTE]
> Noderna i mållagret motsvarar nervceller som är kärnnodernas centrala noder. Om det `KernelShape[d]` till exempel `KernelShape[d]/2` är udda motsvarar den centrala kärnnoden. Om `KernelShape[d]` är jämn, är den `KernelShape[d]/2 - 1`centrala noden på . Om `Padding[d]` är Falskt har därför de `KernelShape[d]/2` första och de sista noderna inte motsvarande noder i mållagret. För att undvika denna situation, definiera **Utfyllnad** som [sant, sant, ..., sant].

Förutom de fyra attribut som beskrivits tidigare stöder svarsnormaliseringspaket även följande attribut:

+ **Alfa**: (obligatoriskt) Anger ett flyttalsvärde `α` som motsvarar i föregående formel.
+ **Beta**: (obligatoriskt) Anger ett flyttalsvärde `β` som motsvarar i föregående formel.
+ **Förskjutning**: (valfritt) Anger ett flyttalsvärde som motsvarar `k` i föregående formel. Det standardlägger till 1.

I följande exempel definieras ett svarsnormaliseringspaket med hjälp av dessa attribut:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ Källlagret innehåller fem kartor, var och en med en dimension på 12x12, totalt i 1440 noder.
+ Värdet **för KernelShape** anger att det här är samma kartnormaliseringslager, där grannskapet är en 3x3-rektangel.
+ Standardvärdet **för utfyllnad** är Falskt, vilket innebär att mållagret bara har 10 noder i varje dimension. Om du vill inkludera en nod i mållagret som motsvarar varje nod i källlagret lägger du till Utfyllnad = [sant, sant, sant]; och ändra storleken på RN1 till [5, 12, 12].

## <a name="share-declaration"></a>Aktiedeklaration

Net# stöder eventuellt definiera flera paket med delade vikter. Vikterna för två buntar kan delas om deras strukturer är desamma. Följande syntax definierar paket med delade vikter:

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

Följande aktiedeklaration anger till exempel lagernamnen, vilket anger att både vikter och fördomar ska delas:

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

+ Indatafunktionerna är uppdelade i två lika stora indatalager.
+ De dolda lagren beräknar sedan funktioner på högre nivå på de två indatalagren.
+ Aktiedeklarationen anger att *H1* och *H2* måste beräknas på samma sätt från sina respektive ingångar.

Alternativt kan detta specificeras med två separata aktiedeklarationer enligt följande:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Du kan bara använda det korta formuläret när lagren innehåller ett enda paket. I allmänhet är delning endast möjlig när den relevanta strukturen är identisk, vilket innebär att de har samma storlek, samma faltningsgeometri och så vidare.

## <a name="examples-of-net-usage"></a>Exempel på användning av Net#

Det här avsnittet innehåller några exempel på hur du kan använda Net# för att lägga till dolda lager, definiera hur dolda lager interagerar med andra lager och skapa faltningsnätverk.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definiera ett enkelt anpassat neuralt nätverk: "Hello World" exempel

Det här enkla exemplet visar hur du skapar en neural nätverksmodell som har ett enda dolt lager.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

Exemplet illustrerar några grundläggande kommandon enligt följande:

+ Den första raden definierar indatalagret (med namnet `Data`). När du `auto` använder nyckelordet innehåller det neurala nätverket automatiskt alla funktionskolumner i indataexemplen.
+ Den andra raden skapar det dolda lagret. Namnet `H` tilldelas det dolda lagret, som har 200 noder. Det här lagret är helt anslutet till inmatningslagret.
+ Den tredje raden definierar utdatalagret (med namnet `Out`), som innehåller 10 utdatanoder. Om det neurala nätverket används för klassificering finns det en utdatanod per klass. Nyckelordet **sigmoid** anger att utdatafunktionen tillämpas på utdatalagret.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definiera flera dolda lager: exempel på datorseende

Följande exempel visar hur du definierar ett något mer komplext neuralt nätverk, med flera anpassade dolda lager.

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

Det här exemplet illustrerar flera funktioner i det neurala nätverksspecifikationsspråket:

+ Strukturen har två inmatningslager `Pixels` och `MetaData`.
+ Lagret `Pixels` är ett källlager för två anslutningspaket, med mållager `ByRow` och `ByCol`.
+ Lagren `Gather` och `Result` är mållager i flera anslutningspaket.
+ Utdatalagret `Result`är ett mållager i två anslutningspaket. Det ena med `Gather` det andra dolda lagret som ett `MetaData` mållager och det andra med indatalagret som mållager.
+ De dolda `ByRow` lagren `ByCol`och ange filtrerad anslutning med hjälp av predikatuttryck. Mer exakt är noden i `ByRow` [x, y] ansluten `Pixels` till noderna i som har den första indexkoordinaten som är lika med nodens första koordinat, x. På samma sätt är `ByCol` noden i [x, y] `Pixels` ansluten till noderna där den andra indexkoordinaten inom en av nodens andra koordinat, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definiera ett faltningsnätverk för klassificering av fleraklasser: exempel på siffraigenkänning

Definitionen av följande nätverk är utformad för att känna igen siffror, och det illustrerar några avancerade tekniker för att anpassa ett neuralt nätverk.

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

+ Strukturen har ett enda `Image`inmatningslager.
+ Nyckelordet `convolve` anger att lagren `Conv1` `Conv2` namnges och är faltningslager. Var och en av dessa lager deklarationer följs av en lista över faltning attribut.
+ Nätet har ett tredje `Hid3`dolt lager, , som är `Conv2`helt ansluten till den andra dolda lagret, .
+ Utdatalagret `Digit`är endast kopplat till det `Hid3`tredje dolda lagret. Nyckelordet `all` anger att utdatalagret `Hid3`är helt anslutet till .
+ Faltningens aritet är tre: `InputShape`tupplaras `KernelShape` `Stride`längd `Sharing`, , och .
+ Antalet vikter per kärna `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`är . Eller `26 * 50 = 1300`.
+ Du kan beräkna noderna i varje dolt lager enligt följande:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Det totala antalet noder kan beräknas med hjälp av lagrets deklarerade dimensionalitet [50, 5, 5], enligt följande:`MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Eftersom `Sharing[d]` är Falskt `d == 0`endast för , `MapCount * NodeCount\[0] = 10 * 5 = 50`antalet kärnor är .

## <a name="acknowledgements"></a>Bekräftelser

Net# språk för att anpassa arkitekturen i neurala nätverk har utvecklats på Microsoft av Shon Katzenberger (Arkitekt, Machine Learning) och Alexey Kamenev (Software Engineer, Microsoft Research). Den används internt för maskininlärningsprojekt och program som sträcker sig från bildidentifiering till textanalys. Mer information finns [i Neural Nets i Azure Machine Learning studio - Introduktion till Net#](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
