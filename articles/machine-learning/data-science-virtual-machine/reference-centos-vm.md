---
title: 'Referens: CentOS DSVM'
description: Information om verktyg som ingår i CentOS-Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 9366eb1bde05d80b8882ee28aa9eb03a75f75964
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174726"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Referens: CentOS (Linux) Data Science Virtual Machine

Linux-Data Science Virtual Machine (DSVM) är en CentOS-baserad virtuell Azure-dator. Linux-DSVM levereras med en samling förinstallerade verktyg som du kan använda för data analys och maskin inlärning. 

Viktiga program varu komponenter som ingår i en Linux-DSVM är:

* Linux-CentOS distributions operativ system.
* Microsoft Machine Learning Server.
* Anaconda python-distribution (version 3,5 och 2,7), inklusive populära data analys bibliotek.
* JuliaPro, en granskad distribution av Julia-språket och populära vetenskapliga och data analys bibliotek.
* Spark fristående instans och Hadoop med en nod (HDFS, garn).
* JupyterHub, en Jupyter Notebook-server som stöder R-, python-, PySpark-och Julia-kärnor.
* Azure Storage Explorer.
* Azure CLI, Azures kommando rads gränssnitt för att hantera Azure-resurser.
* PostgresSQL-databas.
* Machine Learning-verktyg:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), ett djup inlärnings program verktyg från Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), ett snabbt Machine Learning-system som stöder metoder som online, hashing, allreduce, reduktioner, learning2search, aktiv och interaktiv utbildning.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), ett verktyg som ger snabb och korrekt utökat träd implementering.
  * [Rattle](https://togaware.com/rattle/)är ett verktyg som gör det enkelt att komma igång med data analys och maskin inlärning. Rattle erbjuder både GUI-baserad data utforskning och modellering genom att använda automatisk R-kod generation.
* Azure SDK i Java, python, Node. js, ruby och PHP.
* Bibliotek i R och python som ska användas i Azure Machine Learning och andra Azure-tjänster.
* Utvecklingsverktyg och redigerare (RStudio, pycharm med, IntelliJ, emacs, gedit, vi).

Datavetenskap omfattar iteration av en serie uppgifter:

1. Hitta, Läs in och Förbearbeta data.
1. Skapa och testa modeller.
1. Distribuera modeller för användning i intelligenta program.

Dataexperter använda olika verktyg för att utföra dessa uppgifter. Det kan vara tids krävande att hitta rätt versioner av program varan och sedan ladda ned, kompilera och installera program varan.

Linux-DSVM kan under lätta den här belastningen avsevärt. Använd Linux-DSVM för att komma igång med ditt analys projekt. Linux-DSVM hjälper dig att arbeta med uppgifter på olika språk, inklusive R, python, SQL, Java och C++. Sol förmörkelse tillhandahåller en lättanvänd IDE för utveckling och testning av koden. Azure SDK, som ingår i DSVM, hjälper dig att bygga dina program med hjälp av olika tjänster i Linux för Microsofts moln plattform. Andra språk är förinstallerade, inklusive ruby, perl, PHP och Node. js.

Det finns inga program varu avgifter för DSVM-avbildningen. Du betalar bara de avgifter för Azure-maskinvara som utvärderas utifrån storleken på den virtuella dator som du etablerar med DSVM-avbildningen. Mer information om beräknings avgifter finns i [listan data science Virtual Machine för Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) på Azure Marketplace.


## <a name="machine-learning-server"></a>Machine Learning Server

R är ett av de mest populära språk för dataanalys och maskininlärning. Om du vill använda R för din analys har DSVM Machine Learning Server med Microsoft R Open och Math kernel-biblioteket. I matematiskt kernel-bibliotek optimeras vanliga matematiska åtgärder i analytiska algoritmer. R Open är helt kompatibelt med CRAN R. Alla R-bibliotek som publiceras i CRAN kan installeras på R Open. 

Du kan använda Machine Learning Server för att skala och operationalisera R-modeller till webb tjänster. Du kan redigera din R-program i ett standard-redigerare som RStudio, vi och Emacs. Emacs-redigeraren är förinstallerad på DSVM. Paketet emacs ESS (emacs pratar Statistics) fören klar arbetet med R-filer i emacs-redigeraren.

Om du vill öppna R-konsolen går du till gränssnittet och anger **R**. Det här kommandot tar dig till en interaktiv miljö. För att utveckla R-programmet använder du vanligt vis ett redigerings program som emacs eller vi och kör sedan skripten i R. RStudio erbjuder en fullständig grafisk IDE för att utveckla R-programmet.

Ett R-skript som du kan använda för att installera de [översta 20 R-paketen](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) ingår i DSVM. Du kan köra det här skriptet när du är i det interaktiva R-gränssnittet. Som tidigare nämnts öppnar du det gränssnittet genom att skriva **R**i gränssnittet.  

## <a name="python"></a>Python

Anaconda python installeras med python 3,5-och 2,7-miljöerna. 2,7-miljön kallas _rot_ och 3,5-miljön kallas _py35_. Den här distributionen innehåller grundläggande Python tillsammans med ungefär 300 av de mest populära analyspaket matematiska, teknik och data.

Py35 miljön är standardinställningen. Använd följande kommando för att aktivera rot miljön (2,7):

```bash
source activate root
```

Använd följande kommando för att aktivera py35-miljön igen:

```bash
source activate py35
```

Om du vill anropa en python-interaktiv session skriver du **python**i gränssnittet. 

Installera ytterligare python-bibliotek med Conda eller pip. För pip aktiverar du rätt miljö först om du inte vill använda standardvärdet:

```bash
source activate root
pip install <package>
```

Eller ange den fullständiga sökvägen till pip:

```bash
/anaconda/bin/pip install <package>
```

För Conda ska du alltid ange miljö namnet (py35 eller root):

```bash
conda install <package> -n py35
```

Om du har ett grafiskt gränssnitt eller har konfigurerat begäran om x11-vidarebefordran kan du ange **pycharm med** för att öppna PYCHARM med python IDE. Du kan använda standard-textredigerare. Dessutom kan du använda Spyder, en python IDE som är paketerad med Anaconda python-distributioner. Spyder måste en grafisk desktop eller X11 vidarebefordran. Det grafiska Skriv bordet har en genväg till Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

Anaconda-distributionen levereras också med en Jupyter Notebook, en miljö för att dela kod och analys. Få åtkomst till Jupyter Notebook via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn och lösen ord.

Jupyter Notebook servern är förkonfigurerad med python 2, python 3 och R-kernel. Använd **Jupyter Notebook** Skriv bords ikonen för att öppna webbläsaren och komma åt Jupyter Notebook-servern. Om du har åtkomst till DSVM via SSH-eller X2Go-klienten, kan du också komma åt Jupyter Notebook server på\/https:/localhost: 8000/.

> [!NOTE]
> Fortsätt om du får några certifikatvarningar.

Du kan komma åt Jupyter notebook-server från valfri värddator. Ange **https:\//DSVMDNS\>-namn eller IP-adress: 8000/.\<**

> [!NOTE]
> Port 8000 öppnas i brand väggen som standard när DSVM är etablerad. 

Microsoft har paketerade exempel antecknings böcker, en i python och en i R. Du kan se länken till exemplen på Jupyter Notebook start sida när du har autentiserat dig för Jupyter Notebook med hjälp av ditt lokala Linux-användarnamn och lösen ord. Om du vill skapa en ny antecknings bok väljer du **ny**och väljer sedan den språk-kernel som du vill använda. Om du inte ser knappen **nytt** väljer du ikonen **Jupyter** längst upp till vänster för att gå till hem sidan för notebook-servern.

## <a name="spark-standalone"></a>Spark fristående 

En instans av läget Spark standalone är förinstallerad på Linux-DSVM för att hjälpa dig att utveckla Spark-program lokalt innan du testar och distribuerar dem i stora kluster. 

Du kan köra PySpark program via Jupyter-kernel. När du öppnar Jupyter väljer du knappen **nytt** och du bör se en lista över tillgängliga kernels. **Spark – python** är PySpark-kärnan som gör att du kan bygga Spark-program med hjälp av python-språket. Du kan också använda en python IDE som pycharm med eller Spyder för att bygga Spark-programmet. 

I den här fristående instansen körs Spark-stacken i det anropande klient programmet. Den här funktionen gör det snabbare och enklare att felsöka problem jämfört med att utveckla i ett Spark-kluster.

Jupyter tillhandahåller en exempel PySpark Notebook. Du hittar den i katalogen SparkML under arbets katalogen för Jupyter ($HOME/notebooks/SparkML/pySpark). 

Om du använder programmering i R för Spark kan du använda Machine Learning Server, sparker eller sparklyr. 

Innan du kör i en spark-kontext i Machine Learning Server måste du göra ett engångs steg för att aktivera en lokal instans av Hadoop HDFS och garn för en enda nod. Som standard är Hadoop-tjänster installerat men inaktiverat på DSVM. Om du vill aktivera Hadoop-tjänster kör du följande kommandon som rot första gången:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Du kan stoppa de Hadoop-relaterade tjänsterna när du inte behöver dem genom att `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`köra.

/Dsvm/samples/MRS-katalogen innehåller ett exempel som visar hur du utvecklar och testar Machine Learning Server i en fjärr Spark-kontext (den fristående Spark-instansen på DSVM).

## <a name="ides-and-editors"></a>IDE: er och redigeringsprogram

Du kan välja bland flera kod redigerare, inklusive vi/VIM, emacs, gedit, pycharm med, RStudio, Sol förmörkelse, LaTeX och IntelliJ. 

* gedit, Sol förmörkelse, IntelliJ, R Studio och pycharm med är grafiska redigerings program. Om du vill använda dem måste du vara inloggad på ett grafiskt skriv bord. Du öppnar dem med hjälp av meny gen vägar för Skriv bordet och program.

* Vim och emacs är textbaserade redigerare. I emacs fungerar ESS tilläggs paketet med R i emacs-redigeraren enklare. Du hittar mer information på ess- [webbplatsen](https://ess.r-project.org/).

* Sol förmörkelse är en utöknings bar IDE med öppen källkod som har stöd för flera språk. Sol förmörkelse IDE för Java-utvecklare är versionen som är installerad på DSVM. Du kan installera plugin-program för flera populära språk för att utöka miljön. 

  Plugin-programmet Azure Toolkit for Eclipse installeras med Sol förmörkelse på DSVM. Du kan använda Azure Toolkit for Eclipse för att skapa, utveckla, testa och Distribuera Azure-program med hjälp av utvecklings miljön för Sol förmörkelse som stöder språk som Java.

  Azure SDK för Java installeras också med Azure Toolkit for Eclipse på DSVM. Azure SDK för Java ger dig till gång till olika Azure-tjänster inifrån en Java-miljö. 
  
  Mer information finns i [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* LaTeX installeras via texlive-paketet, tillsammans med ett Emacs tilläggs paket som kallas [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Det här paketet fören klar redigeringen av dina LaTeX-dokument i emacs. 

## <a name="databases"></a>Databaser

Linux-DSVM ger dig åtkomst till flera databas-och kommando rads verktyg.

### <a name="postgressql"></a>PostgresSQL

Databasen med öppen källkod är PostgresSQL tillgänglig på DSVM, där tjänster som kör och initdb har slutförts. Du måste skapa databaser och användare. Mer information finns i PostgresSQL- [dokumentationen](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL är en grafisk SQL-klient som kan ansluta till olika databaser (inklusive SQL Server, PostgresSQL och MySQL) och köra SQL-frågor. Du kan köra SQuirreL SQL från en grafisk fjärrskrivbordssession (till exempel via X2Go-klienten) genom att använda en Skriv bords ikon. Eller så kan du köra-klienten med hjälp av följande kommando i gränssnittet:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Ställa in din drivrutiner och databasen alias innan den första användningen. JDBC-drivrutinerna finns på/usr/share/Java/jdbcdrivers.

Mer information finns i [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Kommando rads verktyg för att komma åt SQL Server

Paketets ODBC-drivrutinen för SQL Server levereras med två kommandoradsverktyg:

* **BCP**: BCP-verktyget Mass kopiering av data mellan en instans av SQL Server och en datafil i ett användardefinierat format. Du kan använda BCP-verktyget för att importera ett stort antal nya rader till SQL Server tabeller eller för att exportera data från tabeller till datafiler. Om du vill importera data till en tabell måste du använda en format fil som skapats för tabellen. Eller så måste du förstå tabell strukturen och vilka typer av data som är giltiga för kolumnerna.

  Mer information finns i [ansluta till BCP](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: Du kan använda verktyget sqlcmd för att ange Transact-SQL-uttryck, system procedurer och skriptfiler i kommando tolken. Verktyget sqlcmd använder ODBC för att köra Transact-SQL-batchar.

  Mer information finns i [ansluta med SQLCMD](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Det finns vissa skillnader i det här verktyget mellan Linux-och Windows-plattformar. Se dokumentationen för mer information.

### <a name="database-access-libraries"></a>Bibliotek för åtkomst av databasen

Bibliotek för databas åtkomst är tillgängliga i R och python:

* I R kan du använda RODBC-paketet eller dplyr-paketet för att fråga eller köra SQL-uttryck på databas servern.
* I python ger pyodbc-biblioteket databas åtkomst med ODBC som underliggande lager.

## <a name="azure-tools"></a>Azure-verktyg

Följande Azure-verktyg är installerade på DSVM:

* **Azure CLI**: Du kan använda kommando rads gränssnittet i Azure för att skapa och hantera Azure-resurser via Shell-kommandon. Öppna Azure-verktygen genom att ange **Azure-hjälpen**. Mer information finns i den [dokumentationssidan för Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer är ett grafiskt verktyg som du kan använda för att bläddra bland de objekt som du har lagrat på ditt Azure Storage-konto och för att ladda upp och ladda ned data till och från Azure-blobbar. Du kan komma åt Storage Explorer från ikonen genvägen på skrivbordet. Du kan också öppna den från en Shell-prompt genom att ange **StorageExplorer**. Du måste vara inloggad från en X2Go-klient eller konfigurera begäran om x11-vidarebefordring.
* **Azure-bibliotek**: Följande bibliotek är förinstallerade på DSVM:
  
  * **Python**: Azure-relaterade bibliotek i python är *Azure*, *azureml*, *pydocumentdb*och *pyodbc*. Med de tre första biblioteken kan du komma åt Azure storage-tjänster, Azure Machine Learning och Azure Cosmos DB (en NoSQL-databas på Azure). Fjärde biblioteket pyodbc (tillsammans med Microsoft ODBC-drivrutinen för SQL Server), ger åtkomst till SQL Server, Azure SQL Database och Azure SQL Data Warehouse från Python med hjälp av en ODBC-gränssnittet. Ange **pip lista** att se alla bibliotek. Glöm inte att köra det här kommandot i både Python 2.7 och 3,5 miljöer.
  * **R**: Azure-relaterade bibliotek i R är AzureML och RODBC.
  * **Java**: Listan med Azure Java-bibliotek finns i katalogen/dsvm/sdk/AzureSDKJava på DSVM. Viktiga bibliotek är Azure lagring och hantering av API: er, Azure Cosmos DB och JDBC-drivrutiner för SQL Server.  

Du kan komma åt [Azure Portal](https://portal.azure.com) från den förinstallerade Firefox-webbläsaren. I Azure Portal kan du skapa, hantera och övervaka Azure-resurser.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning är en helt hanterad moln tjänst som du kan använda för att bygga, distribuera och dela lösningar för förutsägelse analys. Du bygger dina experiment och modeller från Azure Machine Learning Studio. Om du vill komma åt Azure Machine Learning från en webbläsare på DSVM går du till [Microsoft Azure Machine Learning](https://studio.azureml.net).

När du har loggat in på Azure Machine Learning Studio kan du använda en experiment arbets yta för att bygga ett logiskt flöde för Machine Learning-algoritmer. Du har också till gång till en Jupyter Notebook som finns på Azure Machine Learning. Antecknings boken kan arbeta sömlöst med experimenten i Machine Learning Studio. 

Operationalisera de maskin inlärnings modeller som du skapar genom att figursätta dem i ett webb tjänst gränssnitt. Genom att använda Machine Learning-modeller kan klienter skrivna på valfritt språk för att anropa förutsägelser från dessa modeller. Mer information finns i den [dokumentation om Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Du kan också bygga dina modeller i R eller python på DSVM och sedan distribuera dem i produktion på Azure Machine Learning. Microsoft har installerat bibliotek i R (**azureml**) och python (**azureml**) för att stödja den här funktionen.

Information om hur du distribuerar modeller i R och python till Azure Machine Learning finns i [tio saker du kan göra på data science Virtual Machine](vm-do-ten-things.md).

> [!NOTE]
> Anvisningarna i [tio saker du kan göra på data science Virtual Machine](vm-do-ten-things.md) skrevs för Windows-versionen av DSVM. Men information om distribution av modeller till Azure Machine Learning gäller även för Linux-DSVM.

## <a name="machine-learning-tools"></a>Machine learning-verktyg

DSVM levereras med några maskin inlärnings verktyg och algoritmer som är förkompilerade och förinstallerade lokalt. Exempel på dessa är:

* **Microsoft Cognitive Toolkit**: Ett djup inlärnings verktyg.
* **Vowpal Wabbit**: En snabb online-inlärnings algoritm.
* **XGBoost**: Ett verktyg som tillhandahåller optimerade, förstärkta träd algoritmer.
* **Python**: Anaconda python levereras med Machine Learning-algoritmer med bibliotek som Scikit-lär. Du kan installera andra bibliotek med hjälp av den `pip install` kommando.
* **R**: Ett omfattande bibliotek med Machine Learning-funktioner är tillgängligt för R. förinstallerade bibliotek omfattar lm, GLM, randomForest och rpart. Du kan installera andra bibliotek genom att `install.packages(<lib name>)`köra.

Microsoft Cognitive Toolkit, Vowpal Wabbit och XGBoost diskuteras i detalj i nästa avsnitt.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit är ett djup inlärnings verktyg med öppen källkod. Det är ett kommando rads verktyg (CNTK) och finns redan i sökvägen.

Kör följande kommandon i gränssnittet för att köra ett grundläggande exempel:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Mer information finns i [GITHUB CNTK-lagringsplatsen](https://github.com/Microsoft/CNTK) och [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit är ett maskin inlärnings system som använder tekniker som online, hashing, allreduce, reduktioner, learning2search, aktiv och interaktiv utbildning.

Kör följande kommandon för att köra verktyget på ett Basic-exempel:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Vowpal Wabbit-demo katalogen innehåller andra, större demonstrationer. Mer information om Vowpal-Wabbit finns i [GitHub Vowpal Wabbit-lagringsplatsen](https://github.com/JohnLangford/vowpal_wabbit) och [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

XGBoost-biblioteket är utformat och optimerat för förstärkta algoritmer (Tree). Målet med XGBoost-biblioteket är att skicka datorernas beräknings gränser till de extrem värden som krävs för att skapa storskaliga träd förstärkningar som är skalbara, bärbara och korrekta.

XGBoost tillhandahålls som en kommando rad och som ett R-bibliotek.

Om du vill använda XGBoost-biblioteket i R startar du en interaktiv R-session (i gränssnittet, anger **R**) och läser sedan in biblioteket.

Här är ett enkelt exempel som du kan köra i R-prompten:

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

Kör följande kommandon i gränssnittet för att köra XGBoost-kommando raden:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

En modell fil skrivs till den angivna katalogen. Information om det här demonstrations exemplet på GitHub finns i [binära klassificeringar](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Mer information om XGBoost finns i XGBoost- [dokumentationen](https://xgboost.readthedocs.org/en/latest/) och [XGBoost GitHub-lagringsplatsen](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Spännen

Rattle (*R* *A*nalysdatamängd *t*OOL *t*o *L*Erhåll *E*-asily) använder GUI-baserad data utforskning och modellering. Rattle:
- Visar statistiska och visuella sammanfattningar av data.
- Transformerar data som kan modelleras.
- Skapar både inövervakade och övervakade modeller från data.
- Visar prestanda för modeller grafiskt.
- Resultat av nya data uppsättningar.
- Genererar R-kod.
- Replikerar åtgärder i användar gränssnittet som kan köras direkt i R eller används som utgångs punkt för mer analys.

Om du vill köra Rattle måste du vara inloggad på en grafiskt fjärrskrivbordssession. I en Terminal anger du **r** för att öppna r-miljön. Ange följande kommandon i R-Kommandotolken:

```R
library(rattle)
rattle()
```

Ett grafiskt gränssnitt som har en uppsättning flikar öppnas. Använd följande snabb starts steg i Rattle för att använda en exempel data uppsättning för väder och skapa en modell. I vissa av stegen uppmanas du att automatiskt installera och läsa in vissa nödvändiga R-paket som inte redan finns i systemet.

> [!NOTE]
> Om du inte har behörighet att installera paketet i system katalogen (standard) kan du se en prompt i R-konsolens fönster för att installera paket i ditt personliga bibliotek. Om du ser dessa prompter anger du **y**.

1. Välj **Kör**.
1. En dialog ruta där du blir ombedd att läsa in exempel data uppsättningen för väder. Välj **Ja** om du vill läsa in exemplet.
1. Välj fliken **modell** .
1. Välj **Kör** för att bygga ett besluts träd.
1. Välj **rit** för att Visa besluts trädet.
1. Välj alternativet **skog** och välj sedan **Kör** för att bygga en slumpmässig skog.
1. Välj fliken **utvärdera** .
1. Välj alternativet **risk** och välj sedan **Kör** för att visa två **riskhanterings diagram (kumulativa)** .
1. Välj fliken **logg** för att visa den genererade R-koden för föregående åtgärder. (På grund av ett fel i den aktuella versionen av Rattle måste du infoga ett **#** tecken framför **Exportera loggen** i texten i loggen.)
1. Välj knappen **Exportera** för att spara R-skript filen med namnet *weather_script. R* till arbetsmappen.

Du kan avsluta Rattle och R. Nu kan du ändra det genererade R-skriptet. Du kan också använda skriptet som det är och köra det när som helst för att upprepa allt som utfördes i Rattle-ANVÄNDARGRÄNSSNITTET. I synnerhet för nybörjare i R är detta ett sätt att snabbt utföra analyser och maskin inlärning i ett enkelt grafiskt gränssnitt, samtidigt som kod skapas automatiskt i R för att ändra eller för inlärning.
