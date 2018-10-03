---
title: Datavetenskap med Linux Data Science Virtual Machine i Azure | Microsoft Docs
description: Hur du utför flera vanliga datavetenskapsuppgifter med den virtuella datorn datavetenskap för Linux.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: gokuma
ms.openlocfilehash: c125f503166e3f63599dbd09acf42c69fab8d2ce
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044662"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Datavetenskap med en Linux virtuell dator för datavetenskap på Azure
Den här genomgången visar hur du utför flera vanliga datavetenskapsuppgifter med den virtuella datorn datavetenskap för Linux. Linux Data Science Virtual Machine (DSVM) är en avbildning av virtuell dator som är tillgängliga på Azure som är förinstallerade med en uppsättning verktyg som ofta används för dataanalys och maskininlärning. Viktiga programkomponenterna är uppdelat i den [etablera Linux Data Science Virtual Machine](linux-dsvm-intro.md) avsnittet. VM-avbildning gör det enkelt att komma igång datavetenskap på några minuter, utan att behöva installera och konfigurera var och en av verktyg individuellt. Du kan enkelt skala upp den virtuella datorn, om det behövs och stoppa den när den inte används. Den här resursen är därför både elastisk och kostnadseffektiv.

Data science-åtgärder som visas i den här genomgången följer du stegen som beskrivs i den [Team Data Science Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Den här processen ger en systematisk metod för datavetenskap som gör det möjligt för datautvecklarteam att samarbeta effektivt under hela livscykeln för att skapa intelligenta program. Data science process ger också en iterativ ramverk för datavetenskap som kan följas av en person.

Analyserar vi de [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datauppsättningen i den här genomgången. Det här är en uppsättning e-postmeddelanden som markerats som skräppost eller skinka (vilket innebär att de inte är skräppost), och innehåller även vissa statistik på innehållet i e-postmeddelanden. Statistik ingår diskuteras i nästa men ett avsnitt.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan använda en Linux Data Science-dator, måste du ha följande:

* En **Azure-prenumeration**. Om du inte redan har en, se [skapa ditt kostnadsfria Azure-konto i dag](https://azure.microsoft.com/free/).
* En [ **datavetenskap för Linux VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Mer information om att etablera den här virtuella datorn finns i [etablera Linux Data Science Virtual Machine](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) installerat på datorn och öppnas en XFCE-session. Information om hur du installerar och konfigurerar en **X2Go klienten**, se [installera och konfigurera X2Go klienten](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* För en rullande jämnare upplevelse, växla flaggan gfx.xrender.enabled i om: config i webbläsaren FireFox för virtuella datorer. [Se mer här. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Överväg också att växla *mousewheel.enable_pixel_scrolling* till False. [Instruktionerna här.](https://support.mozilla.org/en-US/questions/981140)
* En **AzureML-konto**. Om du inte redan har en, registrera dig för ny tjänst när den [AzureML-startsidan](https://studio.azureml.net/). Det finns en kostnadsfri användning nivå hjälper dig att komma igång.

## <a name="download-the-spambase-dataset"></a>Hämta spambase datauppsättningen
Den [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datauppsättning är en relativt liten uppsättning data som innehåller endast 4601 exempel. Det här är en lämplig storlek som ska användas när du visar att några av de viktigaste funktionerna i den virtuella datorn för datavetenskap eftersom den håller resurskraven mycket små.

> [!NOTE]
> Den här genomgången har skapats på en D2 v2 medelstora Linux virtuell dator för datavetenskap (CentOS Edition). Den här storleken DSVM är kan hantera procedurerna i den här genomgången.
>
>

Om du behöver mer lagringsutrymme, kan du skapa ytterligare diskar och koppla dem till den virtuella datorn. Dessa diskar använder beständiga Azure storage, så deras data bevaras även om servern är nätverkskonfigurationsinställningar på grund av storleksändring eller stängs av. Om du vill lägga till en disk och koppla den till den virtuella datorn, följer du anvisningarna i [lägga till en disk i en Linux VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). De här stegen används Azure-kommandoradsgränssnittet (Azure CLI), som redan är installerad på DSVM. Så kan du göra de här procedurerna helt från Virtuellt datorn. Ett annat alternativ att öka lagring är att använda [Azure files](../../storage/files/storage-how-to-use-files-linux.md).

Öppna ett terminalfönster för att hämta data, och kör det här kommandot:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Den hämta filen har inte en rubrikrad, då ska vi skapa en annan fil som har en rubrik. Kör följande kommando för att skapa en fil med lämpliga rubriker:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Sedan sammanfoga de två filerna tillsammans med kommandot:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Datauppsättningen har flera typer av statistik på varje e-postmeddelande:

* Kolumner som ***word\_frekvens\_WORD*** visar procentandelen av orden i e-postmeddelandet som matchar *WORD*. Till exempel om *word\_frekvens\_gör* är 1, 1% av alla orden i e-postmeddelandet har *gör*.
* Kolumner som ***char\_frekvens\_CHAR*** visar procentandelen av alla tecken i e-postmeddelandet som var *CHAR*.
* ***kapital\_kör\_längd\_längsta*** är den längsta en sekvens med versaler.
* ***kapital\_kör\_längd\_genomsnittliga*** är den genomsnittliga alla sekvenser av versaler.
* ***kapital\_kör\_längd\_totala*** är den totala längden på alla sekvenser av versaler.
* ***skräppost*** anger om e-postmeddelandet ansågs skräppost eller inte (1 = skräppost, 0 = inte skräppost).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Utforska datauppsättningen med Microsoft R Open
Nu ska vi undersöka data och utföra vissa grundläggande machine learning med R. Den virtuella datorn för datavetenskap som medföljer [Microsoft R Open](https://mran.revolutionanalytics.com/open/) förinstallerat. Med matematikbibliotek i den här versionen av R ger bättre prestanda än olika entrådiga versioner. Microsoft R Open innehåller också reproducerbarhet genom att använda en ögonblicksbild av databasen för CRAN-paket.

För att få kopior av kodexempel som används i den här genomgången kan du klona den **Azure-Machine-Learning-Data-Science** databas med hjälp av git, som är förinstallerade på den virtuella datorn. Kör från git-kommandoraden:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Öppna ett terminalfönster och starta en ny R-session med den interaktiva konsolen R eller Använd RStudio förinstallerat på datorn.


Om du vill importera data och ställa in miljön, kör du:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Visa sammanfattande statistik om varje kolumn:

    summary(data)

För en annan vy av data:

    str(data)

Detta visar vilken typ av varje variabel och de första få värdena i datauppsättningen.

Den *skräppost* kolumnen lästes som ett heltal, men det är faktiskt en kategoriska variabeln (eller faktor). Ange dess typ:

    data$spam <- as.factor(data$spam)

För att göra vissa utforskande analys använder den [ggplot2](http://ggplot2.org/) paketera ett populära graphing bibliotek för R som redan är installerad på den virtuella datorn. Observera att från sammanfattning av data som visas tidigare, att vi har sammanfattande statistik på frekvensen av tecknet utropstecken. Nu ska vi rita de frekvenserna med följande kommandon:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Eftersom fältet noll är skeva området, ska vi ta bort det:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Det finns ett icke-trivialt densitet ovan 1 som ser intressant ut. Låt oss titta på bara dessa data:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Dela den sedan efter skräppost vs skinka:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

De här exemplen ska att du kan göra liknande områden i de andra kolumnerna att utforska data i dem.

## <a name="train-and-test-an-ml-model"></a>Träna och testa en ML-modell
Nu ska vi träna ett par machine learning-modeller att klassificera e-postmeddelanden i datauppsättningen innehåller span eller skinka. Vi träna en modell för trädet beslut och en slumpmässig Skogsmodell i det här avsnittet och sedan kontrollera deras arbete du utfört sina förutsägelser.

> [!NOTE]
> Paketets rpart (rekursiv partitionering och Regressionsträd) som används i följande kod är redan installerad på den virtuella datorn för datavetenskap.
>
>

Först ska vi dela upp datauppsättningen i uppsättningar för träning och testning:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Och sedan skapa ett beslutsträd för att klassificera e-postmeddelanden.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Här är resultatet:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Använd följande kod för att fastställa hur väl den utför på träningsmängden:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

För att fastställa hur väl utför den i test-grupp:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Nu ska vi prova också en slumpmässig Skogsmodell. Slumpmässig skogar träna en mängd olika beslutsträd och utdata en klass som är vilket läge du klassificeringar från alla enskilda beslutsträd. De tillhandahåller en kraftfullare machine learning-metod som de rätta för tendens av en modell för trädet beslutet att overfit en datauppsättning för träning.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Distribuera en modell för Azure ML
[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) är en molnbaserad tjänst som gör det enkelt att skapa och distribuera modeller för förutsägelseanalys. En bra funktioner i AzureML är förmågan att publicera alla R-funktion som en webbtjänst. AzureML-R-paketet är det enkelt att göra direkt från våra R-session på DSVM distribution.

Du måste logga in på Azure Machine Learning Studio om du vill distribuera beslut trädet koden i föregående avsnitt. Du behöver arbetsyte-ID och en Autentiseringstoken för att logga in. Du hittar de här värdena och initiera AzureML-variabler med dem:

Välj **inställningar** i den vänstra menyn. Obs din **ARBETSYTE-ID**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Välj **Auktoriseringstoken** från den overhead menyn och anteckna dina **primära Auktoriseringstoken**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Läs in den **AzureML** paketera och ange värden för variabler med ditt ID-token och arbetsyta i R-sessionen på DSVM:

    if(!require("AzureML")) install.packages("AzureML")
    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Nu ska vi förenkla modellen för att göra det enklare att implementera den här demonstrationen. Välj de här tre variablerna i beslutsträdet närmast roten och skapa ett nytt träd med bara de här tre variablerna:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Vi behöver en förutsägelsefunktion som tar funktionerna som indata och returnerar de förväntade värdena:

    predictSpam <- function(newdata) {
      predictDF <- predict(model.rpart, newdata = newdata)
      return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }


Publicera funktionen predictSpam i AzureML med hjälp av den **publishWebService** funktionen:

    spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)


Den här funktionen använder den **predictSpam** fungerar, skapar en webbtjänst med namnet **spamWebService** med definierade indata och utdata och returnerar information om den nya slutpunkten.

Visa information om den senaste publicerade webb-tjänsten, inklusive dess API-slutpunkt och få åtkomst till nycklar med kommandot:

    s<-tail(services(ws, name = "spamWebService"), 1)
    ep <- endpoints(ws,s)
    ep

Prova på först 10 raderna i testet ange:

    consume(ep, smallTestSet[1:10, ])


## <a name="use-other-tools-available"></a>Använda andra verktyg som är tillgängliga
De återstående avsnitten visar hur du använder några av verktygen som installeras på den virtuella datorn datavetenskap för Linux. Här är listan över verktyg som beskrivs:

* XGBoost
* Python
* Jupyterhub
* Spännen
* PostgreSQL & Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) är ett verktyg som tillhandahåller implementering av snabba och exakta bättre trädet.

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
Anaconda Python-distributioner 2.7 och 3.5 har installerats i DSVM för utveckling med hjälp av Python.

> [!NOTE]
> Anaconda distributionen omfattar [Conda](http://conda.pydata.org/docs/index.html), som kan användas för att skapa anpassade miljöer för Python som har olika versioner och/eller installerade paket.
>
>

Nu ska vi läsa en del av datauppsättningen spambase och klassificera e-postmeddelanden med support vector virtuella datorer i scikit-Läs:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Att göra förutsägelser:

    clf.predict(X.ix[0:20, :])

För att visa hur du publicerar en AzureML-slutpunkt, vi gör en enklare modell tre variabler som vi gjorde när vi tidigare publicerade R-modellen.

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Publicera modellen till AzureML:

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
> Detta är endast tillgänglig för python 2.7 och stöds inte ännu på 3.5. Kör med **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
Anaconda-distribution i DSVM levereras med en Jupyter-anteckningsbok, en plattformsoberoende miljö att dela kod för Python, R eller Julia och analys. Jupyter-anteckningsboken sker via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn och lösenord vid ***https://\<VM DNS-namn eller IP-adress\>: 8000 /***. Alla konfigurationsfiler för JupyterHub finns i katalogen **/etc/jupyterhub**.

> [!NOTE]
> Du använder Python Package Manager (via den `pip` kommandot) från en Jupyter-anteckningsbok i den aktuella kerneln följande kommando kan användas i kodcell, till exempel:
```python
   import sys
   ! {sys.executable} -m pip install numpy -y
```
>
>

> [!NOTE]
> Att använda Conda-installationsprogrammet (via den `conda` kommandot) från en Jupyter-anteckningsbok i den aktuella kerneln följande kommando kan användas i kodcell, till exempel:
```python
   import sys
   ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
```
>
>

Flera exempelanteckningsböcker som är redan installerade på den virtuella datorn:

* Se den [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) för en Python notebook i exemplet.
* Se [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) ett exempel **R** anteckningsboken.
* Se den [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) för ett annat prov **Python** anteckningsboken.

> [!NOTE]
> Julia-språket är också tillgängligt från kommandoraden på Linux Data Science-dator.
>
>

## <a name="rattle"></a>Spännen
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (den R analytiska Verktyg att lära dig enkelt) är ett grafiskt R-verktyg för datautvinning. Den har ett intuitivt gränssnitt som gör det enkelt att läsa in, utforska, och omvandla data och skapa och utvärdera modeller.  Artikeln [Rattle: A Data Mining GUI för R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) innehåller en genomgång som visar dess funktioner.

Installera och starta spännen med följande kommandon:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Installation krävs inte på DSVM. Men spännen kan du uppmanas att installera ytterligare paket när det läses in.
>
>

Spännen använder ett fliken-baserat gränssnitt. De flesta av flikarna motsvarar stegen i den [Data Science Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), t.ex. läser in data eller genom att utforska den. Data science process flödar från vänster till höger i flikarna. Men den senaste fliken innehåller en logg över R-kommandon som körs av spännen.

Läsa in och konfigurera datauppsättningen:

* Om du vill läsa in filen, Välj den **Data** fliken
* Välj Väljaren bredvid **Filename** och välj **spambaseHeaders.data**.
* Läsa in filen. Välj **kör** i den övre raden med knappar. Du bör se en sammanfattning av varje kolumn, inklusive dess identifierade datatyp, oavsett om det är en inmatning, ett mål eller annan typ av variabeln och antalet unika värden.
* Spännen korrekt har identifierat den **skräppost** kolumnen som mål. Markera kolumnen som skräppost och ange sedan den **Data måltypen** till **Categoric**.

Att utforska data:

* Välj den **utforska** fliken.
* Klicka på **sammanfattning**, sedan **kör**, för att se viss information om variabeltyperna och vissa sammanfattande statistik.
* Om du vill visa andra typer av statistik om varje variabel, välj andra alternativ som **Beskriv** eller **grunderna**.

Den **utforska** fliken kan du skapa många insiktsfulla områden. Att rita ett histogram för data:

* Välj **distributioner**.
* Kontrollera **Histogram** för **word_freq_remove** och **word_freq_you**.
* Välj **Kör**. Du bör se båda densitet områden i ett enskilt diagram-fönster, där det är tydligt att ordet ”du” visas mycket oftare i e-postmeddelanden än ”ta bort”.

Korrelations-områden som också är intressant. Skapa en:

* Välj **korrelation** som den **typ**, sedan
* Välj **Kör**.
* Spännen varnar dig om att den rekommenderar högst 40 variabler. Välj **Ja** att visa området.

Det finns några intressanta samband som kommer upp: ”teknik” korreleras starkt att ”HP” och ”labs”, till exempel. Det är också starkt korreleras mot ”650”, eftersom riktnumret datauppsättning bidragsgivare 650.

De numeriska värdena efter samband mellan ord är tillgängliga i fönstret utforska. Det är intressant att Observera att till exempel ”teknik” korreleras negativt med ”dina” och ”pengar”.

Spännen kan omvandla datauppsättningen för att hantera några vanliga problem. Exempelvis kan du skala om funktioner, sedan imputera saknade värden, hantera extremvärden och ta bort variabler eller observationer med data som saknas. Spännen kan även identifiera association-reglerna mellan observationer och/eller variabler. De här flikarna ligger utanför omfånget för den här introduktionsgenomgång.

Spännen kan också analysera kluster. Nu ska vi undanta vissa funktioner för att göra det lättare att läsa utdata. På den **Data** fliken **Ignorera** bredvid varje variabler utom dessa tio objekt:

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

Gå sedan tillbaka till den **kluster** fliken **KMeans**, och ange den *antalet kluster* till 4. Sedan **köra**. Resultaten visas i utdatafönstret. Ett kluster har hög frekvens av ”george” och ”hp” och är förmodligen ett legitimt företagsbehov e-postmeddelande.

Skapa en enkel beslut trädet machine learning-modell:

* Välj den **modellen** fliken
* Välj **trädet** som den **typ**.
* Välj **kör** ska visas i trädet i textformat i utdatafönstret.
* Välj den **Rita** för att visa en grafisk version. Detta liknar ganska trädet vi fick tidigare med hjälp av *rpart*.

En bra funktioner i spännen är förmågan att köra flera machine learning-metoder och utvärderar dem snabbt. Här följer stegen:

* Välj **alla** för den **typ**.
* Välj **Kör**.
* När den är klar kan du klicka på valfritt enskilt **typ**, till exempel **SVM**, och granska resultaten.
* Du kan också jämföra prestanda för modeller i valideringen anges med den **utvärdera** fliken. Till exempel den **fel matrisen** val av visar du felmatris, övergripande fel och genomsnittlig klass-fel för varje modell i verifieringen grupp.
* Du kan också rita ROC kurvor, utföra analyser av känslighet och göra andra typer av modellen utvärderingar.

När du är klar att skapa modeller, väljer den **Log** fliken för att visa R-kod som körs av spännen under sessionen. Du kan välja den **exportera** för att spara den.

> [!NOTE]
> Det finns en bugg i aktuella versionen av spännen. Om du vill ändra skriptet eller använda den för att upprepa steg senare, måste du infoga tecknet # framför * exportera den här loggen... * i texten i loggen.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
DSVM levereras med PostgreSQL installerat. PostgreSQL är en avancerad relationsdatabas med öppen källkod. Det här avsnittet visar hur du läser in datauppsättningarna skräppost i PostgreSQL och frågar den.

Innan du kan läsa in data som du vill tillåta lösenordsautentisering från den lokala värden. I Kommandotolken:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Längst ned i konfigurationsfilen är flera rader som specificerar de tillåtna anslutningarna:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Ändra raden ”IPv4 lokala anslutningar” för md5 istället för ident, så vi kan logga in med ett användarnamn och lösenord:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Och starta om tjänsten postgres:

    sudo systemctl restart postgresql

Om du vill starta psql, en interaktiv terminal PostgreSQL, som inbyggd postgres-användare, kör du följande kommando från en kommandotolk:

    sudo -u postgres psql

Skapa ett nytt användarkonto med samma användarnamn som Linux-konto du för närvarande är inloggad som och ge den ett lösenord:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Sedan logga in i psql som dina användare:

    psql

Och importera data till en ny databas:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Nu ska vi utforska data och köra några frågor med **Squirrel SQL**, ett grafiskt verktyg som låter dig interagera med databaser via en JDBC-drivrutin.

Kom igång genom att starta Squirrel SQL på Program-menyn. Du ställer in drivrutinen:

* Välj **Windows**, sedan **visa drivrutiner**.
* Högerklicka på **PostgreSQL** och välj **ändra drivrutinen**.
* Välj **Extra klassen sökväg**, sedan **lägga till**.
* Ange ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** för den **filnamn** och
* Välj **Öppna**.
* Välj drivrutiner i listan och välj **org.postgresql.Driver** i **klassnamn**, och välj **OK**.

Konfigurera anslutningen till den lokala servern:

* Välj **Windows**, sedan **Visa alias.**
* Välj den **+** knappen för att göra ett nytt alias.
* Ge den namnet *skräppost databasen*, Välj **PostgreSQL** i den **drivrutinen** utskriftsjobb.
* Ange URL: en *jdbc:postgresql://localhost/spam*.
* Ange din *användarnamn* och *lösenord*.
* Klicka på **OK**.
* Öppna den **anslutning** fönstret dubbelklickar du på den ***skräppost databasen*** alias.
* Välj **Anslut**.

Köra några frågor:

* Välj den **SQL** fliken.
* Ange en enkel fråga som `SELECT * from data;` i fråga textrutan överst på SQL-flik.
* Tryck på **Ctrl-ange** att köra den. Som standard returnerar de första 100 raderna i Squirrel SQL från din fråga.

Det finns många fler frågor som du kan köra för att utforska dessa data. Hur fungerar till exempel hur ofta ordet *gör* skiljer sig åt mellan skräppost och skinka?

    SELECT avg(word_freq_make), spam from data group by spam;

Eller vad är egenskaper för e-postmeddelande som innehåller ofta *3d*?

    SELECT * from data order by word_freq_3d desc;

De flesta e-postmeddelanden som har en hög förekomst av *3d* är program som verkar vara skräppost, så det kan vara en mycket användbar funktion för att bygga en förutsägelsemodell för att klassificera e-postmeddelanden.

Om du vill utföra maskininlärning med data som lagras i en PostgreSQL-databas kan du använda [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Azure SQL Data Warehouse är en molnbaserad skalbar databas som kan bearbeta massiva mängder data, relationella såväl som icke-relationella. Mer information finns i [vad är Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Om du vill ansluta till datalagret och skapa tabellen, kör du följande kommando från en kommandotolk:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Sedan i sqlcmd-kommandotolk:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopiera data med bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Radbrytningar i den hämta filen är Windows-format, men bcp förväntar sig UNIX-format, så vi behöver tala bcp som med flaggan - r.
>
>

Och fråga med sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Du kan också fråga med Squirrel SQL. Följa liknande steg för PostgreSQL med hjälp av Microsoft MSSQL Server JDBC Driver, som finns i ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Nästa steg
En översikt över avsnitt som vägleder dig genom de uppgifter som utgör Data Science process i Azure finns i [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

En beskrivning av andra slutpunkt till slutpunkt genomgång som visar stegen i Team Data Science Process för specifika scenarier finns i [Team Data Science Process genomgångar](../team-data-science-process/walkthroughs.md). Genomgångar visar också hur du kombinera molnet och lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program.
