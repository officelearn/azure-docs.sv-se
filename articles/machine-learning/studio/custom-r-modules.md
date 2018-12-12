---
title: Definiera anpassade R-moduler – Azure Machine Learning Studio | Microsoft Docs
description: Det här avsnittet beskriver hur du skapar och distribuerar en anpassad R-modul i Azure Machine Learning. Den förklarar vad anpassade R-moduler är och vilka filer som används för att definiera dessa.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 6cbc628a-7e60-42ce-9f90-20aaea7ba630
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/29/2017
ms.openlocfilehash: 5cddc767b4652df6753cc57eb7305b46ec45e19d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098649"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio"></a>Definiera anpassade R-moduler för Azure Machine Learning Studio

Det här avsnittet beskriver hur du skapar och distribuerar en anpassad R-modul i Azure Machine Learning Studio. Den förklarar vad anpassade R-moduler är och vilka filer som används för att definiera dessa. Det illustrerar hur du skapar filer som definierar en modul och hur du registrerar modulen för distribution i en Machine Learning-arbetsyta. Element och attribut som används i definitionen för anpassad modul sedan beskrivs i detalj. Hur du använder extra funktioner och filer och flera utdata diskuteras också. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="what-is-a-custom-r-module"></a>Vad är en anpassad R-modul?
En **anpassad modul** är en användardefinierad modul som kan överföras till din arbetsyta och köras som en del av ett Azure Machine Learning-experiment. En **anpassad R-modul** är en anpassad modul som utför en användardefinierad R-funktion. **R** är ett programmeringsspråk för statistiska beräkningar och grafik som används av statistiker och dataexperter för att implementera algoritmer. R är för närvarande det enda språk som stöds i anpassade moduler, men stöd för ytterligare språk är schemalagd för framtida versioner.

Anpassade moduler har **förstklassig status** i Azure Machine Learning i den mening att de kan användas precis som andra moduler. De kan köras med andra moduler som ingår i publicerade experiment eller i visualiseringar. Du har kontroll över den algoritm som implementeras av modulen, indata och utdata-portarna som ska användas, modellering parametrar och andra olika körningsbeteenden. Ett experiment som innehåller anpassade moduler publiceras också i Azure AI-galleriet för att enkelt dela.

## <a name="files-in-a-custom-r-module"></a>Filer i en anpassad R-modul
En anpassad R-modul definieras av en .zip-fil som innehåller minst två filer:

* En **källfilen** som implementerar R-funktionen som exponeras av modulen
* En **XML-definitionsfilen** som beskriver gränssnittet anpassad modul

