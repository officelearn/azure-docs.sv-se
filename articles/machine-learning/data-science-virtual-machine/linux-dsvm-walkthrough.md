---
title: Utforska Linux
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du slutför flera vanliga datavetenskapsuppgifter med hjälp av Virtuell dator för Linux Data Science.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 1298d5fe1d7407e836f454c2130a913dde6f8eec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755139"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Datavetenskap med en virtuell virtuell linuxdatavetenskapdator i Azure

Den här genomgången visar hur du slutför flera vanliga datavetenskapsuppgifter med hjälp av Linux Data Science Virtual Machine (DSVM). Linux DSVM är en virtuell avbildning som är tillgänglig i Azure och som är förinstallerad med en samling verktyg som ofta används för dataanalys och maskininlärning. De viktigaste programvarukomponenterna är specificerade i [Tillhandahålla Linux Data Science Virtual Machine](linux-dsvm-intro.md). DSVM-avbildningen gör det enkelt att komma igång med datavetenskap på några minuter, utan att behöva installera och konfigurera vart och ett av verktygen individuellt. Du kan enkelt skala upp DSVM om du behöver, och du kan stoppa den när den inte används. DSVM-resursen är både elastisk och kostnadseffektiv.

De datavetenskapsuppgifter som visas i den här genomgången följer stegen i [Vad är teamdatavetenskapsprocessen?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Team Data Science Process är ett systematiskt tillvägagångssätt för datavetenskap som hjälper grupper av datavetare att effektivt samarbeta under livscykeln för att bygga intelligenta applikationer. Data science-processen ger också ett iterativt ramverk för datavetenskap som kan följas av en individ.

I den här genomgången analyserar vi spambase-datauppsättningen. [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) Spambase är en uppsättning e-postmeddelanden som är markerade antingen spam eller skinka (inte spam). Spambase innehåller också viss statistik om innehållet i e-postmeddelandena. Vi pratar om statistiken senare i genomgången.

## <a name="prerequisites"></a>Krav

Innan du kan använda en Linux DSVM måste du ha följande förutsättningar:

* **Azure-prenumeration**. Information om hur du skaffar en Azure-prenumeration finns i [Skapa ditt kostnadsfria Azure-konto idag](https://azure.microsoft.com/free/).
* [**Linux Data Science virtuell maskin**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Information om hur du etablerar den virtuella datorn finns i [Etablera virtuell virtuell dator för Linux Data Science](linux-dsvm-intro.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) installerat på din dator med en öppen XFCE-session. Mer information finns i [Installera och konfigurera X2Go-klienten](linux-dsvm-intro.md#x2go).
* För en smidigare rullningsupplevelse, i DSVM:s Firefox `gfx.xrender.enabled` webbläsare, växla flaggan i `about:config`. [Läs mer](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Också överväga `mousewheel.enable_pixel_scrolling` `False`att ställa in till . [Läs mer](https://support.mozilla.org/questions/981140).
* **Azure Machine Learning-konto**. Om du inte redan har ett kan du registrera dig för ett nytt konto på [startsidan](https://azure.microsoft.com/free/services/machine-learning//)för Azure Machine Learning .

## <a name="download-the-spambase-dataset"></a>Ladda ner datauppsättningen för spambase

Datauppsättningen [för spambase](https://archive.ics.uci.edu/ml/datasets/spambase) är en relativt liten uppsättning data som innehåller 4 601 exempel. Datauppsättningen är en praktisk storlek för att demonstrera några av de viktigaste funktionerna i DSVM eftersom det håller resurskraven blygsamma.

> [!NOTE]
> Den här genomgången skapades med hjälp av en D2 v2-storlek Linux DSVM (Ubuntu 18.04 Edition). Du kan använda en DSVM den här storleken för att slutföra de procedurer som visas i den här genomgången.

Om du behöver mer lagringsutrymme kan du skapa ytterligare diskar och koppla dem till din DSVM. Diskarna använder beständig Azure-lagring, så deras data bevaras även om servern återetableras på grund av storleksändring eller stängs av. Om du vill lägga till en disk och bifoga den till din DSVM slutför du stegen i [Lägg till en disk till en Virtuell Linux.](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Stegen för att lägga till en disk använder Azure CLI, som redan är installerat på DSVM. Du kan slutföra stegen helt från själva DSVM. Ett annat alternativ för att öka lagringsutrymmet är att använda [Azure Files](../../storage/files/storage-how-to-use-files-linux.md).

Om du vill hämta data öppnar du ett terminalfönster och kör sedan det här kommandot:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

Den nedladdade filen har ingen rubrikrad. Nu ska vi skapa en annan fil som har ett huvud. Kör det här kommandot om du vill skapa en fil med lämpliga rubriker:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Sammanfoga sedan ihop de två filerna:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

Datauppsättningen har flera typer av statistik för varje e-postmeddelande:

* Kolumner som **\_ordet\_freq WORD** anger procentandelen ord i e-postmeddelandet som matchar *WORD*. Till exempel, om **\_ordet freq\_göra** är **1**, då 1% av alla ord i e-postmeddelandet var att *göra*.
* Kolumner som **\_char\_freq CHAR** anger procentandelen av alla tecken i e-postmeddelandet som är *CHAR*.
* **\_kapitalkörningslängd\_\_längst** är den längsta längden på en sekvens av versaler.
* **\_kapitalkörningslängd\_\_genomsnitt** är den genomsnittliga längden på alla sekvenser av versaler.
* **\_versalt\_\_körningslängd är** den totala längden på alla sekvenser av versaler.
* **spam** anger om e-postmeddelandet ansågs spam eller inte (1 = spam, 0 = inte spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Utforska datauppsättningen med hjälp av R Open

Låt oss undersöka data och göra några grundläggande maskininlärning med hjälp av R. DSVM levereras med [Microsoft R Open](https://mran.revolutionanalytics.com/open/) förinstallerat. De flertrådade matematiska biblioteken i den förinstallerade versionen av R ger bättre prestanda än versioner med en tråd. R Open ger också reproducerbarhet genom en ögonblicksbild av CRAN-paketarkivet.

Om du vill hämta kopior av kodexemplen som används i den här genomgången använder du git för att klona Azure-Machine-Learning-Data-Science-databasen. Git är förinstallerat på DSVM. Kör på kommandoraden git:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Öppna ett terminalfönster och starta en ny R-session i R-konsolen. Du kan också använda RStudio, som är förinstallerad på DSVM.

Så här importerar du data och ställer in miljön:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Så här visar du sammanfattningsstatistik om varje kolumn:

```R
summary(data)
```

För en annan vy av data:

```R
str(data)
```

I den här vyn visas typen av varje variabel och de första värdena i datauppsättningen.

**Spamkolumnen** lästes som ett heltal, men det är faktiskt en kategorisk variabel (eller faktor). Så här anger du dess typ:

```R
data$spam <- as.factor(data$spam)
```

För att göra några undersökande analys, använd [ggplot2](https://ggplot2.tidyverse.org/) paketet, ett populärt grafbibliotek för R som är förinstallerat på DSVM. Baserat på de sammanfattande data som visas tidigare har vi sammanfattande statistik om frekvensen för utropstecknet. Låt oss rita dessa frekvenser här genom att köra följande kommandon:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Eftersom nollstrecket är skeva tomten, låt oss eliminera det:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Det finns en nontrivial densitet över 1 som ser intressant. Låt oss bara titta på dessa data:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Sedan dela den med spam kontra skinka:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

De här exemplen bör hjälpa dig att skapa liknande diagram och utforska data i de andra kolumnerna.

## <a name="train-and-test-a-machine-learning-model"></a>Träna och testa en maskininlärningsmodell

Låt oss träna ett par maskininlärningsmodeller för att klassificera e-postmeddelandena i datauppsättningen som innehåller antingen skräppost eller skinka. I det här avsnittet tränar vi en beslutsträdmodell och en slumpmässig skogsmodell. Sedan testar vi precisionen i förutsägelserna.

> [!NOTE]
> Paketet *rpart* (Rekursiv partitionering och regressionsträd) som används i följande kod är redan installerat på DSVM.

Låt oss först dela upp datauppsättningen i utbildningsuppsättningar och testuppsättningar:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Skapa sedan ett beslutsträd för att klassificera e-postmeddelandena:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Här är resultatet:

![Ett diagram över beslutsträdet som har skapats](./media/linux-dsvm-walkthrough/decision-tree.png)

Om du vill ta reda på hur bra den fungerar på träningsuppsättningen använder du följande kod:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Så här avgör du hur bra det fungerar på testuppsättningen:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Låt oss också prova en slumpmässig skogsmodell. Slumpmässiga skogar tränar en mängd beslutsträd och ut ur en klass som är klasssättet för klassificeringarna från alla enskilda beslutsträd. De ger en mer kraftfull maskininlärningsmetod eftersom de korrigerar för tendensen hos en beslutsträdmodell att övermontera en utbildningsdatauppsättning.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Djupinlärningshandledningar och genomgångar

Förutom de rambaserade proverna tillhandahålls också en uppsättning omfattande genomgångar. Dessa genomgångar hjälper dig att få fart på din utveckling av djupinlärningsprogram i domäner som bild- och text- och språkförståelse.

- [Köra neurala nätverk över olika ramverk](https://github.com/ilkarman/DeepLearningFrameworks): En omfattande genomgång som visar hur du migrerar kod från ett ramverk till ett annat. Det visar också hur du jämför modell- och körningsprestanda över ramverk. 

- [En vägledning för att skapa en heltäckande lösning för att identifiera produkter i bilder:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)Bildidentifiering är en teknik som kan lokalisera och klassificera objekt i bilder. Tekniken har potential att ge stora belöningar i många verkliga affärsområden. Återförsäljare kan till exempel använda den här tekniken för att avgöra vilken produkt en kund har plockat upp från hyllan. Den här informationen hjälper i sin tur butiker att hantera produktlager. 

- [Djupinlärning för ljud:](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)Den här självstudien visar hur man tränar en djupinlärningsmodell för ljudhändelsedetektering på den [urbana ljuddatauppsättningen](https://urbansounddataset.weebly.com/). Självstudien ger en översikt över hur du arbetar med ljuddata.

- [Klassificering av textdokument](https://github.com/anargyri/lstm_han): Den här genomgången visar hur man bygger och tränar två olika neurala nätverksarkitekturer: Hierarchical Attention Network och Long Short Term Memory (LSTM). Dessa neurala nätverk använder Keras API för djupinlärning för att klassificera textdokument. Keras är en front till tre av de mest populära djupinlärningsramverken: Microsoft Cognitive Toolkit, TensorFlow och Theano.

## <a name="other-tools"></a>Andra verktyg

De återstående avsnitten visar hur du använder några av de verktyg som är installerade på Linux DSVM. Vi diskuterar dessa verktyg:

* XGBoost
* Python
* JupyterHub (på)
* Rattle
* PostgreSQL och SQuirreL SQL
* SQL Server-datalager

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) ger en snabb och exakt förstärkt trädimplementering.

```R
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
```

XGBoost kan också anropa från Python eller en kommandorad.

### <a name="python"></a>Python

För Python-utveckling installeras Anaconda Python-distributionerna 3.5 och 2.7 på DSVM.

> [!NOTE]
> Anaconda-distributionen inkluderar [Conda](https://conda.pydata.org/docs/index.html). Du kan använda Conda för att skapa anpassade Python-miljöer som har olika versioner eller paket installerade i dem.

Låt oss läsa i några av spambase datauppsättning och klassificera e-postmeddelanden med stöd vektor maskiner i Scikit-learn:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Så här gör du förutsägelser:

```Python
clf.predict(X.ix[0:20, :])
```

Om du vill visa hur du publicerar en Slutpunkt för Azure Machine Learning ska vi skapa en mer grundläggande modell. Vi använder de tre variabler som vi använde när vi publicerade R-modellen tidigare:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Så här publicerar du modellen i Azure Machine Learning:

```Python
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
```

> [!NOTE]
> Det här alternativet är endast tillgängligt för Python 2.7. Det stöds ännu inte på Python 3.5. Kör du **använd /anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub (på)

Anaconda-distributionen i DSVM levereras med en Jupyter Notebook, en plattformsoberoende miljö för att dela Python-, R- eller Julia-kod och analys. Den Jupyter Notebook nås via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn\<och lösenord på https:// DSVM DNS-namn eller IP-adress\>:8000/. Alla konfigurationsfiler för JupyterHub finns i /etc/jupyterhub.

> [!NOTE]
> Om du vill använda Python `pip` Package Manager (via kommandot) från en Jupyter-anteckningsbok i den aktuella kärnan använder du det här kommandot i kodcellen:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Om du vill använda Installationsprogrammet `conda` för Conda (via kommandot) från en Jupyter-anteckningsbok i den aktuella kärnan använder du det här kommandot i en kodcell:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Flera exempel bärbara datorer är redan installerade på DSVM:

* Exempel på Python-anteckningsböcker:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Exempel på R-anteckningsbok:
  * [Introtutorialinr](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Julia-språket är också tillgängligt från kommandoraden på Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) är ett grafiskt R-verktyg för datautvinning. Rattle har ett intuitivt gränssnitt som gör det enkelt att ladda, utforska och omvandla data samt att bygga och utvärdera modeller. [Rattle: A Data Mining GUI för R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) ger en genomgång som visar Rattle funktioner.

Installera och starta Rattle genom att köra dessa kommandon:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Du behöver inte installera Rattle på DSVM. Du kan dock uppmanas att installera ytterligare paket när Rattle öppnas.

Rattle använder ett flikbaserat gränssnitt. De flesta flikarna motsvarar steg i [teamdatavetenskapsprocessen,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)som att läsa in data eller utforska data. Data science-processen flödar från vänster till höger genom flikarna. Den sista fliken innehåller en logg över R-kommandon som kördes av Rattle.

Så här läser du in och konfigurerar datauppsättningen:

1. Om du vill läsa in filen markerar du fliken **Data.**
1. Välj väljaren bredvid **Filnamn**och välj sedan **spambaseHeaders.data**.
1. Så här läser du in filen. välj **Kör**. Du bör se en sammanfattning av varje kolumn, inklusive dess identifierade datatyp. om det är en indata, ett mål eller någon annan typ av variabel. och antalet unika värden.
1. Rattle har korrekt identifierat **spam** kolumnen som mål. Markera **kolumnen skräppost** och ange sedan **måldatatypen** på **kategoriskt**.

Så här utforskar du data:

1. Välj fliken **Utforska.**
1. Om du vill visa information om variabla typer och viss sammanfattningsstatistik väljer du > **Sammanfattningsdr0**. **Summary**
1. Om du vill visa andra typer av statistik om varje variabel väljer du andra alternativ, till exempel **Beskriv** eller **Grunderna**.

Du kan också använda fliken **Utforska** för att generera insiktsfulla ritplaner. Så här ritar du ett histogram av data:

1. Välj **Distributioner**.
1. För **word_freq_remove** och **word_freq_you**väljer du **Histogram**.
1. Välj **Kör**. Du bör se båda densiteten tomter i ett enda diagram fönster, där det är klart att ordet _du_ visas mycket oftare i e-postmeddelanden än _att ta bort_.

**Korrelationen** tomter är också intressanta. Så här skapar du en ritlott:

1. För **Typ**väljer du **Korrelation**.
1. Välj **Kör**.
1. Rattle varnar dig att det rekommenderar högst 40 variabler. Välj **Ja** om du vill visa ritytan.

Det finns några intressanta korrelationer som kommer upp: _tekniken_ är starkt korrelerad till _HP_ och _laboratorier_, till exempel. Det är också starkt korrelerade till _650_ eftersom riktnumret för datauppsättningen givare är 650.

De numeriska värdena för korrelationerna mellan ord är tillgängliga i fönstret **Utforska.** Det är intressant att notera, till exempel, att _tekniken_ är negativt korrelerad med _din_ och _pengar._

Skallra kan omvandla datauppsättningen för att hantera några vanliga problem. Den kan till exempel skala om funktioner, tillskriva saknade värden, hantera extremvärden och ta bort variabler eller observationer som saknar data. Skallra kan också identifiera associationsregler mellan observationer och variabler. Dessa flikar omfattas inte av den här inledande genomgången.

Skallra kan också köra klusteranalys. Låt oss utesluta vissa funktioner för att göra utdata lättare att läsa. På fliken **Data** väljer du **Ignorera** bredvid var och en av variablerna utom dessa 10 objekt:

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

Gå tillbaka till fliken **Kluster.** Välj **KMeans**och ange sedan **Antal kluster** till **4**. Välj **Kör**. Resultaten visas i utdatafönstret. Ett kluster har hög frekvens av _George_ och _HP_, och är förmodligen en legitim verksamhet e-post.

Så här skapar du en grundläggande modell för beslutsträdsmaskininlärning:

1. Välj fliken **Modell,**
1. För **typen**väljer du **Träd**.
1. Välj **Kör om** du vill visa trädet i textform i utdatafönstret.
1. Välj knappen **Rita** om du vill visa en grafisk version. Beslutsträdet liknar trädet vi fick tidigare med hjälp av rpart.

En användbar funktion i Rattle är dess förmåga att köra flera machine learning-metoder och snabbt utvärdera dem. Här är stegen:

1. För **Typ**väljer du **Alla**.
1. Välj **Kör**.
1. När Rattle körs kan du välja **valfritt typvärde,** till exempel **SVM,** och visa resultatet.
1. Du kan också jämföra prestanda för modellerna på valideringsuppsättningen med hjälp av fliken **Utvärdera.** Valet **Felmatris** visar till exempel förvirringsmatrisen, det övergripande felet och det genomsnittliga klassfelet för varje modell i valideringsuppsättningen. Du kan också rita ROC-kurvor, köra känslighetsanalys och göra andra typer av modellutvärderingar.

När du är klar med byggmodeller väljer du fliken **Logg** för att visa R-koden som kördes av Rattle under sessionen. Du kan välja knappen **Exportera** för att spara den.

> [!NOTE]
> Den aktuella utgåvan av Rattle innehåller en bugg. Om du vill ändra skriptet eller använda det för **#** att upprepa dina steg senare måste du infoga ett tecken framför *Exportera den här loggen ...* i loggens text.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL och SQuirreL SQL

DSVM levereras med PostgreSQL installerat. PostgreSQL är en sofistikerad relationsdatabas med öppen källkod. I det här avsnittet visas hur du läser in datauppsättningen för skräppostbasen i PostgreSQL och sedan frågar den.

Innan du kan läsa in data måste du tillåta lösenordsautentisering från localhost. Kör följande i en kommandotolk:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

Längst ned i config-filen finns flera rader som beskriver de tillåtna anslutningarna:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Ändra **IPv4 lokala anslutningar** rad för att använda **md5** istället för **ident,** så att vi kan logga in med hjälp av ett användarnamn och lösenord:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Starta sedan om PostgreSQL-tjänsten:

```Bash
sudo systemctl restart postgresql
```

Om du vill starta *psql* (en interaktiv terminal för PostgreSQL) som den inbyggda postgres-användaren kör du det här kommandot:

```Bash
sudo -u postgres psql
```

Skapa ett nytt användarkonto med hjälp av användarnamnet för det Linux-konto som du använde för att logga in. Skapa ett lösenord:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Logga in på psql:

```Bash
psql
```

Importera data till en ny databas:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Nu ska vi utforska data och köra några frågor med hjälp av SQuirreL SQL, ett grafiskt verktyg som du kan använda för att interagera med databaser via en JDBC-drivrutin.

För att komma igång, på **programmenyn,** öppna SQuirreL SQL. Så här konfigurerar du drivrutinen:

1. Välj Drivrutiner för **Windows** > **Visa**.
1. Högerklicka på **PostgreSQL** och välj **Ändra drivrutin**.
1. Välj **Extra klasssökväg** > **Lägg till**.
1. För **Filnamn**anger du **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Välj **Öppna**.
1. Välj **Listdrivrutiner**. För **Klassnamn**väljer du **org.postgresql.Driver**och väljer sedan **OK**.

Så här konfigurerar du anslutningen till den lokala servern:

1. Välj **Windows** > **Visa alias.**
1. Välj **+** knappen om du vill skapa ett nytt alias. För det nya aliasnamnet anger du **Skräppostdatabas**. 
1. För **drivrutin**väljer du **PostgreSQL**.
1. Ange webbadressen till **jdbc:postgresql://localhost/spam**.
1. Ange ditt användarnamn och lösenord.
1. Välj **OK**.
1. Om du vill öppna **anslutningsfönstret** dubbelklickar du på **skräppostdatabasaliaset.**
1. Välj **Anslut**.

Så här kör du några frågor:

1. Välj **fliken SQL.**
1. Ange en grundläggande fråga högst upp på **SQL-fliken,** till exempel `SELECT * from data;`.
1. Tryck på Ctrl+Retur för att köra frågan. Som standard returnerar SQuirreL SQL de första 100 raderna från frågan.

Det finns många fler frågor som du kan köra för att utforska dessa data. Till exempel, hur frekvensen av ordet *gör* skiljer sig mellan spam och skinka?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Eller, vilka är egenskaperna hos e-post som ofta innehåller *3d?*

```SQL
SELECT * from data order by word_freq_3d desc;
```

De flesta e-postmeddelanden som har en hög förekomst av *3D* tydligen är spam. Den här informationen kan vara användbar för att skapa en prediktiv modell för att klassificera e-postmeddelanden.

Om du vill göra maskininlärning med hjälp av data som lagras i en PostgreSQL-databas kan du överväga att använda [MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse är en molnbaserad, utskalningsdatabas som kan bearbeta stora mängder data, både relationella och icke-relationella. Mer information finns i [Vad är Azure SQL Data Warehouse?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Om du vill ansluta till informationslagret och skapa tabellen kör du följande kommando från en kommandotolk:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

Kör det här kommandot i sqlcmd-prompten:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Kopiera data med hjälp av bcp:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> Den hämtade filen innehåller radslut i Windows-stil. Bcp-verktyget förväntar sig radslut i Unix-stil. Använd flaggan -r för att berätta för bcp.

Fråga sedan med sqlcmd:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Du kan också fråga med hjälp av SQuirreL SQL. Följ steg som liknar PostgreSQL med hjälp av SQL Server JDBC-drivrutinen. JDBC-drivrutinen finns i mappen /usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Nästa steg

En översikt över artiklar som går igenom de uppgifter som ingår i datascience-processen i Azure finns i [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

En beskrivning av genomgångar från slutpunkt till slutpunkt som visar stegen i teamdatavetenskapsprocessen för specifika scenarier finns i [genomgångar](../team-data-science-process/walkthroughs.md)av Team Data Science Process . Genomgångarna illustrerar också hur du kombinerar moln- och lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligent program.
