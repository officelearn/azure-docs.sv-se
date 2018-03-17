---
title: Datavetenskap med den Linux datavetenskap virtuell dator i Azure | Microsoft Docs
description: "Hur du utför flera gemensamma datavetenskap med Linux datavetenskap VM."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: bradsev;paulsh
ms.openlocfilehash: 9b8a9b9bba242fd7c86dc285a77317a5821948df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Datavetenskap med en Linux datavetenskap virtuell dator på Azure
Den här genomgången visar hur du utför flera gemensamma datavetenskap med Linux datavetenskap VM. Linux Data vetenskap virtuell dator (DSVM) är en avbildning av virtuell dator som är tillgängliga på Azure som är förinstallerade med en samling verktyg som används ofta för dataanalys och maskininlärning. Viktiga programkomponenterna specificerade i den [etablera Linux datavetenskap virtuell dator](linux-dsvm-intro.md) avsnittet. VM-avbildning gör det enkelt att komma igång gör datavetenskap i minuter, utan att installera och konfigurera verktygen separat. Du kan enkelt skala upp den virtuella datorn, om det behövs och stoppa den inte under användning. Den här resursen är därför både elastisk och kostnadseffektiv.

Datavetenskap uppgifter som visas i den här genomgången följer du stegen som beskrivs i den [Team datavetenskap Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Denna process tillhandahåller systematiskt till datavetenskap som gör att grupper data forskare samarbeta effektivt under hela livscykeln för att skapa intelligent program. Vetenskap av data innehåller också en iterativ ramverk för datavetenskap som kan följas av en enskild.

Vi analysera den [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datauppsättning i den här genomgången. Det här är en uppsättning e-postmeddelanden som är markerade som skräppost eller skinka (vilket innebär att de inte är skräppost), och innehåller även vissa statistik på innehållet i e-postmeddelanden. Statistik ingår beskrivs i nästa men ett avsnitt.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan använda en Linux datavetenskap virtuell dator, måste du ha följande:

* En **Azure-prenumeration**. Om du inte redan har en, se [skapa din kostnadsfria Azure-konto idag](https://azure.microsoft.com/free/).
* En [ **Linux datavetenskap VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Information om att etablera den här virtuella datorn finns i [etablera Linux datavetenskap virtuell dator](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) installerat på datorn och öppna en XFCE-session. Information om hur du installerar och konfigurerar en **X2Go klienten**, se [installera och konfigurera X2Go klienten](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* För en jämnare rullande upplevelse växla gfx.xrender.enabled-flaggan i om: config i webbläsaren FireFox för virtuella datorer. [Se mer här. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Överväg också att växla *mousewheel.enable_pixel_scrolling* till False. [Anvisningarna här.](https://support.mozilla.org/en-US/questions/981140)
* En **AzureML konto**. Om du inte redan har en registrering för nya på den [AzureML webbsida](https://studio.azureml.net/). Det finns en fri användning nivå som hjälper dig att komma igång.

## <a name="download-the-spambase-dataset"></a>Hämta spambase datauppsättningen
Den [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) dataset är en relativt liten uppsättning data som innehåller endast 4601 exempel. Det här är en lämplig storlek som ska användas när du visar att vissa av de viktigaste funktionerna i den virtuella datorn vetenskap Data eftersom den håller resurskraven liten.

> [!NOTE]
> Den här genomgången skapades på en D2 v2-format Linux datavetenskap virtuell dator. Den här storleken DSVM är kan hantera procedurerna i den här genomgången.
>
>

Om du behöver mer lagringsutrymme kan du skapa ytterligare diskar och koppla dem till den virtuella datorn. Diskarna använder beständiga Azure storage, så data bevaras även om servern etableras på grund av storleksändring eller är avstängd. Om du vill lägga till en disk och koppla den till den virtuella datorn, följ instruktionerna i [lägga till en disk till en Linux-VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). De här stegen använder Azure-kommandoradsgränssnittet (Azure CLI) som redan är installerad på DSVM. De här procedurerna kan därför göras helt från Virtuellt datorn. Ett annat alternativ att öka lagringsutrymmet är att använda [Azure files](../../storage/files/storage-how-to-use-files-linux.md).

Hämtar data, öppna ett terminalfönster och kör det här kommandot:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Den hämta filen har inte en rubrikrad vi skapa en annan fil som har ett sidhuvud. Kör detta kommando för att skapa en fil med lämpliga rubriker:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Sedan sammanfoga två filer tillsammans med kommandot:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Datamängden har flera typer av statistik på varje e-postmeddelande:

* Kolumner som ***word\_frekvens\_WORD*** visar procentandelen av orden i e-postmeddelandet som matchar *WORD*. Till exempel om *word\_frekvens\_Se* är 1, 1% av alla ord i e-postmeddelandet har *Se*.
* Kolumner som ***char\_frekvens\_CHAR*** ange hur stor procentandel av alla tecken i e-postmeddelandet som var *CHAR*.
* ***kapital\_kör\_längd\_längsta*** är den längsta en sekvens med stora bokstäver.
* ***kapital\_kör\_längd\_genomsnittlig*** är den genomsnittliga längden på alla sekvenser av stora bokstäver.
* ***kapital\_kör\_längd\_totala*** är den totala längden för alla sekvenser av stora bokstäver.
* ***skräppost*** anger om e-postmeddelandet ansågs skräppost eller inte (1 = skräppost, 0 = inte skräppost).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Utforska datamängd med Microsoft R öppna
Vi granska data och utföra vissa grundläggande maskininlärning R. Den virtuella datorn vetenskapliga Data som medföljer [Microsoft R öppna](https://mran.revolutionanalytics.com/open/) förinstallerat. Flertrådade matematiska bibliotek i den här versionen av R ger bättre prestanda än olika Enkeltrådig versioner. Microsoft R öppna ger också reproducerbara med hjälp av en ögonblicksbild av databasen för CRAN-paket.

För att få kopior av kodexempel som används i den här genomgången klona den **Azure-Machine-Learning--datavetenskap** databas med hjälp av git, som är förinstallerat på den virtuella datorn. Kör från kommandoraden git:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Öppna ett terminalfönster och starta en ny R-session med interaktiva R-konsolen.

> [!NOTE]
> Du kan också använda RStudio för följande procedurer. Om du vill installera RStudio, kör du kommandot i en terminal: `./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Om du vill importera data och ställa in miljön, kör du:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Visa sammanfattande statistik om varje kolumn:

    summary(data)

För en annan vy av data:

    str(data)

Detta visar vilken typ av varje variabel och första några värden i datauppsättningen.

Den *skräppost* lästes kolumnen som ett heltal, men det är faktiskt en kategoriska variabeln (eller faktor). Att ange dess typ:

    data$spam <- as.factor(data$spam)

Vissa undersökande analyser, använder den [ggplot2](http://ggplot2.org/) paketet, ett populärt graphing bibliotek för R som redan är installerad på den virtuella datorn. Meddelande från sammanfattningsdata visas tidigare, vi har sammanfattande statistik på frekvensen för utropstecken tecken. Vi ska rita dessa frekvenser med följande kommandon:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Eftersom fältet noll är skeva området, ska vi ta bort det:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Det finns en icke-trivial densitet ovan 1 som ser ut intressant. Nu ska vi titta på bara data:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Dela sedan upp den av skräppost vs skinka:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

De här exemplen ska att du kan göra liknande områden i andra kolumner att utforska data i dem.

## <a name="train-and-test-an-ml-model"></a>Träna och testa en ML-modell
Nu ska vi träna machine learning-modeller att klassificera e-post i datamängden innehåller span eller skinka några. Vi träna en beslut trädet modell och en slumpmässigt Skogsmodell i det här avsnittet och testa sina korrektheten i sina förutsägelser.

> [!NOTE]
> Paketets rpart (rekursiv partitionering och Regressionsträd) som används i följande kod är redan installerad på den virtuella datorn vetenskapliga Data.
>
>

Först ska vi dela datauppsättningen i uppsättningar för träning och testning:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Och sedan skapa ett beslutsträd för att klassificera e-postmeddelanden.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Här är ett resultat:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Ta reda på hur väl utförs på träningsmängden genom att använda följande kod:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

För att avgöra hur väl utförs på uppsättningen test:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Prova med en slumpmässig Skogsmodell också. Slumpmässiga skogar tränar en mängd olika beslutsträd och utdata för en klass som är i läget för klassificeringar från alla enskilda beslutsträd. De ger mer kraftfulla machine learning-metoden som de rätta för tendens av en modell för trädet beslut att overfit en utbildning dataset.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Distribuera en modell till Azure ML
[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) är en molnbaserad tjänst som gör det enkelt att skapa och distribuera förutsägelseanalysmodeller. En bra funktioner i AzureML är möjligheten att publicera någon R-funktion som en webbtjänst. AzureML-R-paket är det enkelt att göra direkt från våra R-session på DSVM distribution.

Du måste logga in på Azure Machine Learning Studio om du vill distribuera beslut trädet koden från föregående avsnitt. Du behöver ditt arbetsyte-ID och ett Autentiseringstoken för att logga in. Att hitta dessa värden och initiera AzureML-variabler med dem:

Välj **inställningar** på den vänstra menyn. Obs din **ARBETSYTE-ID**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Välj **auktorisering token** från Administration-menyn och Observera din **primära auktorisering Token**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Läs in den **AzureML** paketet och ange sedan värdena för variabler med arbetsytan och token-ID i R-sessionen på DSVM:

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Vi kan förenkla modellen för att göra det enklare att implementera den här demonstrationen. Välj tre variablerna i beslutsträdet närmast roten och skapa ett nytt träd med hjälp av de tre variablerna:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Vi behöver en förutsägelsefunktionen som använder funktionerna som indata och returnerar de förväntade värdena:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publicera funktionen predictSpam till AzureML med hjälp av den **publishWebService** funktionen:

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Den här funktionen använder den **predictSpam** fungera, skapar en webbtjänst med namnet **spamWebService** med definierade in- och utdataenheter och returnerar information om ny slutpunkt.

Visa information om publicerade webb-tjänsten, inklusive dess API-slutpunkt och nycklar med kommandot:

    spamWebService[[2]]

För att testa första 10 raderna testets ange:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Använda andra verktyg som är tillgängliga
De återstående avsnitten visar hur du använder vissa av verktygen som installeras på Linux datavetenskap VM. Här är listan över verktyg som beskrivs:

* XGBoost
* Python
* Jupyterhub
* Spännen
* PostgreSQL & Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) är ett verktyg som ger en snabb och exakt ökat trädet implementering.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost kan också anropa från python eller en kommandorad.

## <a name="python"></a>Python
För utveckling med hjälp av Python, har Anaconda Python-distributioner 2.7 och 3.5 installerats i DSVM.

> [!NOTE]
> Anaconda distribution innehåller [Condas](http://conda.pydata.org/docs/index.html), som kan användas för att skapa anpassade miljöer för Python som har olika versioner och/eller paket som är installerade.
>
>

Vi läses in några av spambase datauppsättningen och klassificera e-postmeddelanden med support vector datorer i scikit-Läs mer:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Att göra förutsägelser:

    clf.predict(X.ix[0:20, :])

Om du vill visa hur du publicerar en AzureML-slutpunkt, se en enklare modell tre variabler som vi gjorde när det tidigare publicerade R-modellen.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Att publicera modellen till AzureML:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

> [!NOTE]
> Detta är endast tillgängligt för python 2.7 och ännu stöds inte på 3.5. Kör med **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
Anaconda-distribution i DSVM levereras med en Jupyter-anteckningsbok en plattformsoberoende miljö att dela Python, R eller Julia kod och analys. Jupyter-anteckningsbok sker via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn och lösenord vid ***https://\<VM DNS-namn eller IP-adress\>: 8000 /***. Alla konfigurationsfiler för JupyterHub finns i katalogen **/etc/jupyterhub**.

Flera exempel bärbara datorer har redan installerats på den virtuella datorn:

* Finns det [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) för en bärbar dator Python exempel.
* Se [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) ett exempel **R** bärbar dator.
* Finns det [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) för en annan exemplet **Python** bärbar dator.

> [!NOTE]
> Julia språket är också tillgänglig från kommandoraden på Linux datavetenskap VM.
>
>

## <a name="rattle"></a>Spännen
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (den R analytiska verktyget att lära dig enkelt) är ett grafiskt verktyg för R för datautvinning. Den har ett intuitivt gränssnitt som gör det enkelt att läsa in, utforska, transformera data och skapa och utvärdera modellerna.  Artikeln [Rattle: A Data Mining GUI för R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) ger en genomgång som visar dess funktioner.

Installera och starta spännen med följande kommandon:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Installation krävs inte på DSVM. Men spännen kan du uppmanas att installera ytterligare paket när det läses in.
>
>

Spännen använder ett fliken-baserat gränssnitt. De flesta av flikarna motsvarar stegen i den [datavetenskap Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), t.ex. läser in data eller Utforska den. Vetenskap av data som flödar från vänster till höger i flikarna. Men fliken senaste innehåller en logg över R-kommandon som körs av spännen.

Läsa och konfigurera datauppsättningen:

* Om du vill läsa in filen, Välj den **Data** sedan fliken
* Välj Väljaren bredvid **Filename** och välj **spambaseHeaders.data**.
* Läsa in filen. Välj **Execute** i den översta raderna. Du bör se en sammanfattning av varje kolumn, inklusive identifierade datatyp, oavsett om det är ett indata, ett mål eller annan typ av variabeln och antalet unika värden.
* Spännen har identifierats i **skräppost** kolumnen som mål. Markera kolumnen som skräppost och sedan ange den **Data måltypen** till **Categoric**.

Utforska data:

* Välj den **utforska** fliken.
* Klicka på **sammanfattning**, sedan **kör**, för att se viss information om variabeln typer och vissa sammanfattande statistik.
* Om du vill visa andra typer av statistik om varje variabel, välj andra alternativ som **beskriver** eller **grunderna**.

Den **utforska** fliken kan du skapa många insiktsfulla områden. Att rita ett histogram data:

* Välj **distributioner**.
* Kontrollera **Histogram** för **word_freq_remove** och **word_freq_you**.
* Välj **köra**. Du bör se både densitet områden i ett enda graph-fönster, där det är tydligt att ordet ”du” visas oftare i e-postmeddelanden än ”ta bort”.

Korrelations-områdena är också intressant. Skapa en:

* Välj **korrelation** som den **typen**, sedan
* Välj **köra**.
* Spännen varnar rekommenderar högst 40 variabler. Välj **Ja** att visa området.

Det finns vissa intressanta korrelationer som tas upp: ”teknik” korreleras starkt ”HP” och ”labs”, till exempel. Det är också starkt serverbearbetningstiden ”650” eftersom riktnummer dataset bidragsgivare 650.

De numeriska värdena för korrelationer mellan ord är tillgängliga i fönstret utforska. Det är intressant att Observera, till exempel ”teknik” korreleras negativt med ”din/ditt” och ”pengar”.

Spännen kan omvandla dataset för att hantera några vanliga problem. Exempelvis kan du skala om funktioner, sedan imputera värden som saknas, hantera avvikare och ta bort variabler eller observationer med data som saknas. Spännen kan även identifiera kopplingen regler mellan observationer och/eller variabler. De här flikarna ligger utanför omfånget för den här introduktionsgenomgång.

Spännen kan också utföra klustret analys. Vi ska utesluta vissa funktioner som gör att utdata blir lättare att läsa. På den **Data** , Välj **Ignorera** bredvid varje variabler utom objekten tio:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* skräppost

Gå tillbaka till den **klustret** , Välj **KMeans**, och ange den *antalet kluster* till 4. Sedan **köra**. Resultatet visas i utdatafönstret. Ett kluster har hög frekvens av ”george” och ”hp” och beror troligen på ett legitimt företag e-postmeddelande.

Att skapa en enkel beslut trädet maskininlärningsmodell:

* Välj den **modellen** fliken
* Välj **träd** som den **typen**.
* Välj **Execute** ska visas i trädet i textformat i utdatafönstret.
* Välj den **Rita** för att visa en grafisk version. Detta liknar ganska trädet vi fick tidigare med hjälp av *rpart*.

En bra funktioner i spännen är möjligheten att köra flera metoder för machine learning och snabbt utvärdera dem. Här följer du proceduren:

* Välj **alla** för den **typen**.
* Välj **köra**.
* När den är klar kan du klicka på varje enskild **typen**, till exempel **SVM**, och visa resultatet.
* Du kan också jämföra prestanda för modeller i valideringen anges med den **utvärdera** fliken. Till exempel den **fel matrisen** visar du förvirring matris, övergripande fel och genomsnittlig klass-fel för varje modell på uppsättningen validering.
* Du kan också rita ROC kurvor, utföra analyser av känslighet och göra andra typer av modellen.

När du är klar med att skapa modeller, välja den **loggen** att visa R-koden körs av spännen under din session. Du kan välja den **exportera** för att spara den.

> [!NOTE]
> Det finns ett fel i den aktuella versionen av spännen. Om du vill ändra skriptet eller använda den för att upprepa steg senare, måste du infoga tecknet # framför * exportera den här loggen... * texten i loggen.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
DSVM medföljer PostgreSQL installerad. PostgreSQL är en avancerad relationsdatabas med öppen källkod. Det här avsnittet visar hur du läsa vår datauppsättning för skräppost i PostgreSQL och fråga den.

Innan du kan läsa in data som du vill tillåta en lösenordsautentisering från den lokala värden. Vid en kommandotolk:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Längst ned i konfigurationsfilen är flera rader som innehåller information om de tillåtna anslutningarna:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Ändra raden ”IPv4 lokala anslutningar” om du vill använda md5 i stället för ident, så att vi kan logga in med ett användarnamn och lösenord:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Och starta om tjänsten postgres:

    sudo systemctl restart postgresql

Om du vill starta psql, en interaktiv terminal PostgreSQL som inbyggda postgres användare, kör du följande kommando från en kommandotolk:

    sudo -u postgres psql

Skapa ett nytt användarkonto med samma användarnamn som Linux-konto du är för närvarande inloggad som och ge det ett lösenord:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Logga sedan in att psql som dina användare:

    psql

Och importera data till en ny databas:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Nu ska vi utforska data och köra några frågor med **Squirrel SQL**, ett grafiskt verktyg som gör att du kan interagera med databaser via en JDBC-drivrutinen.

Kom igång genom att starta Squirrel SQL på menyn program. Att ställa in drivrutinen:

* Välj **Windows**, sedan **visa drivrutiner**.
* Högerklicka på **PostgreSQL** och välj **ändra drivrutinen**.
* Välj **Extra klassen sökvägen**, sedan **lägga till**.
* Ange ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** för den **filnamn** och
* Välj **öppna**.
* Välj drivrutiner i listan och markera **org.postgresql.Driver** i **klassnamn**, och välj **OK**.

Konfigurera anslutningen till den lokala servern:

* Välj **Windows**, sedan **Visa alias.**
* Välj den  **+**  för att göra ett nytt alias.
* Ge den namnet *skräppost databasen*, Välj **PostgreSQL** i den **drivrutinen** listrutan.
* Ange Webbadressen *jdbc:postgresql://localhost/spam*.
* Ange din *användarnamn* och *lösenord*.
* Klicka på **OK**.
* Öppna den **anslutning** fönstret dubbelklickar du på den ***skräppost databasen*** alias.
* Välj **Anslut**.

Köra några frågor:

* Välj den **SQL** fliken.
* Ange en enkel fråga som `SELECT * from data;` i textrutan fråga överst på SQL-fliken.
* Tryck på **Ctrl-ange** att köra den. Som standard returnerar Squirrel SQL de första 100 raderna från frågan.

Det finns många fler frågor som du kan köra om du vill utforska informationen. Hur fungerar till exempel frekvensen av ordet *Se* skiljer sig åt mellan skräppost och skinka?

    SELECT avg(word_freq_make), spam from data group by spam;

Eller vilka är de egenskaper som e-post som innehåller ofta *3d*?

    SELECT * from data order by word_freq_3d desc;

De flesta e-postmeddelanden som har en hög förekomst av *3d* är uppenbarligen skräppost, så det kan vara en användbar funktion för att skapa en förutsägelsemodell för att klassificera e-postmeddelanden.

Om du vill utföra maskininlärning med data som lagras i en PostgreSQL-databas kan du använda [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Azure SQL Data Warehouse är en molnbaserad skalbar databas som kan bearbeta massiva mängder data, relationella såväl som icke-relationella. Mer information finns i [vad är Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

För att ansluta till data warehouse och skapa tabellen, kör du följande kommando från en kommandotolk:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Vid sqlcmd-kommandotolk:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopiera data med bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Radbrytningar i den hämta filen är Windows-format, men bcp förväntar UNIX-format, så vi behöver berätta bcp som med - r-flaggan.
>
>

Och fråga med sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Du kan också fråga med Squirrel SQL. Följa liknande steg för PostgreSQL, med hjälp av Microsoft MSSQL Server JDBC Driver som finns i ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Nästa steg
En översikt över ämnen som vägleder dig igenom de aktiviteter som utgör processen datavetenskap i Azure finns [Team datavetenskap Process](http://aka.ms/datascienceprocess).

En beskrivning av andra slutpunkt till slutpunkt-genomgångar som visar stegen i teamet vetenskap av data för specifika scenarier finns [Team datavetenskap Process genomgång](../team-data-science-process/walkthroughs.md). Genomgångar visar även hur du kombinerar molnet och lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program.
