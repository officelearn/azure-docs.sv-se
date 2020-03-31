---
title: Använda matematisk åtgärd
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Använd matematiska åtgärder i Azure Machine Learning för att tillämpa en matematisk åtgärd på kolumnvärden i en pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456751"
---
# <a name="apply-math-operation"></a>Använda matematisk åtgärd

I den här artikeln beskrivs en modul med Azure Machine Learning designer (förhandsversion).

Använd åtgärden Använd matematiska termer för att skapa beräkningar som tillämpas på numeriska kolumner i indatauppsättningen. 

Matematiska operationer som stöds omfattar vanliga aritmetiska funktioner som multiplikation och division, trigonometriska funktioner, en mängd olika avrundningsfunktioner och specialfunktioner som används i datavetenskap som gamma- och felfunktioner.  

När du har definierat en åtgärd och kört pipelinen läggs värdena till i datauppsättningen. Beroende på hur du konfigurerar modulen kan du:

+ Lägg till resultaten i datauppsättningen. Detta är särskilt användbart när du verifierar resultatet av åtgärden.
+ Ersätt kolumnvärden med de nya beräknade värdena.
+ Generera en ny kolumn för resultat och visa inte de ursprungliga data. 

Leta efter den åtgärd du behöver i följande kategorier:  

- [Basic](#basic-math-operations)  
  
     Funktionerna i kategorin **Grundläggande** kan användas för att manipulera ett enda värde eller en kolumn med värden. Du kan till exempel få det absoluta värdet av alla tal i en kolumn eller beräkna kvadratroten för varje värde i en kolumn.  
  
-   [Jämför](#comparison-operations)  
  
      Funktionerna i kategorin **Jämför** används alla som jämförelse: du kan göra en parvis jämförelse av värdena i två kolumner, eller så kan du jämföra varje värde i en kolumn med en angiven konstant. Du kan till exempel jämföra kolumner för att avgöra om värdena var desamma i två datauppsättningar. Du kan också använda en konstant, till exempel ett högsta tillåtna värde, för att hitta extremvärden i en numerisk kolumn.  
  
-   [Åtgärder](#arithmetic-operations)  
  
     Kategorin **Operationer** omfattar de grundläggande matematiska funktionerna: addition, subtraktion, multiplikation och division. Du kan arbeta med antingen kolumner eller konstanter. Du kan till exempel lägga till värdet i kolumn A i värdet i kolumn B. Du kan också subtrahera en konstant, till exempel ett tidigare beräknat medelvärde, från varje värde i kolumn A.  
  
-   [Avrundning](#rounding-operations)  
  
     Kategorin **Avrundning** innehåller en mängd olika funktioner för att utföra operationer som avrundning, tak, golv och trunkering till olika precisionsnivåer. Du kan ange precisionsnivå för både decimal- och heltal.  
  
-   [Särskilda](#special-math-functions)  
  
     Kategorin **Special** innehåller matematiska funktioner som särskilt används inom datavetenskap, till exempel elliptiska integraler och gaussiska felfunktionen.  
  
-   [Trigonometriska](#trigonometric-functions)  
  
     **Trigonometriska** kategorin omfattar alla standardtrigonometriska funktioner. Du kan till exempel konvertera radianer till grader eller beräkna funktioner som tangent i antingen radianer eller grader.
     Dessa funktioner är unary, vilket innebär att de tar en enda kolumn med värden som indata, tillämpa trigonometriska funktionen och returnera en kolumn med värden som följd.  Därför måste du se till att indatakolumnen är lämplig typ och innehåller rätt typ av värden för den angivna åtgärden.   

## <a name="how-to-configure-apply-math-operation"></a>Konfigurera Tillämpa matematisk åtgärd  

Modulen **Använd matematisk operation** kräver en datauppsättning som innehåller minst en kolumn som bara innehåller tal. Numren kan vara diskreta eller kontinuerliga men måste vara av en numerisk datatyp, inte en sträng.

Du kan använda samma åtgärd på flera numeriska kolumner, men alla kolumner måste finnas i samma datauppsättning. 

Varje instans av den här modulen kan bara utföra en typ av åtgärd åt gången. Om du vill utföra komplexa matematiska åtgärder kan du behöva kedja ihop flera instanser av modulen **Använd matematiska åtgärder.**  
  
1.  Lägg till modulen **Använd matematiska åtgärder** i pipelinen.

1. Anslut en datauppsättning som innehåller minst en numerisk kolumn.  

1.  Markera en eller flera källkolumner som beräkningen ska utföras på.   
  
    - Alla kolumner som du väljer måste vara en numerisk datatyp. 
    - Dataintervallet måste vara giltigt för den valda matematiska operationen. Annars kan ett fel eller NaN(inte ett tal) uppstå. Ln(-1.0) är till exempel en ogiltig åtgärd `NaN`och resulterar i ett värde på .
  
1.  Klicka på **Kategori** om du vill välja vilken **typ** av matematisk åtgärd som ska utföras.
    
1. Välj en viss åtgärd i listan i den kategorin.
  
1.  Ange ytterligare parametrar som krävs för varje typ av åtgärd.  
  
1.  Använd alternativet **Utdataläge** för att ange hur du vill att matematiska åtgärden ska genereras: 

    - **Lägg till**. Alla kolumner som används som indata ingår i utdatauppsättningen, plus att ytterligare en kolumn läggs till som innehåller resultatet av matematiska åtgärder.
    - **På plats**. Värdena i kolumnerna som används som indata ersätts med de nya beräknade värdena. 
    - **ResultatOnly**. En enskild kolumn returneras som innehåller resultatet av matematiska åtgärder.
  
1.  Skicka pipelinen.  
  
## <a name="results"></a>Resultat

Om du genererar resultaten med alternativen **Lägg till** eller **Resultatet anger** kolumnrubrikerna för den returnerade datauppsättningen åtgärden och kolumnerna som användes. Om du till exempel jämför två kolumner med operatorn **Lika med,** skulle resultatet se ut så här:  
  
-   **Lika med (Col2_Col1),** vilket indikerar att du testat Col2 mot Col1.  
-   **Lika med(Col2_$10),** vilket indikerar att du jämförde kolumn 2 med konstanten 10.  

Även om du använder alternativet **Inplace** tas inte källdata bort eller ändras. kolumnen i den ursprungliga datauppsättningen är fortfarande tillgänglig i designern. Om du vill visa de ursprungliga data kan du ansluta modulen [Lägg till kolumner](add-columns.md) och ansluta dem till utdata från Använd matematisk **åtgärd**.  
    
## <a name="basic-math-operations"></a>Grundläggande matematiska operationer 

Funktionerna i kategorin **Grundläggande** tar vanligtvis ett enda värde från en kolumn, utför den fördefinierade åtgärden och returnerar ett enda värde. För vissa funktioner kan du ange en konstant eller en kolumnuppsättning som ett andra argument.  
  
 Azure Machine Learning stöder följande funktioner i kategorin **Grundläggande:**  

### <a name="abs"></a>Abs

Returnerar det absoluta värdet för de markerade kolumnerna.  
  
### <a name="atan2"></a>Atan2

Returnerar en omvänd tangent med fyra kvadranter.  

Markera de kolumner som innehåller punktkoordinaterna. För det andra argumentet, som motsvarar x-koordinaten, kan du också ange en konstant.  

Motsvarar ATAN2-funktionen i Matlab.  

### <a name="conj"></a>Conj (konj)

Returnerar konjugatet för värdena i den markerade kolumnen.  

### <a name="cuberoot"></a>KubRoot

Beräknar kubroten för värdena i den valda kolumnen.  

### <a name="doublefactorial"></a>DoubleFactorial (dubbelfaktoriellt)  
 Beräknar den dubbla faktoriellt för värden i den valda kolumnen. Den dubbla faktoriella är en förlängning av den normala faktoriella funktionen, och det betecknas som x!!.  

### <a name="eps"></a>Eps

Returnerar storleken på gapet mellan det aktuella värdet och det näst högsta dubbelprecisionsnumret. Motsvarar EPS-funktionen i Matlab.  
  
### <a name="exp"></a>Exp

Returnerar e som höjts till värdets kraft i den valda kolumnen. Detta är samma som Excel EXP-funktionen.  

### <a name="exp2"></a>Exp2 (på andra)

Returnerar argumentens bas-2 exponentiellt, vilket löser för y = x * 2<sup>t</sup> där t är en kolumn med värden som innehåller exponenter.  

Markera kolumnen som innehåller exponentvärdena t i **kolumnuppsättningen.**

För **Exp2** kan du ange ett andra argument x, som kan vara antingen en konstant eller en annan kolumn med värden. I **Den andra argumenttypen**anger du om du anger multiplikatorn x som en konstant eller ett värde i en kolumn.  

Om du till exempel väljer en {0,1,2,3,4,5} kolumn med värdena för både multiplikatorn och exponenten returnerar funktionen {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 (på andra sätt) 

Returnerar den negativa exponenten för värden i den markerade kolumnen.  

### <a name="factorial"></a>Fakultet
Returnerar faktoriellt för värden i den markerade kolumnen.  

### <a name="hypotenuse"></a>Hypotenusan
Beräknar hypotenusen för en triangel där längden på ena sidan anges som en kolumn med värden, och längden på den andra sidan anges antingen som en konstant eller som två kolumner.  

### <a name="ln"></a>Ln

Returnerar den naturliga logaritmen för värdena i den valda kolumnen.  

### <a name="lnplus1"></a>LnPlus1 (LnPlus1)

Returnerar den naturliga logaritmen plus en för värdena i den valda kolumnen.  

### <a name="log"></a>Logga

Returnerar loggen för värdena i den markerade kolumnen, med tanke på den angivna basen.  

Du kan ange basen (det andra argumentet) antingen som en konstant eller genom att markera en annan kolumn med värden.  

### <a name="log10"></a>Log10

Returnerar basvärdena 10 logaritm för den valda kolumnen.  

### <a name="log2"></a>Logga2

Returnerar bas2 logaritmvärdena för den valda kolumnen.  

### <a name="nthroot"></a>NthRoot (ort)
Returnerar värdets n: e rot med hjälp av ett n som du anger.  

Markera de kolumner som du vill beräkna roten för med alternativet **Kolumnuppsättning.**  

I **Andra argumenttypen**markerar du en annan kolumn som innehåller roten eller anger en konstant som ska användas som rot.  

Om det andra argumentet är en kolumn används varje värde i kolumnen som värdet n för motsvarande rad. Om det andra argumentet är en konstant skriver du värdet för n i textrutan **Andra argument.**
### <a name="pow"></a>Pow

Beräknar X som höjts till effekten av Y för vart och ett av värdena i den valda kolumnen.  

Markera först de kolumner som innehåller **basen**, som ska vara en flottör, med alternativet **Kolumnuppsättning.**  

I **Andra argumenttyp**markerar du den kolumn som innehåller exponenten eller anger en konstant som ska användas som exponent.  

Om det andra argumentet är en kolumn används varje värde i kolumnen som exponent för motsvarande rad. Om det andra argumentet är en konstant skriver du värdet för exponenten i textrutan **Andra argument.**  

### <a name="sqrt"></a>Sqrt

Returnerar kvadratroten för värdena i den markerade kolumnen.  

### <a name="sqrtpi"></a>SqrtPi (SqrtPi)

För varje värde i den valda kolumnen multiplicerar du värdet med pi och returnerar sedan resultatets kvadratrot.  

### <a name="square"></a>Square

Kvadrater på värdena i den markerade kolumnen.  

## <a name="comparison-operations"></a>Jämförelseåtgärder  

Använd jämförelsefunktionerna i Azure Machine Learning designer när som helst som du behöver testa två uppsättningar värden mot varandra. I en pipeline kan du till exempel behöva göra dessa jämförelseåtgärder:  

- Utvärdera en kolumn med modell för sannolikhetspoäng mot ett tröskelvärde.
- Ta reda på om två resultatuppsättningar är desamma. För varje rad som är olika lägger du till en FALSE-flagga som kan användas för vidare bearbetning eller filtrering.  

### <a name="equalto"></a>Lika med

Returnerar Sant om värdena är desamma.  

### <a name="greaterthan"></a>GreaterThan

Returnerar Sant om värdena i **kolumnuppsättningen** är större än den angivna konstanten eller större än motsvarande värden i jämförelsekolumnen.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Returnerar Sant om värdena i **kolumnuppsättningen** är större än eller lika med den angivna konstanten, eller större än eller lika med motsvarande värden i jämförelsekolumnen.  

### <a name="lessthan"></a>LessThan

Returnerar Sant om värdena i **kolumnuppsättningen** är mindre än den angivna konstanten, eller mindre än motsvarande värden i jämförelsekolumnen.  
  
### <a name="lessthanorequalto"></a>Mindre Än MerqualTo

Returnerar Sant om värdena i **kolumnuppsättningen** är mindre än eller lika med den angivna konstanten, eller mindre än eller lika med motsvarande värden i jämförelsekolumnen.  

### <a name="notequalto"></a>InteequalTo

Returnerar Sant om värdena i **kolumnuppsättningen** inte är lika med konstant- eller jämförelsekolumnen och returnerar Falskt om de är lika.  

### <a name="pairmax"></a>PairMax (1993

Returnerar värdet som är större – värdet i **kolumnuppsättningen** eller värdet i konstant- eller jämförelsekolumnen.  

### <a name="pairmin"></a>PairMin (på annat sätt)

Returnerar det värde som är mindre – värdet i **kolumnuppsättningen** eller värdet i konstant- eller jämförelsekolumnen  
  
##  <a name="arithmetic-operations"></a>Aritmetiska åtgärder   

Innehåller de grundläggande aritmetiska operationerna: addition och subtraktion, division och multiplikation.  Eftersom de flesta åtgärder är binära, kräver två tal, väljer du först operationen och väljer sedan den kolumn eller de tal som ska användas i det första och andra argumenten.

Den ordning i vilken du väljer kolumner för division och subtraktion kan verka kontraintuitivt. För att göra det lättare att förstå resultaten anger kolumnrubriken dock operationsnamnet och den ordning i vilken kolumnerna användes.

Åtgärd|Num1 (1)|Num2 (På andra)|Resultatkolumn|Resultatvärde|
----|----|----|----|----
|Addition|1|5|Lägg till(Num2_Num1)| 4|
|Multiplikation|1|5|Flera(Num2_Num1)|5|
|Subtraktion|1|5|Subtrahera(Num2_Num1)|4|
|Subtraktion|0|1|Subtrahera(Num2_Num1)|0|
|Division|1|5|Dela(Num2_Num1)|5|
|Division|0|1|Dela(Num2_Num1)|Infinity|

### <a name="add"></a>Lägg till

Ange källkolumnerna med hjälp av **kolumnuppsättningen**och lägg sedan till ett tal som anges i **andra argumentet**i dessa värden .  

Om du vill lägga till värdena i två kolumner väljer du en kolumn eller kolumner med **kolumnuppsättning**och väljer sedan en andra kolumn med **andra argumentet**.  

### <a name="divide"></a>Dividera

Delar värdena i **Kolumn som anges** med en konstant eller med kolumnvärdena som definieras i Andra **argumentet**.  Med andra ord väljer du divisorn först, och sedan utdelningen. Utdatavärdet är kvoten.

### <a name="multiply"></a>Multiplicera

Multiplicerar värdena i **Kolumnuppsättningen** med de angivna konstant- eller kolumnvärdena.  

### <a name="subtract"></a>Subtrahera

Ange vilken kolumn med värden som ska användas *(minuend)* genom att välja en annan kolumn med alternativet **Kolumnuppsättning.** Ange sedan det nummer som ska subtraheras *(subtrahend)* med hjälp av listrutan **Andra argument.** Du kan välja antingen en konstant eller en kolumn med värden.

##  <a name="rounding-operations"></a>Avrundning 

Azure Machine Learning designer stöder en mängd avrundning åtgärder. För många åtgärder måste du ange hur mycket precision som ska användas vid avrundning. Du kan använda antingen en statisk precisionsnivå, angiven som en konstant, eller så kan du använda ett dynamiskt precisionsvärde som erhålls från en kolumn med värden.  

- Om du använder en konstant ställer du in **Precisionstyp** till **Konstant** och skriver sedan antalet siffror som ett heltal i textrutan **Konstant precision.** Om du skriver ett icke-heltal uppstår inget fel i modulen, men resultaten kan vara oväntade.  

- Om du vill använda ett annat precisionsvärde för varje rad i datauppsättningen anger du **Precisionstyp** till **ColumnSet**och väljer sedan den kolumn som innehåller lämpliga precisionsvärden.  

### <a name="ceiling"></a>Ceiling

Returnerar taket för värdena i **kolumnuppsättningen**.  

### <a name="ceilingpower2"></a>TakPower2

Returnerar det fyrkantiga taket för värdena i **kolumnuppsättningen**.  

### <a name="floor"></a>Floor

Returnerar golvet för värdena i **kolumnuppsättningen**till den angivna precisionen.  

### <a name="mod"></a>Mod

Returnerar den fraktionerade delen av värdena i **kolumnuppsättningen**till den angivna precisionen.  

### <a name="quotient"></a>Kvot

Returnerar den fraktionerade delen av värdena i **kolumnuppsättningen**till den angivna precisionen.  

### <a name="remainder"></a>Resten

Returnerar resten för värdena i **kolumnuppsättningen**.  

### <a name="rounddigits"></a>RoundDigits (Rundans: 10

Returnerar värdena i **kolumnuppsättningen**, avrundade med 4/5-regeln till det angivna antalet siffror.  

### <a name="rounddown"></a>RoundDown

Returnerar värdena i **kolumnuppsättningen**, avrundat nedåt till det angivna antalet siffror.  

### <a name="roundup"></a>RoundUp

Returnerar värdena i **kolumnuppsättningen**, avrundat uppåt till angivet antal siffror.  

### <a name="toeven"></a>ToEven (toeven)

Returnerar värdena i **kolumnuppsättningen**, avrundat till närmaste helt, jämna tal.  

### <a name="toodd"></a>ToOdd (toodd)

Returnerar värdena i **kolumnuppsättningen**, avrundat till närmaste hela, udda tal.  

### <a name="truncate"></a>Truncate

Trunkerar värdena i **Kolumnuppsättningen** genom att ta bort alla siffror som inte tillåts av den angivna precisionen.  
  
## <a name="special-math-functions"></a>Särskilda matematiska funktioner

Denna kategori omfattar specialiserade matematiska funktioner som ofta används inom datavetenskap. Om inget annat anges är funktionen oarisk och returnerar den angivna beräkningen för varje värde i den valda kolumnen eller kolumnerna.  

### <a name="beta"></a>Beta

Returnerar värdet för Eulers betafunktion.  

### <a name="ellipticintegrale"></a>ElliptiskaIntegrale
Returnerar värdet för den ofullständiga elliptiska integralen.  
  

### <a name="ellipticintegralk"></a>ElliptiskaIntegralK

Returnerar värdet för den fullständiga elliptiska integralen (K).  

### <a name="erf"></a>Erf

Returnerar felfunktionens värde.  

Felfunktionen (även kallad Gauss-felfunktionen) är en speciell funktion av sigmoid-formen som används i sannolikhet för att beskriva diffusion.  

### <a name="erfc"></a>Erfc (på ett år)

Returnerar värdet för den kompletterande felfunktionen.  

Erfc definieras som 1 – erf(x).  

### <a name="erfscaled"></a>Erf Skalad

Returnerar värdet för den skalade felfunktionen.  

Den skalade versionen av felfunktionen kan användas för att undvika aritmetiskt underflöde.  

### <a name="erfinverse"></a>ErfInvers

Returnerar värdet för funktionen omvänd erf.  

### <a name="exponentialintegralein"></a>ExponentiellIntegralEin

Returnerar värdet för exponentiell integral Ei.  

### <a name="gamma"></a>Gamma

Returnerar gammafunktionens värde.  

### <a name="gammaln"></a>GammaLn (på)

Returnerar gammafunktionens naturliga logaritm.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Returnerar värdet för den normaliserade ofullständiga gammafunktionen.  

Den här funktionen tar ett andra argument, som kan tillhandahållas antingen som en konstant eller en kolumn med värden.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Returnerar värdet för den omvända legaliserade ofullständiga gammafunktionen.  

Den här funktionen tar ett andra argument, som kan tillhandahållas antingen som en konstant eller en kolumn med värden.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Returnerar värdet för den normaliserade ofullständiga gammafunktionen.  

Den här funktionen tar ett andra argument, som kan tillhandahållas antingen som en konstant eller en kolumn med värden.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Returnerar värdet för den omvända generaliserade regulariserade ofullständiga gammafunktionen.

Den här funktionen tar ett andra argument, som kan tillhandahållas antingen som en konstant eller en kolumn med värden.  

### <a name="polygamma"></a>PolyGamma (1)

Returnerar polygammafunktionens värde.  

Den här funktionen tar ett andra argument, som kan tillhandahållas antingen som en konstant eller en kolumn med värden.

##  <a name="trigonometric-functions"></a>Trigonometriska funktioner 

Denna kategori iInkluderar de flesta av de viktiga trigonometriska och omvända trigonometriska funktioner. Alla trigonometriska funktioner är unary och kräver inga ytterligare argument.  

### <a name="acos"></a>Acos

Beräknar arcussinet för kolumnvärdena.  

### <a name="acosdegree"></a>AcosDegree (AcosDegree)

Beräknar pelarnamnets arccosinus i grader.  

### <a name="acosh"></a>Acosh (acosh)

Beräknar kolumnvärdenas hyperboliska arccosinus.  

### <a name="acot"></a>Acot

Beräknar ljuskokten för kolumnvärdena.  

### <a name="acotdegrees"></a>AcotDegrees

Beräknar ljuskokningen för kolumnvärdena i grader.  

### <a name="acoth"></a>Acoth (Acoth)

Beräknar kolumnvärdenas hyperboliska arccotangent.  

### <a name="acsc"></a>Acsc (acsc)

Beräknar den bågkodaliga för kolumnvärdena.  

### <a name="acscdegrees"></a>AcscDegrees (

Beräknar arccosecant av kolumnvärdena, i grader.  
### <a name="asec"></a>Asec (Asec)

Beräknar kolumnvärdenas bågsväljning.  

### <a name="asecdegrees"></a>AsecDegrees (AsecDegrees)

Beräknar kolumnvärdenas bågsväljning i grader.  

### <a name="asech"></a>Asech (samma dag som en)

Beräknar kolumnvärdenas hyperboliska bågsekant.  

### <a name="asin"></a>Asin

Beräknar arkein för kolumnvärdena.  

### <a name="asindegrees"></a>AsinDegrees

Beräknar arkeinvärdet för kolumnvärdena i grader.  

### <a name="asinh"></a>Asinh (10000)

Beräknar hyperbolisk arcsine för kolumnvärdena.  

### <a name="atan"></a>Atan

Beräknar arkusen för kolumnvärdena.  

### <a name="atandegrees"></a>AtanDegrees

Beräknar ljusglädnet för kolumnvärdena, i grader.  

### <a name="atanh"></a>Atanh (atanh)

Beräknar kolumnvärdenas hyperboliska arcustangent.  

### <a name="cos"></a>Cos

Beräknar cosinus för kolumnvärdena.  

### <a name="cosdegrees"></a>CosDegrees (cosdegrees)

Beräknar cosinus för kolumnvärdena, i grader.  

### <a name="cosh"></a>Cosh

Beräknar hyperbolisk cosinus för kolumnvärdena.  

### <a name="cot"></a>Cot

Beräknar cotangent för kolumnvärdena.  

### <a name="cotdegrees"></a>CotDegrees (cotDegrees)

Beräknar cotangent för kolumnvärdena, i grader.  

### <a name="coth"></a>Coth (coth)
Beräknar hyperbolisk cotangent för kolumnvärdena.  

### <a name="csc"></a>Csc

Beräknar cosecant för kolumnvärdena.  

### <a name="cscdegrees"></a>CscDegrees (0)

Beräknar cosecant för kolumnvärdena, i grader.  

### <a name="csch"></a>Csch (på sätt och av)

Beräknar hyperbolisk cosecant för kolumnvärdena.  

### <a name="degreestoradians"></a>GraderTillRadianer

Konverterar grader till radianer.  

### <a name="sec"></a>Sec

Beräknar varslare för kolumnvärdena.  

### <a name="asecdegrees"></a>aSecDegrees (aSecDegrees)

Beräknar varslare för kolumnvärdena i grader.  

### <a name="asech"></a>aSech (på nytt)

Beräknar kolumnvärdenas hyperboliska spant.  

### <a name="sign"></a>Tecken

Returnerar tecknet för kolumnvärdena.  

### <a name="sin"></a>Sin

Beräknar kolumnvärdenas sinus.  

### <a name="sinc"></a>Sinc (sinc)

Beräknar kolumnvärdenas sinus-cosinus-värde.  

### <a name="sindegrees"></a>SinDegrees (sindegrees)

Beräknar sinus för kolumnvärdena, i grader.  

### <a name="sinh"></a>Sinh

Beräknar kolumnvärdenas hyperboliska sinus.  

### <a name="tan"></a>Tan

Beräknar tangenten för kolumnvärdena.  

### <a name="tandegrees"></a>TanDegrees (

Beräknar tangenten för argumentet, i grader.  

### <a name="tanh"></a>Tanh

Beräknar kolumnvärdenas hyperboliska tangent.  
  
## <a name="technical-notes"></a>Tekniska anmärkningar

Var försiktig när du väljer mer än en kolumn som den andra operatorn. Resultaten är lätta att förstå om åtgärden är enkel, till exempel att lägga till en konstant i alla kolumner. 

Anta att datauppsättningen har flera kolumner och du lägger till datauppsättningen i sig själv. I resultaten läggs varje kolumn till i sig själv enligt följande:  
  
|Num1 (1)|Num2 (På andra)|Num3 (1)|Lägg till(Num1_Num1)|Lägg till(Num2_Num2)|Lägg till(Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Om du behöver utföra mer komplexa beräkningar kan du kedja flera instanser av **Använd matematisk åtgärd**. Du kan till exempel lägga till två kolumner med hjälp av en förekomst av **Använd matematisk operation**och sedan använda en annan instans av Använd matematisk **operation** för att dividera summan med en konstant för att hämta medelvärdet.  
  
Du kan också använda någon av följande moduler för att göra alla beräkningar samtidigt med SQL-, R- eller Python-skriptet:
 
+ [Köra R-skript](execute-r-script.md)
+ [Köra Python-skript](execute-python-script.md)
+ [Använda SQL-transformering](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
