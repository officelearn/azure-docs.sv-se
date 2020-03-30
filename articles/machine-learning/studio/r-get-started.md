---
title: Komma igång med R
titleSuffix: ML Studio (classic) - Azure
description: Använd den här R-programmeringshandledningen för att komma igång med R-språket med Azure Machine Learning Studio (klassiskt) för att skapa en prognoslösning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 1b347707b3c656bd692a29f0fd748c1503be4fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218000"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Komma igång med R-programmeringsspråket i Azure Machine Learning Studio (klassiskt)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introduktion

Den här självstudien hjälper dig att börja utöka Azure Machine Learning Studio (klassisk) med hjälp av programmeringsspråket R. Följ denna R programmering handledning för att skapa, testa och köra R-kod i Studio (klassisk). När du arbetar igenom handledning, kommer du att skapa en komplett prognoslösning med hjälp av R-språket i Studio (klassisk).  

Azure Machine Learning Studio (klassisk) innehåller många kraftfulla moduler för maskininlärning och datamanipulering. Det kraftfulla R-språket har beskrivits som analytics lingua franca. Lyckligtvis kan analyser och datamanipulering i Studio (klassisk) utökas med hjälp av R. Den här kombinationen ger skalbarhet och enkel distribution av Studio (klassisk) med flexibilitet och djup analys av R.

### <a name="forecasting-and-the-dataset"></a>Prognostisering och datauppsättning

Prognoser är en allmänt använd och ganska användbar analysmetod. Vanliga användningsområden sträcker sig från att förutsäga försäljning av säsongsartiklar, bestämma optimala lagernivåer, till att förutsäga makroekonomiska variabler. Prognoser görs vanligtvis med tidsseriemodeller.

Tidsseriedata är data där värdena har ett tidsindex. Tidsindexet kan vara regelbundet, t.ex. En tidsseriemodell baseras på tidsseriedata. Programmeringsspråket R innehåller ett flexibelt ramverk och omfattande analyser för tidsseriedata.

I den här guiden kommer vi att arbeta med Kalifornien mejeriproduktion och prisdata. Dessa uppgifter omfattar månatlig information om produktionen av flera mejeriprodukter och priset på mjölkfett, en referensvara.

