---
title: Snabbstartsguide för R språket för Machine Learning | Microsoft Docs
description: Använd den här R programming självstudiekursen att komma igång snabbt med R-språk med Azure Machine Learning Studio för att skapa en lösning för prognosmodellen.
keywords: Snabbstart, r språk, r programmeringsspråk, r programming självstudiekursen
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 99a3a0fd-b359-481a-b236-66868deccd96
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 97107bb5ca1a598906cac9adbf508b2d15668e7d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227248"
---
# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning"></a>Snabbstartssjälvstudier till R-programmeringsspråket för Azure Machine Learning

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introduktion
Den här snabbstartsguide hjälper dig att snabbt börja utöka Azure Machine Learning med hjälp av R-programmeringsspråket. Den här R programming kursen för att skapa, testa och köra R-koden i Azure Machine Learning. När du arbetar igenom kursen skapar du en fullständig prognosmodellen lösning med hjälp av R-språk i Azure Machine Learning.  

Microsoft Azure Machine Learning innehåller många kraftfulla machine learning och data manipulation moduler. Kraftfulla R språk har beskrivits som lingua franca av analytics. Lyckligtvis kan analytics och data manipulation i Azure Machine Learning utökas med hjälp av R. Den här kombinationen ger skalbarhet och enkelhet för distribution av Azure Machine Learning med flexibilitet och djupgående analys av R.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="forecasting-and-the-dataset"></a>Prognoser och datauppsättningen
Prognoser är en mycket anställda och ganska användbart analytiska metod. Vanliga användningsområden mellan förutsäga försäljning när objekt bestämma optimal inventering nivåer att förutsäga makroekonomiska variabler. Prognoser görs vanligtvis med tiden serie modeller.

Tid series-data är data som värden har ett index över tid. Tid indexet kan vara Normal, t.ex. varje månad eller varje minut eller oregelbundna. En tidsseriemodell baseras på tidpunkt series-data. R programmeringsspråket innehåller ett flexibelt ramverk och omfattande analytics för tid series-data.

I den här snabbstartsguide kommer arbeta med California mjölkproduktion och priser data. Dessa data innehåller månatliga information om produktion av flera mejeriprodukter och priset för mjölkfett, en vanlig prestandamått.

De data som används i den här artikeln, tillsammans med R-skript kan vara [hämtas här](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/blob/master/studio-samples/cadairydata.csv). Dessa data har ursprungligen syntetiskt från information som är tillgänglig från University of Wisconsin på https://dairymarkets.com.

### <a name="organization"></a>Organisation
Vi kommer att gå igenom flera steg som du lär dig att skapa, testa och köra analytics och data manipulation R-koden i Azure Machine Learning-miljö.  

* Först ska vi titta närmare grunderna i R-språk i Azure Machine Learning Studio-miljön.
* Vi vidare sedan diskutera olika aspekter av i/o för data, R-koden och grafik i Azure Machine Learning-miljö.
* Vi kommer sedan att skapa den första delen av vår prognosmodellen lösning genom att skapa koden för Datarensning och omvandling.
* Med våra data förberedd kommer vi att utföra en analys av korrelationer mellan flera variabler i vår datauppsättning.
* Slutligen skapar vi en säsongsbaserade prognosmodellen tidsseriemodell för mjölkproduktion.

## <a id="mlstudio"></a>Interagera med R språk i Machine Learning Studio
Det här avsnittet tar dig igenom grunderna interagerar med R programmeringsspråk i Machine Learning Studio-miljön. R-språket tillhandahåller ett kraftfullt verktyg för att skapa anpassade analytics och data manipulation moduler i Azure Machine Learning-miljön.

Jag använder RStudio för att utveckla, testa och felsöka R-koden i liten skala. Den här koden är sedan Klipp ut och klistra in i en [köra R-skriptet] [ execute-r-script] modul i Machine Learning Studio är redo att köras.  

### <a name="the-execute-r-script-module"></a>Modulen köra R-skriptet
I Machine Learning Studio R-skript körs inom den [köra R-skriptet] [ execute-r-script] modul. Ett exempel på den [köra R-skriptet] [ execute-r-script] modul i Machine Learning Studio illustreras i bild 1.

 ![R programmeringsspråket: köra R Script modul har valts i Machine Learning Studio][1]

*Bild 1. Machine Learning Studio-miljön visar modulen köra R-skriptet som valts.*

Titta på bild 1 och nu ska vi titta på några av de viktigaste delarna av Machine Learning Studio-miljön för att arbeta med den [köra R-skriptet] [ execute-r-script] modul.

* Modulerna i experimentet visas i rutan i mitten.
* Den övre delen av den högra rutan innehåller ett fönster för att visa och redigera din R-skript.  
* Den nedre delen av högra fönstret visar några egenskaper för den [köra R-skriptet][execute-r-script]. Du kan visa loggar fel och utdata genom att klicka på lämplig punkter med det här fönstret.

Vi kommer förstås att diskutera den [köra R-skriptet] [ execute-r-script] mer detaljerat i resten av det här dokumentet.

När du arbetar med funktioner för komplexa R rekommenderar jag att redigera, testa och felsöka i RStudio. Precis som med alla programvaruutveckling utöka din kod inkrementellt och testa på små enkla testfall. Sedan Klipp ut och klistra in dina funktioner i fönstret R-skriptet i den [köra R-skriptet] [ execute-r-script] modul. Den här metoden kan du utnyttja både RStudio integrerad utvecklingsmiljö (IDE) och kraften i Azure Machine Learning.  

#### <a name="execute-r-code"></a>Kör R-kod
Alla R-koden i den [köra R-skriptet] [ execute-r-script] modulen kommer att köras när du kör experimentet genom att klicka på den **kör** knappen. När körningen har slutförts är markerat visas på den [köra R-skriptet] [ execute-r-script] ikon.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Defensiva R kodning för Azure Machine Learning
Om du utvecklar R-koden för, exempelvis en webbtjänst via Azure Machine Learning, bör du definitivt planera hur koden ska hantera indata oväntade data och undantag. Om du vill behålla tydlighetens skull har jag inte med mycket form kontrollerar eller undantagshantering i de flesta kodexempel visas. Men när vi går vidare får jag du några exempel på funktioner med hjälp av R: s funktion för undantagshantering.  