Ytterligare extra filer kan också inkluderas i ZIP-filen som innehåller funktioner som kan nås från anpassad modul. Det här alternativet beskrivs i den **argument** ingår i referensavsnittet **element i XML-definitionsfilen** följa snabbstarten-exempel.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Snabbstartsexemplet: definiera, paketera och registrera en anpassad R-modul
Det här exemplet illustrerar hur du konstruerar filer som krävs för en anpassad R-modul, paketera dem till en zip-fil och sedan registrera modulen i Machine Learning-arbetsyta. Exempel zip-paketet och exempel-filer kan hämtas från [hämta CustomAddRows.zip filen](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Källfilen
Studera exemplet med en **anpassad Lägg till rader** modul som ändrar standardimplementeringen av den **Lägg till rader** modulen används för att sammanfoga rader (observationer) från två datauppsättningar (dataramar). Standard **Lägg till rader** modulen lägger till rader i den andra inkommande datauppsättningen i slutet av den första datauppsättningen för indata med den `rbind` algoritmen. Den anpassade `CustomAddRows` funktionen accepterar två datauppsättningar på samma sätt, men även accepterar en parameter med booleska växlingsutrymme som en ytterligare indata. Om swap-parametern anges till **FALSKT**, samma datauppsättning som standardimplementeringen returneras. Men om parametern växling **SANT**, funktionen lägger till rader i första datauppsättningen för indata i slutet av andra datauppsättningen i stället. Den CustomAddRows.R-fil som innehåller implementeringen av R `CustomAddRows` funktionen exponeras av den **anpassad Lägg till rader** modulen har följande R-kod.

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
Att exponera det `CustomAddRows` funktion som en Azure Machine Learning-modul, en XML-definitionsfilen måste skapas för att ange hur **anpassad Lägg till rader** modul bör ser ut och fungerar. 

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


Det är viktigt att Observera att värdet för den **id** attribut för den **indata** och **Arg** element i XML-filen måste matcha parametern funktionsnamnen av R-kod i den CustomAddRows.R filen exakt: (*dataset1*, *dataset2*, och *växling* i det här exemplet). På samma sätt kan värdet för den **entryPoint** attributet för den **språk** elementet måste matcha namnet på funktionen i R-skriptet exakt: (*CustomAddRows* i det här exemplet) . 

Däremot den **id** attributet för den **utdata** element motsvarar inte alla variabler i R-skriptet. När mer än en utdata krävs bara returnera en lista från R-funktion med resultat som placeras *i samma ordning* som **utdata** element har deklarerats i XML-filen.

### <a name="package-and-register-the-module"></a>Paketera och registrera modulen
Spara dessa två filer som *CustomAddRows.R* och *CustomAddRows.xml* och komprimerar två filerna tillsammans i en *CustomAddRows.zip* fil.

Registrera dem i Machine Learning-arbetsyta genom att gå till din arbetsyta i Machine Learning Studio, klickar du på den **+ ny** knappen längst ned och väljer **modul -> från ZIP-PAKETET** att ladda upp den nya **Anpassad Lägg till rader** modulen.

![Ladda upp Zip](./media/custom-r-modules/upload-from-zip-package.png)

Den **anpassad Lägg till rader** modulen är nu klar att användas av Machine Learning-experiment.

## <a name="elements-in-the-xml-definition-file"></a>Element i XML-definitionsfilen
### <a name="module-elements"></a>Modulen element
Den **modulen** elementet används för att definiera en anpassad modul i XML-filen. Flera moduler kan definieras i en XML-fil med hjälp av flera **modulen** element. Varje modul i din arbetsyta måste ha ett unikt namn. Registrera en anpassad modul med samma namn som en befintlig anpassad modul och den ersätter den befintliga modulen med den nya. Anpassade moduler kan dock vara registrerade med samma namn som en befintlig Azure Machine Learning-modul. Om så de visas i den **anpassad** kategori av modulpaletten.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


I den **modulen** element, kan du ange två ytterligare valfria element:

* en **ägare** element som är inbäddad i modulen  
* en **beskrivning** element som innehåller text som visas i snabb hjälp för modulen och när du hovrar över modul i Machine Learning-Användargränssnittet.

Regler för tecken-begränsningar i modulen element:

* Värdet för den **namn** attribut i den **modulen** element får inte överstiga 64 tecken. 
* Innehållet i den **beskrivning** element får inte överstiga 128 tecken långt.
* Innehållet i den **ägare** element får inte överstiga 32 tecken.

En modul resultaten kan vara deterministisk eller nondeterministic.* * som standard, alla moduler som anses vara deterministisk. Det vill säga ska med en oföränderlig uppsättning indataparametrar och data i modulen returnera samma resultat eacRAND eller en functionh gången den körs. Med det här beteendet kan Azure Machine Learning Studio produktmiljö endast moduler som markerats som deterministisk om en parameter eller indata har ändrats. Returnerar de cachelagrade resultaten innehåller också mycket snabbare körning av experiment.

Det finns funktioner som är icke-deterministisk, till exempel RAND eller en funktion som returnerar aktuellt datum och tid. Om din modul använder en icke-deterministisk funktion, kan du ange att modulen är icke-deterministisk genom att ange den valfria **isDeterministic** attributet **FALSKT**. Detta garanterar att modulen ska köras på nytt när experimentet har körts, även om modulen som indata och parametrar inte har ändrats. 

### <a name="language-definition"></a>Definition av språk
Den **språk** elementet i XML-definitionsfilen för att ange anpassad modul-språk. R är för närvarande det enda språk som stöds. Värdet för den **sourceFile** attributet måste vara namnet på den R-fil som innehåller funktionen som ska anropas när modulen körs. Den här filen måste vara en del av zip-paketet. Värdet för den **entryPoint** attributet är namnet på den funktion som anropas och måste matcha en giltig funktion som definierats med i källfilen.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portar
Inkommande och utgående portarna för en anpassad modul har angetts i underordnade element av den **portar** delen av XML-definitionsfilen. Ordningen på de här elementen bestämmer layouten erfarna (UX) av användare. Den första underordnat **inkommande** eller **utdata** visas i den **portar** elementet i XML-filen blir vänster indataporten i Machine Learning-UX.
Varje indatakort och utdataporten kan ha en valfri **beskrivning** underordnat element som anger den text som visas när du placerar du pekaren över port i Machine Learning-Användargränssnittet.

**Portarna regler**:

* Maximalt antal **inkommande och utgående portar** är 8 för var och en.

### <a name="input-elements"></a>Inmatningselement
Inkommande portar kan du skicka data till din R-funktion och en arbetsyta. Den **datatyper** som stöds för inkommande portar är följande: 

**DataTable:** den här typen skickas till din R-funktion som en data.frame. I själva verket alla typer (till exempel CSV-filer eller ARFF filer) som stöds av Machine Learning och som är kompatibla med **DataTable** konverteras till en data.frame automatiskt. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Den **id** attribut som hör till var **DataTable** indataporten måste ha ett unikt värde och det här värdet måste matcha dess motsvarande med namnet parameter i din R-funktion.
Valfritt **DataTable** portar som inte skickas som indata i ett experiment har värdet **NULL** skickades till funktionen för R och valfritt zip portar ignoreras om indata inte är ansluten. Den **isOptional** attributet är valfritt för både den **DataTable** och **Zip** typer och är *FALSKT* som standard.

**ZIP:** anpassade moduler kan acceptera en zip-fil som indata. Denna indata är packat upp i arbetskatalogen R på din funktion

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

För anpassade R-moduler saknar id för en Zip-port matchar alla parametrar för funktionen R. Det beror på att zip-filen automatiskt ska extraheras till R-arbetskatalog.

**Inkommande regler:**

* Värdet för den **id** attributet för den **indata** elementet måste vara ett giltigt variabelnamn R.
* Värdet för den **id** attributet för den **indata** element får inte vara längre än 64 tecken.
* Värdet för den **namn** attributet för den **indata** element får inte vara längre än 64 tecken.
* Innehållet i den **beskrivning** element får inte vara längre än 128 tecken
* Värdet för den **typ** attributet för den **indata** elementet måste vara *Zip* eller *DataTable*.
* Värdet för den **isOptional** attributet för den **indata** elementet är inte obligatoriskt (och är *FALSKT* som standard om inget värde anges); men om detta anges måste det vara *SANT* eller *FALSKT*.

### <a name="output-elements"></a>Utdata-element
**Standard utgångsportar:** utgångsportar som är mappade till returvärden från din R-funktion som kan sedan användas av efterföljande moduler. *DataTable* är endast standardutdata porttyp som stöds för närvarande. (Stöd för *Learners* och *omvandlar* är kommande.) En *DataTable* utdata har definierats som:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

För utdata i anpassade R-moduler, värdet för den **id** attributet måste inte motsvarar något i R-skript, men det måste vara unikt. För en enda modulen utdata returvärdet från R-funktionen måste vara en *data.frame*. För att mata ut fler än ett objekt av en datatyp som stöds, de lämpliga utgångsportar måste anges i XML-definitionsfilen och objekt som behöver returneras som en lista. Utdata-objekt har tilldelats till utgångsportar från vänster till höger, vilket speglar den ordning som objekt som är placerade i den returnerade listan.

Exempel: Om du vill ändra den **anpassad Lägg till rader** modulen att mata ut de ursprungliga två datauppsättningarna *dataset1* och *dataset2*, förutom den nya domänanslutna datauppsättningen *datauppsättning*, (i en ordning, från vänster till höger, som: *datauppsättning*, *dataset1*, *dataset2*), sedan definierar utgående portar i den CustomAddRows.xml filen på följande sätt:

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


Och returnerar listan över objekt i en lista i rätt ordning i ”CustomAddRows.R”:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Visualisering utdata:** du kan också ange en utgångsport från typen *visualisering*, som visar utdata från R-grafik enhets- och konsolen utdata. Den här porten är inte en del av R-funktionsutdata och stör inte ordningen på de andra port utdatatyper. Lägg till en port för visualisering i anpassade moduler genom att lägga till en **utdata** element med ett värde av *visualisering* för dess **typ** attribut:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Utgående regler:**

* Värdet för den **id** attributet för den **utdata** elementet måste vara ett giltigt variabelnamn R.
* Värdet för den **id** attributet för den **utdata** element får inte vara längre än 32 tecken.
* Värdet för den **namn** attributet för den **utdata** element får inte vara längre än 64 tecken.
* Värdet för den **typ** attributet för den **utdata** elementet måste vara *visualisering*.

### <a name="arguments"></a>Argument
Ytterligare data kan skickas till funktionen R via modulparametrar som definieras i den **argument** element. Dessa parametrar visas i egenskapsfönstret för längst till höger i Machine Learning-Användargränssnittet när modulen har valts. Argument kan vara något av typerna som stöds eller skapa en anpassad uppräkning när det behövs. Liknar den **portar** element, **argument** element kan ha en valfri **beskrivning** element som anger den text som visas när du hovrar musen över den Parameternamnet.
Valfria egenskaper för en modul som standardvärde, minValue och maxValue kan läggas till ett argument som attribut till en **egenskaper** element. Giltiga egenskaper för den **egenskaper** element beror på vilken argumenttyp och beskrivs med stöds Argumenttyperna i nästa avsnitt. Argument med den **isOptional** egenskapen **”true”** inte kräver att användaren anger ett värde. Om ett värde inte har angetts för argumentet, skickas inte argumentet till funktionens startadress. Argumentet för funktionens startadress som är valfria måste du uttryckligen hanteras av funktionen, t.ex. tilldelade standardvärdet NULL i funktionsdefinitionen post punkt. Ett valfritt argument kommer bara att tillämpa de andra argumentet villkor, t.ex. min eller max, om ett värde har angetts av användaren.
Det är viktigt att var och en av parametrarna har unika id-värden som är associerade med dem precis som med in- och utdata. I vårt exempel Snabbstart associerade id /-parameter har *växling*.

### <a name="arg-element"></a>Arg-element
En modul-parameter har definierats med hjälp av den **Arg** underordnat element av den **argument** delen av XML-definitionsfilen. Precis som med de underordnade elementen i den **portar** avsnittet sorteringen av parametrarna i den **argument** avsnittet definierar layouten påträffades i UX. Parametrarna visas, uppifrån nedåt i Användargränssnittet i samma ordning som de definieras i XML-filen. De typer som stöds av Machine Learning för parametrar visas här. 

**int** – ett heltal (32-bitars) typparametern.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Valfria egenskaper*: **min**, **max**, **standard** och **isOptional**

**dubbla** – en typen double-parameter.

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



* *Valfria egenskaper*: **standard** -falskt om inte inställt

**sträng**: en vanlig sträng

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Valfria egenskaper*: **standard** och **isOptional**

**ColumnPicker**: parametern för val av en kolumn. Den här typen renderas i UX-Gränssnittet som en kolumnväljare. Den **egenskapen** elementet används här för att ange id för den port från vilken kolumner väljs, där porttyp måste vara *DataTable*. Resultatet av kolumnen skickas till funktionen R som en lista med strängar som innehåller namn på valda kolumnerna. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Egenskaper som krävs*: **portId** -matchar ID: t för ett indata-element med typen *DataTable*.
* *Valfria egenskaper*:
  
  * **allowedTypes** -filter som kolumnen skriver från vilken du kan välja. Giltiga värden är: 
    
    * numeriskt
    * Boolesk
    * Kategoriska
    * Sträng
    * Etikett
    * Funktion
    * Poäng
    * Alla
  * **standard** -giltig standardinställningarna för väljaren för kolumnen är: 
    
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

**Listrutan**: en användardefinierade uppräknade () listruta. Listrutan objekt anges inom den **egenskaper** element med hjälp av en **objekt** element. Den **id** för var och en **objekt** måste vara unikt och en giltig R-variabel. Värdet för den **namn** av en **objekt** fungerar både som den text som visas och värdet som skickades till funktionen R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Valfria egenskaper*:
  * **standard** -värdet för standardegenskapen måste motsvara med ett id-värde från en av de **objekt** element.

### <a name="auxiliary-files"></a>Extra filer
Alla filer som placeras i ZIP-filen för anpassade modulen ska vara tillgängliga för användning under körningstid. Alla finns katalogstrukturer bevaras. Det innebär att filen sourcing fungerar samma lokalt och i Azure Machine Learning-körning. 

> [!NOTE]
> Observera att alla filer extraheras till ”src'-katalogen så att alla sökvägar ska ha ' src /' prefix.
> 
> 

Anta exempelvis att du vill ta bort alla rader med NAs från datauppsättningen, och även ta bort alla dubblettrader innan du genererar i CustomAddRows och du redan har skrivit en R-funktion som gör detta i en fil RemoveDupNARows.R:

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

Ladda sedan upp en zip-fil som innehåller ”CustomAddRows.R', 'CustomAddRows.xml' och 'RemoveDupNARows.R” som en anpassad R-modul.

## <a name="execution-environment"></a>Körningsmiljö
Körningsmiljö för R-skriptet använder samma version av R som den **kör R-skript** modulen och kan använda samma standard-paket. Du kan också lägga till ytterligare R-paket till din anpassade modulen genom att inkludera dem i en anpassad modul zip-paketet. Bara läsa in dem i ditt R-skript som du använder i din egen R-miljö. 

**Begränsningar av körningsmiljö** omfattar:

* Icke-permanent filsystem: filerna som skrivits när modulen anpassade körs är inte beständiga mellan flera körningar av samma modul.
* Ingen nätverksåtkomst

