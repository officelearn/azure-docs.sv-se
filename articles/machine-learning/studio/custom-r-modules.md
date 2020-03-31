---
title: Definiera anpassade R-moduler
titleSuffix: ML Studio (classic) - Azure
description: I det här avsnittet beskrivs hur du skapar och distribuerar en anpassad R Studio (klassisk). Det förklarar vad anpassade R-moduler är och vilka filer som används för att definiera dem.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 5b8dab14a9416795eccef1f71988a048c8bedb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218171"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Definiera anpassade R-moduler för Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

I det här avsnittet beskrivs hur du skapar och distribuerar en anpassad R Studio (klassisk). Det förklarar vad anpassade R-moduler är och vilka filer som används för att definiera dem. Den illustrerar hur du skapar de filer som definierar en modul och hur du registrerar modulen för distribution i en Machine Learning-arbetsyta. De element och attribut som används i definitionen av den anpassade modulen beskrivs sedan mer i detalj. Hur du använder hjälpfunktioner och filer och flera utdata diskuteras också. 



## <a name="what-is-a-custom-r-module"></a>Vad är en anpassad R-modul?
En **anpassad modul** är en användardefinierad modul som kan överföras till din arbetsyta och köras som en del av Azure Machine Learning Studio (klassiskt) experiment. En **anpassad R-modul** är en anpassad modul som kör en användardefinierad R-funktion. **R** är ett programmeringsspråk för statistisk databehandling och grafik som ofta används av statistiker och dataforskare för att implementera algoritmer. För närvarande är R det enda språk som stöds i anpassade moduler, men stöd för ytterligare språk är schemalagt för framtida versioner.

Anpassade moduler har **förstklassig status** i Azure Machine Learning Studio (klassisk) i den meningen att de kan användas precis som alla andra moduler. De kan köras med andra moduler, som ingår i publicerade experiment eller i visualiseringar. Du har kontroll över algoritmen som implementeras av modulen, in- och utdataportarna som ska användas, modelleringsparametrarna och andra olika körningsbeteenden. Ett experiment som innehåller anpassade moduler kan också publiceras i Azure AI Gallery för enkel delning.

## <a name="files-in-a-custom-r-module"></a>Filer i en anpassad R-modul
En anpassad R-modul definieras av en ZIP-fil som innehåller minst två filer:

* En **källfil** som implementerar R-funktionen som exponeras av modulen
* En **XML-definitionsfil** som beskriver det anpassade modulgränssnittet

