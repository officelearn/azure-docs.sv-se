---
Rubrik: Snabbstartssjälvstudier R språk titleSuffix: Beskrivning av Azure Machine Learning Studio: Använd den här programming R-självstudiekursen för att komma igång snabbt med R-språket med Azure Machine Learning Studio för att skapa en lösning för prognostisering.
tjänster: machine learning ms.service: machine learning ms.component: studio ms.topic: artikel

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro ms.date: 01/06/2017
---
# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning-studio"></a>Snabbstartssjälvstudier R-programmeringsspråket för Azure Machine Learning Studio

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introduktion
I den här kursen hjälper dig att snabbt börja utöka Azure Machine Learning med hjälp av R-programmeringsspråket. Den här kursen R programmeringsspråk för att skapa, testa och köra R-kod i Azure Machine Learning. När du har gått igenom kursen kommer du skapa en komplett lösning för prognostisering med hjälp av R-språket i Azure Machine Learning.  

Microsoft Azure Machine Learning innehåller många kraftfulla machine learning och data manipulation moduler. Kraftfulla R-språket har beskrivits som lingua franca analysens. Lyckligtvis kan manipulering av analyser och data i Azure Machine Learning utökas med hjälp av R. Den här kombinationen ger skalbarhet och enkel distribution av Azure Machine Learning med flexibilitet och djupgående analys av R.



### <a name="forecasting-and-the-dataset"></a>Prognoser och datauppsättningen
Prognoser är en mycket anställda och ganska användbart analytiska metod. Vanliga användningsområden sträcker sig från att förutsäga försäljning på säsongens objekt, avgör optimala lagernivåer, att förutsäga makroekonomiska variabler. Prognostisering görs normalt med time series-modeller.

Time series-data är data där värdena har ett index över tid. Tid indexet kan vara vanliga, t.ex. varje månad eller varje minut eller oregelbundet. En tidsseriemodell baseras på time series-data. R-programspråket innehåller ett flexibelt ramverk och omfattande analys för time series-data.

I den här snabbstartsguiden ska vi arbeta med Kalifornien mejeriproduktion och priser för data. Dessa data innehåller månatliga information på flera varaktiga konsumtionsvaror produktions- och priset för mjölkfett, ett benchmark-vanlig.

De data som används i den här artikeln, tillsammans med R-skript kan vara [hämtade här](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/blob/master/studio-samples/cadairydata.csv). Dessa data har ursprungligen syntetiskt från information som är tillgängliga från University of Wisconsin på https://dairymarkets.com.

### <a name="organization"></a>Organisation
Vi kommer att gå igenom flera steg som du lär dig att skapa, testa och köra analys- och manipulering av R-kod i Azure Machine Learning-miljö.  

* Först ska vi utforska grunderna i att använda R-språket i Azure Machine Learning Studio-miljön.
* Vi fortsätter sedan att diskutera olika aspekter av i/o för data, R-kod- och i Azure Machine Learning-miljö.
* Vi kommer sedan att skapa den första delen av vår prognostiseringslösningen genom att skapa koden för Datarensning och transformering.
* Med våra förberett ska vi köra en analys av korrelationer mellan flera variabler i vår datauppsättning.
* Slutligen skapar vi en säsongens prognosmodellen tidsseriemodell för mjölkproduktion.

## <a id="mlstudio"></a>Interagera med R-språket i Machine Learning Studio
Det här avsnittet tar dig igenom grunderna för att interagera med R-programspråket i Machine Learning Studio-miljön. R-språket tillhandahåller ett kraftfullt verktyg för att skapa anpassade analytics och data manipulation moduler i Azure Machine Learning-miljö.

Jag använder RStudio för att utveckla, testa och felsöka R-kod i liten skala. Den här koden är sedan åtgärderna klipp ut och klistra in i en [kör R-skript] [ execute-r-script] modul i Machine Learning Studio kan köras.  

### <a name="the-execute-r-script-module"></a>Modulen köra R-skript
Inom Machine Learning Studio, R-skript körs inom den [kör R-skript] [ execute-r-script] modulen. Ett exempel på den [kör R-skript] [ execute-r-script] modul i Machine Learning Studio illustreras i bild 1.

 ![R-programspråket: Kör R-skript-modulen som valts i Machine Learning Studio][1]

*Bild 1. Machine Learning Studio-miljön som visar modulen köra R-skript som valts.*

Titta på bild 1 och nu ska vi titta på några av de viktigaste delarna av Machine Learning Studio-miljön för att arbeta med den [kör R-skript] [ execute-r-script] modulen.

* Moduler i experimentet visas i den mittersta rutan.
* Den övre delen av den högra rutan innehåller ett fönster för att visa och redigera dina R-skript.  
* Den nedre delen av högra fönstret visar vissa egenskaper för den [kör R-skript][execute-r-script]. Du kan visa fel- och utdataloggar loggarna genom att klicka på lämplig punkter med det här fönstret.

Vi kommer naturligtvis att diskutera den [kör R-skript] [ execute-r-script] mer detaljerat i resten av det här dokumentet.

När du arbetar med avancerade funktioner för R, rekommenderar jag att redigera, testa och felsöka i RStudio. Utöka din kod stegvis precis som med alla programutveckling och testa det på små enkla testfall. Klipp och klistra in dina funktioner i fönstret för R-skript på den [kör R-skript] [ execute-r-script] modulen. Den här metoden kan du dra nytta av både RStudio integrated development environment (IDE) och kraften i Azure Machine Learning.  

#### <a name="execute-r-code"></a>Köra R-kod
Alla R-kod i den [kör R-skript] [ execute-r-script] modul som körs när du kör experimentet genom att klicka på den **kör** knappen. När körningen har slutförts markeras visas på den [kör R-skript] [ execute-r-script] ikon.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Skydden R kodning för Azure Machine Learning
Om du utvecklar R-kod för, exempelvis en webbtjänst med hjälp av Azure Machine Learning, bör du definitivt planera hur din kod så hanterar indata oväntade data och undantag. Om du vill behålla tydlighet har jag inte med mycket vägen kontrollerar eller undantagshantering i de flesta kodexempel som visas. Men när vi går vidare ger jag dig flera exempel på funktioner med hjälp av RS undantag för hantering av meddelandeströmmar.  