De data som används i den här artikeln, tillsammans med R-skript, kan hämtas från [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Data i `cadairydata.csv` filen var ursprungligen syntetiseras från information [https://dairymarkets.com](https://dairymarkets.com)som finns tillgänglig från University of Wisconsin på .

### <a name="organization"></a>Organisation

Vi går igenom flera steg när du lär dig hur du skapar, testar och kör R-kod för analys och datamanipulering i den klassiska miljön i Azure Machine Learning Studio (klassiskt).  

* Först kommer vi att utforska grunderna i att använda R-språket i Azure Machine Learning Studio (klassisk) miljö.
* Sedan går vi vidare till att diskutera olika aspekter av I/O för data, R-kod och grafik i Azure Machine Learning Studio (klassisk) miljö.
* Vi kommer sedan att konstruera den första delen av vår prognoslösning genom att skapa kod för datarengöring och omvandling.
* Med våra data förberedda kommer vi att utföra en analys av korrelationerna mellan flera av variablerna i vår datauppsättning.
* Slutligen kommer vi att skapa en säsongsbunden tidsserieprognosmodell för mjölkproduktion.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interagera med R-språk i Machine Learning Studio (klassiskt)

Det här avsnittet tar dig igenom grunderna i att interagera med R-programmeringsspråket i Machine Learning Studio -miljön (klassisk). R-språket är ett kraftfullt verktyg för att skapa anpassade analys- och datamanipuleringsmoduler i den klassiska miljön i Azure Machine Learning Studio (klassiskt).

Jag kommer att använda RStudio för att utveckla, testa och felsöka R-kod i liten skala. Den här koden klipps sedan ut och klistras in i en [Execute R Script-modul][execute-r-script] som är redo att köras i Azure Machine Learning Studio (klassisk).  

### <a name="the-execute-r-script-module"></a>Modulen Kör R-skript

I Machine Learning Studio (klassisk) körs R-skript i modulen [Kör R-skript.][execute-r-script] Ett exempel på modulen [Kör R-skript][execute-r-script] i Machine Learning Studio (klassisk) visas i bild 1.

 ![R programmeringsspråk: Modulen Kör R Script som valts i Machine Learning Studio (klassisk)](./media/r-quickstart/fig1.png)

*Bild 1. Machine Learning Studio -miljön (klassisk) som visar den valda modulen Kör R-skript.*

Med hänvisning till bild 1, låt oss titta på några av de viktigaste delarna av Machine Learning Studio (klassisk) miljö för att arbeta med [Execute R Script][execute-r-script] modulen.

* Modulerna i experimentet visas i mittrutan.
* Den övre delen av den högra rutan innehåller ett fönster för att visa och redigera R-skript.  
* Den nedre delen av den högra rutan visar vissa egenskaper för [Execute R Script][execute-r-script]. Du kan visa fel- och utdataloggarna genom att välja lämpliga platser i det här fönstret.

Vi kommer naturligtvis att diskutera [Execute R Script][execute-r-script] mer i detalj i resten av den här artikeln.

När du arbetar med komplexa R-funktioner rekommenderar jag att du redigerar, testar och felsöker i RStudio. Som med alla mjukvaruutveckling, utöka din kod stegvis och testa den på små enkla testfall. Klipp sedan ut och klistra in funktionerna i R-skriptfönstret i modulen [Kör R-skript.][execute-r-script] Med den här metoden kan du utnyttja både den integrerade utvecklingsmiljön för RStudio (IDE) och kraften i Azure Machine Learning Studio (klassisk).  

#### <a name="execute-r-code"></a>Kör R-kod

Alla R-koder i modulen [Kör R-skript][execute-r-script] körs när du kör experimentet genom att välja **knappen Kör.** När körningen är klar visas en bock på ikonen [Kör R-skript.][execute-r-script]

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Defensiv R-kodning för Azure Machine Learning

Om du utvecklar R-kod för, säg, en webbtjänst med hjälp av Azure Machine Learning Studio (klassisk), bör du definitivt planera hur din kod kommer att hantera en oväntad datainmatning och undantag. För att bevara tydligheten har jag inte tagit med mycket i vägen för kontroll eller undantagshantering i de flesta av de kodexempel som visas. Men när vi fortsätter kommer jag att ge dig flera exempel på funktioner med hjälp av R: s undantagshantering kapacitet.  

Om du behöver en mer komplett behandling av R undantag hantering, rekommenderar jag att du läser de tillämpliga avsnitten i boken av Wickham nedan i [Ytterligare läsning](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Felsöka och testa R i Machine Learning Studio (klassisk)

För att upprepa rekommenderar jag att du testar och felsöker din R-kod i liten skala i RStudio. Det finns dock fall där du måste spåra R-kodproblem i själva [körningen R-skriptet.][execute-r-script] Dessutom är det bra att kontrollera dina resultat i Machine Learning Studio (klassisk).

Utdata från körningen av din R-kod och på Azure Machine Learning Studio (klassisk) plattform finns främst i output.log. Viss ytterligare information kommer att ses i error.log.  

Om ett fel uppstår i Machine Learning Studio (klassisk) när du kör din R-kod, bör din första åtgärd vara att titta på error.log. Den här filen kan innehålla användbara felmeddelanden som hjälper dig att förstå och korrigera felet. Om du vill visa error.log väljer du **Visa fellogg** i **egenskapsfönstret** för [kör R-skriptet][execute-r-script] som innehåller felet.

Jag körde till exempel följande R-kod, med en odefinierad variabel y, i en Kör R Script-modul: [Execute R Script][execute-r-script]

```R
x <- 1.0
z <- x + y
```

Den här koden kan inte köras, vilket resulterar i ett feltillstånd. Om du väljer **Visa fellogg** i **egenskapsfönstret** visas den display som visas i bild 2.

  ![Felmeddelandet dyker upp](./media/r-quickstart/fig2.png)

*Figur 2. Popup-fönster med felmeddelande.*

Det ser ut som vi måste titta i output.log för att se R felmeddelandet. Markera [kör R-skriptet][execute-r-script] och välj sedan objektet **Visa output.log** i **egenskapsfönstret** till höger. Ett nytt webbläsarfönster öppnas och jag ser följande.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Det här felmeddelandet innehåller inga överraskningar och identifierar tydligt problemet.

Om du vill kontrollera värdet för ett objekt i R kan du skriva ut dessa värden till filen output.log. Reglerna för granskning av objektvärden är i stort sett desamma som i en interaktiv R-session. Om du till exempel skriver ett variabelnamn på en rad skrivs objektets värde ut på filen output.log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Paket i Machine Learning Studio (klassisk)

Studio levereras med över 350 förinstallerade R språkpaket. Du kan använda följande kod i modulen [Kör R-skript][execute-r-script] för att hämta en lista över de förinstallerade paketen.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Om du inte förstår den sista raden i den här koden för tillfället, läs vidare. I resten av denna artikel kommer vi att diskutera utförligt med R i Studio (klassisk) miljö.

### <a name="introduction-to-rstudio"></a>Introduktion till RStudio

RStudio är en allmänt använd IDE för R. Jag kommer att använda RStudio för redigering, testning och felsökning av några av R-koden som används i den här guiden. När R-koden har testats och klart kan du helt enkelt klippa ut och klistra in från RStudio-redigeraren i en Machine Learning Studio (klassisk) Execute R Script-modul. [Execute R Script][execute-r-script]  

Om du inte har R programmeringsspråk installerat på din stationära dator, rekommenderar jag att du gör det nu. Gratis nedladdningar av R-språk med öppen källkod finns på [https://www.r-project.org/](https://www.r-project.org/)THE Comprehensive R Archive Network (CRAN) på . Det finns nedladdningar tillgängliga för Windows, Mac OS och Linux/UNIX. Välj en spegel i närheten och följ hämtningsanvisningarna. Dessutom innehåller CRAN en mängd användbara analys- och datamanipuleringspaket.

Om du inte har gjort det tidigare i RStudio bör du hämta och installera skrivbordsversionen. Du hittar RStudio-nedladdningarna för Windows, Mac OS och http://www.rstudio.com/products/RStudio/Linux/UNIX på . Följ anvisningarna för att installera RStudio på din stationära dator.  

En handledning introduktion till RStudio finns på [Använda RStudio IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Jag ger lite ytterligare information om hur du använder RStudio i [Guide to RStudio dokumentation](#appendixa) nedan.  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Hämta data in och ut ur modulen Kör R-skript

I det här avsnittet kommer vi att diskutera hur du får data till och från [modulen Kör R Script.][execute-r-script] Vi kommer att granska hur du hanterar olika datatyper som läss in i och ut från modulen [Kör R-skript.][execute-r-script]

Den fullständiga koden för det här avsnittet finns i [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Läsa in och kontrollera data i Machine Learning Studio (klassiskt)

#### <a name="load-the-dataset"></a><a id="loading"></a>Ladda datauppsättningen

Vi börjar med att läsa in filen **csdairydata.csv** i Azure Machine Learning Studio (klassisk).

1. Starta din Azure Machine Learning Studio -miljö (klassisk).
1. Välj **+ NYTT** längst ned till vänster på skärmen och välj **Datauppsättning**.
1. Välj **Från lokal fil**och **bläddra** sedan för att markera filen.
1. Kontrollera att du har valt **Generic CSV-fil med header (.csv)** som typ för datauppsättningen.
1. Markera markeringen.
1. När datauppsättningen har överförts bör du se den nya datauppsättningen genom att välja fliken **Datauppsättning.**  

#### <a name="create-an-experiment"></a>Skapa ett experiment

Nu när vi har lite data i Machine Learning Studio (klassisk), måste vi skapa ett experiment för att göra analysen.  

1. Välj **+ NYTT** längst ned till vänster och välj **Experiment**och sedan tomt **experiment**.
1. Du kan namnge experimentet genom att välja och ändra **experimentet som skapats på ...** titeln högst upp på sidan. Ändra den till till **CA Dairy Analysis**.
1. Till vänster på experimentsidan expanderar du **Sparade datauppsättningar**och sedan **Mina datauppsättningar**. Du bör se **cadairydata.csv** som du laddade upp tidigare.
1. Dra och släpp **csdairydata.csv-datauppsättningen** på experimentet.
1. Skriv [Kör R Script][execute-r-script]i rutan **Sökexperimentobjekt** högst upp i den vänstra rutan . Modulen visas i söklistan.
1. Dra och släpp modulen [Kör R-skript][execute-r-script] på din lastpall.  
1. Anslut utdata för **datauppsättningen csdairydata.csv** till den vänstra inmatningen **(Datauppsättning1)** i [Execute R Script][execute-r-script].
1. **Glöm inte att välja "Spara"!**  

Vid denna punkt ditt experiment bör se ut ungefär som figur 3.

![Ca Dairy Analysis-experimentet med datauppsättning och kör R-skriptmodul](./media/r-quickstart/fig3.png)

*Bild 3. Ca Dairy Analysis-experimentet med datauppsättning och kör R Script-modulen.*

#### <a name="check-on-the-data"></a>Kontrollera uppgifterna

Låt oss ta en titt på de data vi har laddat in i vårt experiment. I experimentet väljer du utdata för **datauppsättningen cadairydata.csv** och väljer **visualisera**. Du skulle se något i stil med figur 4.  

![Sammanfattning av datauppsättningen cadairydata.csv](./media/r-quickstart/fig4.png)

*Bild 4. Sammanfattning av datauppsättningen cadairydata.csv.*

I denna uppfattning ser vi en hel del användbar information. Vi kan se de första raderna i den datauppsättningen. Om vi väljer en kolumn visar avsnittet Statistik mer information om kolumnen. Raden Funktionstyp visar oss till exempel vilka datatyper Azure Machine Learning Studio (klassisk) som tilldelats kolumnen. Att ha en snabb titt så här är en bra förstånd kontroll innan vi börjar göra något seriöst arbete.

### <a name="first-r-script"></a>Första R-skriptet

Låt oss skapa ett enkelt första R-skript för att experimentera i Azure Machine Learning Studio (klassisk). Jag har skapat och testat följande skript i RStudio.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Nu måste jag överföra skriptet till Azure Machine Learning Studio (klassisk). Jag kunde helt enkelt klippa och klistra in. Men i det här fallet kommer jag att överföra mitt R-skript via en zip-fil.

### <a name="data-input-to-the-execute-r-script-module"></a>Datainmatning till modulen Kör R-skript

Låt oss ta en titt på ingångarna till [modulen Kör R-skript.][execute-r-script] I det här exemplet kommer vi att läsa Kalifornien mejeri data i [Kör R Script][execute-r-script] modulen.  

Det finns tre möjliga ingångar för modulen [Kör R-skript.][execute-r-script] Du kan använda någon eller alla av dessa ingångar, beroende på ditt program. Det är också fullt rimligt att använda ett R-skript som inte tar någon input alls.  

Låt oss titta på var och en av dessa ingångar, går från vänster till höger. Du kan se namnen på var och en av ingångarna genom att placera markören över indata och läsa verktygstipset.  

#### <a name="script-bundle"></a>Skriptpaket

Med indata för skriptpaket kan du skicka innehållet i en zip-fil till [modulen Kör R-skript.][execute-r-script] Du kan använda något av följande kommandon för att läsa innehållet i zip-filen i din R-kod.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (klassisk) behandlar filer i zip som om de finns i src/ katalogen, så du måste prefixa dina filnamn med det här katalognamnet. Om dragkedjan till exempel innehåller `yourfile.R` `yourData.rdata` filerna och i roten av zip-funktionen, adresserar du dessa om `src/yourfile.R` och `src/yourData.rdata` när du använder `source` och `load`.

Vi har redan diskuterat att läsa in datauppsättningar i [Ladda datauppsättningen](#loading). När du har skapat och testat R-skriptet som visas i föregående avsnitt gör du följande:

1. Spara R-skriptet i ett . R-fil. Jag kallar min skriptfil "simpleplot. R". Här är innehållet.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Skapa en zip-fil och kopiera skriptet till den här zip-filen. I Windows kan du högerklicka på filen och välja **Skicka till**och sedan **komprimerad mapp**. Detta kommer att skapa en ny zip-fil som innehåller "simpleplot. R"-fil.

1. Lägg till filen i **datauppsättningarna** i Azure Machine Learning Studio (klassisk), och ange typen som **zip**. Du bör nu se zip-filen i dina datauppsättningar.

1. Dra och släpp zip-filen från **datauppsättningar** till **ML Studio -arbetsytan (klassiskt).**

1. Anslut utdata för **zip-dataikonen** till **skriptpaketinmatningen** i modulen [Kör R-skript.][execute-r-script]

1. Skriv `source()` funktionen med postnummernamnet i kodfönstret för modulen [Kör R-skript.][execute-r-script] I mitt fall `source("src/simpleplot.R")`skrev jag .  

1. Se till att du väljer **Spara**.

När de här stegen är klara kör modulen [Kör R-skript][execute-r-script] R R-skriptet i zip-filen när experimentet körs. Vid denna punkt ditt experiment bör se ut ungefär som figur 5.

![Experimentera med R-skript med dragkedja](./media/r-quickstart/fig6.png)

*Figur 5. Experimentera med ett R-skript med dragkedja.*

#### <a name="dataset1"></a>Dataset1

Du kan skicka en rektangulär datatabell till din R-kod med hjälp av Dataset1-indata. I vårt enkla `maml.mapInputPort(1)` skript läser funktionen data från port 1. Dessa data tilldelas sedan till ett dataramsvariabelnamn i koden. I vårt enkla manus utför den första kodraden uppgiften.

```R
cadairydata <- maml.mapInputPort(1)
```

Kör experimentet genom att välja knappen **Kör.** När körningen är klar väljer du modulen [Kör R-skript][execute-r-script] och väljer sedan **Visa utdatalogg** i egenskapsfönstret. En ny sida ska visas i webbläsaren som visar innehållet i filen output.log. När du rullar nedåt bör du se något liknande följande.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Längre ner på sidan finns mer detaljerad information om kolumnerna, som kommer att se ut ungefär så här.

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

Dessa resultat är mestadels som förväntat, med 228 observationer och 9 kolumner i dataramen. Vi kan se kolumnnamnen, R-datatypen och ett exempel på varje kolumn.

> [!NOTE]
> Samma utskrivna utdata är praktiskt tillgänglig från R-enhetens utdata [för modulen Kör R-skript.][execute-r-script] Vi kommer att diskutera utgångarna för [modulen Kör R-skript][execute-r-script] i nästa avsnitt.  

#### <a name="dataset2"></a>Dataset2

Beteendet för Dataset2-indata är identiskt med Dataset1. Med den här ingången kan du skicka en andra rektangulär datatabell till din R-kod. Funktionen `maml.mapInputPort(2)`, med argumentet 2, används för att skicka dessa data.  

### <a name="execute-r-script-outputs"></a>Köra R-skriptutdata

#### <a name="output-a-dataframe"></a>Mata ut en dataram

Du kan mata ut innehållet i en R-dataram som en rektangulär tabell via datauppsättningen Result1 med hjälp av `maml.mapOutputPort()` funktionen. I vårt enkla R-skript utförs detta av följande rad.

```
maml.mapOutputPort('cadairydata')
```

När du har kört experimentet väljer du utdataporten Result Dataset1 och väljer sedan **Visualisera**. Du skulle se något i stil med figur 6.

![Visualisering av produktionen av Kalifornien mejeri data](./media/r-quickstart/fig7.png)

*Figur 6. Visualisering av produktionen av Kalifornien mejeri data.*

Den här utdata ser identisk med indata, precis som vi förväntade oss.  

### <a name="r-device-output"></a>R-enhetsutgång

Enhetsutdata för modulen [Kör R-skript][execute-r-script] innehåller meddelanden och grafikutdata. Både standardutdata och standardfelmeddelanden från R skickas till utdataporten R-enhet.  

Om du vill visa utdata för R-enheten markerar du porten och sedan på **Visualisera**. Vi ser standardutdata och standardfel från R-skriptet i figur 7.

![Standardutdata och standardfel från R-enhetsporten](./media/r-quickstart/fig8.png)

*Figur 7. Standardutdata och standardfel från R-enhetsporten.*

Bläddra ner ser vi grafikutgången från vårt R-skript i figur 8.  

![Grafikutdata från R-enhetsporten](./media/r-quickstart/fig9.png)

*Figur 8. Grafikutdata från R-enhetsporten.*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Datafiltrering och omvandling

I det här avsnittet kommer vi att utföra vissa grundläggande datafiltrering och omvandlingsåtgärder på Kaliforniens mejeridata. I slutet av detta avsnitt kommer vi att ha data i ett format som lämpar sig för att bygga en analytisk modell.  

Mer specifikt kommer vi i det här avsnittet att utföra flera vanliga datarengörings- och omvandlingsuppgifter: typomvandling, filtrering på dataramar, lägger till nya beräknade kolumner och värdeomvandlingar. Denna bakgrund bör hjälpa dig att hantera de många variationer som uppstått i verkliga problem.

Den fullständiga R-koden för det här avsnittet finns i [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Skriv omvandlingar

Nu när vi kan läsa Kalifornien mejeri data i R-koden i [Kör R Script][execute-r-script] modulen, måste vi se till att data i kolumnerna har avsedd typ och format.  

R är ett dynamiskt maskinskrivet språk, vilket innebär att datatyper tvingas från ett till ett annat efter behov. De atomära datatyperna i R innehåller numeriska, logiska och tecken. Faktortypen används för att lagra kategoriska data på ett komprimerat sätt. Du hittar mycket mer information om datatyper i referenserna i [Ytterligare läsning](#appendixb) nedan.

När tabelldata läss in i R från en extern källa är det alltid en bra idé att kontrollera de resulterande typerna i kolumnerna. Du kanske vill ha en kolumn av typtecken, men i många fall visas detta som faktor eller vice versa. I andra fall representeras en kolumn som du tycker ska vara numerisk av teckendata, t.ex.  

Lyckligtvis är det lätt att konvertera en typ till en annan, så länge kartläggning är möjlig. Du kan till exempel inte konvertera "Nevada" till ett numeriskt värde, men du kan konvertera det till en faktor (kategorisk variabel). Som ett annat exempel kan du konvertera ett numeriskt 1 till ett tecken "1" eller en faktor.  

Syntaxen för någon av dessa `as.datatype()`konverteringar är enkel: . Dessa typkonverteringsfunktioner inkluderar följande.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Om du tittar på datatyperna för de kolumner vi matar in i föregående avsnitt: alla kolumner är av typen numeriska, med undantag för kolumnen "Månad", som är av typen tecken. Låt oss konvertera detta till en faktor och testa resultaten.  

Jag har tagit bort raden som skapade scatterplot matrisen och lagt till en rad konvertera "Månad" kolumnen till en faktor. I mitt experiment kommer jag bara klippa och klistra in R-koden i kodfönstret i [Execute R Script][execute-r-script] Module. Du kan också uppdatera zip-filen och ladda upp den till Azure Machine Learning Studio (klassisk), men det tar flera steg.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Låt oss köra den här koden och titta på utdataloggen för R-skriptet. Relevanta uppgifter från loggen visas i figur 9.

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

*Figur 9. Sammanfattning av dataramen med en faktorvariabel.*

Typen för Månad ska nu säga "**Faktor w / 14 nivåer**". Detta är ett problem eftersom det bara finns 12 månader under året. Du kan också kontrollera att typen i **Visualisera** av datauppsättningsporten är**kategorisk.**

Problemet är att kolumnen Månad inte har kodats systematiskt. I vissa fall kallas en månad april och i andra förkortas den som april. Vi kan lösa detta problem genom att trimma strängen till 3 tecken. Kodraden ser nu ut så här:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Kör experimentet igen och visa utdataloggen. De förväntade resultaten visas i figur 10.  

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

*Figur 10. Sammanfattning av dataramen med rätt antal faktornivåer.*

Vår faktorvariabel har nu de önskade 12 nivåerna.

### <a name="basic-data-frame-filtering"></a>Grundläggande dataramfiltrering

R-dataramar stöder kraftfulla filtreringsfunktioner. Datauppsättningar kan subsetted med hjälp av logiska filter på antingen rader eller kolumner. I många fall krävs komplexa filterkriterier. Referenserna i [Ytterligare läsning](#appendixb) nedan innehåller omfattande exempel på filtrering av dataramar.  

Det finns en bit av filtrering vi bör göra på vår datauppsättning. Om du tittar på kolumnerna i dataramen för cadairydata visas två onödiga kolumner. Den första kolumnen innehåller bara ett radnummer, vilket inte är särskilt användbart. Den andra kolumnen, År.Månad, innehåller överflödig information. Vi kan enkelt utesluta dessa kolumner med hjälp av följande R-kod.

> [!NOTE]
> Från och med nu i detta avsnitt kommer jag bara visa dig den extra koden jag lägger till i [Execute R Script][execute-r-script] modulen. Jag kommer att **before** lägga `str()` till varje ny rad innan funktionen. Jag använder den här funktionen för att verifiera mina resultat i Azure Machine Learning Studio (klassisk).

Jag lägger till följande rad i min R-kod i modulen [Kör R-skript.][execute-r-script]

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Kör den här koden i experimentet och kontrollera resultatet från utdataloggen. Dessa resultat visas i figur 11.

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

*Figur 11. Sammanfattning av dataramen med två kolumner borttagna.*

Goda nyheter! Vi får de förväntade resultaten.

### <a name="add-a-new-column"></a>Lägga till en ny kolumn

För att skapa tidsseriemodeller är det praktiskt att ha en kolumn som innehåller månaderna sedan tidsseriens början. Vi skapar en ny kolumn "Month.Count".

För att hjälpa till att organisera koden `num.month()`kommer vi att skapa vår första enkla funktion, . Vi kommer sedan att använda den här funktionen för att skapa en ny kolumn i dataramen. Den nya koden är följande.

```R
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
```

Kör nu det uppdaterade experimentet och använd utdataloggen för att visa resultaten. Dessa resultat visas i figur 12.

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

*Figur 12. Sammanfattning av dataramen med den ytterligare kolumnen.*

Det ser ut som allt fungerar. Vi har den nya kolumnen med de förväntade värdena i vår dataram.

### <a name="value-transformations"></a>Värdeomvandlingar

I det här avsnittet kommer vi att utföra några enkla omvandlingar på värdena i några av kolumnerna i vår dataram. R-språket stöder nästan godtyckliga värdeomvandlingar. Hänvisningarna i [Ytterligare läsning](#appendixb) nedan innehåller omfattande exempel.

Om du tittar på värdena i sammanfattningarna av vår dataram bör du se något konstigt här. Är mer glass än mjölk som produceras i Kalifornien? Nej, naturligtvis inte, eftersom detta är meningslöst, sorgligt eftersom detta faktum kan vara att vissa av oss glass älskare. Enheterna är olika. Priset är i enheter av amerikanska pounds, är mjölk i enheter av 1 M amerikanska pounds, glass är i enheter på 1.000 US liter, och keso är i enheter på 1.000 us pounds. Förutsatt glass väger ca 6,5 pounds per gallon, kan vi enkelt göra multiplikation för att konvertera dessa värden så att de är alla i lika enheter på 1.000 pounds.

För vår prognosmodell använder vi en multiplikativ modell för trend och säsongsrensning av dessa data. En loggomvandling gör det möjligt för oss att använda en linjär modell, vilket förenklar den här processen. Vi kan tillämpa loggomvandlingen i samma funktion där multiplikatorn tillämpas.

I följande kod definierar jag `log.transform()`en ny funktion och tillämpar den på raderna som innehåller de numeriska värdena. R-funktionen `Map()` används för `log.transform()` att tillämpa funktionen på de markerade kolumnerna i dataramen. `Map()`liknar `apply()` men tillåter mer än en lista med argument till funktionen. Observera att en lista över multiplikatorer `log.transform()` levererar det andra argumentet till funktionen. Funktionen `na.omit()` används som lite rensning för att säkerställa att vi inte har saknade eller odefinierade värden i dataramen.

```R
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
```

Det händer en hel `log.transform()` del i funktionen. De flesta av denna kod är att kontrollera potentiella problem med argument eller hantera undantag, som fortfarande kan uppstå under beräkningarna. Endast ett fåtal rader av denna kod faktiskt göra beräkningar.

Målet med den defensiva programmeringen är att förhindra fel på en enda funktion som förhindrar bearbetning från att fortsätta. Ett abrupt misslyckande med en långvarig analys kan vara ganska frustrerande för användarna. För att undvika den här situationen måste standardreturvärden väljas som begränsar skador på bearbetning nedströms. Ett meddelande produceras också för att varna användarna om att något har gått fel.

Om du inte är van vid defensiv programmering i R, kan all denna kod verkar lite överväldigande. Jag kommer att gå igenom de stora stegen:

1. En vektor med fyra meddelanden har definierats. Dessa meddelanden används för att kommunicera information om några av de möjliga fel och undantag som kan uppstå med den här koden.
2. Jag returnerar ett värde av NA för varje fall. Det finns många andra möjligheter som kan ha färre biverkningar. Jag skulle kunna returnera en vektor av nollor, eller den ursprungliga inmatningsvektorn, till exempel.
3. Kontroller körs på argumenten till funktionen. I varje fall, om ett fel upptäcks, returneras ett standardvärde `warning()` och ett meddelande skapas av funktionen. Jag använder `warning()` snarare `stop()` än som den senare kommer att avsluta verkställigheten, precis vad jag försöker undvika. Observera att jag har skrivit denna kod i en procedurmässig stil, som i detta fall ett funktionellt tillvägagångssätt verkade komplicerat och dunkelt.
4. Loggberäkningarna är inslagna `tryCatch()` så att undantagen inte orsakar ett abrupt stopp för bearbetningen. Utan `tryCatch()` de flesta fel som utlöses av R-funktioner resultera i en stoppsignal, vilket gör just det.

Kör den här R-koden i experimentet och ta en titt på de utskrivna utdata i filen output.log. Du kommer nu att se de omformade värdena för de fyra kolumnerna i loggen, som visas i figur 13.

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

*Figur 13. Sammanfattning av de transformerade värdena i dataramen.*

Vi ser att värderingarna har förändrats. Mjölkproduktionen överstiger nu i hög grad all annan produktion av mejeriprodukter, och erinrar om att vi nu tittar på en loggskala.

Vid denna punkt våra data rensas upp och vi är redo för vissa modellering. Om man tittar på visualisering sammanfattning för resultatdatauppsättning utdata av vår [Kör R Script][execute-r-script] modul, kommer du att se "Månad" kolumnen är "kategorisk" med 12 unika värden, igen, precis som vi ville.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Tidsserieobjekt och korrelationsanalys

I det här avsnittet kommer vi att utforska några grundläggande R-tidsserieobjekt och analysera korrelationerna mellan några av variablerna. Vårt mål är att mata ut en dataram som innehåller parvis korrelationsinformation vid flera fördröjningar.

Den fullständiga R-koden för det här avsnittet finns i [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Tidsserieobjekt i R

Som redan nämnts är tidsserier en serie datavärden indexerade efter tid. R-tidsserieobjekt används för att skapa och hantera tidsindexet. Det finns flera fördelar med att använda tidsserieobjekt. Tidsserieobjekt befriar dig från de många detaljerna för att hantera de tidsserieindexvärden som är inkapslade i objektet. Dessutom tidsserie objekt kan du använda många tidsserier metoder för plottning, utskrift, modellering, etc.

Klassen POSIXct tidsserier används ofta och är relativt enkel. Denna tidsserieklass mäter tiden från epokens början, 1 januari 1970. Vi kommer att använda POSIXct tidsserieobjekt i det här exemplet. Andra allmänt använda R-tidsserier objekt klasser inkluderar zoo och xts, utbyggbara tidsserier.

### <a name="time-series-object-example"></a>Exempel på tidsserieobjekt

Låt oss komma igång med vårt exempel. Dra och släpp en **ny** [Execute R Script-modul][execute-r-script] i experimentet. Anslut utdataporten Result Dataset1 för den befintliga [execute r-skriptmodulen][execute-r-script] till indataporten Dataset1 i den nya [execute r-skriptmodulen.][execute-r-script]

Som jag gjorde för de första exemplen, som vi framsteg genom exemplet, på vissa punkter kommer jag att visa endast inkrementella ytterligare rader R-kod vid varje steg.  

#### <a name="reading-the-dataframe"></a>Läsa dataramen

Som ett första steg ska vi läsa i en dataram och se till att vi får de förväntade resultaten. Följande kod bör göra jobbet.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Kör experimentet. Loggen för den nya formen Kör R-skript ska se ut som bild 14.

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

*Figur 14. Sammanfattning av dataramen i modulen Kör R-skript.*

Dessa data är av de förväntade typerna och formatet. Observera att kolumnen Månad är av typen faktor och har det förväntade antalet nivåer.

#### <a name="creating-a-time-series-object"></a>Skapa ett tidsserieobjekt

Vi måste lägga till ett tidsserieobjekt till vår dataram. Ersätt den aktuella koden med följande, som lägger till en ny kolumn med klassen POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Kolla loggen. Det bör se ut som figur 15.

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

*Figur 15. Sammanfattning av dataramen med ett tidsserieobjekt.*

Vi kan se i sammanfattningen att den nya kolumnen är i själva verket av klass POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Utforska och omvandla data

Låt oss utforska några av variablerna i den här datauppsättningen. En scatterplot matris är ett bra sätt att producera en snabb titt. Jag ersätter `str()` funktionen i föregående R-kod med följande rad.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Kör den här koden och se vad som händer. Tomten som produceras vid R-enhetens port ska se ut som figur 16.

![Spridningsmatris för valda variabler](./media/r-quickstart/fig17.png)

*Figur 16. Spridningsmatris för valda variabler.*

Det finns några udda utseende struktur i relationerna mellan dessa variabler. Kanske beror detta på trender i data och av det faktum att vi inte har standardiserat variablerna.

### <a name="correlation-analysis"></a>Korrelationsanalys

För att utföra korrelationsanalys behöver vi både de-trend och standardisera variablerna. Vi skulle helt `scale()` enkelt kunna använda R-funktionen, som både centra och skalor variabler. Den här funktionen kan mycket väl köras snabbare. Men jag vill visa dig ett exempel på defensiv programmering i R.

Funktionen `ts.detrend()` nedan utför båda dessa åtgärder. Följande två rader med kod de-trend data och sedan standardisera värden.

```R
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
```

Det händer en hel `ts.detrend()` del i funktionen. De flesta av denna kod är att kontrollera potentiella problem med argument eller hantera undantag, som fortfarande kan uppstå under beräkningarna. Endast ett fåtal rader av denna kod faktiskt göra beräkningar.

Vi har redan diskuterat ett exempel på defensiv programmering i Värdeomvandlingar. Båda beräkningsblocken är `tryCatch()`insvepta i . För vissa fel är det vettigt att returnera den ursprungliga inmatningsvektorn, och i andra fall returnerar jag en vektor av nollor.  

Observera att den linjära regression som används för de-trending är en tidsserieregression. Prediktorvariabeln är ett tidsserieobjekt.  

När `ts.detrend()` det väl har definierats tillämpar vi det på variablerna av intresse i vår dataram. Vi måste tvinga den resulterande listan som skapats av `lapply()` datadataframe med hjälp `as.data.frame()`av . På grund av `ts.detrend()`defensiva aspekter av , underlåtenhet att bearbeta en av variablerna kommer inte att förhindra korrekt bearbetning av de andra.  

Den sista raden kod skapar en parvis scatterplot. När R-koden har körts visas resultaten av scatterplot i figur 17.

![Parvis scatterplot av de-trendade och standardiserade tidsserier](./media/r-quickstart/fig18.png)

*Figur 17. Parvis scatterplot av de-trendade och standardiserade tidsserier.*

Du kan jämföra dessa resultat med dem som visas i figur 16. Med trenden bort och variablerna standardiserade, ser vi mycket mindre struktur i relationerna mellan dessa variabler.

Koden för att beräkna korrelationer som R ccf-objekt är följande.

```R
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
```

Om du kör den här koden visas loggen i bild 18.

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

*Figur 18. Lista över ccf-objekt från parvis korrelationsanalys.*

Det finns ett korrelationsvärde för varje fördröjning. Inget av dessa korrelationsvärden är tillräckligt stora för att vara betydande. Vi kan därför dra slutsatsen att vi kan modellera varje variabel oberoende av varandra.

### <a name="output-a-dataframe"></a>Mata ut en dataram
Vi har beräknat parvis korrelationer som en lista över R ccf objekt. Detta utgör lite av ett problem som resultatet Dataset utdataporten verkligen kräver en dataram. Vidare är ccf objektet i sig en lista och vi vill bara värdena i den första delen av denna lista, korrelationer på de olika släpar.

Följande kod extraherar fördröjningsvärdena från listan över ccf-objekt, som själva är listor.

```R
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
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

Den första raden av kod är lite knepigt, och en viss förklaring kan hjälpa dig att förstå det. Arbeta inifrån och ut har vi följande:

1. Operatorn '**[[**' med argumentet '**1**' väljer korrelationsvektorn vid fördröjningarna från det första elementet i ccf-objektlistan.
2. Funktionen `do.call()` använder `rbind()` funktionen över elementen i `lapply()`listan returnerar av .
3. Funktionen `data.frame()` tvingar resultatet som produceras `do.call()` av till en dataram.

Observera att radnamnen finns i en kolumn i dataramen. Om du gör det bevaras radnamnen när de matas ut från [Execute R Script][execute-r-script].

Om du kör koden visas utdata som visas i bild 19 när jag **visualiserar** utdata vid datauppsättningsporten Resultat. Radnamnen finns i den första kolumnen, som avsett.

![Resultat från korrelationsanalysen](./media/r-quickstart/fig20.png)

*Figur 19. Resultat från korrelationsanalysen.*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Exempel på tidsserier: säsongsprognoser

Våra data är nu i en form som lämpar sig för analys, och vi har fastställt att det inte finns några signifikanta samband mellan variablerna. Låt oss gå vidare och skapa en prognosmodell för tidsserier. Med hjälp av denna modell kommer vi att prognos Kalifornien mjölkproduktion för 12 månader 2013.

Vår prognosmodell kommer att ha två komponenter, en trendkomponent och en säsongsbunden komponent. Den fullständiga prognosen är produkten av dessa två komponenter. Den här typen av modell kallas en multiplikativ modell. Alternativet är en additiv modell. Vi har redan tillämpat en log transformation till variablerna av intresse, vilket gör denna analys tractable.

Den fullständiga R-koden för det här avsnittet finns i [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Skapa dataramen för analys

Börja med att lägga till en **ny** [Execute R Script-modul][execute-r-script] i experimentet. Anslut **resultatdatauppsättningsutdata** för den befintliga [Execute R Script-modulen][execute-r-script] till **datauppsättning1-indata** för den nya modulen. Resultatet bör se ut ungefär som figur 20.

![Experimentet med den nya execute r-skriptmodulen har lagts till](./media/r-quickstart/fig21.png)

*Figur 20. Experimentet med den nya execute r-skriptmodulen har lagts till.*

Som med korrelationsanalysen som vi just slutfört måste vi lägga till en kolumn med ett POSIXct tidsserieobjekt. Följande kod kommer att göra just detta.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Kör den här koden och titta på loggen. Resultatet ska se ut som figur 21.

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

*Figur 21. En sammanfattning av dataramen.*

Med detta resultat är vi redo att starta vår analys.

### <a name="create-a-training-dataset"></a>Skapa en träningsdatauppsättning

Med dataramen konstruerad måste vi skapa en utbildningsdatauppsättning. Dessa uppgifter kommer att omfatta alla observationer utom de sista 12, av år 2013, som är vår testdatauppsättning. Följande kod delar dataramen och skapar områden för mejeriproduktionen och prisvariablerna. Jag skapar sedan tomter av de fyra produktions- och prisvariablerna. En anonym funktion används för att definiera vissa förstärkningar för diagram och sedan `Map()`iterera över listan över de andra två argumenten med . Om du tänker att en för slinga skulle ha fungerat bra här, du har rätt. Men eftersom R är ett funktionellt språk jag visar dig en funktionell strategi.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Om du kör koden skapas serierna av tidsserier från R-enhetens utdata som visas i figur 22. Observera att tidsaxeln är i enheter av datum, en trevlig fördel med tidsserier plotmetoden.

![Första tidsserietomter i Kalifornien mejeriproduktion och prisdata](./media/r-quickstart/unnamed-chunk-161.png)

![Andra av tidsserietomter i Kalifornien mejeriproduktion och prisdata](./media/r-quickstart/unnamed-chunk-162.png)

![Tredjedel av tidsserietomter i Kalifornien mejeriproduktion och prisdata](./media/r-quickstart/unnamed-chunk-163.png)

![Fjärde av tid serie tomter i Kalifornien mejeriproduktion och prisdata](./media/r-quickstart/unnamed-chunk-164.png)

*Figur 22. Tidsserier tomter i Kalifornien mejeriproduktion och prisdata.*

### <a name="a-trend-model"></a>En trendmodell

Efter att ha skapat en tid serie objekt och har haft en titt på data, låt oss börja konstruera en trendmodell för Kalifornien mjölkproduktion data. Vi kan göra detta med en tidsserieregression. Det framgår dock av handlingen att vi kommer att behöva mer än en sluttning och avlyssna för att exakt modellera den observerade trenden i träningsdata.

Med tanke på den lilla omfattningen av data kommer jag att bygga modellen för trend i RStudio och sedan klippa ut och klistra in den resulterande modellen i Azure Machine Learning Studio (klassisk). RStudio tillhandahåller en interaktiv miljö för den här typen av interaktiv analys.

Som ett första försök, kommer jag att försöka en polynom regression med befogenheter upp till 3. Det finns en verklig risk för överpassning av dessa typer av modeller. Därför är det bäst att undvika höga order. Funktionen `I()` hämmar tolkningen av innehållet (tolkar innehållet "som det är") och låter dig skriva en bokstavligen tolkad funktion i en regressionsekvation.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

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

Från P-värden (`Pr(>|t|)`) i denna utgång kan vi se att kvadrattermen kanske inte är betydande. Jag kommer `update()` att använda funktionen för att ändra denna modell genom att släppa kvadrattermen.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

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

Det här ser bättre ut. Alla villkor är betydande. Värdet 2e-16 är dock ett standardvärde och bör inte tas på för stort allvar.  

Som ett sanity test, låt oss göra en tid serie tomt i Kalifornien mejeriproduktion data med trendkurvan visas. Jag har lagt till följande kod i Azure Machine Learning Studio (klassisk) [Kör R Script-modell][execute-r-script] (inte RStudio) för att skapa modellen och göra en tomt. Resultatet visas i figur 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Kalifornien mjölkproduktion data med trendmodell visas](./media/r-quickstart/unnamed-chunk-18.png)

*Figur 23. Kalifornien mjölkproduktionsdata med trendmodell visas.*

Det ser ut som trendmodellen passar data ganska bra. Vidare verkar det inte finnas bevis för övermontering, såsom udda vicka i modellkurvan.  

### <a name="seasonal-model"></a>Säsongsmodell

Med en trendmodell i handen måste vi driva på och inkludera säsongseffekterna. Vi kommer att använda månaden på året som en dummy variabel i den linjära modellen för att fånga månads-för-månad-effekten. Observera att när du introducerar faktorvariabler i en modell får skärningspunkten inte beräknas. Om du inte gör detta är formeln överspecificerad och R kommer att släppa en av de önskade faktorerna men behålla skärningspunkten.

Eftersom vi har en tillfredsställande trendmodell kan vi använda `update()` funktionen för att lägga till de nya termerna till den befintliga modellen. -1 i uppdateringsformeln släpper skärningspunkten. Fortsätter i RStudio för tillfället:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

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

Vi ser att modellen inte längre har en intercept sikt och har 12 betydande månader faktorer. Detta är precis vad vi ville se.

Låt oss göra en annan tid serie tomt i Kalifornien mejeriproduktion data för att se hur väl den säsongsbetonade modellen fungerar. Jag har lagt till följande kod i Azure Machine Learning Studio (klassisk) [Kör R Script][execute-r-script] för att skapa modellen och göra en tomt.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Om du kör den här koden i Azure Machine Learning Studio (klassisk) skapas ritytan som visas i figur 24.

![Kalifornien mjölkproduktion med modell inklusive säsongsbetonade effekter](./media/r-quickstart/unnamed-chunk-20.png)

*Figur 24. Kalifornien mjölkproduktion med modell inklusive säsongsbetonade effekter.*

Det är ganska uppmuntrande att uppgifterna i figur 24 är lämpliga. Både trenden och säsongseffekten (månatlig variation) ser rimliga ut.

Som en annan kontroll på vår modell, låt oss ta en titt på resterna. Följande kod beräknar de förväntade värdena från våra två modeller, beräknar restvärdena för säsongsmodellen och ritar sedan dessa rester för träningsdata.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Restområdet visas i figur 25.

![Rester av säsongsmodellen för utbildningsdata](./media/r-quickstart/unnamed-chunk-21.png)

*Figur 25. Rester av säsongsmodellen för träningsdata.*

Dessa rester ser rimliga ut. Det finns ingen särskild struktur, förutom effekten av lågkonjunkturen 2008-2009, som vår modell inte tar hänsyn till särskilt väl.

Det område som visas i figur 25 är användbart för att upptäcka eventuella tidsberoende mönster i resterna. Den uttryckliga metoden för att beräkna och rita resterna jag använde placerar resterna i tidsordning på tomten. Om, å andra sidan, hade `milk.lm$residuals`jag ritat, skulle tomten inte ha varit i tidsordning.

Du kan `plot.lm()` också använda för att producera en serie diagnostiska ritplaner.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Denna kod producerar en serie diagnostiska områden som visas i figur 26.

![Första av diagnostiska tomter för säsongsmodellen](./media/r-quickstart/unnamed-chunk-221.png)

![Andra diagnostiska tomter för säsongsmodellen](./media/r-quickstart/unnamed-chunk-222.png)

![Tredjedel av de diagnostiska tomterna för säsongsmodellen](./media/r-quickstart/unnamed-chunk-223.png)

![Fjärde av diagnostiska tomter för säsongsmodellen](./media/r-quickstart/unnamed-chunk-224.png)

*Figur 26. Diagnostiska tomter för säsongsmodellen.*

Det finns några mycket inflytelserika punkter som identifierats i dessa tomter, men inget som orsakar stor oro. Vidare kan vi se från normal Q-Q tomt att resterna är nära normalt distribueras, ett viktigt antagande för linjära modeller.

### <a name="forecasting-and-model-evaluation"></a>Prognostisering och modellutvärdering

Det finns bara en sak till att göra för att komplettera vårt exempel. Vi måste beräkna prognoser och mäta felet mot de faktiska uppgifterna. Vår prognos kommer att vara för de 12 månaderna 2013. Vi kan beräkna ett felmått för den här prognosen till faktiska data som inte ingår i vår utbildningsdatauppsättning. Dessutom kan vi jämföra prestanda på 18 års utbildningsdata med 12 månaders testdata.  

Ett antal mått används för att mäta prestanda för tidsseriemodeller. I vårt fall kommer vi att använda root mean square (RMS) fel. Följande funktion beräknar RMS-felet mellan två serier.  

```R
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
```

Som med `log.transform()` den funktion som vi diskuterade i avsnittet "Värdeomvandlingar" finns det en hel del felkontroll och undantagsåterställningskod i den här funktionen. De principer som används är desamma. Arbetet utförs på två ställen `tryCatch()`insvept i . För det första är tidsserierna exponentierade, eftersom vi har arbetat med loggarna över värdena. För det andra beräknas det faktiska RMS-felet.  

Utrustad med en funktion för att mäta RMS-felet, låt oss bygga och mata ut en dataram som innehåller RMS-fel. Vi kommer att inkludera termer för trendmodellen ensam och den kompletta modellen med säsongsfaktorer. Följande kod gör jobbet genom att använda de två linjära modeller vi har konstruerat.

```R
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
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Om du kör den här koden visas utdata som visas i bild 27 vid utdataporten Result Dataset.

![Jämförelse av RMS-fel för modellerna](./media/r-quickstart/fig26.png)

*Figur 27. Jämförelse av RMS-fel för modellerna.*

Från dessa resultat ser vi att lägga till säsongsfaktorer till modellen minskar RMS-felet avsevärt. Inte alltför överraskande är RMS-felet för träningsdata lite mindre än för prognosen.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Guide till RStudio-dokumentation

RStudio är ganska väl dokumenterat. Här är några länkar till de viktigaste delarna i RStudio-dokumentationen för att komma igång.

* **Skapa projekt** - Du kan organisera och hantera din R-kod i projekt med hjälp av RStudio. Mer information [finns i Använda projekt.](https://support.rstudio.com/hc/articles/200526207-Using-Projects) Jag rekommenderar att du följer dessa anvisningar och skapar ett projekt för R-kodexemplen i den här artikeln.  
* **Redigera och köra R-kod** - RStudio ger en integrerad miljö för redigering och körning av R-kod. Mer information finns i [Redigerings- och körkoden.](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code)
* **Felsökning** - RStudio innehåller kraftfulla felsökningsfunktioner. Mer information om dessa funktioner [finns i Felsökning med RStudio.](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) Information om felsökningsfunktioner för brytpunkt finns i [Felsökning av brytpunkter](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a name="further-reading"></a><a id="appendixb"></a>Mer information

Den här R-programmeringsstudien täcker grunderna i vad du behöver för att använda R-språket med Azure Machine Learning Studio (klassisk). Om du inte är bekant med R, två introduktioner finns på CRAN:

* [R för nybörjare](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) av Emmanuel Paradis är ett bra ställe att börja.  
* [En introduktion till R](https://cran.r-project.org/doc/manuals/R-intro.html) av W. N. Venables et. Al. går in i lite mer djup.

Det finns många böcker på R som kan hjälpa dig att komma igång. Här är några jag tycker är användbara:

* **The Art of R Programming: A Tour of Statistical Software Design** av Norman Matloff är en utmärkt introduktion till programmering i R.  
* **R Kokbok** av Paul Teetor ger ett problem och lösning strategi för att använda R.  
* **R in Action** av Robert Kabacoff är en annan användbar introduktionsbok. Den tillhörande [Quick R-webbplatsen](https://www.statmethods.net/) är en användbar resurs.
* **R Inferno** av Patrick Burns är en förvånansvärt humoristisk bok som handlar om ett antal knepiga och svåra ämnen som kan uppstå vid programmering i R. Boken är tillgänglig gratis på [The R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Om du vill ha en djupdykning i avancerade ämnen i R, ta en titt på boken **Advanced R** av Hadley Wickham. Online-versionen av denna bok är [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/)tillgänglig gratis på .

En katalog med R-tidsseriepaket finns i [CRAN Task View: Time Series Analysis](https://cran.r-project.org/web/views/TimeSeries.html). Information om specifika objektpaket för tidsserier bör du läsa dokumentationen för det paketet.

Boken **Introductory Time Series** med R av Paul Cowpertwait och Andrew Metcalfe ger en introduktion till att använda R för tidsserieanalys. Många fler teoretiska texter ger R exempel.

Här är några bra internet resurser:

* DataCamp lär R i bekvämligheten av din webbläsare med videolektioner och kodningsövningar. Det finns interaktiva tutorials på de senaste R-tekniker och paket. Ta gratis [interaktiva R handledning](https://www.datacamp.com/courses/introduction-to-r).
* [Lär R Programmering, Den definitiva guide](https://www.programiz.com/r-programming) från Programiz.
* En snabb [R Tutorial](https://www.cyclismo.org/tutorial/R/) av Kelly Black från Clarkson University.
* Det finns över 60 R resurser som anges på [Top R språkresurser för att förbättra dina datakunskaper](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