Om du behöver en mer komplett behandling av R-undantagshantering rekommenderar du läst tillämpliga avsnitt av boken av Wickham som anges i [bilaga B - ytterligare läsning](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Felsöka och testa R i Machine Learning Studio
Om du vill upprepar, bör jag du testa och felsöka din R-koden i liten skala i RStudio. Det finns dock fall där du behöver spåra R kodproblem i den [köra R-skriptet] [ execute-r-script] sig själv. Dessutom är det bra idé att kontrollera resultaten i Machine Learning Studio.

Utdata från körning av R-koden och på plattformen Azure Machine Learning finns främst i output.log. Ytterligare information som visas i error.log.  

Om ett fel uppstår i Machine Learning Studio när du kör din R-kod, ska din första erhåller titta på error.log. Den här filen kan innehålla användbara felmeddelanden som hjälper dig att förstå och åtgärda felet. Om du vill visa error.log klickar du på **visa felloggen** på den **egenskapsrutan** för den [köra R-skriptet] [ execute-r-script] som innehåller felet.

Till exempel kört R följande kod, med ett odefinierat variabel y i en [köra R-skriptet] [ execute-r-script] modulen:

    x <- 1.0
    z <- x + y

Den här koden kan inte köra, vilket resulterar i ett feltillstånd. Klicka på **visa felloggen** på den **egenskapsrutan** ger det som visas i bild 2 visas.

  ![Felmeddelande popup][2]

*Figur 2. Popup-felmeddelandet.*

Det verkar som om vi behöver titta i output.log till R felmeddelande visas. Klicka på den [köra R-skriptet] [ execute-r-script] och klicka sedan på den **visa output.log** objektet på den **egenskapsrutan** till höger. Öppnar ett nytt webbläsarfönster och visas nedan.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Det här felmeddelandet innehåller inga överraskningar och tydligt identifierar problemet.

Du kan skriva dessa värden till output.log filen om du vill kontrollera värdet för alla objekt i R. Regler för att undersöka objektets värden är i stort sett desamma som för en interaktiv R-session. Till exempel om du skriver ett variabelnamn på en rad, skrivs värdet för objektet till filen output.log.  

#### <a name="packages-in-machine-learning-studio"></a>Paket i Machine Learning Studio
Azure Machine Learning innehåller över 350 förinstallerade R språkpaketen. Du kan använda följande kod i den [köra R-skriptet] [ execute-r-script] modul för att hämta en lista över de förinstallerade paket.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Om du inte förstår den sista raden i den här koden för tillfället, läsa på. I resten av det här dokumentet diskuteras omfattande använda R i Azure Machine Learning-miljö.

### <a name="introduction-to-rstudio"></a>Introduktion till RStudio
RStudio är en mycket vanlig IDE för R. Jag använder RStudio för redigering, testa och felsöka vissa av R-koden som används i den här snabbstartsguide. När R-koden har testats och är klara kan du bara klippa ut och klistra in från redigeraren RStudio till en Machine Learning Studio [köra R-skriptet] [ execute-r-script] modul.  

Om du inte har programmeringsspråket R installerat på den stationära datorn rekommenderar jag du göra det nu. Kostnadsfri nedladdning av språk med öppen källkod R är tillgängliga på den omfattande R Arkiv nätverk (CRAN) på [ http://www.r-project.org/ ](http://www.r-project.org/). Det finns hämtningsbara filer för Windows, Mac OS x och Linux/UNIX. Välj en närliggande spegling och följ instruktionerna för hämtning. Dessutom innehåller CRAN en mängd användbara analytics och data manipulation paket.

Om du har använt RStudio, bör du hämta och installera skrivbordsversionen. Du hittar RStudio hämtningsbara filer för Windows, Mac OS x och Linux/UNIX på http://www.rstudio.com/products/RStudio/. Följ anvisningarna som visas och installerar RStudio på den stationära datorn.  

En självstudiekurs introduktion till RStudio är tillgänglig på https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

Jag ange ytterligare information om hur du använder RStudio i [bilaga A][appendixa].  

## <a id="scriptmodule"></a>Hämta data till och från modulen köra R-skriptet
I det här avsnittet diskuteras hur du hämta data i och ut ur den [köra R-skriptet] [ execute-r-script] modul. Granskar vi hur du hanterar olika datatyper läsa in och ut från den [köra R-skriptet] [ execute-r-script] modul.

Den fullständiga koden för det här avsnittet finns i zip-filen som du hämtade tidigare.

### <a name="load-and-check-data-in-machine-learning-studio"></a>Läsa in och kontrollera data i Machine Learning Studio
#### <a id="loading"></a>Läsa in datauppsättningen
Vi startar genom att läsa in den **csdairydata.csv** filen till Azure Machine Learning Studio.

* Starta din Azure Machine Learning Studio-miljö.
* Klicka på **+ ny** längst ned till vänster på skärmen, Välj **Dataset**.
* Välj **från lokal fil**, och sedan **Bläddra** att välja filen.
* Kontrollera att du har valt **generiska CSV-fil med rubriken (.csv)** som typen för datauppsättningen.
* Klicka på kryssmarkeringen.
* När dataset har överförts du bör se den nya datamängden genom att klicka på den **datauppsättningar** fliken.  

#### <a name="create-an-experiment"></a>Skapa ett experiment
Nu när vi har vissa data i Machine Learning Studio måste vi skapa ett experiment om du vill göra analys.  

* Klicka på **+ ny** vid lägre vänster och välj **Experiment**, sedan **tomt Experiment**.
* Du kan kalla experimentet genom att välja och ändra, den **Experiment skapas på...**  rubrik överst på sidan. Till exempel ändra den till **CA Mejeri Analysis**.
* Till vänster på sidan experiment, expandera **sparade datauppsättningar**, och sedan **Mina datauppsättningar**. Du bör se den **cadairydata.csv** som du överfört tidigare.
* Dra och släpp den **csdairydata.csv dataset** på experimentet.
* I den **Sök experimentera objekt** rutan överst till vänster, typen [köra R-skriptet][execute-r-script]. Modulen som visas i listan visas.
* Dra och släpp den [köra R-skriptet] [ execute-r-script] modul till din utbud.  
* Ansluta utdata från den **csdairydata.csv dataset** längst till vänster inkommande (**Dataset1**) för den [köra R-skriptet][execute-r-script].
* **Glöm inte att klicka på 'Spara'!**  

Nu experimentet bör se ut ungefär som bild 3.

![Kanada Mejeri analysen experimentera med datauppsättningen och köra R-skriptet modulen][3]

*Bild 3. Kanada Mejeri analysen experimentera med datauppsättningen och köra R-skriptet modulen.*

#### <a name="check-on-the-data"></a>Kontrollera data
Låt oss ta en titt på de data som vi har lästs in i vårt experiment. I experiment, klicka på utdata från den **cadairydata.csv dataset** och välj **visualisera**. Du bör se något liknande bild 4.  

![Sammanfattning av cadairydata.csv datauppsättningen][4]

*Bild 4. Sammanfattning av cadairydata.csv dataset.*

I den här vyn visas mycket användbar information. Vi kan se de första flera raderna i denna dataset. Om vi Markera en kolumn visas i statistik-delen mer information om kolumnen. Raden funktionen visar exempelvis oss vilka datatyper som Azure Machine Learning Studio tilldelats kolumnen. Med en snabb ser ut så här är en bra förstånd kontroll innan vi börjar utföra allvarliga arbete.

### <a name="first-r-script"></a>Första R-skriptet
Nu ska vi skapa ett enkelt första R-skript om du vill experimentera med i Azure Machine Learning Studio. Jag har skapat och testat följande skript i RStudio.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    str(cadairydata)
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Nu behöver jag överföra skriptet till Azure Machine Learning Studio. Jag kan bara klippa och klistra in. Men i det här fallet överför jag R-skriptet via en zip-fil.

### <a name="data-input-to-the-execute-r-script-module"></a>Datainmatning till modulen köra R-skriptet
Låt oss ta en titt på indata till det [köra R-skriptet] [ execute-r-script] modul. I det här exemplet ska vi läsa California mjölkproducerande data till den [köra R-skriptet] [ execute-r-script] modul.  

Det finns tre möjliga indata för den [köra R-skriptet] [ execute-r-script] modul. Du kan använda någon av eller alla dessa indata, beroende på ditt program. Det är också perfekt rimligt att använda ett R-skript som tar inga indata alls.  

Nu ska vi titta på var och en av dessa indata som kommer från vänster till höger. Du kan se namnen på alla indata genom att placera markören över indata och läsa tooltip.  

#### <a name="script-bundle"></a>Skript-paket
Skriptet paket indata kan du skicka innehållet i en zip-fil i [köra R-skriptet] [ execute-r-script] modul. Du kan använda något av följande kommandon för att läsa innehållet i zip-filen till din R-kod.

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [!NOTE]
> Azure Machine Learning behandlar filer i zip som om de finns i src / directory, så du behöver prefixet filnamn med namnet på den här katalogen. Om zip innehåller filerna som till exempel `yourfile.R` och `yourData.rdata` i roten av zip, skulle du lösa dessa som `src/yourfile.R` och `src/yourData.rdata` när du använder `source` och `load`.
> 
> 

Vi diskuterade redan inläsning datauppsättningar i [inläsning datauppsättningen](#loading). När du har skapat och testat R-skriptet som visas i föregående avsnitt, gör du följande:

1. Spara R-skriptet i en. R-fil. Jag anropa min skriptfilen ”simpleplot. R ”. Här är innehållet.
   
        ## Only one of the following two lines should be used
        ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
        ## If in RStudio, use the second line with read.csv()
        cadairydata <- maml.mapInputPort(1)
        # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
        str(cadairydata)
        pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
        ## The following line should be executed only when running in
        ## Azure Machine Learning Studio
        maml.mapOutputPort('cadairydata')
2. Skapa en zip-fil och kopiera skriptet till den här zipfilen. I Windows, kan du högerklicka på filen och välja **skicka till**, och sedan **komprimerad mapp**. Detta skapar en ny zip-fil som innehåller ”simpleplot. R ”-fil.
3. Lägg till filen till den **datauppsättningar** i Machine Learning Studio, anger vilken typ som **zip**. Du bör nu se zip-filen i dina datauppsättningar.
4. Dra och släpp zip-filen från **datauppsättningar** till den **ML Studio arbetsytan**.
5. Ansluta utdata från den **zip data** ikon för att den **skript paket** indata för den [köra R-skriptet] [ execute-r-script] modulen.
6. Typ av `source()` funktionen med namn på din zip-filen i kodfönstret för den [köra R-skriptet] [ execute-r-script] modul. Min om jag skrivit `source("src/simpleplot.R")`.  
7. Kontrollera att du klickar på **spara**.

När dessa steg har slutförts i [köra R-skriptet] [ execute-r-script] modulen utför R-skriptet i zip-filen när du kör experimentet. Nu experimentet bör se ut ungefär som bild 5.

![Experimentera med komprimerade R-skriptet][6]

*Bild 5. Experimentera med komprimerade R-skriptet.*

#### <a name="dataset1"></a>Dataset1
Du kan skicka en rektangulär tabell med data till din R-kod med hjälp av Dataset1 indata. I vår enkelt skript i `maml.mapInputPort(1)` funktionen läser data från port 1. Dessa data sedan tilldelas ett dataframe variabelnamn i koden. I vår enkelt skript utförs den första raden i koden tilldelningen.

    cadairydata <- maml.mapInputPort(1)

Kör experimentet genom att klicka på den **kör** knappen. När körningen har slutförts klickar du på den [köra R-skriptet] [ execute-r-script] modul och klicka sedan på **Visa logg för utdata** i egenskapsfönstret. En ny sida ska visas i webbläsaren visar innehållet i filen output.log. När du rullar bör du se något som liknar följande.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Längre bort från sidans är mer detaljerad information om de kolumner som ser ut ungefär så här.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

De här resultaten returneras är främst som förväntat med 228 observationer och 9 kolumner i dataframe. Vi kan se kolumnnamnen, datatypen R och ett exempel på varje kolumn.

> [!NOTE]
> Den här samma utskriften är lättillgängliga från R enheten utdata från den [köra R-skriptet] [ execute-r-script] modul. Utdata för diskuteras de [köra R-skriptet] [ execute-r-script] modul i nästa avsnitt.  
> 
> 

#### <a name="dataset2"></a>Dataset2
Beteendet för Dataset2 indata är identisk med Dataset1. Med den här indata skickar du en andra rektangulär tabell med data i R-koden. Funktionen `maml.mapInputPort(2)`, med argumentet 2 används till att överföra data.  

### <a name="execute-r-script-outputs"></a>Köra R-skriptet utdata
#### <a name="output-a-dataframe"></a>Utdata för en dataframe
Du kan spara innehållet i ett R-dataframe som en rektangulär tabell genom resultatet Dataset1 porten med hjälp av den `maml.mapOutputPort()` funktion. Detta görs i vår enkelt R-skript med följande rad.

    maml.mapOutputPort('cadairydata')

När du har kört experimentet, klicka på utdataporten resultatet Dataset1 och klicka sedan på **visualisera**. Du bör se något liknande bild 6.

![Visualisering av utdata från California mjölkproducerande data][7]

*Bild 6. Visualisering av utdata från California mjölkproducerande data.*

Den här utdatan ser ut identiskt med indata, precis som förväntades.  

### <a name="r-device-output"></a>R-enheter
Enheten utdata från den [köra R-skriptet] [ execute-r-script] modulen innehåller meddelanden och grafik. Både standard utdata och standardfel meddelanden från R skickas till utdataporten R-enhet.  

Om du vill visa resultatet R-enhet på porten och klicka sedan på **visualisera**. Vi kan se standardutdata och standardfel från R-skriptet på bild 7.

![Standardutdata och standardfel port R-enhet][8]

*Bild 7. Standardutdata och standardfel port R-enhet.*

Rulla nedåt vi se grafik utdata från våra R-skriptet i figur 8.  

![Grafik utdata från porten som R-enhet][9]

*Figur 8. Grafik utdata från porten R-enhet.*  

## <a id="filtering"></a>Filtrering av data och omvandling
I det här avsnittet ska vi utföra vissa grundläggande data filtrering och transformeringsåtgärder på California mjölkproducerande data. I slutet av det här avsnittet har vi data i ett format som är lämpliga för att skapa en modell för analys.  

I det här avsnittet kommer vi mer specifikt utföra flera gemensamma data rensning och omvandling av uppgifter: Skriv omvandling ger filtrering på dataframes, lägga till nya beräknade kolumner, och värdet transformationer. Den här bakgrunden hjälper dig att hantera flera av påträffades i verkliga problem.

Den fullständiga R-koden för det här avsnittet finns i zip-filen som du hämtade tidigare.

### <a name="type-transformations"></a>Typen omvandlingar
Nu när vi kan läsa California mjölkproducerande data in R-koden i den [köra R-skriptet] [ execute-r-script] modul, som vi behöver säkerställa att data i kolumnerna som har rätt typ och format.  

R är ett dynamiskt skrivna språk, vilket innebär att datatyperna är tvingas från varandra efter behov. Datatyperna atomic i R innehåller numeriska logiska och tecknet. Faktor-typ används för att lagra compactly kategoriska data. Du hittar mer information om datatyper i referenser i [bilaga B - ytterligare läsa](#appendixb).

När tabelldata läses in i R från en extern källa, är det alltid en bra idé att kontrollera de resulterande typerna i kolumnerna. Du kanske vill typtecknet i en kolumn, men i många fall detta kommer att visas som faktor eller vice versa. I annat fall en kolumn som du tror måste vara representeras numeriskt av teckendata, t.ex. Peka nummer '1,23' i stället för 1,23 som ett flyttal.  

Det är lyckligtvis enkelt att konvertera en typ till en annan, så länge mappningen är möjliga. Exempelvis kan du konvertera 'Nevada' till ett numeriskt värde, men du kan konvertera den till en faktor (kategoriska variabel). Ett annat exempel kan du konvertera numeriska 1 till tecknet '1' eller en faktor.  

Syntaxen för någon av de här konverteringarna är enkel: `as.datatype()`. Dessa funktioner för konvertering av typen inkluderar följande.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Titta på datatyperna för kolumnerna vi indata i föregående avsnitt: alla kolumner är av typen numeriska, förutom kolumnen ”månad”, vilket är av typen tecken. Vi konvertera det till en faktor och testa resultaten.  

Jag har tagit bort raden som skapats scatterplot matrisen och lägga till en rad, konvertera kolumnen ”månad” till en faktor. I mitt experiment kommer jag bara klipp ut och klistra in R-koden i kodfönstret för den [köra R-skriptet] [ execute-r-script] modul. Du kan också uppdatera zip-filen och överföra den till Azure Machine Learning Studio, men det tar flera steg.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(cadairydata$Month)
    str(cadairydata) # Check the result
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Vi kör den här koden och titta på utdataloggen för R-skriptet. Relevanta data från loggen visas i bild 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Bild 9. Sammanfattning av dataframe med en faktor variabel.*

Typen för månaden ska nu stå '**faktor med 14 nivåer**'. Detta är ett problem Eftersom det finns endast 12 månader år. Du kan också kontrollera som typen i **visualisera** för resultatet Dataset porten är '**Categorical**'.

Problemet är att kolumnen ”månad” inte har har ett kodat systematiskt. I vissa fall kallas en månad April och i andra det förkortas april. Vi kan lösa detta problem genom att minska strängen som ska 3 tecken. Koden för nu ser ut ungefär så här:

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Kör experimentet och Visa logg för utdata. Ett förväntat resultat visas i bild 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Bild 10. Sammanfattning av dataframe med rätt antal faktor nivåer.*

Vår faktor variabeln har nu önskade 12 nivåer.

### <a name="basic-data-frame-filtering"></a>Grundläggande data ram filtrering
R dataframes stöder kraftfulla filtreringsfunktioner. Datauppsättningar kan vara deluppsättning med hjälp av logiska filter på rader eller kolumner. I många fall måste avancerade filtervillkor utföras. Referenser i [bilaga B - ytterligare läsa](#appendixb) innehåller omfattande exempel på filtrering dataframes.  

Det finns en bit för filtrering ska vi gör i vår datauppsättning. Om du tittar på kolumnerna i cadairydata dataframe visas två onödiga kolumner. Den första kolumnen innehåller bara ett radnummer som inte är användbar. Den andra kolumnen Year.Month, innehåller redundant information. Vi kan enkelt utesluta dessa kolumner med hjälp av följande R-koden.

> [!NOTE]
> Hädanefter i det här avsnittet I kommer bara att visa ytterligare kod jag lägger till i den [köra R-skriptet] [ execute-r-script] modul. Jag lägger till varje ny rad **innan** den `str()` funktion. Jag kan använda den här funktionen för att verifiera min resulterar i Azure Machine Learning Studio.
> 
> 

Jag lägger till följande rad R-koden i den [köra R-skriptet] [ execute-r-script] modul.

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Kör den här koden i experimentet och kontrollera resultatet från logg för utdata. Dessa resultat visas i figur 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figur 11. Sammanfattning av dataframe med två kolumner som har tagits bort.*

Goda nyheter! Vi hämta ett förväntat resultat.

### <a name="add-a-new-column"></a>Lägg till en ny kolumn
Om du vill skapa modeller för tid serien kommer att vara praktiskt att ha en kolumn som innehåller månader sedan starten av tidsserier. Vi skapar en ny kolumn 'Month.Count'.

För att ordna koden som skapar vi vårt första enkla funktionen `num.month()`. Vi kommer sedan att tillämpa den här funktionen om du vill skapa en ny kolumn i dataframe. Den nya koden är som följer.

    ## Create a new column with the month count
    ## Function to find the number of months from the first
    ## month of the time series
    num.month <- function(Year, Month) {
      ## Find the starting year
      min.year  <- min(Year)

      ## Compute the number of months from the start of the time series
      12 * (Year - min.year) + Month - 1
    }

    ## Compute the new column for the dataframe
    cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

Nu kör uppdaterade experimentet och visa resultat med hjälp av logg för utdata. Dessa resultat visas i figur 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figur 12. Sammanfattning av dataframe med ytterligare kolumnen.*

Det verkar som att allt fungerar. Vi har den nya kolumnen med de förväntade värdena i vår dataframe.

### <a name="value-transformations"></a>Värdet omvandlingar
I det här avsnittet utför vi några enkla omformningar på värdena i några av våra dataframe kolumner. R-språket stöder nästan godtyckligt värde transformationer. Referenser i [bilaga B - ytterligare läsning](#appendixb) innehåller omfattande exempel.

Om du tittar på värden i sammanfattningen av våra dataframe bör du se något udda här. Flera glass än mjölk produceras i Kalifornien? Nej, förstås eftersom detta är meningslös tråkigt som detta faktum kan inte till några av oss glass älskare. Enheterna är olika. Priset är i enheter om oss pund mjölk är i enheter om 1 miljon USA pund, glass är i enheter om 1 000 oss gallon och Keso är i enheter om 1 000 USA pund. Under förutsättning att glass väger om 6.5 pund per gallon, göra vi enkelt multiplikation om du vill konvertera dessa värden så att de är på 1 000 pund lika enheter.

För vår prognosmodellen använder vi en Multiplicerande modell för trend och säsongsbaserade justering av dessa data. En logg omvandling kan vi använda en linjär modell, förenkla den här processen. Vi kan använda loggen omvandling i samma funktion där multiplikatorn används.

I följande kod I definierar en ny funktion `log.transform()`, och tillämpas på rader som innehåller numeriska värden. R `Map()` funktionen används för att tillämpa den `log.transform()` fungerar som de markerade kolumnerna för dataframe. `Map()` liknar `apply()` men gör att mer än en lista över argument till funktionen. Observera att en lista över multiplikatorer lämnar det andra argumentet för den `log.transform()` funktion. Den `na.omit()` funktion används som en bit för rensning så vi inte har saknas eller är odefinierad värden i dataframe.

    log.transform <- function(invec, multiplier = 1) {
      ## Function for the transformation, which is the log
      ## of the input value times a multiplier

      warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                           "ERROR: Arguments to function log.transform must be greate than zero",
                           "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                           "ERROR: Invalid time seies value encountered in function log.transform"
                           )

      ## Check the input arguments
      if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
      if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
      if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

      ## Wrap the multiplication in tryCatch
      ## If there is an exception, print the warningmessage to
      ## standard error and return NA
      tryCatch(log(multiplier * invec),
               error = function(e){warning(warningmessages[4]); NA})
    }


    ## Apply the transformation function to the 4 columns
    ## of the dataframe with production data
    multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
    cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

    ## Get rid of any rows with NA values
    cadairydata <- na.omit(cadairydata)  

Det finns en bit sker i den `log.transform()` funktionen. De flesta av den här koden söker efter potentiella problem med argument eller hantering av undantag som fortfarande kan uppstå under beräkningarna. Endast några få rader med den här koden genomföra de nödvändiga beräkningarna.

Målet med defensiva programmering är att förhindra fel på en enskild funktion som förhindrar bearbetningen fortsätter. Ett abrupt fel i en tidskrävande analys kan vara ganska frustrerande för användare. För att undvika den här situationen måste du valt Standard returvärden som begränsar skada nedströms bearbetning. Ett meddelande skapas också att varna användarna om att något är fel.

Om du inte är för defensiva programmering i R kan den här koden verka lite överväldigande. Jag tar dig igenom de viktigaste stegen:

1. En vektor med fyra meddelanden har definierats. Dessa meddelanden används för att kommunicera information om några av de möjliga fel och undantag som kan uppstå med koden.
2. Jag returnera ett värde na för varje fall. Det finns många möjligheter som kan ha färre sidoeffekter. Jag kan returnera en vektor för nollor eller ursprungliga inkommande vektorn, till exempel.
3. Kontroller körs på argument till funktionen. Om ett fel upptäcks ett standardvärde returneras i varje fall och ett meddelande som genereras av den `warning()` funktion. Jag använder `warning()` snarare än `stop()` som denna avslutas körning och exakt vad jag försöker undvika. Observera att jag har skrivit koden i samma format som det här fallet en funktionell metod som visat sig komplexa och otydligt.
4. Log-beräkningar placeras i `tryCatch()` så att undantag som inte orsakar en abrupt stopp för bearbetning. Utan `tryCatch()` de flesta fel som skapats av R funktioner resultera i en stoppsignal som utför precis så.

Kör den här koden för R i experimentet och ta en titt på utskriften i filen output.log. Du ser nu omvandlade värdena för fyra kolumner i loggen, som visas i figur 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figur 13. Sammanfattning av transformerade värdena i dataframe.*

Vi kan se värdena som har transformerats. Nu mjölkproduktion överskrider alla andra mejeriprodukter produktion, återkalla att vi nu titta på en logaritmisk skala.

Nu våra data rensas och vi är klara för vissa modellering. Titta på visualiseringen sammanfattning för resultatet Dataset-utdata från våra [köra R-skriptet] [ execute-r-script] modulen, ser du kolumnen 'Månad' 'Categorical' med 12 unika värden igen, precis som vi vill.

## <a id="timeseries"></a>Tid serie objekt och korrelation analys
I det här avsnittet kommer vi utforska några grundläggande R tid serie objekt och analysera samband mellan vissa variabler. Vårt mål är att spara en dataframe som innehåller informationen på flera beräkningstider pairwise korrelation.

Den fullständiga R-koden för det här avsnittet finns i zip-filen som du hämtade tidigare.

### <a name="time-series-objects-in-r"></a>Tid serie objekt i R
Serien är en serie datavärden som indexeras av tid som redan nämnts, tid. R tid serie objekt används för att skapa och hantera tid indexet. Det finns flera fördelar med att använda tid serie objekt. Tid serie objekt för att frigöra från många detaljer för att hantera tid index serievärden som är inkapslade i objektet. Dessutom kan tid serie objekt du använda många tid serie metoder för att rita upp, skriva ut modellering osv.

Klassen POSIXct tid serien används ofta och är relativt enkel. Den här tidsserier klass åtgärder tid från början epok, 1 januari 1970. I det här exemplet ska vi använda POSIXct tid serie objekt. Andra vanliga objektklasser för R tid serien omfattar zoo och xts, extensible tidsserier.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Tid serien objektet exempel
Nu sätter vi igång med våra exempel. Dra och släpp en **nya** [köra R-skriptet] [ execute-r-script] modul i experimentet. Ansluta utdataporten Dataset1 resultatet av den befintliga [köra R-skriptet] [ execute-r-script] modul till Dataset1 inkommande port för den nya [köra R-skriptet] [ execute-r-script] modul.

Som jag gjorde första exempel när vi går genom exempel vid vissa tidpunkter visar jag endast inkrementella ytterligare kodraderna R i varje steg.  

#### <a name="reading-the-dataframe"></a>Läsa dataframe
Som ett första steg bör vi läses in en dataframe och se till att vi får det förväntade resultatet. Följande kod ska göra jobbet.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

Kör nu experimentet. Logg över formen köra R-skriptet ska se ut som figur 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Figur 14. Sammanfattning av dataframe i modulen köra R-skriptet.*

Dessa data är av den förväntade typer och format. Observera att kolumnen 'Månad' är av typen faktor och det förväntade antalet nivåer.

#### <a name="creating-a-time-series-object"></a>Skapa en serie tidsobjekt
Vi behöver lägga till en serie tidsobjekt i vår dataframe. Ersätt den aktuella koden med följande, vilket lägger till en ny kolumn i klassen POSIXct.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Nu finns i loggfilen. Det bör se ut figur 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figur 15. Sammanfattning av dataframe med en serie tidsobjekt.*

Vi kan se från som den nya kolumnen har i själva verket klassen POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Utforska och omvandla data
Låt oss utforska några variabler i denna dataset. En matris med scatterplot är ett bra sätt att skapa en titt på. Jag ersätta den `str()` funktionen i den föregående R-koden med följande rad.

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Kör den här koden och se vad som händer. Området genereras på porten R-enhet bör se ut som bild 16.

![Scatterplot matris för valda variabler][17]

*Bild 16. Scatterplot matris för valda variabler.*

Det finns vissa odd-looking strukturen i relationerna mellan dessa variabler. Detta inträffar kanske från trender i data och det faktum att vi inte har standardiserats variablerna.

### <a name="correlation-analysis"></a>Korrelation analys
Om du vill utföra analyser av korrelation måste både Frigör trender och standardisera variablerna. Vi kan bara använda R `scale()` funktion, vilket både datacenter och skalas variabler. Den här funktionen kan också snabbare. Men vill jag visa ett exempel på defensiva programing i R.

Den `ts.detrend()` funktionen nedan utför båda av dessa åtgärder. Följande två rader med kod Frigör trend data och standardisera värdena.

    ts.detrend <- function(ts, Time, min.length = 3){
      ## Function to de-trend and standardize a time series

      ## Define some messages if they are NULL  
      messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                    'ERROR: ts.detrend requires argument ts to be of type numeric',
                    paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                    'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                    'ERROR: Detrend regression has failed in ts.detrend',
                    'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
      )
      # Create a vector of zeros to return as a default in some cases
      zerovec  <- rep(length(ts), 0.0)

      # The input arguments are not of the same length, return ts and quit
      if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

      # If the ts is not numeric, just return a zero vector and quit
      if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

      # If the ts is too short, just return it and quit
      if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

      ## Check that the Time variable is of class POSIXct
      if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

      ## De-trend the time series by using a linear model
      ts.frame  <- data.frame(ts = ts, Time = Time)
      tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
               error = function(e){warning(messages[5]); zerovec})

      tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
                 ts <- ts/stdev},
                error = function(e){warning(messages[6]); zerovec})

      ts
    }  
    ## Apply the detrend.ts function to the variables of interest
    df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

    ## Plot the results to look at the relationships
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

Det finns en bit sker i den `ts.detrend()` funktionen. De flesta av den här koden söker efter potentiella problem med argument eller hantering av undantag som fortfarande kan uppstå under beräkningarna. Endast några få rader med den här koden genomföra de nödvändiga beräkningarna.

Vi har redan beskrivs ett exempel på defensiva programmering i [värdet transformationer](#valuetransformations). Båda beräkning block placeras i `tryCatch()`. För vissa fel är det klokt att returnera den ursprungliga inkommande Vectorn och i andra fall måste du återgår en vector nollor.  

Observera att den linjära regressionen för Frigör trender är en tid serien regression. Ge prognoser variabeln är en serie tidsobjekt.  

En gång `ts.detrend()` definieras vi använda den till variabler av intresse för vår dataframe. Vi måste använda den resulterande listan som skapats av `lapply()` till dataframe data med hjälp av `as.data.frame()`. På grund av defensiva aspekter av `ts.detrend()`, det gick inte att bearbeta en av variablerna hindrar inte rätt bearbetningen av de andra.  

Den sista raden med kod skapar en pairwise scatterplot. När du har kört R-koden är resultatet av scatterplot visas i bild 17.

![Pairwise scatterplot tidsseries Frigör daglig och standardiserad][18]

*Figur 17. Pairwise scatterplot tidsseries Frigör daglig och standardiserad.*

Du kan jämföra dessa resultat för att de som visas i bild 16. Vi kan se mycket mindre struktur i relationerna mellan dessa variabler med trenden tas bort och variablerna standardiserade.

Koden för att beräkna korrelationer som R ccf objekt är som följer.

    ## A function to compute pairwise correlations from a
    ## list of time series value vectors
    pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
      ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
    }

    ## A list of the pairwise indices
    corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

    ## Compute the list of ccf objects
    cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

    cadairycorrelations

Kör den här koden genererar loggen visas i bild 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Bild 18. Lista över ccf objekt från pairwise korrelation analys.*

Det finns en correlation-värdet för varje fördröjning. Ingen av dessa värden för korrelation är tillräckligt stor för att vara betydande. Vi kan därför ingå att vi kan modellen varje variabel oberoende av varandra.

### <a name="output-a-dataframe"></a>Utdata för en dataframe
Vi har beräknats pairwise korrelationer som en lista över R ccf objekt. Detta innebär lite problem som utdataporten resultatet Dataset verkligen kräver en dataframe. Dessutom ccf objekt i sin tur är en lista och vi vill bara värdena i det första elementet i den här listan korrelationer på olika beräkningstider.

Följande kod extraherar fördröjning värden från listan över ccf objekt som själva listor.

    df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

    c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
    r.names  <- c("Corr Cot Cheese - Ice Cream",
                  "Corr Cot Cheese - Milk Prod",
                  "Corr Cot Cheese - Fat Price",
                  "Corr Ice Cream - Mik Prod",
                  "Corr Ice Cream - Fat Price",
                  "Corr Milk Prod - Fat Price")

    ## Build a dataframe with the row names column and the
    ## correlation data frame and assign the column names
    outframe <- cbind(r.names, df.correlations)
    colnames(outframe) <- c.names
    outframe


    ## WARNING!
    ## The following line works only in Azure Machine Learning
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

Den första raden i koden är helt lätt och förklaras kan hjälpa dig att förstå den. Arbeta inifrån och ut har vi följande:

1. Den '**[[**-operator med argumentet'**1**' väljer vektorn i samband med beräkningstider från det första elementet i listan över ccf.
2. Den `do.call()` funktion gäller den `rbind()` över elementen i listan returneras av `lapply()`.
3. Den `data.frame()` funktionen tvingar resultatet som produceras av `do.call()` till en dataframe.

Observera att namnen på raden är i en kolumn i dataframe. Gör så bevarar raden namn när de utdata från den [köra R-skriptet][execute-r-script].

Kör koden skapar utdata som visas i figur 19 när jag **visualisera** utdata med resultatet Dataset-port. Raden namnen är i första kolumnen som avsett.

![Resultaten utdata från korrelation analys][20]

*Bild 19. Utdata från korrelation analysen resultat.*

## <a id="seasonalforecasting"></a>Tid serien exempel: när prognoser
Våra data är nu i ett formulär som är lämplig för analys och vi gjort bedömningen att det finns inga betydande samband mellan variablerna. Nu ska vi gå vidare och skapa en tidsserie prognoser modellen. Den här modellen kommer vi prognos California mjölkproduktion för 12 månader från 2013.

Vår prognosmodellen har två komponenter, en komponent som trend och när komponenten. Fullständig prognosen är produkten av dessa två komponenter. Den här typen av modellen kallas en Multiplicerande modell. Alternativet är en additiva modell. Vi har gjort en logg omvandling till variabler av intresse, vilket gör den här analysen tractable.

Den fullständiga R-koden för det här avsnittet finns i zip-filen som du hämtade tidigare.

### <a name="creating-the-dataframe-for-analysis"></a>Skapa dataframe för analys
Starta genom att lägga till en **nya** [köra R-skriptet] [ execute-r-script] modul i experimentet. Ansluta den **resultatet Dataset** utdata från den befintliga [köra R-skriptet] [ execute-r-script] modul till den **Dataset1** indata för den nya modulen. Resultatet bör se ut ungefär 20 bild.

![Experimentera med den nya köra R-skriptet modulen som lagts till][21]

*Figur 20. Experimentera med den nya köra R-skriptet modulen som lagts till.*

Som med korrelation analysen vi precis slutfört måste vi lägga till en kolumn med en serie tidsobjekt POSIXct. Följande kod görs bara detta.

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

Kör den här koden och titta på loggen. Resultatet bör se ut som bild 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figur 21. En sammanfattning av dataframe.*

Vi har det här resultatet redo att börja vår analys.

### <a name="create-a-training-dataset"></a>Skapa en datauppsättning för träning
Vi behöver skapa en datauppsättning för träning med dataframe konstrueras. Dessa data tas alla observationer utom de senaste 12 årets 2013, vilket är vår testdata. Följande kod delmängder av dataframe och skapar områden av mejeriprodukter produktions- och variabler. Jag skapa områden av fyra produktion och pris variabler. En anonym funktion används för att definiera vissa förstärker för ritytans och sedan iterera över lista över de andra två argument med `Map()`. Om du tänker som en för loop skulle har arbetat bra här, är korrekta. Men eftersom R är ett funktionellt språk jag visar du en funktionell metod.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

Kör koden genererar serien tid serien ritas från R enheten utdata som visas i figur 22. Observera att tidsaxeln i enheter av datum, en bra fördel av tiden serien ritas metod.

![Första gången serien områden i Kalifornien mjölkproducerande produktions- och data](./media/r-quickstart/unnamed-chunk-161.png)

![Andra av tid serien områden i Kalifornien mjölkproducerande produktions- och data](./media/r-quickstart/unnamed-chunk-162.png)

![Tredje tid serien områden av California mjölkproducerande produktions- och data](./media/r-quickstart/unnamed-chunk-163.png)

![Fjärde tid serien områden av California mjölkproducerande produktions- och data](./media/r-quickstart/unnamed-chunk-164.png)

*Figur 22. Tid serien områden av California mjölkproduktion och price data.*

### <a name="a-trend-model"></a>En modell för trend
Att ha skapat en serie tidsobjekt och har haft en titt på data kan börja att konstruera en trend modell för California mjölk produktionsdata. Vi kan göra detta med en tid serien regression. Det är dock tydligt från området som vi behöver mer än en lutning och fånga upp för att modellera observerade trenden i utbildning data korrekt.

Liten skala data får kommer jag skapa modell för trender i RStudio klipp ut och klistra in den resulterande modellen i Azure Machine Learning. RStudio ger en interaktiv miljö för den här typen av interaktiv analys.

Som ett första försöket försöker jag en polynom regression befogenheter upp till 3. Det finns en verklig risk för anpassning över dessa typer av modeller. Därför är det bäst att undvika hög ordning villkoren. Den `I()` funktionen hindrar beslutsträdets tolkning av innehållet (tolkar innehållet 'som är') och du kan skriva en bokstavligt tolkad funktion i en regression formel.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

Detta genererar följande.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Från P värden (Pr (> | t |)) i utdata, kan vi se att kvadraten termen inte kanske är viktiga. Jag använder den `update()` funktionen för att ändra den här modellen genom att släppa kvadraten termen.

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

Detta genererar följande.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Detta ser bättre ut. Alla villkor har betydelse. Dock 2e-16-värdet är standardvärdet och bör inte vidtas för allvarligt.  

Förstånd test, se tid serien ritning California mjölkproducerande data med trend kurvan visas. Jag har lagt till följande kod i Azure Machine Learning [köra R-skriptet] [ execute-r-script] modellen (inte RStudio) att skapa modellen och göra en rityta. Resultatet visas i figur 23.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![California mjölk produktionsdata med trend modell visas](./media/r-quickstart/unnamed-chunk-18.png)

*Figur 23. California mjölk produktionsdata med trend modell visas.*

Det verkar som trend-modell passar data ganska bra. Dessutom verkar det inte vara bevis av överdrivet passning som udda wiggles i modellen kurvan.  

### <a name="seasonal-model"></a>När modellen
Med en trend modell i hand som vi behöver push och inkludera säsongsbaserade effekter. Vi använder årets månad som en dummy variabel i den linjära modellen för att avbilda effekten per månad. Observera att när du införa faktor variabler i en modell skärningspunkten inte måste beräknas. Om du inte gör detta formeln anges över och R kommer släpper du en av de önskade faktorerna men behålla skärningspunkt termen.

Eftersom vi har en tillfredsställande trend modell kan vi använda den `update()` funktionen för att lägga till de nya villkoren i den befintliga modellen. -1 i uppdateringen formeln utelämnar skärningspunkt termen. Om du fortsätter i RStudio för tillfället:

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

Detta genererar följande.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Vi kan se att modellen inte längre har en skärningspunkt term och är 12 betydande månad faktorer. Detta är exakt vad vi vill se.

Vi behöver kontrollera en annan tid serien ritytans California mjölkproducerande data att se hur väl när modellen fungerar. Jag har lagt till följande kod i Azure Machine Learning [köra R-skriptet] [ execute-r-script] att skapa modellen och göra en rityta.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Kör den här koden i Azure Machine Learning ger området som visas i figur 24.

![California mjölkproduktion med modellen inklusive säsongsbaserade effekter](./media/r-quickstart/unnamed-chunk-20.png)

*Figur 24. California mjölkproduktion med modellen inklusive säsongsbaserade effekter.*

Anpassa till de data som visas i figur 24 är ganska uppmuntra. Både trenden och när effekten (månatliga variation) ser rimliga.

Som en annan kontroll av vår modell ska vi ta en titt på residualer. Följande kod beräknar de förväntade värdena från våra två modeller, beräknar residualer för när modellen och ritar dessa residualer för utbildning-data.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

Av kvarvarande området illustreras i bild 25.

![Residualer av när modellen för utbildning-data](./media/r-quickstart/unnamed-chunk-21.png)

*Bild 25. Residualer av när modellen för utbildning-data.*

Dessa residualer leta rimliga. Det finns inga särskilda struktur, utom effekten av 2008-2009 nedgång, som vår modell ingen hänsyn tas till särskilt väl.

Området som visas i bild 25 är användbart för att upptäcka eventuella tid beroende mönster i residualer. Beräkna och rita restvärden jag använde explicit metoden placerar residualer i tid ordning i området. Om du å andra sidan hade funktion `milk.lm$residuals`, skulle inte ha varit området i tid ordning.

Du kan också använda `plot.lm()` att skapa en serie av diagnostiska områden.

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Den här koden genererar en serie av diagnostiska områden som visas i bild 26.

![Första av diagnostiska områden för när modellen](./media/r-quickstart/unnamed-chunk-221.png)

![Andra av diagnostiska områden för när modellen](./media/r-quickstart/unnamed-chunk-222.png)

![Tredje av diagnostiska områden för när modellen](./media/r-quickstart/unnamed-chunk-223.png)

![Fjärde av diagnostiska områden för när modellen](./media/r-quickstart/unnamed-chunk-224.png)

*Bild 26. Diagnostik ritas för när modellen.*

Det finns några hög inflytelserik punkter som anges i dessa områden, men inget att orsaka viktig för oss. Dessutom kan vi se från området Normal Q-Q att residualer är nära normalt distribuerade ett viktigt antagande för linjära modeller.

### <a name="forecasting-and-model-evaluation"></a>Prognosmodellen och modellen utvärdering
Det finns något för att slutföra vårt exempel. Vi behöver beräkna prognoser och mäta felet mot de faktiska data. Vår prognos blir för 12 månader från 2013. Vi kan beräkna ett fel mått för den här prognosen till de faktiska data som inte är del av vår datauppsättning för träning. Dessutom kan vi jämföra prestanda på träningsdata till 12 månader från testdata 18 år.  

Ett antal mått för att mäta prestanda i tid serie modeller. I vårt fall ska vi använda roten medelvärdet fyrkant (RMS)-fel. Följande funktion beräknar RMS-fel mellan två serier.  

    RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
      ## Function to compute the RMS error or difference between two
      ## series or vectors

      messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                    "ERROR: Input vector to function RMS.error is too short",
                    "ERROR: Input vectors to function RMS.error must be of same length",
                    "WARNING: Funtion rms.error has received invald input time series.")

      ## Check the arguments
      if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
        warning(messages[1])
        return(NA)}

      if(length(series1) < min.length) {
        warning(messages[2])
        return(NA)}

      if((length(series1) != length(series2))) {
           warning(messages[3])
        return(NA)}

      ## If is.log is TRUE exponentiate the values, else just copy
      if(is.log) {
        tryCatch( {
          temp1 <- exp(series1)
          temp2 <- exp(series2) },
          error = function(e){warning(messages[4]); NA}
        )
      } else {
        temp1 <- series1
        temp2 <- series2
      }

     ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute the RMS error in a dataframe
      tryCatch( {
        sqrt(sum((temp1 - temp2)^2) / length(temp1))},
        error = function(e){warning(messages[4]); NA})
    }

