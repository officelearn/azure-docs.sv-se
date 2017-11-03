---
title: Redigera anpassade R-moduler i Azure Machine Learning | Microsoft Docs
description: "Snabbstart för att skapa anpassade R-moduler i Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6cbc628a-7e60-42ce-9f90-20aaea7ba630
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 03/24/2017
ms.author: bradsev;ankarlof
ms.openlocfilehash: cf3f0e79a9f873a57ef6b7f5233d324faee3e017
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="author-custom-r-modules-in-azure-machine-learning"></a>Skapa anpassade R-moduler i Azure Machine Learning
Det här avsnittet beskriver hur du skapar och distribuerar en anpassad R-modul i Azure Machine Learning. Det förklarar anpassade R-moduler är och vilka filer som används för att definiera dessa. Det visar hur du skapar filer som definierar en modul och att registrera modulen för distribution i en Machine Learning-arbetsytan. Element och attribut som används i definitionen av den anpassa modulen sedan beskrivs i detalj. Hur du använder extra funktioner och filer och flera utflöden diskuteras också. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="what-is-a-custom-r-module"></a>Vad är en anpassad R-modul?
En **anpassad modul** är en användardefinierad modul som kan överföras till din arbetsyta och köras som en del av en Azure Machine Learning-experiment. En **anpassad R-modul** är en anpassad modul som kör en användardefinierad R-funktion. **R** är ett programmeringsspråk för statistisk databehandling och bilder som ofta används av forskare statistiker och data för att implementera algoritmer. R är för närvarande det enda språk som stöds i anpassade moduler, men stöd för ytterligare språk är schemalagd för framtida versioner.

Anpassade moduler har **förstklassigt status** i Azure Machine Learning i den mening att de kan användas som en annan modul. De kan köras med andra moduler som ingår i publicerade experiment eller visualiseringar. Du har kontroll över den algoritm som implementeras av modulen, indata och utdata portarna som ska användas, modellering parametrar och andra olika runtime-funktioner. Ett experiment som innehåller anpassade moduler kan också publiceras i Cortana Intelligence Gallery lättare att dela.

## <a name="files-in-a-custom-r-module"></a>Filer i en anpassad R-modul
En anpassad R-modul har definierats som en .zip-fil som innehåller minst två filer:

* En **källfilen** som implementerar R-funktionen som exponeras av modul
* En **XML-definitionsfilen** som beskriver gränssnittet anpassad modul

