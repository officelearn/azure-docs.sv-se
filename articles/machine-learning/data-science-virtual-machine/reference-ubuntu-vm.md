---
title: 'Referens: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Information om verktyg som ingår i Ubuntu Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 5c184e7f1dc828c3f9ff8d449d29ab3aaa4d1cf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525829"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referens: Ubuntu (Linux) Data Science Virtual Machine

Se nedan för en lista över tillgängliga verktyg på din Ubuntu Data Science Virtual Machine. 

## <a name="deep-learning-libraries"></a>Bibliotek för djupinlärning

### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit är en verktygslåda med öppen källkod för djupinlärning. Python-bindningar är tillgängliga i rot- och py35 Conda-miljöer. Den har också ett kommandoradsverktyg (CNTK) som redan finns i sökvägen.

Exempel på Python-anteckningsböcker finns i JupyterHub. Om du vill köra ett grundläggande exempel på kommandoraden kör du följande kommandon i skalet:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Mer information finns i CNTK-avsnittet i [GitHub](https://github.com/Microsoft/CNTK) och [CNTK-wikin](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe är en deep learning ram från Berkeley Vision and Learning Center. Den finns i /opt/caffe. Du hittar exempel i /opt/caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 är en deep learning ram från Facebook som bygger på Caffe. Den är tillgänglig i Python 2.7 i Conda-rotmiljön. Om du vill aktivera den kör du följande kommando från skalet:

```bash
source /anaconda/bin/activate root
```

Vissa exempel anteckningsböcker är tillgängliga i JupyterHub.

### <a name="h2o"></a>H2o

H2O är en snabb, minnesbaserad, distribuerad maskininlärning och prediktiv analysplattform. Ett Python-paket installeras i både rot- och py35 Anaconda-miljöer. Ett R-paket är också installerat. 

Om du vill öppna H2O `java -jar /dsvm/tools/h2o/current/h2o.jar`från kommandoraden kör du . Det finns olika [kommandoradsalternativ](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) som du kanske vill konfigurera. Du kan komma åt webbgränssnittet http://localhost:54321 i Flow genom att bläddra till för att komma igång. Exempel på anteckningsböcker finns också i JupyterHub.

### <a name="keras"></a>Keras

Keras är ett neuralt nätverks-API på hög nivå i Python. Det kan köras ovanpå TensorFlow, Microsoft Cognitive Toolkit eller Theano. Den är tillgänglig i rot- och py35 Python-miljöer.

### <a name="mxnet"></a>MXNet

MXNet är ett ramverk för djupinlärning som utformats för både effektivitet och flexibilitet. Den har R- och Python-bindningar som ingår i DSVM. Exempel anteckningsböcker ingår i JupyterHub och exempelkod finns i /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA SIFFROR

NVIDIA Deep Learning GPU Training System, känt som DIGITS, är ett system för att förenkla vanliga djupinlärningsuppgifter. Dessa uppgifter omfattar hantering av data, design och utbildning av neurala nätverk på GPU-system och övervakning av prestanda i realtid med avancerad visualisering.

SIFFROR finns som en tjänst som kallas *siffror*. Starta tjänsten och http://localhost:5000 bläddra till för att komma igång.

SIFFROR installeras också som en Python-modul i Conda-rotmiljön.

### <a name="tensorflow"></a>TensorFlow

TensorFlow är Googles djupinlärningsbibliotek. Det är ett programbibliotek med öppen källkod för numerisk beräkning med hjälp av dataflödesdiagram. TensorFlow är tillgängligt i py35 Python-miljön och vissa exempelanteckningsböcker ingår i JupyterHub.

### <a name="theano"></a>Theano

Theano är ett Python-bibliotek för effektiv numerisk beräkning. Den är tillgänglig i rot- och py35 Python-miljöer. 

### <a name="torch"></a>Torch

Torch är ett vetenskapligt datorramverk med brett stöd för maskininlärningsalgoritmer. Den finns i /dsvm/tools/torch, och den **interaktiva** sessionen och LuaRocks pakethanteraren finns på kommandoraden. Exempel finns i /dsvm/samples/torch.

PyTorch finns även i rotaconda-miljön. Exempel är i /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R är ett av de mest populära språken för dataanalys och maskininlärning. Om du vill använda R för dina analyser har den virtuella datorn Microsoft Machine Learning Server med Microsoft R Open och Math Kernel Library. Math Kernel Library optimerar matematiska åtgärder som är vanliga i analytiska algoritmer. Microsoft R Open är 100 procent kompatibelt med CRAN R och alla R-bibliotek som publiceras i CRAN kan installeras på Microsoft R Open. 

Machine Learning Server ger dig skalning och operationalisering av R-modeller i webbtjänster. Du kan redigera dina R-program i en av standardredigerarna, till exempel RStudio, vi eller Emacs. Om du föredrar att använda Emacs-redigeraren har den förinstallerats. Emacs ESS-paketet (Emacs Speaks Statistics) förenklar arbetet med R-filer inom Emacs-redigeraren.

Om du vill öppna R-konsolen anger du **R** i skalet. Det här kommandot tar dig till en interaktiv miljö. Om du vill utveckla ditt R-program använder du vanligtvis en redigerare som Emacs eller vi och kör sedan skripten inom R. Med RStudio har du en fullständig grafisk IDE för att utveckla ditt R-program.

Det finns också ett R-skript för dig att installera [Top 20 R-paket](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) om du vill. Du kan köra skriptet när du har i det interaktiva gränssnittet R. Som tidigare nämnts kan du öppna gränssnittet genom att ange **R** i skalet.  

## <a name="python"></a>Python

Anaconda Python installeras med Python 2.7- och 3.5-miljöer. 2,7-miljön kallas _rot_, och 3,5 miljön kallas _py35_. Den här distributionen innehåller bas python tillsammans med cirka 300 av de mest populära matematik-, teknik- och dataanalyspaketen.

Py35-miljön är standard. Om du vill aktivera rotmiljön (2.7) använder du det här kommandot:

```bash
source activate root
```

Om du vill aktivera py35-miljön igen använder du det här kommandot:

```bash
source activate py35
```

Om du vill anropa en interaktiv Python-session anger du **python** i skalet. 

Installera ytterligare Python-bibliotek med Conda eller pip. För pip, aktivera rätt miljö först om du inte vill ha standard:

```bash
source activate root
pip install <package>
```

Eller ange den fullständiga sökvägen till pip:

```bash
/anaconda/bin/pip install <package>
```

För Conda bör du alltid ange miljönamnet (py35 eller root):

```bash
conda install <package> -n py35
```

Om du använder ett grafiskt gränssnitt eller har konfigurerat X11-vidarebefordran kan du ange **pycharm** för att öppna PyCharm Python IDE. Du kan använda standardtextredigerarna. Dessutom kan du använda Spyder, en Python IDE som levereras med Anaconda Python-distributioner. Spyder behöver en grafisk skrivbord eller X11 vidarebefordran. Det grafiska skrivbordet har en genväg till Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

Den Anaconda distributionen kommer också med en Jupyter anteckningsbok, en miljö för att dela kod och analys. Den Jupyter anteckningsboken är nås via JupyterHub. Du loggar in med ditt lokala Användarnamn och lösenord för Linux.

Den bärbara Jupyter-servern har förkonfigurerats med Python 2-, Python 3- och R-kärnor. Använd skrivbordsikonen **Jupyter Notebook** för att öppna webbläsaren och komma åt den bärbara servern. Om du är på den virtuella datorn via SSH eller X2Go-klienten [https://localhost:8000/](https://localhost:8000/)kan du också komma åt Jupyter notebook server på .

> [!NOTE]
> Fortsätt om du får några certifikatvarningar.

Du kan komma åt Jupyter notebook server från någon värd. Ange **https://\<VM DNS-namn eller\>IP-adress :8000/**.

> [!NOTE]
> Port 8000 öppnas som standard i brandväggen när den virtuella datorn etableras. 

Vi har paketerat exempel anteckningsböcker - en i Python och en i R. Du kan se länken till exemplen på den bärbara hemsidan när du autentiserar till Jupyter-anteckningsboken med hjälp av ditt lokala Användarnamn och lösenord för Linux. Du kan skapa en ny anteckningsbok genom att välja **Ny**och sedan välja lämplig språkkärna. Om knappen **Ny** inte visas väljer du **Jupyter-ikonen** längst upp till vänster för att gå till startsidan för den bärbara servern.

## <a name="apache-spark-standalone"></a>Apache Spark fristående

En fristående instans av Apache Spark är förinstallerad på Linux DSVM för att hjälpa dig att utveckla Spark-program lokalt innan du testar och distribuerar dem på stora kluster. 

Du kan köra PySpark-program via Jupyter-kärnan. När du öppnar Jupyter väljer du knappen **Nytt** och du bör se en lista över tillgängliga kärnor. **Spark - Python** är PySpark-kärnan som låter dig skapa Spark-program med hjälp av Python-språket. Du kan också använda en Python IDE som PyCharm eller Spyder för att bygga din Spark program. 

I den här fristående instansen körs Spark-stacken inom det anropande klientprogrammet. Den här funktionen gör det snabbare och enklare att felsöka problem jämfört med att utveckla på ett Spark-kluster.

Jupyter tillhandahåller ett exempel på pysparks anteckningsbok. Du hittar den i SparkML-katalogen under hemkatalogen för Jupyter ($HOME/bärbara datorer/SparkML/pySpark). 

Om du programmerar i R för Spark kan du använda Microsoft Machine Learning Server, SparkR eller Sparklyr. 

Innan du kör i en Spark-kontext i Microsoft Machine Learning Server måste du göra ett engångsinstallationssteg för att aktivera en lokal ennod Hadoop HDFS- och Yarn-instans. Som standard installeras Hadoop-tjänster men inaktiveras på DSVM. Om du vill aktivera den måste du köra följande kommandon som root första gången:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Du kan stoppa Hadoop-relaterade tjänster när du inte ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```behöver dem genom att köra .

Katalogen /dsvm/samples/MRS innehåller ett exempel som visar hur du utvecklar och testar Microsoft Machine Learning Server i en fjärr spark-kontext (den fristående Spark-instansen på DSVM).

## <a name="ides-and-editors"></a>ID:er och redaktörer

Du kan välja mellan flera kodredigerare, inklusive vi/Vim, Emacs, PyCharm, RStudio och IntelliJ. 

PyCharm, RStudio och IntelliJ är grafiska redigerare. Om du vill använda dem måste du vara inloggad på ett grafiskt skrivbord. Du öppnar dem med hjälp av genvägar till skrivbords- och programmenyn.

Vim och Emacs är textbaserade redaktörer. På Emacs gör ESS-tilläggspaketet det enklare att arbeta med R inom Emacs-redigeraren. Du hittar mer information på [ESS webbplats](https://ess.r-project.org/).

LaTex installeras via texlive-paketet, tillsammans med ett Emacs-tilläggspaket som heter [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Det här paketet gör det enklare att redigera dina LaTex-dokument inom Emacs.  

## <a name="databases"></a>Databaser

### <a name="graphical-sql-client"></a>Grafisk SQL-klient

SQuirrel SQL, en grafisk SQL-klient, kan ansluta till olika databaser (till exempel Microsoft SQL Server och MySQL) och köra SQL-frågor. Du kan köra SQuirrel SQL från en grafisk skrivbordssession (till exempel via X2Go-klienten) med hjälp av en skrivbordsikon. Du kan också köra klienten med hjälp av följande kommando i skalet:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Innan den första användningen konfigurerar du drivrutiner och databasalias. JDBC-drivrutinerna finns på /usr/share/java/jdbcdrivers.

Mer information finns i [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Kommandoradsverktyg för åtkomst till Microsoft SQL Server

ODBC-drivrutinspaketet för SQL Server levereras också med två kommandoradsverktyg:

- **bcp**: Bcp-verktyget kopierar data mellan en instans av Microsoft SQL Server och en datafil i ett användarspecificerat format. Du kan använda bcp-verktyget för att importera ett stort antal nya rader till SQL Server-tabeller eller för att exportera data från tabeller till datafiler. Om du vill importera data till en tabell måste du använda en formatfil som skapats för den tabellen. Du måste också förstå tabellens struktur och vilka typer av data som är giltiga för dess kolumner.

  Mer information finns i [Ansluta med bcp](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd**: Du kan ange Transact-SQL-uttryck med hjälp av sqlcmd-verktyget. Du kan också ange systemprocedurer och skriptfiler i kommandotolken. Det här verktyget använder ODBC för att köra Transact-SQL-batchar.

  Mer information finns i [Ansluta med sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Det finns vissa skillnader i det här verktyget mellan Linux och Windows-plattformar. Mer information finns i dokumentationen.

### <a name="database-access-libraries"></a>Databasåtkomstbibliotek

Bibliotek finns i R och Python för databasåtkomst:

* I R kan du använda RODBC-paketet eller dplyr-paketet för att fråga eller köra SQL-uttryck på databasservern.
* I Python ger pyodbc-biblioteket databasåtkomst med ODBC som underliggande lager.  

## <a name="azure-tools"></a>Azure-verktyg

Följande Azure-verktyg är installerade på den virtuella datorn:

* **Azure CLI**: Du kan använda kommandoradsgränssnittet i Azure för att skapa och hantera Azure-resurser via skalkommandon. Om du vill öppna Azure-verktygen anger du **azure-hjälp**. Mer information finns på [sidan Azure CLI-dokumentation](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer:** Azure Storage Explorer är ett grafiskt verktyg som du kan använda för att bläddra igenom de objekt som du har lagrat i ditt Azure-lagringskonto och för att ladda upp och hämta data till och från Azure-blobbar. Du kan komma åt Storage Explorer från genvägsikonen på skrivbordet. Du kan också öppna den från en skalfråga genom att ange **StorageExplorer**. Du måste vara inloggad från en X2Go-klient eller ha X11-vidarebefordran inställd.
* **Azure-bibliotek:** Följande är några av de förinstallerade biblioteken.
  
  * **Python**: Azure-relaterade bibliotek i Python är *azure,* *azureml*, *pydocumentdb*och *pyodbc*. Med de tre första biblioteken kan du komma åt Azure-lagringstjänster, Azure Machine Learning och Azure Cosmos DB (en NoSQL-databas på Azure). Det fjärde biblioteket, pyodbc (tillsammans med Microsoft ODBC-drivrutinen för SQL Server) ger åtkomst till SQL Server, Azure SQL Database och Azure SQL Data Warehouse från Python med hjälp av ett ODBC-gränssnitt. Ange **pip-listan** för att se alla bibliotek i listan. Var noga med att köra det här kommandot i både Python 2.7- och 3.5-miljöerna.
  * **R**: Azure-relaterade bibliotek i R är AzureML och RODBC.
  * **Java**: Listan över Azure Java-bibliotek finns i katalogen /dsvm/sdk/AzureSDKJava på den virtuella datorn. De viktigaste biblioteken är Azure-api:er för lagring och hantering, Azure Cosmos DB och JDBC-drivrutiner för SQL Server.  

Du kan komma åt [Azure-portalen](https://portal.azure.com) från den förinstallerade Webbläsaren Firefox. På Azure-portalen kan du skapa, hantera och övervaka Azure-resurser.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning är en fullständigt hanterad molntjänst som gör att du kan skapa, distribuera och dela lösningar för prediktiv analys. Du kan skapa dina experiment och modeller från Azure Machine Learning Studio (klassisk). Du kan komma åt den från en webbläsare på den virtuella datavetenskapsmaskinen genom att besöka [Microsoft Azure Machine Learning](https://studio.azureml.net).

När du har loggat in på Azure Machine Learning Studio (klassisk) kan du använda en experimentarbetsyta för att skapa ett logiskt flöde för maskininlärningsalgoritmerna. Du har också åtkomst till en Jupyter-anteckningsbok som finns på Azure Machine Learning och som kan fungera sömlöst med experimenten i Azure Machine Learning Studio (klassisk). 

Operationalisera de maskininlärningsmodeller som du har byggt genom att slå in dem i ett webbtjänstgränssnitt. Operationalisering av maskininlärningsmodeller gör det möjligt för klienter som är skrivna på alla språk att anropa förutsägelser från dessa modeller. Mer information finns i [machine learning-dokumentationen](https://azure.microsoft.com/documentation/services/machine-learning/).

Du kan också skapa dina modeller i R eller Python på den virtuella datorn och sedan distribuera dem i produktion på Azure Machine Learning. Vi har installerat bibliotek i R **(AzureML)** och Python **(azureml)** för att aktivera den här funktionen.

Information om hur du distribuerar modeller i R och Python till Azure Machine Learning finns i [Tio saker du kan göra på den virtuella datorn för datavetenskap](vm-do-ten-things.md).

> [!NOTE]
> Dessa instruktioner skrevs för Windows-versionen av Virtual Machine för datavetenskap. Men informationen där om distribution av modeller till Azure Machine Learning gäller för Linux-virtuella datorn.

## <a name="machine-learning-tools"></a>Verktyg för maskininlärning

Den virtuella datorn levereras med verktygsinlärningsverktyg och algoritmer som har förkompilerats och förinstallerats lokalt. Exempel på dessa är:

* **Vowpal Wabbit**: En snabb online lärande algoritm.
* **xgboost**: Ett verktyg som ger optimerade, förstärkta trädalgoritmer.
* **Skallra:** Ett R-baserat grafiskt verktyg för enkel datautforskning och modellering.
* **Python**: Anaconda Python levereras med maskininlärningsalgoritmer med bibliotek som Scikit-learn. Du kan installera andra bibliotek `pip install` med kommandot.
* **LightGBM**: Ett snabbt, distribuerat, högpresterande gradienthöjande ramverk baserat på beslutsträdsalgoritmer.
* **R**: Ett omfattande bibliotek med maskininlärningsfunktioner är tillgängligt för R. Förinstallerade bibliotek inkluderar lm, glm, randomForest och rpart. Du kan installera andra bibliotek genom att köra det här kommandot:
  
        install.packages(<lib name>)

Här är ytterligare information om de tre första verktygsinlärningsverktygen i listan.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit är ett maskininlärningssystem som använder tekniker som online, hashing, allreduce, reduktioner, learning2search, aktiv och interaktivt lärande.

Om du vill köra verktyget i ett grundläggande exempel använder du följande kommandon:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Det finns andra, större demos i den katalogen. För mer information om Vowpal Wabbit, se [detta avsnitt av GitHub](https://github.com/JohnLangford/vowpal_wabbit) och [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

Xgboost-biblioteket är utformat och optimerat för förstärkta (träd) algoritmer. Syftet med detta bibliotek är att driva beräkningsgränser för maskiner till de ytterligheter som behövs för att ge storskaliga träd öka som är skalbar, bärbar och korrekt.

Det tillhandahålls som en kommandorad och ett R-bibliotek. Om du vill använda det här biblioteket i R kan du starta en interaktiv R-session (genom att ange **R** i skalet) och läsa in biblioteket.

Här är ett enkelt exempel som du kan köra i en R-prompt:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Om du vill köra xgboost-kommandoraden är det här kommandona som ska köras i skalet:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

En MODELLFIL skrivs till den angivna katalogen. Du hittar information om det här demoexemplet [på GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Mer information om xgboost finns på [dokumentationssidan för xgboost](https://xgboost.readthedocs.org/en/latest/) och dess [GitHub-databas](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Skallra **(R** **A**nalytical **T**ool **T**o **L**tjäna **E**asily) använder GUI-baserade data utforskning och modellering. Den presenterar statistiska och visuella sammanfattningar av data, omvandlar data som lätt kan modelleras, bygger både oövervakade och övervakade modeller från data, presenterar prestanda för modeller grafiskt och betygsätter nya datauppsättningar. Det genererar också R-kod, replikera de åtgärder i användargränssnittet som kan köras direkt i R eller användas som en utgångspunkt för vidare analys.

För att köra Rattle måste du vara i en grafisk skrivbordssession. På terminalen anger du **R** för att öppna R-miljön. Ange följande kommandon i R-prompten:

```R
library(rattle)
rattle()
```

Nu öppnas ett grafiskt gränssnitt med en uppsättning flikar. Använd följande snabbstartssteg i Rattle för att använda en exempelvädersdatauppsättning och skapa en modell. I några av stegen uppmanas du att automatiskt installera och läsa in några nödvändiga R-paket som inte redan finns på systemet.

> [!NOTE]
> Om du inte har åtkomst till att installera paketet i systemkatalogen (standard) kan du se en uppmaning i R-konsolfönstret om att installera paket i ditt personliga bibliotek. Svara **y** om du ser dessa uppmaningar.

1. Välj **Kör**.
1. En dialogruta visas och du får frågan om du vill använda exempelviten för väderdata. Välj **Ja** om du vill läsa in exemplet.
1. Välj fliken **Modell.**
1. Välj **Kör för** att skapa ett beslutsträd.
1. Välj **Rita** om du vill visa beslutsträdet.
1. Välj alternativet **Skog** och välj **Kör** för att skapa en slumpmässig skog.
1. Välj fliken **Utvärdera.**
1. Välj alternativet **Risk** och välj **Kör** för att visa två **prestandadiagram för risk (kumulativa).**
1. Välj fliken **Logg** om du vill visa den genererade R-koden för föregående operationer.
   (På grund av ett fel i den aktuella **#** versionen av Rattle måste du infoga ett tecken framför Exportera den **här loggen** i loggens text.)
1. Välj knappen **Exportera** om du vill spara R-skriptfilen *weather_script. R* till hemmappen.

Du kan avsluta Rattle och R. Nu kan du ändra det genererade R-skriptet. Eller använd skriptet som det är och kör det när som helst för att upprepa allt som gjordes inom Rattle UI. Speciellt för nybörjare i R, är detta ett sätt att snabbt göra analys och maskininlärning i ett enkelt grafiskt gränssnitt, samtidigt som automatiskt generera kod i R för att ändra eller lära.

## <a name="next-steps"></a>Nästa steg

Har du fler frågor? Överväg att skapa en [supportbiljett](https://azure.microsoft.com/support/create-ticket/).