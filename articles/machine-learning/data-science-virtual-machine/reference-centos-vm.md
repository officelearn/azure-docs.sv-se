---
title: 'Referens: Virtual Machine för CentOS Data Science'
titleSuffix: Azure Data Science Virtual Machine
description: Information om verktyg som ingår i Virtual Machine för CentOS Data Science
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: db49a9f5e0e6675d93cb58d6af9c92fac21e8b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525846"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Referens: CentOS (Linux) Virtual Machine för datavetenskap

Linux Data Science Virtual Machine (DSVM) är en virtuell Azure-baserad Azure-dator. Linux DSVM levereras med en samling förinstallerade verktyg som du kan använda för dataanalys och maskininlärning. 

De viktigaste programvarukomponenterna som ingår i en Linux DSVM är:

* Linux CentOS distribution operativsystem.
* Microsoft Machine Learning Server.
* Anaconda Python-distribution (version 3.5 och 2.7), inklusive populära dataanalysbibliotek.
* JuliaPro, en kurerad distribution av Julia-språket och populära vetenskapliga och dataanalysbibliotek.
* Spark Fristående förekomst och ennod Hadoop (HDFS, YARN).
* JupyterHub, en fleranvändarserver för Jupyter som stöder R-, Python-, PySpark- och Julia-kärnor.
* Utforskaren för Azure Storage.
* Azure CLI, Azure-kommandoradsgränssnittet för hantering av Azure-resurser.
* PostgresSQL databas.
* Verktyg för maskininlärning:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), en verktygslåda för djupinlärningsprogram från Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), ett snabbt maskininlärningssystem som stöder tekniker som online, hashing, allreduce, minskningar, learning2search, aktiv och interaktivt lärande.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), ett verktyg som ger snabb och exakt förstärkt trädimplementering.
  * [Rattle](https://togaware.com/rattle/), ett verktyg som gör det enkelt att komma igång med dataanalys och maskininlärning i R. Rattle erbjuder både GUI-baserad datautforskning och modellering med hjälp av automatisk R-kodgenerering.
* Azure SDK i Java, Python, Node.js, Ruby och PHP.
* Bibliotek i R och Python som ska användas i Azure Machine Learning och andra Azure-tjänster.
* Utvecklingsverktyg och redaktörer (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

Datavetenskap innebär att iterera på en sekvens av uppgifter:

1. Hitta, läsa in och förbehandla data.
1. Bygg- och testmodeller.
1. Distribuera modeller för förbrukning i intelligenta program.

Dataforskare använder olika verktyg för att slutföra dessa uppgifter. Det kan vara tidskrävande att hitta rätt versioner av programvaran och sedan ladda ner, kompilera och installera programvaran.

Linux DSVM kan minska denna börda avsevärt. Använd Linux DSVM för att få fart på ditt analysprojekt. Linux DSVM hjälper dig att arbeta med uppgifter på olika språk, inklusive R, Python, SQL, Java och C++. Eclipse ger en lätt att använda IDE för att utveckla och testa din kod. Azure SDK, som ingår i DSVM, hjälper dig att skapa dina program med hjälp av olika tjänster på Linux för Microsofts molnplattform. Andra språk är förinstallerade, inklusive Ruby, Perl, PHP och Node.js.

Det tillkommer inga programvaruavgifter för DSVM-avbildningen. Du betalar bara de Azure-maskinvaruanvändningsavgifter som bedöms baserat på storleken på den virtuella datorn som du etablerar med DSVM-avbildningen. Mer information om beräkningsavgifterna finns i [data science virtual machine for Linux (CentOS) i](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) Azure Marketplace.


## <a name="machine-learning-server"></a>Machine Learning Server

R är ett av de mest populära språken för dataanalys och maskininlärning. Om du vill använda R för dina analyser har DSVM Machine Learning Server med Microsoft R Open och Math Kernel Library. Math Kernel Library optimerar vanliga matematiska åtgärder i analytiska algoritmer. R Open är fullt kompatibelt med CRAN R. Alla R-bibliotek som publiceras i CRAN kan installeras på R Open. 

Du kan använda Machine Learning Server för att skala och operationalisera R-modeller till webbtjänster. Du kan redigera dina R-program i en av standardredigerarna, till exempel RStudio, vi eller Emacs. Emacs-redigeraren är förinstallerad på DSVM. Emacs ESS-paketet (Emacs Speaks Statistics) förenklar arbetet med R-filer i Emacs-redigeraren.

Om du vill öppna R-konsolen anger du **R**i skalet . Det här kommandot tar dig till en interaktiv miljö. För att utveckla ditt R-program använder du vanligtvis en redigerare som Emacs eller vi och kör sedan skripten i R. RStudio erbjuder en fullständig grafisk IDE för att utveckla ditt R-program.

Ett R-skript som du kan använda för att installera de [översta 20 R-paketen](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) ingår i DSVM. Du kan köra det här skriptet när du är i R interaktiva gränssnittet. Som tidigare nämnts, för att öppna det gränssnittet, i skalet, ange **R**.  

## <a name="python"></a>Python

Anaconda Python installeras med Python 3.5- och 2.7-miljöerna. 2,7-miljön kallas _rot_ och 3,5-miljön kallas _py35_. Den här distributionen innehåller bas python tillsammans med cirka 300 av de mest populära matematik-, teknik- och dataanalyspaketen.

Py35-miljön är standard. Om du vill aktivera rotmiljön (2.7) använder du det här kommandot:

```bash
source activate root
```

Om du vill aktivera py35-miljön igen använder du det här kommandot:

```bash
source activate py35
```

Om du vill anropa en interaktiv Python-session anger du **python**i skalet . 

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

Den Anaconda distributionen kommer också med en Jupyter Notebook, en miljö för att dela kod och analys. Öppna Jupyter Notebook via JupyterHub. Du loggar in med ditt lokala Användarnamn och lösenord för Linux.

Jupyter Notebook-servern är förkonfigurerad med Python 2-, Python 3- och R-kärnor. Använd **jupyter notebook-ikonen** för att öppna webbläsaren och komma åt Jupyter Notebook-servern. Om du öppnar DSVM via SSH eller X2Go-klienten kan du även\/komma åt Jupyter Notebook-servern på https: /localhost:8000/.

> [!NOTE]
> Fortsätt om du får några certifikatvarningar.

Du kan komma åt Jupyter notebook server från någon värd. Ange **\//\<https: DSVM DNS-namn eller IP-adress\>:8000/**.

> [!NOTE]
> Port 8000 öppnas som standard i brandväggen när DSVM etableras. 

Microsoft har paketerat exempelböcker, en i Python och en i R. Du kan se länken till exemplen på Jupyter Notebook hemsida när du autentisera till Jupyter Notebook med hjälp av din lokala Linux användarnamn och lösenord. Om du vill skapa en ny anteckningsbok väljer du **Ny**och väljer sedan den språkkärna som du vill använda. Om knappen **Ny** inte visas väljer du **Jupyter-ikonen** längst upp till vänster för att gå till startsidan för den bärbara servern.

## <a name="spark-standalone"></a>Spark Fristående 

En instans av Spark Fristående läge är förinstallerad på Linux DSVM för att hjälpa dig att utveckla Spark-program lokalt innan du testar och distribuerar dem på stora kluster. 

Du kan köra PySpark-program via Jupyter-kärnan. När du öppnar Jupyter väljer du knappen **Nytt** och du bör se en lista över tillgängliga kärnor. **Spark - Python** är PySpark-kärnan som låter dig skapa Spark-program med hjälp av Python-språket. Du kan också använda en Python IDE som PyCharm eller Spyder för att bygga din Spark program. 

I den här fristående instansen körs Spark-stacken i det anropande klientprogrammet. Den här funktionen gör det snabbare och enklare att felsöka problem jämfört med att utveckla på ett Spark-kluster.

Jupyter tillhandahåller ett exempel på pysparks anteckningsbok. Du hittar den i SparkML-katalogen under hemkatalogen för Jupyter ($HOME/bärbara datorer/SparkML/pySpark). 

Om du programmerar i R för Spark kan du använda Machine Learning Server, SparkR eller Sparklyr. 

Innan du kör i en Spark-kontext i Machine Learning Server måste du göra ett engångsinställningssteg för att aktivera en lokal ennod Hadoop HDFS- och YARN-instans. Som standard installeras Hadoop-tjänster men inaktiveras på DSVM. Om du vill aktivera Hadoop-tjänster kör du följande kommandon som root första gången:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Du kan stoppa Hadoop-relaterade tjänster när du inte `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`behöver dem genom att köra .

Katalogen /dsvm/samples/MRS innehåller ett exempel som visar hur du utvecklar och testar Machine Learning Server i en fjärr spark-kontext (den fristående Spark-instansen på DSVM).

## <a name="ides-and-editors"></a>ID:er och redaktörer

Du kan välja mellan flera kodredigerare, inklusive vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX och IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio och PyCharm är grafiska redaktörer. Om du vill använda dem måste du vara inloggad på ett grafiskt skrivbord. Du öppnar dem med hjälp av genvägar till skrivbords- och programmenyn.

* Vim och Emacs är textbaserade redaktörer. På Emacs gör ESS-tilläggspaketet det enklare att arbeta med R i Emacs-redigeraren. Du hittar mer information på [ESS webbplats](https://ess.r-project.org/).

* Eclipse är en öppen källkod, utökningsbar IDE som stöder flera språk. Eclipse IDE för Java-utvecklare är den version som är installerad på DSVM. Du kan installera plugin-program för flera populära språk för att utöka miljön. 

  Plugin-programmet Azure Toolkit for Eclipse installeras med Eclipse på DSVM. Du kan använda Azure Toolkit for Eclipse för att skapa, utveckla, testa och distribuera Azure-program med hjälp av eclipse-utvecklingsmiljön som stöder språk som Java.

  Azure SDK för Java installeras också med Azure Toolkit för Eclipse på DSVM. Azure SDK för Java ger dig tillgång till olika Azure-tjänster inifrån en Java-miljö. 
  
  Mer information finns i [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* LaTeX installeras via texlive-paketet, tillsammans med ett Emacs-tilläggspaket som heter [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Det här paketet gör det enklare att redigera dina LaTeX-dokument i Emacs. 

## <a name="databases"></a>Databaser

Linux DSVM ger dig tillgång till flera databas- och kommandoradsverktyg.

### <a name="postgressql"></a>PostgresSQL

PostgresSQL med öppen källkod finns tillgänglig på DSVM, med tjänster som körs och initdb slutförda. Du måste skapa databaser och användare. Mer information finns i [PostgresSQL-dokumentationen](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL är en grafisk SQL-klient som kan ansluta till olika databaser (inklusive SQL Server, PostgresSQL och MySQL) och köra SQL-frågor. Du kan köra SQuirreL SQL från en grafisk skrivbordssession (till exempel via X2Go-klienten) med hjälp av en skrivbordsikon. Du kan också köra klienten med hjälp av följande kommando i skalet:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Innan den första användningen konfigurerar du drivrutiner och databasalias. JDBC-drivrutinerna finns på /usr/share/java/jdbcdrivers.

Mer information finns i [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Kommandoradsverktyg för åtkomst till SQL Server

ODBC-drivrutinspaketet för SQL Server levereras också med två kommandoradsverktyg:

* **bcp**: Bcp-verktyget kopierar data mellan en instans av SQL Server och en datafil i ett användarspecificerat format. Du kan använda bcp-verktyget för att importera ett stort antal nya rader till SQL Server-tabeller eller för att exportera data från tabeller till datafiler. Om du vill importera data till en tabell måste du använda en formatfil som skapats för den tabellen. Du måste också förstå tabellens struktur och vilka typer av data som är giltiga för dess kolumner.

  Mer information finns i [Ansluta med bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: Du kan använda sqlcmd-verktyget för att ange Transact-SQL-uttryck, systemprocedurer och skriptfiler i kommandotolken. Sqlcmd-verktyget använder ODBC för att köra Transact-SQL-batchar.

  Mer information finns i [Ansluta med sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Det finns vissa skillnader i det här verktyget mellan Linux och Windows-plattformar. Mer information finns i dokumentationen.

### <a name="database-access-libraries"></a>Databasåtkomstbibliotek

Bibliotek för databasåtkomst är tillgängliga i R och Python:

* I R kan du använda RODBC-paketet eller dplyr-paketet för att fråga eller köra SQL-uttryck på databasservern.
* I Python ger pyodbc-biblioteket databasåtkomst med ODBC som underliggande lager.

## <a name="azure-tools"></a>Azure-verktyg

Följande Azure-verktyg är installerade på DSVM:

* **Azure CLI**: Du kan använda kommandoradsgränssnittet i Azure för att skapa och hantera Azure-resurser via skalkommandon. Om du vill öppna Azure-verktygen anger du **azure-hjälp**. Mer information finns på [sidan Azure CLI-dokumentation](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer:** Azure Storage Explorer är ett grafiskt verktyg som du kan använda för att bläddra igenom de objekt som du har lagrat i ditt Azure-lagringskonto och för att ladda upp och hämta data till och från Azure-blobbar. Du kan komma åt Storage Explorer från genvägsikonen på skrivbordet. Du kan också öppna den från en skalfråga genom att ange **StorageExplorer**. Du måste vara inloggad från en X2Go-klient eller ha X11-vidarebefordran inställd.
* **Azure-bibliotek**: Följande bibliotek är förinstallerade på DSVM:
  
  * **Python**: Azure-relaterade bibliotek i Python är *azure,* *azureml*, *pydocumentdb*och *pyodbc*. Med de tre första biblioteken kan du komma åt Azure-lagringstjänster, Azure Machine Learning och Azure Cosmos DB (en NoSQL-databas på Azure). Det fjärde biblioteket, pyodbc (tillsammans med Microsoft ODBC-drivrutinen för SQL Server) ger åtkomst till SQL Server, Azure SQL Database och Azure SQL Data Warehouse från Python med hjälp av ett ODBC-gränssnitt. Ange **pip-listan** för att se alla bibliotek i listan. Var noga med att köra det här kommandot i både Python 2.7- och 3.5-miljöerna.
  * **R**: Azure-relaterade bibliotek i R är AzureML och RODBC.
  * **Java**: Listan över Azure Java-bibliotek finns i katalogen /dsvm/sdk/AzureSDKJava på DSVM. De viktigaste biblioteken är Azure-api:er för lagring och hantering, Azure Cosmos DB och JDBC-drivrutiner för SQL Server.  

Du kan komma åt [Azure-portalen](https://portal.azure.com) från den förinstallerade Webbläsaren Firefox. I Azure-portalen kan du skapa, hantera och övervaka Azure-resurser.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning är en fullständigt hanterad molntjänst som du kan använda för att skapa, distribuera och dela lösningar för prediktiv analys. Du skapar dina experiment och modeller från Azure Machine Learning Studio (klassisk). Om du vill komma åt Azure Machine Learning från en webbläsare på DSVM går du till [Microsoft Azure Machine Learning](https://studio.azureml.net).

När du har loggat in på Azure Machine Learning Studio (klassisk) kan du använda en experimentarbetsyta för att skapa ett logiskt flöde för maskininlärningsalgoritmerna. Du har också tillgång till en Jupyter-anteckningsbok som finns på Azure Machine Learning. Anteckningsboken kan fungera sömlöst med experimenten i Azure Machine Learning Studio (klassisk). 

Operationalize de maskininlärningsmodeller som du skapar genom att slå in dem i ett webbtjänstgränssnitt. Operationalisering av maskininlärningsmodeller gör det möjligt för klienter som är skrivna på alla språk att anropa förutsägelser från dessa modeller. Mer information finns i [machine learning-dokumentationen](https://azure.microsoft.com/documentation/services/machine-learning/).

Du kan också skapa dina modeller i R eller Python på DSVM och sedan distribuera dem i produktion på Azure Machine Learning. Microsoft har installerat bibliotek i R (**AzureML**) och Python (**azureml**) för att stödja den här funktionen.

Information om hur du distribuerar modeller i R och Python till Azure Machine Learning finns i [Tio saker du kan göra på den virtuella datorn för datavetenskap](vm-do-ten-things.md).

> [!NOTE]
> Instruktionerna i [tio saker du kan göra på Data Science Virtual Machine](vm-do-ten-things.md) skrevs för Windows-versionen av DSVM. Informationen om hur du distribuerar modeller till Azure Machine Learning gäller dock även för Linux DSVM.

## <a name="machine-learning-tools"></a>Verktyg för maskininlärning

DSVM levereras med några verktygsinlärningsverktyg och algoritmer som är förkompilerade och förinstallerade lokalt. Exempel på dessa är:

* **Microsoft Cognitive Toolkit**: En djupinlärningsverktygslåda.
* **Vowpal Wabbit**: En snabb online lärande algoritm.
* **XGBoost**: Ett verktyg som ger optimerade, förstärkta trädalgoritmer.
* **Python**: Anaconda Python levereras med maskininlärningsalgoritmer med bibliotek som Scikit-learn. Du kan installera andra bibliotek `pip install` med kommandot.
* **R**: Ett omfattande bibliotek med maskininlärningsfunktioner är tillgängligt för R. Förinstallerade bibliotek inkluderar lm, glm, randomForest och rpart. Du kan installera andra `install.packages(<lib name>)`bibliotek genom att köra .

Microsoft Cognitive Toolkit, Vowpal Wabbit och XGBoost diskuteras mer i detalj i nästa avsnitt.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit är en deep learning-verktygslåda med öppen källkod. Det är ett kommandoradsverktyg (CNTK) och finns redan i PATH.

Om du vill köra ett grundläggande exempel kör du följande kommandon i skalet:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Mer information finns i [GitHub CNTK-databasen](https://github.com/Microsoft/CNTK) och [CNTK-wikin](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit är ett maskininlärningssystem som använder tekniker som online, hashing, allreduce, reduktioner, learning2search, aktiv och interaktivt lärande.

Om du vill köra verktyget i ett grundläggande exempel kör du följande kommandon:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Den Vowpal Wabbit demo katalog innehåller andra, större demos. För mer information om Vowpal Wabbit, se [GitHub Vowpal Wabbit förvaret](https://github.com/JohnLangford/vowpal_wabbit) och [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

XGBoost-biblioteket är utformat och optimerat för förstärkta (träd) algoritmer. Målet med XGBoost biblioteket är att driva beräkningsgränser för maskiner till de ytterligheter som behövs för att ge storskaliga träd öka som är skalbar, bärbar och korrekt.

XGBoost tillhandahålls som en kommandorad och som ett R-bibliotek.

Om du vill använda XGBoost-biblioteket i R startar du en interaktiv R-session (i skalet, anger **R**) och läser sedan in biblioteket.

Här är ett enkelt exempel som du kan köra på R-prompten:

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

Om du vill köra XGBoost-kommandoraden kör du dessa kommandon i skalet:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

En MODELLFIL skrivs till den angivna katalogen. Information om det här demoexemplet på GitHub finns i [Binärklassificering](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Mer information om XGBoost finns i [XGBoost-dokumentationen](https://xgboost.readthedocs.org/en/latest/) och [XGBoost GitHub-databasen](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*tjäna *E*asily) använder GUI-baserade data prospektering och modellering. Skallra:
- Presenterar statistiska och visuella sammanfattningar av data.
- Omvandlar data som lätt kan modelleras.
- Bygger både oövervakade och övervakade modeller från data.
- Presenterar prestanda modeller grafiskt.
- Betyg på nya datauppsättningar.
- Genererar R-kod.
- Replikerar åtgärder i användargränssnittet som kan köras direkt i R eller användas som utgångspunkt för mer analys.

Om du vill köra Rattle måste du vara inloggad på en grafisk skrivbordssession. I en terminal anger du **R** för att öppna R-miljön. Ange följande kommandon i R-prompten:

```R
library(rattle)
rattle()
```

Ett grafiskt gränssnitt med en uppsättning flikar öppnas. Använd följande snabbstartssteg i Rattle för att använda en exempelvädersdatauppsättning och skapa en modell. I några av stegen uppmanas du att automatiskt installera och läsa in några nödvändiga R-paket som inte redan finns på systemet.

> [!NOTE]
> Om du inte har behörighet att installera paketet i systemkatalogen (standard) kan du se en uppmaning i R-konsolfönstret om att installera paket i ditt personliga bibliotek. Om dessa uppmaningar visas anger du **y**.

1. Välj **Kör**.
1. I en dialogruta uppmanas du att läsa in exempelviten för väderdata. Välj **Ja** om du vill läsa in exemplet.
1. Välj fliken **Modell.**
1. Välj **Kör för** att skapa ett beslutsträd.
1. Välj **Rita** om du vill visa beslutsträdet.
1. Välj alternativet **Skog** och välj sedan **Kör** för att skapa en slumpmässig skog.
1. Välj fliken **Utvärdera.**
1. Välj alternativet **Risk** och välj sedan **Kör** för att visa två **prestandadiagram för risk (kumulativa).**
1. Välj fliken **Logg** om du vill visa den genererade R-koden för föregående operationer. (På grund av ett fel i den aktuella **#** versionen av Rattle måste du infoga ett tecken framför Exportera den **här loggen** i loggens text.)
1. Välj knappen **Exportera** om du vill spara R-skriptfilen *weather_script. R* till hemmappen.

Du kan avsluta Rattle och R. Nu kan du ändra det genererade R-skriptet. Eller använd skriptet som det är och kör det när som helst för att upprepa allt som gjordes i Rattle UI. Speciellt för nybörjare i R, är detta ett sätt att snabbt göra analys och maskininlärning i ett enkelt grafiskt gränssnitt, samtidigt som automatiskt generera kod i R för att ändra eller för lärande.

## <a name="next-steps"></a>Nästa steg

Har du fler frågor? Överväg att skapa en [supportbiljett](https://azure.microsoft.com/support/create-ticket/).