Ytterligare extra filer kan också tas med i ZIP-filen som innehåller funktioner som kan nås från anpassad modul. Beskrivs i det här alternativet den **argument** ingår i referensavsnittet **element i XML-definitionsfilen** följande exempel för Snabbstart.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Snabbstart exempel: definiera, paketera och registrera en anpassad R-modul
Det här exemplet illustrerar hur du skapar de filer som krävs av en anpassad R-modul, paketera dem till en zip-fil och sedan registrera modulen i Machine Learning-arbetsytan. Exempel zip-paketet och exempel filer kan hämtas från [hämta CustomAddRows.zip filen](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Källfilen
Studera exemplet med en **anpassad Lägg till rader** modul som ändrar standard implementeringen av den **lägga till rader** modulen används för att sammanfoga rader (observationer) från två datamängder (dataramar). Standarden **lägga till rader** modulen lägger till raderna i andra inkommande datauppsättningen i slutet av den första inkommande dataset med hjälp av den `rbind` algoritmen. Den anpassade `CustomAddRows` funktionen accepterar två datauppsättningar på samma sätt, men även accepterar en parameter med booleska växlingsutrymme som en ytterligare indata. Om parametern växling är **FALSKT**, returnerar samma datauppsättning som standard implementering. Men om parametern växlingen **SANT**, funktionen lägger till rader i första inkommande datauppsättningen i slutet av den andra datamängden i stället. CustomAddRows.R-fil som innehåller implementeringen av R `CustomAddRows` funktion som exponeras av den **anpassad Lägg till rader** modulen har följande R-kod.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>XML-definitionsfilen
Gör detta `CustomAddRows` fungera som en XML-definitionsfilen en Azure Machine Learning-modul måste skapas för att ange hur **anpassad Lägg till rader** modulen ska ser ut och fungerar. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


Det är viktigt att Observera att värdet för den **id** attribut för den **indata** och **%d{arg/** element i XML-filen måste matcha funktionen parameternamn för R-koden i filen CustomAddRows.R exakt: (*dataset1*, *dataset2*, och *växlingen* i exemplet). På liknande sätt kan värdet för den **entryPoint** attribut för den **språk** elementet måste matcha namnet på funktionen i R-skriptet exakt: (*CustomAddRows* i exemplet). 

Däremot den **id** attributet för den **utdata** element motsvarar inte någon variabler i R-skriptet. När flera utdata krävs bara returnera en lista från R-funktionen med resultat som placeras *i samma ordning* som **utdata** element deklareras i XML-filen.

### <a name="package-and-register-the-module"></a>Paketera och registrera modulen
Spara dessa två filer som *CustomAddRows.R* och *CustomAddRows.xml* och zip två filer tillsammans i en *CustomAddRows.zip* fil.

Om du vill registrera dem i Machine Learning-arbetsytan, gå till din arbetsyta i Machine Learning Studio klickar du på den **+ ny** knappen längst ned och välj **modul -> från ZIP-PAKETET** att ladda upp den nya **anpassad Lägg till rader** modulen.

![Ladda upp Zip](./media/custom-r-modules/upload-from-zip-package.png)

Den **anpassad Lägg till rader** modulen är nu redo att användas av Machine Learning-experiment.

## <a name="elements-in-the-xml-definition-file"></a>Element i XML-definitionsfilen
### <a name="module-elements"></a>Modulen element
Den **modulen** elementet används för att definiera en anpassad modul i XML-filen. Flera moduler kan definieras i en XML-fil med flera **modulen** element. Varje modul på arbetsytan måste ha ett unikt namn. Registrera en anpassad modul med samma namn som en befintlig anpassad modul och den ersätter den befintliga modulen med den nya. Anpassade moduler kan dock vara registrerade med samma namn som en befintlig Azure Machine Learning-modul. Om så är fallet bör de visas i den **anpassad** modulpaletten kategori.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


I den **modulen** element, du kan ange två ytterligare valfritt element:

* en **ägare** element som är inbäddad i modulen  
* en **beskrivning** element som innehåller text som visas i snabbt hjälp för modulen och när du hovrar över modul i Machine Learning-Gränssnittet.

Regler för tecken-gränser i modulen-element:

* Värdet för den **namn** attribut i den **modulen** element får inte överstiga 64 tecken. 
* Innehållet i den **beskrivning** element får inte överstiga 128 tecken.
* Innehållet i den **ägare** element får inte överstiga 32 tecken.

En modul resultaten kan vara deterministisk eller nondeterministic.* * som standard, alla moduler som anses vara entydig. Det vill säga ska med en oföränderlig uppsättning indataparametrar och data modulen returnera samma resultat eacRAND eller en functionh körningen. Det här beteendet, Azure Machine Learning Studio Kör endast moduler som har markerats som deterministisk om en parameter eller indata har ändrats. Returnerar de cachelagrade resultaten ger också mycket snabbare körning av experiment.

Det finns funktioner som är icke-deterministisk, till exempel SLUMP eller en funktion som returnerar aktuellt datum och tid. Om din modulen använder en icke-deterministisk funktion, kan du ange att modulen är icke-deterministiska genom att ange den valfria **isDeterministic** attributet **FALSKT**. Detta garanterar att modulen körs när testet körs, även om modulen indata och parametrar inte har ändrats. 

### <a name="language-definition"></a>Definition av språk
Den **språk** elementet i XML-definitionsfilen används för att ange anpassad modul språk. R är för närvarande det enda språket som stöds. Värdet för den **sourceFile** attributet måste vara namnet på den R-fil som innehåller funktionen som ska anropas när modulen körs. Den här filen måste vara en del av zip-paketet. Värdet för den **entryPoint** attributet är namnet på den funktion som anropas och måste matcha en giltig funktion som definierats med i källfilen.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portar
Inkommande och utgående portarna för en anpassad modul har angetts i underordnade element för den **portar** avsnitt i XML-definitionsfilen. Ordningen på elementen bestämmer layouten erfarna (UX) av användare. Den första underordnat **inkommande** eller **utdata** som anges i den **portar** element i XML-filen blir den vänstra indataporten i Machine Learning UX.
Varje indata och utdataport kan ha en valfri **beskrivning** underordnat element som anger den text som visas när du håller markören över porten i Machine Learning-Gränssnittet.

**Portarna regler**:

* Maximalt antal **inkommande och utgående portarna** är 8 för varje.

### <a name="input-elements"></a>Inkommande element
Portar kan du skicka data till din R-funktionen och arbetsytan. Den **datatyper** som stöds för inkommande portar är följande: 

**DataTable:** den här typen har överförts till din R-funktion som en data.frame. I själva verket några typer (till exempel CSV-filer eller ARFF-filer) som stöds av Machine Learning och som är kompatibla med **DataTable** konverteras till en data.frame automatiskt. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Den **id** attribut som är associerade med varje **DataTable** indataport måste ha ett unikt värde och värdet måste matcha motsvarande namngiven parameter i R-funktionen.
Valfria **DataTable** portar som inte angavs som indata i ett experiment har värdet **NULL** skickades till funktionen för R och valfria zip portar ignoreras om indata inte är anslutet. Den **isOptional** attributet är valfritt för både den **DataTable** och **Zip** datatyper och är *FALSKT* som standard.

**ZIP:** anpassade moduler kan acceptera en zip-fil som indata. Den här indata packat till R arbetskatalog för din funktion

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

För anpassade R-moduler saknar id för en Zip-port matchar alla parametrar för funktionen R. Det beror på att zip-filen har extraherats automatiskt till R arbetskatalogen.

**Regler för inkommande:**

* Värdet för den **id** attribut för den **indata** elementet måste vara ett giltigt R variabelnamn.
* Värdet för den **id** attribut för den **indata** element får inte vara längre än 64 tecken.
* Värdet för den **namn** attribut för den **indata** element får inte vara längre än 64 tecken.
* Innehållet i den **beskrivning** element får inte vara längre än 128 tecken
* Värdet för den **typen** attribut för den **indata** elementet måste vara *Zip* eller *DataTable*.
* Värdet för den **isOptional** attribut för den **indata** elementet är inte obligatoriska (och *FALSKT* som standard om inget värde anges); men om detta anges måste det vara *true* eller *false*.

### <a name="output-elements"></a>Utdata element
**Standard utgående portar:** portar är mappade till returvärden från din R-funktionen, som sedan kan användas av efterföljande moduler. *DataTable* är bara standardutdata porttyp som stöds för närvarande. (Stöd för *inlärning* och *transformerar* är kommande.) En *DataTable* utdata har definierats som:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Utdata i anpassade R-moduler, värdet för den **id** attribut behöver inte överensstämmer med något i R-skriptet, men det måste vara unikt. En enda modulen utdata för returvärdet från R-funktionen måste vara en *data.frame*. För att kunna utdata fler än ett objekt av-datatypen stöds på lämpliga portar måste anges i XML-definitionsfilen och objekt som ska returneras som en lista. Utdata objekt tilldelade utgående portar från vänster till höger, avspeglar den ordning som objekten placeras i den returnerade listan.

Till exempel om du vill ändra den **anpassad Lägg till rader** modul till de ursprungliga två datamängder utdata *dataset1* och *dataset2*, utöver den nya sammanfogade datamängden *dataset*, (i en ordning, från vänster till höger som: *dataset*, *dataset1*, *dataset2*), definiera utdata-portar i filen CustomAddRows.xml enligt följande:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


Och returnerar en lista med objekt i en lista i rätt ordning i 'CustomAddRows.R':

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Visualiseringen utdata:** du kan också ange en utdataporten av typen *visualiseringen*, som visar utdata från R grafik enheten och konsolen utdata. Den här porten är inte en del av R funktionen utdata och påverkar inte ordningen på de andra utdata port-typerna. Lägg till en port för visualisering anpassade moduler genom att lägga till en **utdata** element med värdet *visualiseringen* för dess **typen** attribut:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Utgående regler:**

* Värdet för den **id** attribut för den **utdata** elementet måste vara ett giltigt R variabelnamn.
* Värdet för den **id** attribut för den **utdata** element får inte vara längre än 32 tecken.
* Värdet för den **namn** attribut för den **utdata** element får inte vara längre än 64 tecken.
* Värdet för den **typen** attribut för den **utdata** elementet måste vara *visualiseringen*.

### <a name="arguments"></a>Argument
Ytterligare data som kan skickas till funktionen R via modulparametrar som definieras i den **argument** element. Dessa parametrar visas i egenskapsrutan längst till höger i Machine Learning Användargränssnittet när modulen är markerat. Argument kan vara någon av typerna som stöds eller så kan du skapa en anpassad uppräkning vid behov. Liknar den **portar** element, **argument** element kan ha en valfri **beskrivning** element som anger den text som visas när markören över parameternamn.
Valfria egenskaper för en modul som standardvärde, minValue och maxValue kan läggas till ett argument som attribut till en **egenskaper** element. Giltiga egenskaperna för den **egenskaper** element beror på vilken argumenttyp och beskrivs med stöds Argumenttyperna i nästa avsnitt. Argument med den **isOptional** egenskapen **”true”** inte kräver att användaren anger ett värde. Om ett värde inte har angetts för argumentet, skickas inte argumentet till funktionens startadress. Funktionens startadress-argument som är valfritt måste hanteras direkt av funktionen, t.ex. tilldelade standardvärdet NULL i funktionsdefinitionen post punkt. Ett valfritt argument kommer bara att verkställa de andra argumentet villkor, d.v.s. min eller max, om ett värde har angetts av användaren.
Det är viktigt att var och en av parametrarna har unika ID-värden som är kopplade till dem som in- och utdataenheter. I vårt exempel Snabbstart associerade id /-parametern har *växlingen*.

### <a name="arg-element"></a>%D{arg/ element
En modul-parameter har definierats med hjälp av den **%d{arg/** underordnat element för den **argument** avsnitt i XML-definitionsfilen. Med de underordnade elementen i den **portar** avsnittet sorteringen av parametrarna i den **argument** avsnittet definierar layouten påträffades i UX. Parametrarna visas, uppifrån nedåt i Användargränssnittet i samma ordning som de definieras i XML-filen. De typer som stöds av Machine Learning för parametrar i den här listan. 

**int** – ett heltal (32-bitars) typparameter.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Valfria egenskaper*: **min**, **max**, **standard** och **isOptional**

**dubbla** – en parameter av typen double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Valfria egenskaper*: **min**, **max**, **standard** och **isOptional**

**bool** – en boolesk parameter som representeras av en kryssruta i UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Valfria egenskaper*: **standard** -falskt om inte angetts

**strängen**: en vanlig sträng

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Valfria egenskaper*: **standard** och **isOptional**

**ColumnPicker**: en parameter för val av kolumn. Den här typen visas i UX med väljaren för en kolumn. Den **egenskapen** elementet används här för att ange id för porten från vilken kolumner väljs, där porttyp måste vara *DataTable*. Resultatet av kolumnen skickas till funktionen R som en lista med strängar som innehåller de valda kolumnnamn. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Obligatoriska egenskaper*: **portId** -matchar id för ett indata-element med typen *DataTable*.
* *Valfria egenskaper*:
  
  * **allowedTypes** -filter kolumnen typer från vilken du kan välja. Giltiga värden är: 
    
    * numeriskt
    * Booleskt värde
    * Kategoriska
    * Sträng
    * Etikett
    * Funktion
    * Poäng
    * Alla
  * **standard** -giltig standardvalen för väljaren för kolumnen innehåller: 
    
    * Ingen
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Alla

**Listrutan**: ett användardefinierat uppräknade () listrutan. Dropdown-objekt har angetts i den **egenskaper** element med ett **objektet** element. Den **id** för varje **objektet** måste vara unika och en giltig R-variabel. Värdet för den **namn** av en **objektet** fungerar både som den text som visas och värdet som skickades till funktionen R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Valfria egenskaper*:
  * **standard** -värdet för standardegenskapen måste överensstämma med ett id-värde från en av de **objektet** element.

### <a name="auxiliary-files"></a>Extra filer
Alla filer som placeras i en anpassad modul ZIP-filen kommer att vara tillgängliga för användning under körning. Alla finns katalogstrukturer bevaras. Det innebär att filen ursprung fungerar samma lokalt och i Azure Machine Learning-körning. 

> [!NOTE]
> Observera att alla filer extraheras till 'src-katalogen så att alla sökvägar som ska ha ' src /-prefix.
> 
> 

Anta exempelvis att du vill ta bort alla rader med NAs från datauppsättningen och även ta bort alla dubbla rader innan mata ut den till CustomAddRows och du redan har skrivit ett R-funktion som gör detta i en fil RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Du kan styra den extra filen RemoveDupNARows.R i funktionen CustomAddRows:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Därefter ladda upp en zip-fil som innehåller 'CustomAddRows.R', 'CustomAddRows.xml' och 'RemoveDupNARows.R' som en anpassad R-modul.

## <a name="execution-environment"></a>Körningsmiljö
Körningsmiljön för R-skriptet använder samma version av R som den **köra R-skriptet** modul och kan använda samma standard paket. Du kan också lägga till ytterligare R-paket till din anpassade modul genom att inkludera dem i en anpassad modul zip-paketet. Bara läsa in dem i din R-skriptet som i din egen miljö för R. 

**Begränsningar av körningsmiljön** omfattar:

* Icke-beständiga filsystem: filer som skrivits när modulen anpassade körs är inte beständiga över flera körningar av samma modul.
* Ingen nätverksåtkomst