Ytterligare hjälpfiler kan också inkluderas i ZIP-filen som innehåller funktioner som kan nås från den anpassade modulen. Det här alternativet beskrivs i avsnittet **Argument** i referensavsnittet **Element i XML-definitionsfilen** i snabbstartsexempelet.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Exempel på snabbstart: definiera, paketera och registrera en anpassad R-modul
Det här exemplet illustrerar hur du skapar de filer som krävs av en anpassad R-modul, paketerar dem till en zip-fil och registrerar sedan modulen på arbetsytan Machine Learning. Exemplet zip-paket och exempelfiler kan hämtas från [Download CustomAddRows.zip fil](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Källfilen
Tänk på exemplet med en anpassad **tilläggsradmodul** som ändrar standardimplementeringen av modulen Lägg till **rader** som används för att sammanfoga rader (observationer) från två datauppsättningar (dataramar). Standardmodulen **Lägg till rader** lägger till raderna för den andra indatauppsättningen i `rbind` slutet av den första indatauppsättningen med hjälp av algoritmen. Den `CustomAddRows` anpassade funktionen accepterar på samma sätt två datauppsättningar, men accepterar också en boolesk växlingsparameter som en extra indata. Om växlingsparametern är inställd **på FALSKT**returneras samma datauppsättning som standardimplementeringen. Men om växlingsparametern är **SANT**lägger funktionen till rader med första indatauppsättningen i slutet av den andra datauppsättningen i stället. CustomAddRows.R-filen som innehåller implementeringen `CustomAddRows` av R-funktionen som exponeras av modulen **Anpassade tilläggsrader** har följande R-kod.

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
Om du `CustomAddRows` vill visa den här funktionen som den klassiska modulen i Azure Machine Learning Studio måste en XML-definitionsfil skapas för att ange hur modulen **Anpassade tilläggsrader** ska se ut och fungera. 

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


Det är viktigt att notera att värdet för **id-attributen** **för indata-** och **arg-elementen** i XML-filen måste matcha funktionsparameternamnen för R-koden i filen CustomAddRows.R EXAKT: (*dataset1*, *dataset2*och *byt* i exemplet). På samma sätt måste värdet för **attributet entryPoint** för **språkelementet** matcha namnet på funktionen i R-skriptet EXAKT: (*CustomAddRows* i exemplet). 

**Id-attributet** för **utdataelementet** motsvarar däremot inte några variabler i R-skriptet. När mer än en utdata krävs returnerar du helt enkelt en lista från R-funktionen med resultat *placerade i samma ordning* som **utdataelement deklareras** i XML-filen.

### <a name="package-and-register-the-module"></a>Paketera och registrera modulen
Spara dessa två filer som *CustomAddRows.R* och *CustomAddRows.xml* och sedan zip de två filerna tillsammans i en *CustomAddRows.zip* fil.

Om du vill registrera dem på arbetsytan Machine Learning går du till arbetsytan i Azure Machine Learning Studio (klassisk), klickar på knappen **+NY** längst ned och väljer **MODULE -> FRÅN ZIP-PAKETET** för att ladda upp den nya modulen Lägg till rader för **anpassade rader.**

![Ladda upp zip](./media/custom-r-modules/upload-from-zip-package.png)

Modulen **Anpassad tilläggsrader** är nu klar att nås av dina Machine Learning-experiment.

## <a name="elements-in-the-xml-definition-file"></a>Element i XML-definitionsfilen
### <a name="module-elements"></a>Modulelement
**Module-elementet** används för att definiera en anpassad modul i XML-filen. Flera moduler kan definieras i en XML-fil med flera **modulelement.** Varje modul på arbetsytan måste ha ett unikt namn. Registrera en anpassad modul med samma namn som en befintlig anpassad modul och den ersätter den befintliga modulen med den nya. Anpassade moduler kan dock registreras med samma namn som en befintlig Azure Machine Learning Studio -modul (klassisk). I så fall visas de i kategorin **Anpassad** i modulpaletten.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


I **moduleelementet** kan du ange ytterligare två valfria element:

* ett **ägarelement** som är inbäddat i modulen  
* ett **beskrivningselement** som innehåller text som visas i snabbhjälp för modulen och när du hovrar över modulen i maskininlärningsgränssnittet.

Regler för teckengränser i modulelementen:

* Värdet för **namnattributet** i **moduleelementet** får inte vara längre än 64 tecken. 
* Innehållet i **beskrivningselementet** får inte vara längre än 128 tecken.
* Ägarelementets **Owner** innehåll får inte vara längre än 32 tecken.

En moduls resultat kan vara deterministiska eller icke-deterministiska.** Som standard anses alla moduler vara deterministiska. Det vill säga, med tanke på en oföränderlig uppsättning indataparametrar och data, bör modulen returnera samma resultat eacRAND eller en funktionstid som den körs. Med tanke på det här beteendet kör Azure Machine Learning Studio (klassisk) bara moduler som markerats som deterministiska om en parameter eller indata har ändrats. Att returnera de cachelagrade resultaten ger också mycket snabbare körning av experiment.

Det finns funktioner som inte är deterministiska, till exempel RAND eller en funktion som returnerar aktuellt datum eller aktuell tid. Om modulen använder en icke-deterministisk funktion kan du ange att modulen inte är deterministisk genom att ange det valfria **attributet isDeterministic** till **FALSKT**. Detta försäkrar att modulen körs igen när experimentet körs, även om modulen indata och parametrar inte har ändrats. 

### <a name="language-definition"></a>Språkdefinition
**Språkelementet** i XML-definitionsfilen används för att ange det anpassade modulspråket. R är för närvarande det enda språk som stöds. Värdet för **attributet sourceFile** måste vara namnet på R-filen som innehåller funktionen som ska anropas när modulen körs. Den här filen måste vara en del av zip-paketet. Värdet för **entryPoint-attributet** är namnet på den funktion som anropas och måste matcha en giltig funktion som definierats med i källfilen.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portar
In- och utdataportarna för en anpassad modul anges i underordnade element i avsnittet **Portar** i XML-definitionsfilen. Ordningen på dessa element bestämmer den layout som användarna upplever (UX). Den första **underordnade indata** eller **utdata som** anges i elementet **Portar** i XML-filen blir den vänstra indataporten i Machine Learning UX.
Varje indata- och utdataport kan ha ett underordnadt **beskrivningselement** som anger den text som visas när du håller muspekaren över porten i maskininlärningsgränssnittet.

**Regler för hamnar:**

* Maximalt antal **in- och utdataportar** är 8 för varje.

### <a name="input-elements"></a>Indataelement
Med indataportar kan du skicka data till din R-funktion och arbetsyta. De **datatyper** som stöds för indataportar är följande: 

**DataTabell:** Den här typen skickas till R-funktionen som en data.frame. Faktum är att alla typer (till exempel CSV-filer eller ARFF-filer) som stöds av Machine Learning och som är kompatibla med **DataTable** konverteras automatiskt till en data.frame. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

**Id-attributet** som är associerat med varje **DataTable-indataport** måste ha ett unikt värde och det här värdet måste matcha motsvarande namngivna parameter i R-funktionen.
Valfria **DataTable-portar** som inte skickas som indata i ett experiment har värdet **NULL** skickat till R-funktionen och valfria zip-portar ignoreras om indata inte är ansluten. Attributet **isOptional** är valfritt för både **DataTable-** och **Zip-typerna** och är *falskt* som standard.

**Dragkedja:** Anpassade moduler kan acceptera en zip-fil som indata. Den här ingången packas upp i din funktions R-arbetskatalog

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

För anpassade R-moduler behöver ID:et för en Zip-port inte matcha några parametrar för R-funktionen. Detta beror på att zip-filen automatiskt extraheras till R arbetskatalogen.

**Indataregler:**

* Värdet för **id-attributet** **för indataelementet** måste vara ett giltigt R-variabelnamn.
* Värdet för **id-attributet** **för indataelementet** får inte vara längre än 64 tecken.
* Värdet för **namnattributet** **för indataelementet** får inte vara längre än 64 tecken.
* Innehållet i **beskrivningselementet** får inte vara längre än 128 tecken
* Värdet för **typattributet** **för indataelementet** måste vara *Zip* eller *DataTable*.
* Värdet för **attributet isOptional** **för indataelementet** krävs inte (och är *falskt* som standard när det inte anges). men om det anges, måste det vara *sant* eller *falskt*.

### <a name="output-elements"></a>Utdataelement
**Standardutmatningsportar:** Utdataportar mappas till returvärdena från R-funktionen, som sedan kan användas av efterföljande moduler. *DataTable* är den enda standardutdataporttyp som för närvarande stöds. (Stöd *till elever* och *transformeringar* är förestående.) En *DataTable-utdata* definieras som:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

För utdata i anpassade R-moduler behöver värdet för **id-attributet** inte motsvara något i R-skriptet, men det måste vara unikt. För en enskild modulutgång måste returvärdet från R-funktionen vara en *data.frame*. För att kunna mata ut mer än ett objekt av en datatyp som stöds måste lämpliga utdataportar anges i XML-definitionsfilen och objekten måste returneras som en lista. Utdataobjekten tilldelas utdataportar från vänster till höger, vilket återspeglar i vilken ordning objekten placeras i den returnerade listan.

Om du till exempel vill ändra modulen **Anpassa till rader** för att mata ut de ursprungliga två datauppsättningarna, *datauppsättningen1* och *datauppsättning2*, förutom den nya kopplade datauppsättningen, *datauppsättningen*(i en ordning, från vänster till höger, som: *datauppsättning*, *datauppsättning1*, *datauppsättning2*), definierar du sedan utdataportarna i filen CustomAddRows.xml enligt följande:

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


Och returnera listan över objekt i en lista i rätt ordning i "CustomAddRows.R":

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Utdata för visualisering:** Du kan också ange en utdataport av typen *Visualisering*, som visar utdata från R-grafikenheten och konsolutdata. Den här porten är inte en del av R-funktionens utgång och stör inte ordningen på de andra utdataporttyperna. Om du vill lägga till en visualiseringsport i de anpassade modulerna lägger du till ett **utdataelement** med värdet *Visualisering* för dess **typattribut:**

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Utdataregler:**

* Värdet för **id-attributet** **för utdataelementet** måste vara ett giltigt R-variabelnamn.
* Värdet för **id-attributet** **för utdataelementet** får inte vara längre än 32 tecken.
* Värdet för **namnattributet** **för utdataelementet** får inte vara längre än 64 tecken.
* Värdet för **typattributet** **för utdataelementet** måste vara *Visualisering*.

### <a name="arguments"></a>Argument
Ytterligare data kan skickas till R-funktionen via modulparametrar som definieras i elementet **Argument.** Dessa parametrar visas i fönstret längst till höger i maskininlärningsgränssnittet när modulen är markerad. Argument kan vara någon av de typer som stöds eller så kan du skapa en anpassad uppräkning när det behövs. I likhet **med elementen Portar** kan **argumentelement** ha ett valfritt **beskrivningselement** som anger den text som visas när du håller musen över parameternamnet.
Valfria egenskaper för en modul, till exempel defaultValue, minValue och maxValue, kan läggas till i alla argument som attribut till ett **egenskapselement.** Giltiga egenskaper för **egenskapselementet** beror på argumenttypen och beskrivs med argumenttyperna som stöds i nästa avsnitt. Argument med egenskapen **isOptional** inställd på **"true"** kräver inte att användaren anger ett värde. Om ett värde inte anges i argumentet skickas inte argumentet till startpunktsfunktionen. Argument för startpunktsfunktionen som är valfri måste hanteras explicit av funktionen, t.ex. Ett valfritt argument kommer bara att framtvinga andra argumentbegränsningar, dvs.
Precis som med indata och utdata är det viktigt att var och en av parametrarna har unika ID-värden som är associerade med dem. I vårt snabbstartsexempel var den associerade id/parametern *swap*.

### <a name="arg-element"></a>Arg element
En modulparameter definieras med hjälp av det **underordnade elementet Arg** i avsnittet **Argument** i XML-definitionsfilen. Precis som med de underordnade elementen i avsnittet **Portar** definierar ordningen på parametrarna i avsnittet **Argument** layouten som påträffas i användarupplevelsen. Parametrarna visas uppifrån och ned i användargränssnittet i samma ordning som de definieras i XML-filen. De typer som stöds av Machine Learning för parametrar visas här. 

**int** – en heltalstyp (32-bitars) typparameter.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Valfria egenskaper:* **min**, **max**, **standard** och **isOptional**

**dubbel** – en dubbeltypsparameter.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Valfria egenskaper:* **min**, **max**, **standard** och **isOptional**

**bool** – en boolesk parameter som representeras av en kryssruta i UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Valfria egenskaper:* **standard** - falskt om det inte anges

**sträng**: en standardsträng

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Valfria egenskaper:* **standard** och **isOptional**

**ColumnPicker**: en kolumnvalsparameter. Den här typen återges i användarupplevelsen som kolumnväljare. **Egenskapselementet** används här för att ange ID för den port från vilken kolumner är markerade, där målporttypen måste vara *DataTable*. Resultatet av kolumnvalet skickas till R-funktionen som en lista med strängar som innehåller de markerade kolumnnamnen. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Obligatoriska egenskaper:* **portId** - matchar ID för ett indataelement med typen *DataTable*.
* *Valfria egenskaper:*
  
  * **allowedTypes** - Filtrerar de kolumntyper som du kan välja mellan. Giltiga värden inkluderar: 
    
    * Numerisk
    * Boolean
    * Kategoriska
    * String
    * Label (Etikett)
    * Funktion
    * Poäng
    * Alla
  * **standard** - Giltiga standardval för kolumnväljaren inkluderar: 
    
    * Inget
    * NumericFeature (numericFeature)
    * Numerisktmärke
    * Numeriskt poäng
    * Numeriskt
    * Booleskfeature
    * Boolesktlabel
    * BooleanScore (BooleanScore)
    * BooleanAll
    * Kategoriskfeatur
    * Kategorisklabel
    * Kategoriskpoäng
    * KategoriskAll
    * StringFeature (Stråk)
    * StringLabel ( Strängalabel)
    * StringScore (Stränga)
    * SträngAll
    * AllLabel (alla år)
    * AllaFeature
    * AllScore (alla)
    * Alla

**Listruta:** en användarspecificerad uppräknad lista (listruta). Listrutan anges i **egenskapselementet** med hjälp av ett **artikelelement.** **Id:t** för varje **artikel** måste vara unikt och en giltig R-variabel. Värdet för **namnet** på ett **objekt** fungerar både som den text som du ser och värdet som skickas till funktionen R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Valfria egenskaper:*
  * **standard** - Värdet för standardegenskapen måste motsvara ett ID-värde från ett av **objektelementen.**

### <a name="auxiliary-files"></a>Hjälpfiler
Alla filer som placeras i din anpassade modul ZIP-fil kommer att vara tillgängliga för användning under körningstiden. Alla katalogstrukturer som finns bevaras. Det innebär att filinköp fungerar på samma sätt lokalt och i körningen av Azure Machine Learning Studio (klassiskt). 

> [!NOTE]
> Observera att alla filer extraheras till "src" katalog så att alla sökvägar bör ha "src /" prefix.
> 
> 

Anta till exempel att du vill ta bort alla rader med NAs från datauppsättningen och även ta bort eventuella dubblettrader innan du skriver ut dem till CustomAddRows, och du har redan skrivit en R-funktion som gör det i en fil RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Du kan köpa hjälpfilen RemoveDupNARows.R i funktionen CustomAddRows:

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

Ladda sedan upp en zip-fil som innehåller "CustomAddRows.R", "CustomAddRows.xml" och "RemoveDupNARows.R" som en anpassad R-modul.

## <a name="execution-environment"></a>Körningsmiljö
Körningsmiljön för R-skriptet använder samma version av R som modulen **Kör R-skript** och kan använda samma standardpaket. Du kan också lägga till ytterligare R-paket till din anpassade modul genom att inkludera dem i det anpassade zip-paketet för modul. Bara ladda dem i ditt R-skript som du skulle i din egen R-miljö. 

**Begränsningar av körningsmiljön** inkluderar:

* Icke-beständigt filsystem: Filer som skrivs när den anpassade modulen körs sparas inte över flera körningar av samma modul.
* Ingen nätverksåtkomst