Om du behöver en fullständig behandling av R undantagshantering jag rekommenderar att du läst tillämpliga avsnitt av boken av Wickham som anges i [bilaga B - ytterligare läsning](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Felsöka och testa R i Machine Learning Studio
Jag vill rekommenderar jag att du testa och felsöka R-kod i liten skala i RStudio. Men det finns fall där du behöver spåra problem med R-kod i den [kör R-skript] [ execute-r-script] själva. Dessutom är det bra att kontrollera resultaten i Machine Learning Studio.

Utdata från körningen av din R-kod och på Azure Machine Learning-plattformen finns främst i output.log. Ytterligare information kan ses i error.log.  

Om ett fel uppstår i Machine Learning Studio när du kör ditt R-kod, bör din första erhåller vara att titta på error.log. Den här filen kan innehålla användbara felmeddelanden för att förstå och åtgärda felet. Om du vill visa error.log, klickar du på **visa felloggen** på den **egenskapsrutan** för den [kör R-skript] [ execute-r-script] som innehåller felet.

Till exempel jag körde följande R-kod med ett odefinierat variabeln y, i en [kör R-skript] [ execute-r-script] modulen:

    x <- 1.0
    z <- x + y

Den här koden kan inte köra, vilket resulterar i ett feltillstånd. När du klickar på **visa felloggen** på den **egenskapsrutan** producerar skärmen som visas i bild 2.

  ![Felmeddelande popup-fönster][2]

*Figur 2. Popup-felmeddelande.*

Det verkar som om vi behöver titta i output.log till R felmeddelande visas. Klicka på den [kör R-skript] [ execute-r-script] och klicka sedan på den **visa output.log** objektet på den **egenskapsrutan** till höger. Ett nytt webbläsarfönster öppnas och visas följande.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Det här felmeddelandet innehåller inga överraskningar och tydligt identifierar problemet.

Om du vill kontrollera värdet för alla objekt i R, kan du skriva ut dessa värden till filen output.log. Reglerna för att undersöka objektvärden är i stort sett desamma som för en interaktiv R-session. Om du skriver ett namn på variabel på en rad, till exempel skrivas värdet för objektet ut till output.log-filen.  

#### <a name="packages-in-machine-learning-studio"></a>Paket i Machine Learning Studio
Azure Machine Learning levereras med över 350 förinstallerade R språk-paket. Du kan använda följande kod i den [kör R-skript] [ execute-r-script] modul för att hämta en lista över de förinstallerade paket.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Läs vidare om du inte förstår den sista raden i den här koden för tillfället. I resten av det här dokumentet berättar vi stor utsträckning använda R i Azure Machine Learning-miljö.

### <a name="introduction-to-rstudio"></a>Introduktion till RStudio
RStudio är ett vanligt IDE för R. Jag använder RStudio för redigering, testning och felsökning på några av R-kod som används i den här snabbstartsguiden. När R-kod är testats och är klara kan du helt enkelt klipp ut och klistra in från RStudio-redigeraren i en Machine Learning Studio [kör R-skript] [ execute-r-script] modulen.  

Om du inte har programmeringsspråket R installerat på din stationära dator rekommenderar jag du göra det nu. Kostnadsfri nedladdning av R-språket med öppen källkod är tillgängliga på den omfattande R Archive Network (CRAN) på [ http://www.r-project.org/ ](http://www.r-project.org/). Det finns hämtningsbara filer för Windows, Mac OS x och Linux/UNIX. Välj en närliggande spegling och följ anvisningarna för hämtning. CRAN innehåller dessutom massor av användbara analys- och manipulering av paket.

Om du är nybörjare på RStudio, bör du hämta och installera skrivbordsversionen. Du hittar RStudio hämtningar för Windows, Mac OS x och Linux/UNIX på http://www.rstudio.com/products/RStudio/. Följ anvisningarna för att installera RStudio på din stationära dator.  

En självstudiekurs introduktion till RStudio är tillgänglig på https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

Jag ger ytterligare information om hur du använder RStudio i [bilaga A][appendixa].  

## <a id="scriptmodule"></a>Hämta data till och från modulen köra R-skript
I det här avsnittet diskuteras hur du hämtar data i och ut ur den [kör R-skript] [ execute-r-script] modulen. Vi kommer att granska hur du hanterar olika datatyper som läser in och ut ur den [kör R-skript] [ execute-r-script] modulen.

Den fullständiga koden för det här avsnittet är i zip-filen som du hämtade tidigare.

### <a name="load-and-check-data-in-machine-learning-studio"></a>Läsa in och kontrollera data i Machine Learning Studio
#### <a id="loading"></a>Läsa in datauppsättningen
Vi börjar genom att läsa in den **csdairydata.csv** -filen i Azure Machine Learning Studio.

* Starta Azure Machine Learning Studio-miljön.
* Klicka på **+ ny** längst ned till vänster på skärmen, Välj **datauppsättning**.
* Välj **från lokal fil**, och sedan **Bläddra** att välja filen.
* Kontrollera att du har valt **generiska CSV-fil med rubrik (.csv)** som typ för datauppsättningen.
* Klicka på kryssmarkeringen.
* När datauppsättningen har överförts, du bör se den nya datauppsättningen genom att klicka på den **datauppsättningar** fliken.  

#### <a name="create-an-experiment"></a>Skapa ett experiment
Nu när vi har några data i Machine Learning Studio, som vi behöver skapa ett experiment för att göra analysen.  

* Klicka på **+ ny** på den nedre vänstra och välj **Experiment**, sedan **tomt Experiment**.
* Du kan kalla experimentet genom att välja och ändra, den **Experiment skapas på...**  rubrik överst på sidan. Till exempel ändra den till **CA Mejeri Analysis**.
* Till vänster på sidan experiment Expandera **sparade datauppsättningar**, och sedan **Mina datauppsättningar**. Du bör se den **cadairydata.csv** som du laddade upp tidigare.
* Dra och släpp den **csdairydata.csv datauppsättning** till experimentet.
* I den **Search experimentera objekt** rutan överst till vänster, typ [kör R-skript][execute-r-script]. Modulen som visas i listan visas.
* Dra och släpp den [kör R-skript] [ execute-r-script] modulen till din utbud.  
* Anslut utdataporten för den **csdairydata.csv datauppsättning** till den vänstra indataporten (**Dataset1**) av den [kör R-skript][execute-r-script].
* **Glöm inte att klicka på ”Spara”!**  

I det här läget experimentet bör se ut ungefär som bild 3.

![CA: N Mejeri analysen experimentera med datauppsättningen och kör R-skript-modulen][3]

*Bild 3. CA: N Mejeri analysen experimentera med datauppsättningen och kör R-skript-modulen.*

#### <a name="check-on-the-data"></a>Kontrollera data
Låt oss ta en titt på de data som vi har läst in i vår experiment. I experimentet, klickar du på utdata från den **cadairydata.csv datauppsättning** och välj **visualisera**. Du bör se något som liknar bild 4.  

![Sammanfattning av cadairydata.csv datauppsättningen][4]

*Bild 4. Sammanfattning av cadairydata.csv datauppsättningen.*

Vi kan se mycket användbar information i den här vyn. Vi kan se flera första raderna i datauppsättningen. Om vi väljer en kolumn visar avsnittet statistik mer information om kolumnen. Till exempel visar funktionstyp raden oss vilka datatyper som Azure Machine Learning Studio som tilldelats kolumnen. Att ha en snabb inblick så här är en bra förstånd kontroll innan vi börjar utföra allvarligt arbete.

### <a name="first-r-script"></a>Första R-skript
Nu ska vi skapa ett enkelt första R-skript för att experimentera med i Azure Machine Learning Studio. Jag har skapat och testat följande skript i RStudio.  

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

Nu ska jag överföra det här skriptet till Azure Machine Learning Studio. Jag kan bara klipp ut och klistra in. Men i det här fallet överför jag min R-skript via en zip-fil.

### <a name="data-input-to-the-execute-r-script-module"></a>Datainmatning till modulen köra R-skript
Låt oss ta en titt på indata till den [kör R-skript] [ execute-r-script] modulen. I det här exemplet ska vi läsa Kalifornien mjölkproducerande data till den [kör R-skript] [ execute-r-script] modulen.  

Det finns tre möjliga indata för den [kör R-skript] [ execute-r-script] modulen. Du kan använda någon eller alla dessa indata, beroende på ditt program. Det är också perfekt rimligt att använda ett R-skript som tar inga indata alls.  

Vi tittar på var och en av dessa indata och gå från vänster till höger. Du kan se namnen på var och en av ingångarna genom att placera markören över indata och läsa knappbeskrivningen.  

#### <a name="script-bundle"></a>Skript-paket
Skriptet paketet indata gör att du kan skicka innehållet i en zip-fil i [kör R-skript] [ execute-r-script] modulen. Du kan använda något av följande kommandon för att läsa innehållet i zip-filen i din R-kod.

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [!NOTE]
> Azure Machine Learning behandlar filer i ZIP-filen som om de finns i src / directory, så du behöver som prefix i din filnamn med namnet på den här katalogen. Om ZIP-filen innehåller filerna som till exempel `yourfile.R` och `yourData.rdata` i roten av ZIP-filen du vill åtgärda dessa som `src/yourfile.R` och `src/yourData.rdata` när du använder `source` och `load`.
> 
> 

Vi redan pratat läser in datauppsättningar i [läser in datauppsättningen](#loading). När du har skapat och testat R-skriptet som visas i föregående avsnitt, gör du följande:

1. Spara R-skriptet i en. R-fil. Anropar jag min skriptfilen ”simpleplot. R ”. Här är innehållet.
   
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
2. Skapa en zip-fil och kopiera skriptet i den här zipfilen. På Windows, kan du högerklicka på filen och välja **skicka till**, och sedan **komprimerad mapp**. Detta skapar en ny zip-fil som innehåller ”simpleplot. R ”-fil.
3. Lägg till din fil till den **datauppsättningar** i Machine Learning Studio, anger du typ som **zip**. Du bör nu se zip-filen i dina datauppsättningar.
4. Dra och släpp zip-filen från **datauppsättningar** till den **ML Studio-arbetsytan**.
5. Anslut utdataporten för den **zip data** ikon för att den **skriptet paket** indata för den [kör R-skript] [ execute-r-script] modulen.
6. Skriv den `source()` funktionen med namnet på din zip-filen i code-fönstret för den [kör R-skript] [ execute-r-script] modulen. Min om jag har skrivit `source("src/simpleplot.R")`.  
7. Kontrollera att du klickar på **spara**.

När dessa steg har slutförts, den [kör R-skript] [ execute-r-script] modulen utför R-skriptet i zip-filen när experimentet har körts. I det här läget experimentet bör se ut ungefär som bild 5.

![Experimentera med hjälp av komprimerade R-skript][6]

*Bild 5. Experimentera med hjälp av komprimerade R-skript.*

#### <a name="dataset1"></a>Dataset1
Du kan skicka en rektangulär tabell med data till din R-kod med hjälp av Dataset1 indata. I vår enkla skript i `maml.mapInputPort(1)` funktionen läser data från port 1. Dessa data tilldelas sedan en dataram variabelnamn i din kod. I vår enkla skript utför den första raden med kod tilldelningen.

    cadairydata <- maml.mapInputPort(1)

Kör experimentet genom att klicka på den **kör** knappen. När körningen är klar klickar du på den [kör R-skript] [ execute-r-script] modulen och klicka sedan på **visa utdata logg** i egenskapsfönstret. En ny sida ska visas i webbläsaren som visar innehållet i filen output.log. När du rullar ner kan du bör se något som liknar följande.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Längre ned på sidan är mer detaljerad information om kolumner, som ser ut ungefär så här.

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

Dessa resultat är främst som förväntat med 228 observationer och 9 kolumner i dataramen. Vi kan se kolumnnamnen, datatypen R och ett exempel på varje kolumn.

> [!NOTE]
> Det här samma utskriften är lättillgängliga från R enheten utdata från den [kör R-skript] [ execute-r-script] modulen. Vi diskuterar utdata för den [kör R-skript] [ execute-r-script] modul i nästa avsnitt.  
> 
> 

#### <a name="dataset2"></a>Dataset2
Beteendet för Dataset2 indata är identisk med Dataset1. Du kan med den här indata för att skicka en andra rektangulär tabell med data i din R-kod. Funktionen `maml.mapInputPort(2)`, med argumentet 2 används för att skicka dessa data.  

### <a name="execute-r-script-outputs"></a>Köra R-skript-utdata
#### <a name="output-a-dataframe"></a>Utdata en dataram
Du kan mata ut innehållet i en dataram i R som en rektangulär tabell genom resultatet Dataset1 porten med hjälp av den `maml.mapOutputPort()` funktion. Detta utförs i vår enkla R-skript med följande rad.

    maml.mapOutputPort('cadairydata')

När experimentet har körts kan du klicka på utdataporten resultatet Dataset1 och klicka sedan på **visualisera**. Du bör se något som liknar bild 6.

![Visualisering av utdata från Kalifornien mjölkproducerande data][7]

*Bild 6. Visualisering av utdata från Kalifornien mjölkproducerande data.*

Dessa utdata ser ut identisk indata, precis som förväntades.  

### <a name="r-device-output"></a>R-enheter
Enheten utdata från den [kör R-skript] [ execute-r-script] modulen innehåller meddelanden och grafik som utdata. Både standard-utdata och standardfel meddelanden från R skickas till utdataporten för R-enhet.  

Om du vill visa utdata för R-enhet, klickar du på porten och klicka sedan på **visualisera**. Vi kan se standardutdata och standardfel från R-skriptet på bild 7.

![Standardutdata och standardfel från R-enhet-port][8]

*Bild 7. Standardutdata och standardfel från R-enhet-port.*

Bläddra nedåt vi ser grafik utdata från våra R-skript i figur 8.  

![Grafik utdata från R-enhet-port][9]

*Figur 8. Grafik utdata från R-enhet-port.*  

## <a id="filtering"></a>Filtrering av data och transformering
I det här avsnittet ska vi utföra vissa grundläggande data filtrering och omvandling åtgärder på Kalifornien mjölkproducerande data. I slutet av det här avsnittet har vi data i ett format som passar för att skapa en analytisk modell.  

Mer specifikt i det här avsnittet vi ska utföra flera vanliga data rensning och transformering uppgifter: Skriv omvandling, filtrering på dataramar, att lägga till nya beräknade kolumner, och värdet transformationer. Den här bakgrunden hjälper dig att hantera flera av påträffades i verkliga problem.

Den fullständiga R-koden för det här avsnittet finns i zip-filen som du hämtade tidigare.

### <a name="type-transformations"></a>Typ omvandlingar
Nu när vi kan läsa in Kalifornien mjölkproducerande datan i R-kod i den [kör R-skript] [ execute-r-script] modulen, som vi behöver att säkerställa att data i kolumnerna som har rätt typ och format.  

R är ett dynamiskt skrivna språk, vilket innebär att datatyperna är tvingas från varandra efter behov. De atomiska datatyperna i R är numeriska, logiska och tecknet. Factor-typen används för att lagra compactly kategoriska data. Du hittar mer information om datatyper i referenser i [bilaga B – mer](#appendixb).

När tabelldata läses in R från en extern källa, är det alltid en bra idé att kontrollera de resulterande typerna i kolumnerna. Vill du kanske en kolumn av typen tecken, men i många fall detta kommer att visas som en faktor eller vice versa. I annat fall en kolumn som du tycker bör vara representeras numeriska av teckendata, t.ex. Peka tal ”1,23” i stället för 1,23 som ett flyttalsvärde.  

Det är som tur är kan enkelt att konvertera en typ till en annan, så länge mappning är möjlig. Exempel: du kan inte konvertera ”Nevada” till ett numeriskt värde, men du kan konvertera den till en faktor (kategoriska variabel). Ett annat exempel är kan du konvertera en numerisk 1 till ett tecken '1' eller en faktor.  

Syntaxen för någon av konverteringarna är enkel: `as.datatype()`. Dessa funktioner för konvertering av typen inkluderar följande.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Titta på datatyperna för de kolumner som vi indata i föregående avsnitt: alla kolumner är av typen numeriska, förutom kolumnen ”månad”, vilket är av typen tecken. Vi omvandla detta till en faktor och testa resultaten.  

Jag har tagit bort den rad som skapas av spridningsdiagrammet matrisen och läggs en rad konvertera kolumnen ”månad” till en faktor. I mitt experiment kommer jag bara klipp ut och klistra in R-kod i code-fönstret för den [kör R-skript] [ execute-r-script] modulen. Du kan också uppdatera zip-filen och överföra den till Azure Machine Learning Studio, men det tar flera steg.  

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

Nu ska vi köra den här koden och titta på utdataloggen för R-skriptet. Relevanta data från loggen visas i bild 9.

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

*Bild 9. Sammanfattning av dataramar med en faktor variabel.*

Typen för månaden ska nu vara '**faktor med 14 nivåer**'. Detta är ett problem Eftersom det finns bara i 12 månader år. Du kan också kontrollera om du vill se som typen i **visualisera** av datauppsättningen som resultatet port är '**Kategoriskt**”.

Problemet är att kolumnen ”månad” inte har tagits kodade systematiskt. I vissa fall kallas en månad April och i andra det förkortas Apr. Vi kan lösa detta problem genom att minska strängen som ska 3 tecken. Rad med kod ser nu ut så här:

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Köra om experimentet och visa utdataloggen. De förväntade resultaten visas i bild 10.  

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

*Bild 10. Sammanfattning av dataramar med rätt antal faktor nivåer.*

Vår factor-variabeln har nu de önskade 12 nivåerna.

### <a name="basic-data-frame-filtering"></a>Grundläggande data frame filtrering
R dataramar stöder kraftfulla filtreringsfunktioner. Datauppsättningar kan vara deluppsättning med logiska filter på rader eller kolumner. I många fall måste komplexa villkor utföras. Referenserna i [bilaga B – mer](#appendixb) innehåller omfattande exempel på filtrering dataramar.  

Det finns en bit av filtrering ska vi göra på vår datauppsättning. Om du tittar på kolumnerna i cadairydata dataframe, visas två onödiga kolumner. Den första kolumnen innehåller bara ett radnummer som inte är mycket användbart. Den andra kolumnen Year.Month, innehåller redundant information. Vi kan enkelt undanta de här kolumnerna med hjälp av följande R-kod.

> [!NOTE]
> Hädanefter i det här avsnittet kommer jag bara att visa ytterligare kod som jag lägger till i den [kör R-skript] [ execute-r-script] modulen. Jag lägger till varje ny rad **innan** den `str()` funktion. Jag kan använda den här funktionen för att verifiera min resultaten i Azure Machine Learning Studio.
> 
> 

Jag lägga till följande rad i R-kod i den [kör R-skript] [ execute-r-script] modulen.

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Kör den här koden i experimentet och kontrollera resultatet från utdataloggen. Resultaten visas i bild 11.

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

*Bild 11. Sammanfattning av dataramar med två kolumner som har tagits bort.*

Goda nyheter! Vi får det förväntade resultatet.

### <a name="add-a-new-column"></a>Lägg till en ny kolumn
Om du vill skapa modeller för time series blir det praktiskt att ha en kolumn som innehåller månader sedan tidsserien. Vi skapar en ny kolumn 'Month.Count'.

För att ordna koden ska vi skapa vårt första enkla funktionen `num.month()`. Sedan ska vi tillämpa den här funktionen för att skapa en ny kolumn i dataramen. Den nya koden är som följer.

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

Nu kör uppdaterade experimentet och använda loggen för att visa resultatet. Resultaten visas i figur 12.

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

*Figur 12. Sammanfattning av dataramar med den ytterligare en kolumnen.*

Det verkar som att allt fungerar. Vi har den nya kolumnen med de förväntade värdena i vår dataframe.

### <a name="value-transformations"></a>Värdet omvandlingar
I det här avsnittet ska vi köra några enkla omvandlingar på värdena som en del av vår dataframe kolumner. R-språket stöder nästan godtyckligt värde transformationer. Referenserna i [bilaga B - ytterligare läsning](#appendixb) innehåller omfattande exempel.

Om du tittar på värdena i sammanfattningar av våra dataramar bör du se något udda här. Mer glass än mjölk som skapas i Kalifornien? Nej, förstås inte, eftersom detta ingen gör sad som detta kan vara att några av oss glass älskare. Enheterna är olika. Priset som anges i definieras av oss pund mjölk är i enheter om 1 miljon US pund, glass är i enheter om 1 000 oss gallon och Keso är i enheter om 1 000 USA pund. Under förutsättning att glass väger håller 6,5 pund per gallon, kan vi enkelt göra multiplikation om du vill konvertera dessa värden så att de finns på lika med 1 000 pund-enheter.

För vår prognosmodellen använder vi en Multiplicerande modell för trender och säsongsbaserade justering av dessa data. En logg omvandling kan vi använda en linjär modell, vilket förenklar den här processen. Vi kan använda log omvandling i samma funktion där multiplikatorn som används.

I följande kod definierar jag en ny funktion `log.transform()`, och tillämpa den på rader som innehåller numeriska värden. R `Map()` funktion används för att tillämpa den `log.transform()` att de markerade kolumnerna av dataramen. `Map()` liknar `apply()` men ger mer än en lista över argument till funktionen. Observera att en lista över multiplicering lämnar det andra argumentet för den `log.transform()` funktion. Den `na.omit()` funktion används som en stund för rensning så vi inte har saknas eller är odefinierad värden i dataramen.

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

Det finns en bitars händer i den `log.transform()` funktion. De flesta av den här koden söker efter potentiella problem med argument eller hantering av undantag som fortfarande kan uppstå under de nödvändiga beräkningarna. Bara några rader i den här koden faktiskt gör de nödvändiga beräkningarna.

Målet med skydden programmering är att förhindra fel på en enskild funktion som förhindrar bearbetning inte kan fortsätta. En kraftig fel på en tidskrävande analys kan vara ganska frustrerande för användare. Om du vill undvika detta måste du valt Standard returvärden som begränsar bearbetningen nedströms skador. Ett meddelande skapas också att varna användarna om att något har gått fel.

Om du inte används för att skydden programmering i R, kan den här koden verka lite överväldigande. Jag vägleder dig igenom de viktigaste stegen:

1. En vektor med fyra meddelanden har definierats. Dessa meddelanden används för att förmedla information om några av de möjliga fel och undantag som kan uppstå med den här koden.
2. Jag returnerar NA värdet för varje scenario. Det finns många andra möjligheter som kan ha färre sidoeffekter. Jag kan returnera en vektor med nollor eller ursprungliga inkommande vektor, till exempel.
3. Kontroller körs på argument till funktionen. I varje fall, om ett fel upptäcks, returneras ett standardvärde och ett meddelande som produceras av det `warning()` funktion. Jag använder `warning()` snarare än `stop()` som det senare avslutas körningen exakt vad jag försöker undvika. Observera att jag har skrivit koden i en procedurmässig format, som i det här fallet en funktionell metod som visat sig komplexa och otydligt.
4. Log-beräkningar är omslutna i `tryCatch()` så att undantag inte orsakar en kraftig stopp för bearbetning av. Utan `tryCatch()` de flesta fel som R-funktioner resulterar i en stoppsignal som gör just detta.

Kör R-kod i experimentet och ta en titt på utskriften i filen output.log. Nu visas transformerade värdena för de fyra kolumnerna i loggen, enligt figur 13.

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

*Figur 13. Sammanfattning av transformerade värdena i dataramen.*

Vi kan se värdena har transformerats. Nu mjölkproduktion överskrider alla andra mejeriprodukt produktion, återkalla att vi nu titta på en logaritmisk skala.

I det här läget våra data rensas och vi är redo för vissa modellering. Titta på visualiseringen sammanfattning för resultatet datauppsättning utdata från vår [kör R-skript] [ execute-r-script] modulen, ser du kolumnen ”månad” 'Kategoriskt' med 12 unika värden igen, precis som vi ville ha.

## <a id="timeseries"></a>Time series-objekt och Korrelations-analys
I det här avsnittet ska vi utforska några grundläggande R time series objekt och analysera samband mellan vissa variabler. Vårt mål är att mata ut en dataram som innehåller pairwise korrelation informationen på flera LACP.

Den fullständiga R-koden för det här avsnittet är i zip-filen som du hämtade tidigare.

### <a name="time-series-objects-in-r"></a>Time series-objekt i R
Som redan nämnts, time-serien är en serie datavärden som indexeras av tid. R time series-objekt används för att skapa och hantera tid indexet. Det finns flera fördelar med att använda time series-objekt. Time series-objekt för att frigöra från många detaljer för att hantera time series indexvärden som är inkapslade i objektet. Dessutom kan time series-objekt du använda metoderna serien många tid för, skriva ut, modellering och så vidare.

Klassen POSIXct time series används ofta och är relativt enkla. Den här tidsserier klassen mått tid från början av epok 1 januari 1970. I det här exemplet ska vi använda POSIXct time series-objekt. Andra vanligt objektklasser för R time series omfattar zoo och xts, extensible tidsserier.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Time series-objektet exempel
Låt oss börja med vårt exempel. Dra och släpp en **nya** [kör R-skript] [ execute-r-script] modulen i experimentet. Anslut utdataporten resultatet Dataset1 på det befintliga [kör R-skript] [ execute-r-script] modul till Dataset1 indataporten för den nya [kör R-skript] [ execute-r-script] modulen.

Som jag gjorde för de första exemplen när vi går igenom exemplet vid vissa tidpunkter visar jag endast de inkrementella ytterligare raderna med R-kod i varje steg.  

#### <a name="reading-the-dataframe"></a>Läser nu när dataramen
Som ett första steg ska vi läsa i en dataram och se till att vi får det förväntade resultatet. Följande kod bör göra jobbet.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

Nu kan köra experimentet. Logg över formen köra R-skriptet bör se ut figur 14.

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

*Figur 14. Sammanfattning av dataramar i modulen köra R-skript.*

Dessa data är av den förväntade typer och format. Observera att kolumnen ”månad” är av typen faktor och det förväntade antalet nivåer.

#### <a name="creating-a-time-series-object"></a>Skapa ett time series-objekt
Vi måste lägga till ett time series-objekt i vår dataframe. Ersätt den aktuella koden med följande, vilket lägger till en ny kolumn i klassen POSIXct.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Nu kan kontrollera loggen. Det bör se ut figur 15.

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

*Figur 15. Sammanfattning av dataramar med ett time series-objekt.*

Vi kan se från som den nya kolumnen har i själva verket klassen POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Utforska och omvandla data
Låt oss utforska några av variablerna i den här datauppsättningen. En matris med spridningsdiagrammet är ett bra sätt att skapa en snabb titt. Jag ersätta den `str()` funktionen i den tidigare R-koden med följande rad.

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Kör den här koden och se vad som händer. Diagram som skapas på porten som R-enhet bör se ut som bild 16.

![Spridningsdiagrammet matris med valda variabler][17]

*Bild 16. Spridningsdiagrammet matris med valda variabler.*

Det finns vissa odd-looking strukturen i relationerna mellan dessa variabler. Detta inträffar kanske från trender i data och det faktum att vi inte har standardiserade variablerna.

### <a name="correlation-analysis"></a>Korrelations-analys
Om du vill utföra korrelationen analys som vi behöver både ta bort trend och standardisera variablerna. Vi kan bara använda R `scale()` som både datacenter och skalar variabler. Den här funktionen kan även köras snabbare. Men vill jag visa ett exempel på skydden programmering i R.

Den `ts.detrend()` funktionen visas nedan utför båda dessa åtgärder. Följande två kodrader trend ta bort data och standardisera värdena.

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

Det finns en bitars händer i den `ts.detrend()` funktion. De flesta av den här koden söker efter potentiella problem med argument eller hantering av undantag som fortfarande kan uppstå under de nödvändiga beräkningarna. Bara några rader i den här koden faktiskt gör de nödvändiga beräkningarna.

Vi har redan beskrivs ett exempel på skydden programmering i [värde transformationer](#valuetransformations). Båda beräkning block är omslutna i `tryCatch()`. För vissa fel det vara bra att returnera den ursprungliga inkommande vektorn och i andra fall kan jag lämna tillbaka en vektor med nollor.  

Observera att den linjära regressionen som används för att ta bort trender är en time series-regression. Ge säkrare prognoser variabeln är ett time series-objekt.  

En gång `ts.detrend()` definieras vi tillämpa den på variabler av intresse för vår dataframe. Vi måste använda den resulterande listan som skapats av `lapply()` till data-dataframe med hjälp av `as.data.frame()`. På grund av skydden aspekter av `ts.detrend()`, det gick inte att bearbeta en av variablerna hindrar inte rätt bearbetningen av de andra.  

Den sista raden i koden skapar en pairwise spridningsdiagrammet. När du har kört R-kod, visas resultatet av spridningsdiagrammet i bild 17.

![Pairwise spridningsdiagrammet för ta bort daglig och standardiserad tidsserie][18]

*Bild 17. Pairwise spridningsdiagrammet för ta bort daglig och standardiserad tidsserie.*

Du kan jämföra de här resultaten returneras till de som visas i bild 16. Vi kan se mycket mindre struktur i relationerna mellan dessa variabler med trenden tas bort och variablerna standardiserade.

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

Kör den här koden skapar loggen visas i bild 18.

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

*Bild 18. Lista över ccf objekt från pairwise Korrelations-analys.*

Det finns ett Korrelationsvärde för varje fördröjning. Ingen av dessa Korrelations-värden är tillräckligt stor för att vara betydande. Vi kan därför ingå att vi kan utforma varje variabel oberoende av varandra.

### <a name="output-a-dataframe"></a>Utdata en dataram
Vi ha beräknade pairwise korrelationer som en lista över R ccf objekt. Detta medför en del problem Eftersom utdataporten resultatet datauppsättning egentligen kräver en dataram. Dessutom ccf objektet är i sig en lista och vi vill bara värdena i det första elementet i den här listan korrelationer på olika LACP.

Följande kod extraherar fördröjning värden från listan över ccf objekt, vilket är själva listor.

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

Det är lite svårt att den första raden i koden och förklaringar kan hjälpa dig att förstå den. Arbeta inifrån och ut har vi följande:

1. Den ”**[[**'operator med argumentet'**1**' väljer vektor korrelationer på LACP från det första elementet i listan ccf objekt.
2. Den `do.call()` funktion gäller den `rbind()` över objekten i listan returneras av `lapply()`.
3. Den `data.frame()` funktioner konverterar resultatet som produceras av `do.call()` till en dataram.

Observera att raden namnen i en kolumn i dataramen. Gör så bevaras raden namn när de är utdata från den [kör R-skript][execute-r-script].

Kör koden producerar de utdata som visas i bild 19 när jag **visualisera** utdata på resultatet datauppsättning-port. Rad-namn är i den första kolumnen som avsett.

![Resultaten utdata från Korrelations-analys][20]

*Bild 19. Resultat utdata från Korrelations-analys.*

## <a id="seasonalforecasting"></a>Time series-exempel: säsongens prognoser
Våra data är nu i ett formulär som är lämplig för analys och vi har bestämt att det finns inga betydande samband mellan variablerna. Nu ska vi gå vidare och skapa en tidsserie prognosmodell. Med den här modellen ska vi göra prognoser Kalifornien mjölkproduktion under de 12 månaderna av 2013.

Vår prognosmodellen har två komponenter, en trend komponent och en säsongens komponent. Fullständig prognosen är produkten av dessa två komponenter. Den här typen av modellen kallas en Multiplicerande modell. Alternativt kan du är en additiva modell. Vi har redan använt en logg-transformering för variabler i närheten, vilket gör den här analysen tractable.

Den fullständiga R-koden för det här avsnittet är i zip-filen som du hämtade tidigare.

### <a name="creating-the-dataframe-for-analysis"></a>Skapa dataframe för analys
Starta genom att lägga till en **nya** [kör R-skript] [ execute-r-script] modulen i experimentet. Anslut den **resultatet datauppsättning** utdata från den befintliga [kör R-skript] [ execute-r-script] modul till den **Dataset1** indata för den nya modulen. Resultatet bör likna bild 20.

![Experimentera med den nya köra R-skript-modulen har lagts till][21]

*Bild 20. Experimentera med den nya köra R-skript-modulen har lagts till.*

Som med Korrelations-analys som vi precis har slutfört, måste vi lägga till en kolumn med ett POSIXct time series-objekt. Följande kod kommer att göra just detta.

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

*Bild 21. En sammanfattning av dataramen.*

Det här resultatet är vi redo att börja analysen.

### <a name="create-a-training-dataset"></a>Skapa en datauppsättning för träning
Nu när dataramen konstrueras som vi behöver skapa en datauppsättning för träning. Dessa data omfattar alla observationer utom de senaste 12 år 2013, vilket är vår test-datauppsättning. Följande kod delmängder nu när dataramen och skapar områden av mjölkproducerande produktions- och variabler. Jag kan sedan skapa områden av fyra produktion och pris variabler. En anonym funktion används för att definiera vissa förstärker för diagram och sedan iterera över listan med de andra två argumenten med `Map()`. Om du tänker som en för loop skulle ha fungerade bra här, har du rätt. Men eftersom R är ett funktionellt språk som jag där du kan se en funktionell metod.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

Kör koden genererar serien med time series ritar från R-enhet-utdata som visas i bild 22. Observera att tidsaxeln i enheter för datum, en bra fördel av tiden serien ritas metod.

![Första gången serien områden i Kalifornien mjölkproducerande produktions- och data](./media/r-quickstart/unnamed-chunk-161.png)

![Andra av time series områden i Kalifornien mjölkproducerande produktions- och data](./media/r-quickstart/unnamed-chunk-162.png)

![Tredjedel av time series områden i Kalifornien mjölkproducerande produktions- och data](./media/r-quickstart/unnamed-chunk-163.png)

![Fjärde av time series områden i Kalifornien mjölkproducerande produktions- och data](./media/r-quickstart/unnamed-chunk-164.png)

*Bild 22. Time series områden i Kalifornien mejeriproduktion och prisdata.*

### <a name="a-trend-model"></a>En trend-modell
Att ha skapat ett time series-objekt och har haft en titt på data kan vi börja att konstruera en trend modell för produktionsdata för Kalifornien mjölk. Vi kan göra detta med en time series-regression. Det är dock tydligt från området som vi behöver mer än en lutning och komma åt för att exakt utforma observerade trend i träningsdata.

Med den mindre storleken på data kan ska jag skapa modell för trender i RStudio klipp och klistra in den resulterande modellen i Azure Machine Learning. RStudio ger en interaktiv miljö för den här typen av interaktiva analyser.

Som ett första försöket försöker jag en polynom regression med stänger upp till 3. Det finns en verklig risk över anpassa dessa typer av modeller. Därför är det bäst att undvika hög ordning villkor. Den `I()` funktionen hindrar beslutsträdets tolkning av innehållet (tolkar innehållet ”som är”) och du kan skriva en bokstavligen tolkad funktion i en regression formel.

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

Från P-värden (Pr (> | t |)) i dessa utdata kan vi se att kvadraten termen inte kan vara betydande. Jag använder den `update()` till att ändra den här modellen genom att släppa kvadraten termen.

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

Detta ser bättre ut. Alla villkor har betydelse. Dock 2e-16-värde är ett standardvärde och bör inte vidtas för allvar.  

Som ett förstånd test vi gör en time series rityta Kalifornien mjölkproducerande data med trend kurvan visas. Jag har lagt till följande kod i Azure Machine Learning [kör R-skript] [ execute-r-script] -modellen (inte RStudio) för att skapa modellen och göra en rityta. Resultatet visas i bild 23.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Kalifornien mjölk produktionsdata med trend modellen visas](./media/r-quickstart/unnamed-chunk-18.png)

*Bild 23. Kalifornien mjölk produktionsdata med trend modellen visas.*

Det verkar som om trend-modell passar data ganska bra. Dessutom kan verkar det inte vara tecken på över montering som udda wiggles i modellen kurvan.  

### <a name="seasonal-model"></a>Säsongens modell
Med en trend modell i hand som vi behöver skickar och inkludera säsongens effekterna. Vi använder årets månad som en dummy variabel i den linjära modellen för att avbilda effekten per månad. Observera att när du introducerar faktor variabler i en modell skärningspunkten inte måste beräknas. Om du inte gör detta anges över formeln och R kommer släpper du en av de önskade faktorerna men behålla skärningspunkt för termen.

Eftersom vi har en tillfredsställande trend vi kan använda den `update()` funktionen för att lägga till de nya villkoren i den befintliga modellen. -1 i update-formeln utelämnar skärningspunkt för termen. Du kan fortsätta i RStudio för tillfället:

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

Vi kan se att modellen inte längre har en skärningspunkt term och har 12 betydande månad faktorer. Det är precis vad vi ville se.

Vi gör en annan tid serie diagram Kalifornien mjölkproducerande data att se hur väl modellen säsongens fungerar. Jag har lagt till följande kod i Azure Machine Learning [kör R-skript] [ execute-r-script] att skapa modellen och göra en rityta.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Kör den här koden i Azure Machine Learning ger området visas i bild 24.

![Kalifornien mjölkproduktion med modellen inklusive säsongens effekter](./media/r-quickstart/unnamed-chunk-20.png)

*Bild 24. Kalifornien mjölkproduktion med modellen inklusive säsongens effekter.*

Anpassa till data som visas i bild 24 är i stället uppmuntra. Titta rimliga både trenden och säsongsbaserade effekten (månatliga variation).

Som en annan kontroll av vår modell ska vi ta en titt på restbelopp. Följande kod beräknar de förväntade värdena från våra två modeller, beräknar restbelopp för säsongens modellen och ritar upp dessa restbelopp för träningsdata.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

Återstående området illustreras i bild 25.

![Restbelopp på säsongens modellen för utbildningsdata](./media/r-quickstart/unnamed-chunk-21.png)

*Bild 25. Restbelopp på säsongens modellen för träningsdata.*

Dessa restbelopp titta rimliga. Det finns inga specifika struktur, förutom effekten av den 2008-2009 tillbakagången som vår modell inte tar hänsyn till särskilt bra.

Diagram som visas i bild 25 är användbart för att upptäcka eventuella tidsberoende mönster i restbelopp. Den explicita metoden för databehandling och rita ut restbelopp jag använde placerar restbelopp i tidsordning i området. Om du å andra sidan hade z `milk.lm$residuals`, området inte skulle ha varit i tidsordning.

Du kan också använda `plot.lm()` att skapa en serie diagnostiska områden.

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Den här koden skapar en serie diagnostiska diagrammen som visas i bild 26.

![Första av diagnostiska områden för säsongens modellen](./media/r-quickstart/unnamed-chunk-221.png)

![Andra av diagnostiska områden för säsongens modellen](./media/r-quickstart/unnamed-chunk-222.png)

![Tredje av diagnostiska områden för säsongens modellen](./media/r-quickstart/unnamed-chunk-223.png)

![Fjärde av diagnostiska områden för säsongens modellen](./media/r-quickstart/unnamed-chunk-224.png)

*Bild 26. Diagnostik ritar för säsongens modellen.*

Det finns ett par med hög inflytelserika punkter som identifieras i dessa områden, men det finns inget att orsaka viktig för oss. Dessutom kan vi se från området Normal frågor och-frågor och att restbelopp är nära normalt distribuerade ett viktigt antagande för linjära modeller.

### <a name="forecasting-and-model-evaluation"></a>Prognostisering och datamodeller utvärdering
Det finns bara en sak att göra för att slutföra vårt exempel. Vi behöver att beräkna prognoser och mäta felet mot de faktiska data. Vår prognosen debiteras under de 12 månaderna av 2013. Vi kan beräkna ett fel mått för prognosen för faktiska data som inte är en del av vår datauppsättning för träning. Vi kan dessutom att jämföra prestanda på träningsdata till de 12 månaderna av testdata 18 år.  

Ett antal mått för att mäta prestanda i time series-modeller. I vårt fall använder vi kvadratiska medelvärdet (RMS)-fel. Följande funktion beräknar RMS-fel mellan två serier.  

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

Precis som med de `log.transform()` funktion som beskrivits i avsnittet ”värde transformationer” det finns en hel del kontrollerar och undantag recovery felkod i den här funktionen. De principer som används är samma. Arbetet utförs på två platser är inneslutna i `tryCatch()`. Tidsserien är först exponentiated, eftersom vi har samarbetat med loggarna för värdena. Dessutom beräknas den faktiska RMS-fel.  

Utrustad med en funktion för att mäta RMS-fel, ska vi skapa och mata ut en dataram som innehåller RMS-fel. Vi att omfatta villkoren för enbart trend modellen och fullständig modellen med säsongens faktorer. Följande kod gör jobbet med hjälp av de två modellerna för linjär som vi har skapat.

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

Kör den här koden skapar utdata visas i bild 27 på utdataporten för datauppsättningen i resultatet.

![Jämförelse av RMS-fel för modeller][26]

*Bild 27. Jämförelse av RMS-fel för modellen.*

De här resultaten ser vi att lägga till på säsongens faktorer i modellen minskar RMS-fel avsevärt. RMS-fel för träningsdata är för förstås, en stund mindre än för prognosen.

## <a id="appendixa"></a>BILAGA A: Guide till RStudio
RStudio är ganska väl dokumenterat, så i den här bilagan jag tillhandahåller länkar till viktiga delar av RStudio-dokumentation för att komma igång.

1. Skapa projekt
   
   Du kan organisera och hantera din R-kod i projekt med hjälp av RStudio. I dokumentationen som använder projekt finns på https://support.rstudio.com/hc/articles/200526207-Using-Projects.
   
   Jag rekommenderar att du följer du dessa anvisningar och skapa ett projekt för R-kodexempel i det här dokumentet.  
2. Redigera och köra R-kod
   
   RStudio är en integrerad miljö för att redigera och köra R-kod. Dokumentation finns på https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.
3. Felsökning
   
   RStudio innehåller kraftfulla funktioner för felsökning. Dokumentationen för de här funktionerna finns på https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.
   
   Brytpunkt felsökningsfunktioner dokumenteras på https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

## <a id="appendixb"></a>BILAGA B: Ytterligare läsning
Den här programming R-självstudien tar upp grunderna för vad du behöver använda R-språket med Azure Machine Learning Studio. Om du inte är bekant med R är två introduktioner tillgängliga på CRAN:

* R för nybörjare av Emmanuel Paradis är ett bra ställe att börja på http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  
* En introduktion till R genom W. N. Venables et. al. hamnar i lite mer djupet på http://cran.r-project.org/doc/manuals/R-intro.html.

Det finns många böcker på R som hjälper dig att komma igång. Här är några jag vara användbara:

* Bilder av R-programmering: En genomgång av statistiska programdesign av Norman Matloff är en utmärkt introduktion till programmering i R.  
* R-Cookbook av Paul Teetor ger en problemet och lösningen metod för med R.  
* R i praktiken genom Robert Kabacoff är en annan användbar inledande boken. Tillhörande snabb R webbplats är en bra resurs på http://www.statmethods.net/.
* R-Inferno av Patrick Burns är en överraskande lättsam bok som behandlar ett antal svårt och dyrt avsnitt som kan uppstå vid programmering i R. Boken är tillgänglig kostnadsfritt på http://www.burns-stat.com/documents/books/the-r-inferno/.
* Om du vill att en djupdykning i avancerade ämnen i R har du en titt på boken Avancerat R genom Hadley Wickham. Online-versionen av den här boken är tillgänglig kostnadsfritt på http://adv-r.had.co.nz/.

En katalog med R time series-paket finns i uppgiftsvyn CRAN för analys av tidsserier: http://cran.r-project.org/web/views/TimeSeries.html. Information om specifika tid serien objekt-paket, bör du i dokumentationen för det paketet.

Boken inledande tidsserier med R genom Paul Cowpertwait och Andrew Metcalfe ger en introduktion till att använda R för analys av tidsserier. Många fler teoretisk texter innehåller R-exempel.

Några bra Internetresurser:

* DataCamp: DataCamp Lär R i bekvämt med din webbläsare med video lektioner och kodning övningarna. Det finns interaktiva självstudier på de senaste R-tekniker och paket. Slutför självstudiekursen kostnadsfria interaktiva R på https://www.datacamp.com/courses/introduction-to-r
* En guide i komma igång med R från Programiz https://www.programiz.com/r-programming
* Snabbgenomgång av Kelly Black från Clarkson University R http://www.cyclismo.org/tutorial/R/
* 60 + R resurserna som beskrevs i http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

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