Med den `log.transform()` funktionen som beskrevs i avsnittet ”värdet transformationer” det finns en hel del kontroll och undantag recovery felkod i den här funktionen. Principerna anställda är samma. Arbetet utförs på två platser kapslas in i `tryCatch()`. De time series är först exponentiated, eftersom vi har arbetat med loggar av värden. Dessutom beräknas faktiska RMS-fel.  

Utrustad med en funktion för att mäta RMS-fel kan vi skapa och utdata en dataframe med RMS-fel. Vi innehåller villkoren för enbart trend modellen och fullständig modellen med säsongsbaserade faktorer. Följande kod gör jobbet med hjälp av två linjära modeller som vi har skapats.

    ## Compute the RMS error in a dataframe
    ## Include the row names in the first column so they will
    ## appear in the output of the Execute R Script
    RMS.df  <-  data.frame(
    rowNames = c("Trend Model", "Seasonal Model"),
      Traing = c(
      RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
      RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
      Forecast = c(
        RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
        RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
    )
    RMS.df

    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('RMS.df')

Kör den här koden skapar utdata som visas i bild 27 på utdataporten resultatet Dataset.

![Jämförelse av RMS-fel för modeller][26]

*Bild 27. Jämförelse av RMS-fel för modeller.*

Från de här resultaten returneras se att lägga till säsongsbaserade faktorer i modellen minskar RMS-fel avsevärt. För förstås, är RMS-fel för utbildning-data en flagga som är mindre än för prognosen.

## <a id="appendixa"></a>BILAGA A: Guide till RStudio
RStudio är ganska väl dokumenterat, så jag ger länkar till delarna av RStudio dokumentationen för att komma igång i den här bilagan.

1. Skapa projekt
   
   Du kan organisera och hantera din R-koden i projekt med hjälp av RStudio. I dokumentationen som använder projekt kan hittas på https://support.rstudio.com/hc/articles/200526207-Using-Projects.
   
   Jag rekommenderar att du följer de här anvisningarna och skapar ett projekt för R-kodexempel i det här dokumentet.  
2. Redigera och köra R-koden
   
   RStudio tillhandahåller en integrerad miljö för redigering och R kod körs. Dokumentation finns på https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.
3. Felsökning
   
   RStudio innehåller kraftfulla felsökningsfunktioner. Dokumentationen för de här funktionerna finns på https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.
   
   Brytpunkt felsökningsfunktioner dokumenteras i https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

## <a id="appendixb"></a>BILAGA B: Kan läsa
Självstudierna R programming beskriver grunderna om vad du behöver använda R-språk med Azure Machine Learning Studio. Om du inte är bekant med R är två introduktioner tillgängliga på CRAN:

* R för nybörjare av Emmanuel Paradis är ett bra ställe att börja på http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  
* En introduktion till R av W. N. Venables et. al. blir lite mer djupet på http://cran.r-project.org/doc/manuals/R-intro.html.

Det finns många böcker på R som kan hjälpa dig att komma igång. Här är några jag användbara:

* Bilder av R-programmering: en visning av statistiska programvara utformning av Norman Matloff är en utmärkt introduktion till programmering i R.  
* R Cookbook av Paul Teetor ger en problemet och lösningen metoden för att använda R.  
* R i praktiken av Robert Kabacoff är en annan användbar inledande bok. Den tillhörande snabb R-webbplatsen är en användbar resurs på http://www.statmethods.net/.
* R Inferno av Patrick Burns är en förstås humoristiskt bok som hanterar ett antal komplicerade och svår ämnen som kan uppstå när programmering i R. Boken är tillgängliga gratis http://www.burns-stat.com/documents/books/the-r-inferno/.
* Om du vill att en djupdykning i avancerade ämnen i R ta en titt på boken Avancerat R av Hadley Wickham. Online-versionen av den här boken är tillgängliga gratis http://adv-r.had.co.nz/.

En katalog med R tid serie paket finns i uppgiftsvyn CRAN för analys av tidsserier: http://cran.r-project.org/web/views/TimeSeries.html. Information om specifika tid serie objekt paket ska du referera till dokumentationen för paketet.

Boken inledande tidsserier med R av Paul Cowpertwait och Andrew Metcalfe innehåller en introduktion till R för analys av tidsserier. Många fler teoretisk texter innehåller R-exempel.

Vissa bra internet-resurser:

* DataCamp: DataCamp Lär R bekvämt i webbläsaren med video erfarenheter och kodning övningarna. Det finns interaktiva självstudier om den senaste R-teknik och paket. Självstudiekursen ledigt interaktiva R på https://www.datacamp.com/courses/introduction-to-r
* En guide i komma igång med R från Programiz https://www.programiz.com/r-programming
* En snabb genomgång R av Kelly Black från Clarkson University http://www.cyclismo.org/tutorial/R/
* 60 + R över resurser i http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/r-quickstart/fig1.png
[2]: ./media/r-quickstart/fig2.png
[3]: ./media/r-quickstart/fig3.png
[4]: ./media/r-quickstart/fig4.png
[5]: ./media/r-quickstart/fig5.png
[6]: ./media/r-quickstart/fig6.png
[7]: ./media/r-quickstart/fig7.png
[8]: ./media/r-quickstart/fig8.png
[9]: ./media/r-quickstart/fig9.png
[10]: ./media/r-quickstart/fig10.png
[11]: ./media/r-quickstart/fig11.png
[12]: ./media/r-quickstart/fig12.png
[13]: ./media/r-quickstart/fig13.png
[14]: ./media/r-quickstart/fig14.png
[15]: ./media/r-quickstart/fig15.png
[16]: ./media/r-quickstart/fig16.png
[17]: ./media/r-quickstart/fig17.png
[18]: ./media/r-quickstart/fig18.png
[19]: ./media/r-quickstart/fig19.png
[20]: ./media/r-quickstart/fig20.png
[21]: ./media/r-quickstart/fig21.png
[22]: ./media/r-quickstart/fig22.png

[26]: ./media/r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
