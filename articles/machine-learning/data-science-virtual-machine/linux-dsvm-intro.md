---
title: Etablera en Linux CentOS datavetenskap virtuell dator i Azure | Microsoft Docs
description: Konfigurera och skapa en Linux datavetenskap virtuell dator på Azure för att utföra analyser och maskininlärning.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: bradsev
ms.openlocfilehash: 7f683fff142a3654249560ed5299ed3cd7cb9cce
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-on-azure"></a>Etablera en Linux CentOS datavetenskap virtuell dator på Azure

Linux datavetenskap virtuella datorn är en CentOS-baserad Azure virtuell dator som ingår i en samling förinstallerade verktyg. Verktygen används ofta för att göra dataanalys och maskininlärning. Viktiga programkomponenterna ingår är:

* Operativsystem: CentOS Linux-distribution.
* Microsoft R Server Developer Edition
* Anaconda Python-distribution (version 2.7 och 3.5), inklusive populära data analysbibliotek
* JuliaPro - en granskad fördelning av Julia språk med populära vetenskapliga och data analytics-bibliotek
* Standalone Spark-instans och nod Hadoop (HDFS, Yarn)
* JupyterHub - en fleranvändar Jupyter-anteckningsbok server som stöder R, Python, PySpark, Julia kärnor
* Azure Lagringsutforskaren
* Azure-kommandoradsgränssnittet (CLI) för att hantera Azure-resurser
* PostgresSQL Database
* Machine learning-verktyg
  * [Kognitiva Toolkit](https://github.com/Microsoft/CNTK): en djup learning toolkit för programvara från Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): snabb machine learning-system som har stöd för tekniker som online, hash, allreduce, minskning, learning2search, aktiv, och interaktiva learning.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): ett verktyg som ger snabb och exakt ökat trädet implementering.
  * [Rattle](http://rattle.togaware.com/) (den R analytiska verktyget att lära dig enkelt): ett verktyg som gör att komma igång med dataanalys och machine learning i R enkelt med GUI-baserade datagranskning och modellering med automatisk kodgenerering för R.
* Azure SDK i Java, Python, node.js, Ruby, PHP
* Bibliotek i R och Python för använder i Azure Machine Learning och andra Azure-tjänster
* Utvecklingsverktyg och redigerare (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi)


Gör datavetenskap omfattar iteration av en serie uppgifter:

1. Hitta, läsa in och förbearbetning av data
2. Skapa och testa modeller
3. Distribuera modeller för användning i intelligent program

Datavetare utföra dessa uppgifter med hjälp av olika verktyg. Det kan ta mycket lång tid att hitta rätt versioner av programvaran och sedan ladda ned, kompilera och installera dessa versioner.

Det här problemet kan underlättar avsevärt av Linux datavetenskap virtuella datorn. Använd den för att projektet analytics. På så sätt kan du arbeta med aktiviteter på olika språk, inklusive R, Python, SQL, Java och C++. Eclipse innehåller en IDE för att utveckla och testa din kod som är lätt att använda. Azure SDK om du ingår i den virtuella datorn kan du skapa program med hjälp av olika tjänster på Linux för Microsoft cloud-plattformen. Dessutom har du åtkomst till andra språk som Ruby, Perl, PHP och node.js som också har förinstallerat.

Det finns inga avgifter för programvara för den här datavetenskap VM-avbildning. Du betalar endast Azure maskinvara användning avgifter som utvärderas baserat på storleken på den virtuella datorn som du etablerar med VM-avbildning. Mer information om beräkning avgifter kan hittas på den [VM lista sida på Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Andra versioner av datavetenskap virtuell dator
En [Ubuntu](dsvm-ubuntu-intro.md) bilden är också tillgängliga med många av samma verktyg som CentOS avbildningen plus djup learning ramverk. En [Windows](provision-vm.md) bilden är också tillgänglig.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa en Linux datavetenskap virtuell dator, måste du ha följande:

* **En Azure-prenumeration**: Om du vill skaffa en finns [hämta kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/).
* **Ett Azure storage-konto**: för att skapa en Se [skapa ett Azure storage-konto](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Alternativt, om du inte vill använda ett befintligt konto lagringskontot kan skapas som en del av processen att skapa den virtuella datorn.

## <a name="create-your-linux-data-science-virtual-machine"></a>Skapa din Linux Data vetenskap virtuell dator
Här följer stegen för att skapa en instans av Linux datavetenskap Virtual Machine:

1. Navigera till den virtuella datorn med på den [Azure-portalen](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2. Klicka på **skapa** (längst ned) så att guiden.![ Konfigurera-data-vetenskap-vm](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3. Följande avsnitt innehåller indata för varje steg i guiden (räkna upp till höger i föregående bild) som används för att skapa Microsoft datavetenskap Virtual Machine. Här följer de indata som behövs för att konfigurera var och en av de här stegen:
   
   a. **Grunderna**:
   
   * **Namnet**: namnet på din server för vetenskap av data som du skapar.
   * **Användarnamnet**: första kontot inloggning ID.
   * **Lösenordet**: första kontolösenord (du kan använda offentliga SSH-nyckeln i stället för lösenord).
   * **Prenumerationen**: Om du har mer än en prenumeration väljer du en som datorn ska skapas och debiteras. Du måste ha behörighet för resursen skapas för den här prenumerationen.
   * **Resursgruppen**: du kan skapa en ny eller Använd en befintlig grupp.
   * **Plats**: Välj datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data, eller så ligger närmast din fysiska platsen för snabbaste nätverksåtkomst.
   
   b. **Storlek**:
   
   * Välj en av de servertyper som uppfyller dina krav på funktionsnivå och kostnaden begränsningar. Välj **visa alla** att se fler alternativ för VM-storlekar.
   
   c. **Inställningar för**:
   
   * **Disktyp**: Välj **Premium** om du föredrar en SSD-enhet (SSD). Annars väljer du **Standard**.
   * **Lagringskontot**: du kan skapa ett nytt Azure storage-konto i din prenumeration eller använda en befintlig på samma plats som har valts på den **grunderna** steg i guiden.
   * **Andra parametrar**: I de flesta fall kan du bara använda standardvärdena. Hovra över informationsmeddelande länken Hjälp om specifika fält för att överväga icke-standardvärden.
   
   d. **Sammanfattning**:
   
   * Kontrollera att all information du angett är korrekt.
   
   e. **Köpa**:
   
   * Starta etableringen, klicka på **köpa**. En länk som villkoren i transaktionen. Den virtuella datorn inte har några ytterligare kostnader utöver beräkning för server-storlek som du har valt i den **storlek** steg.

Etableringen bör ta ungefär 10 20 minuter. Status för etablering av visas på Azure-portalen.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Hur du kommer åt den virtuella Linux datavetenskap
När den virtuella datorn har skapats kan logga du in till den med hjälp av SSH. Använd de kontoautentiseringsuppgifter som du skapade i den **grunderna** avsnitt i steg 3 för gränssnittet text shell. På Windows, kan du ladda ned en SSH-klientverktyg som [Putty](http://www.putty.org). Du kan använda X11 vidarebefordran på Putty eller installera X2Go klienten om du föredrar en grafisk skrivbord (X Windows System).

> [!NOTE]
> Klienten X2Go utföra betydligt bättre än X11 vidarebefordran i testning. Vi rekommenderar att du använder X2Go-klienten för ett grafiskt gränssnitt för fjärrskrivbord.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Installera och konfigurera X2Go klienten
Linux VM är redan etablerade med X2Go server och redo att acceptera anslutningar. Gör följande på klienten för att ansluta till Linux VM grafiska skrivbordet:

1. Hämta och installera klienten för din klientplattform från X2Go [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Kör klienten X2Go och välj **ny Session**. Öppnar ett fönster för konfiguration med flera flikar. Ange följande konfigurationsparametrar:
   * **Sessionen fliken**:
     * **Värden**: värdnamn eller IP-adressen för din Linux datavetenskap VM.
     * **Inloggningen**: användarnamn på Linux-VM.
     * **SSH-Port**: lämna på 22, standardvärdet.
     * **Sessionstyp**: ändra värdet till XFCE. Linux VM stöder för närvarande endast XFCE skrivbordet.
   * **Fliken Media**: Om du inte behöver använda stöd för ljud och klienten utskrift, du kan inaktivera dem.
   * **Delade mappar**: Om du vill kataloger från dina klientdatorer monteras på Linux VM, lägger du till klienten datorn kataloger som du vill dela med den virtuella datorn på den här fliken.

När du loggar in på den virtuella datorn med hjälp av SSH-klienten eller XFCE grafiska via X2Go klienten, är du redo att börja använda verktygen som installeras och konfigureras på den virtuella datorn. På XFCE, kan du se program menyn genvägar och ikoner på skrivbordet för många av verktyg.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Verktygen som installeras på den Linux datavetenskap virtuella datorn
### <a name="microsoft-r-server"></a>Microsoft R Server
R är en av de vanligaste språken för dataanalys och maskininlärning. Om du vill använda R för din analys av har den virtuella datorn Microsoft R Server (FRU) med Microsoft R öppna (MRO) och matematiska Kernel-biblioteket (MKL). MKL optimerar matematiska operations vanligt i analytiska algoritmer. MRO är 100 procent kompatibel med R CRAN och någon av R-bibliotek som publicerats i CRAN kan installeras på MRO. FRU ger dig skalning och operationalization R modeller till webbtjänster. Du kan redigera din R-program på en standard-redigerare, t.ex. RStudio, vi, Emacs eller gedit. Om du använder redigeraren Emacs har arbeta med R-filer i redigeraren Emacs Observera att Emacs paketet EDDELANDEN (Emacs talar statistik), vilket förenklar förinstallerat.

Att starta R konsolen kan du börja skriva **R** i gränssnittet. Då kommer du till en interaktiv miljö. Du använder vanligtvis en redigerare som Emacs eller vi eller gedit för att utveckla ditt R-program och sedan köra skript i R. Med RStudio har du en fullständigt grafiskt IDE-miljö för att utveckla ditt R-program.

Det finns också ett R-skript att installera den [upp 20 R-paket](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) om du vill. Det här skriptet kan köras när du är i R interaktiva gränssnittet, som kan anges (som tidigare nämnts) genom att skriva **R** i gränssnittet.  

### <a name="python"></a>Python
För utveckling med hjälp av Python, har distribution av Anaconda Python 2.7 och 3.5 installerats. Den här distributionen innehåller grundläggande Python tillsammans med ungefär 300 populäraste matematiska, ingenjörer och data analytics paket. Du kan använda standard textredigerare. Du kan dessutom använda Spyder, en Python IDE som medföljer Anaconda Python-distributioner. Spyder måste ha ett grafiskt skrivbord eller X11 vidarebefordran. En genväg till Spyder tillhandahålls i grafiska skrivbordet.

Eftersom vi har både Python 2.7 och 3.5, måste du uttryckligen aktivera önskade Python versionen (conda environment) du vill arbeta med i den aktuella sessionen. Aktiveringen anger sökvägsvariabeln till den önskade versionen av Python.

Kör följande kommando från gränssnittet för att aktivera Python 2.7 conda miljö:

    source /anaconda/bin/activate root

Python 2.7 är installerad på */anaconda/bin*.

Kör följande från gränssnittet för att aktivera Python 3.5 conda miljö:

    source /anaconda/bin/activate py35


Python 3.5 är installerad på */anaconda/envs/py35/bin*.

Om du vill anropa en interaktiv session Python, skriver du **python** i gränssnittet. Du kan ange om du är på ett grafiskt gränssnitt eller har X11 vidarebefordran set in **pycharm** att starta PyCharm Python IDE.

Om du vill installera ytterligare Python-bibliotek, måste du köra ```conda``` eller ````pip```` kommandot under sudo och ange sökvägen till Python package manager (conda eller pip) för att installera rätt Python-miljön. Exempel:

    sudo /anaconda/bin/pip install <package> #pip for Python 2.7
    sudo /anaconda/envs/py35/bin/pip install <package> #pip for Python 3.5
    sudo /anaconda/bin/conda install [-n py27] <package> #conda for Python 2.7, default behavior
    sudo /anaconda/bin/conda install -n py35 <package> #conda for Python 3.5


### <a name="jupyter-notebook"></a>Jupyter-anteckningsbok
Anaconda distribution innehåller också en Jupyter-anteckningsbok en miljö att dela kod och analys. Jupyter-anteckningsbok sker via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn och lösenord.

Jupyter-anteckningsbok servern har konfigurerats med Python 2, 3 Python och R kärnor före. Det finns en skrivbordsikon med namnet ”Jupyter Notebook” för att starta webbläsaren för att komma åt servern för bärbar dator. Om du är på den virtuella datorn via SSH eller X2Go klient kan du kan också besöka [ https://localhost:8000/ ](https://localhost:8000/) Jupyter notebook-servern.

> [!NOTE]
> Fortsätt om du får några certifikatvarningar.
> 
> 

Du kan komma åt Jupyter-anteckningsbok server från valfri värddator. Skriv *https://\<VM DNS-namn eller IP-adressen\>: 8000 /*

> [!NOTE]
> Port 8000 öppnas i brandväggen som standard när den virtuella datorn har etablerats.
> 
> 

Vi har paketerat exempel anteckningsböcker--en i Python och en i R. Du kan se länken prov på startsidan för bärbar dator när du autentiserar dig till Jupyter-anteckningsbok med hjälp av din lokala Linux-användarnamn och lösenord. Du kan skapa en ny anteckningsbok genom att välja **ny**, och sedan språket kernel. Om du inte ser den **ny** , klicka på den **Jupyter** ikon på upp till vänster att gå till startsidan för anteckningsboken-servern.

### <a name="apache-spark-standalone"></a>Apache Spark Standalone 
En fristående instans av Apache Spark är förinstallerat på Linux DSVM som hjälper dig att utveckla Spark-program lokalt först innan du testar och distribuerar stora kluster. Du kan köra PySpark program via Jupyter-kernel. När du öppnar Jupyter och klicka på den **ny** knappen, bör du se en lista över tillgängliga kärnor. ”Spark – Python” är PySpark-kerneln som låter dig skapa Spark program som använder Python språk. Du kan också använda en Python IDE som PyCharm eller Spyder för att skapa du Spark-program. Eftersom detta är en fristående instans, Spark-stacken körs inom det anropande klientprogrammet. Detta gör det snabbare och enklare att felsöka problem jämfört med att utveckla på ett Spark-kluster. 

En bärbar dator PySpark exempel finns på Jupyter som du hittar i katalogen ”SparkML” under arbetskatalogen för Jupyter ($HOME/anteckningsböcker/SparkML/pySpark). 

Om programmering i R Spark ska använda du Microsoft R Server, SparkR eller sparklyr. 

Innan du kör i Spark-kontexten i Microsoft R Server, måste du göra en engångsinställning som installationsprogrammet steg för att aktivera en lokal nod Hadoop HDFS och Yarn-instans. Som standard installeras men inaktiveras på DSVM Hadoop-tjänster. För att aktivera den, måste du köra följande kommandon som rot för första gången:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Du kan stoppa Hadoop-relaterade tjänster när du inte behöver dem genom att köra ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` ett exempel som visar hur du utvecklar och testar FRU i fjärranslutna Spark-kontexten (som är fristående Spark-instans på DSVM) har angetts och är tillgängliga i den `/dsvm/samples/MRS` katalog. 

### <a name="ides-and-editors"></a>IDEs och redigerare
Du kan välja mellan flera kod redigerare. Detta inkluderar vi/VIM Emacs, gEdit, PyCharm, RStudio, Eclipse och IntelliJ. gEdit Eclipse, IntelliJ, RStudio och PyCharm är grafiska redigerare och måste du vara inloggad på ett grafiskt skrivbord ska användas. Dessa redigerare har skrivbord och menyn genvägar till köra dem igen.

**VIM** och **Emacs** är textbaserade redigerare. Vi har installerat ett tillägg paket kallas Emacs talar statistik (EDDELANDEN) som underlättar arbetet med R Emacs Editor Emacs. Mer information finns på [EDDELANDEN](http://ess.r-project.org/).

**Eclipse** är en öppen källa extensible IDE som har stöd för flera språk. Java-utvecklare edition är installerat på den virtuella datorn instansen. Plugin-program är tillgängligt för flera populära språk som kan installeras för att utöka miljön. Vi har också ett plugin-program som installerats i Eclipse kallas **Azure Toolkit för Eclipse**. Det kan du skapa, utveckla, testa och distribuera Azure med Eclipse-utvecklingsmiljön som har stöd för språk som Java-program. Det finns också en **Azure SDK för Java** som ger åtkomst till olika Azure-tjänster från en Java-miljö. Mer information om Azure toolkit för Eclipse kan hittas på [Azure Toolkit för Eclipse](../../azure-toolkit-for-eclipse.md).

**LaTex** installeras via texlive paketet tillsammans med tillägget Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) paket, vilket förenklar redigering LaTex dokument inom Emacs.  

### <a name="databases"></a>Databaser
#### <a name="postgres"></a>Postgres
Öppna källdatabasen **Postgres** är tillgängligt på den virtuella datorn med de tjänster som körs och initdb redan slutförts. Du måste skapa databaser och användare. Mer information finns i [Postgres dokumentationen](https://www.postgresql.org/docs/).  

#### <a name="graphical-sql-client"></a>Grafisk SQL-klient
**SQuirrel SQL**, en grafisk SQL-klient har angetts för att ansluta till olika databaser (till exempel Microsoft SQL Server, Postgres och MySQL) och för att köra SQL-frågor. Du kan du köra från en grafisk session (med X2Go klienten, till exempel). För att anropa SQuirrel SQL kan du starta från ikonen på skrivbordet eller kör följande kommando på gränssnittet.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Ställ in drivrutiner och databasen alias innan den första användningen. JDBC-drivrutiner finns på:

*/usr/share/java/jdbcdrivers*

Mer information finns i [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Kommandoradsverktyg för att komma åt Microsoft SQL Server
Paketets ODBC-drivrutinen för SQL Server levereras med två verktyg för kommandoraden:

**BCP**: bcp-verktyget bulk kopierar data mellan en instans av Microsoft SQL Server och en fil i ett format som angetts av användaren. Bcp-verktyget för att importera många nya rader till SQL Server-tabeller eller exportera data från tabeller till datafiler. Om du vill importera data till en tabell, måste du använda en fil för den tabellen, eller förstå tabellen och vilka typer av data som är giltiga för dess kolumner.

Mer information finns i [ansluter med bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SQLCMD**: du kan ange Transact-SQL-uttryck med sqlcmd-verktyget, samt system procedurer och skriptfiler i Kommandotolken. Det här verktyget använder ODBC för att köra Transact-SQL-satser.

Mer information finns i [ansluter med sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Det finns vissa skillnader i det här verktyget mellan Linux och Windows-plattformar. Finns i dokumentationen för mer information.
> 
> 

#### <a name="database-access-libraries"></a>Bibliotek för åtkomst av databasen
Bibliotek är tillgängligt i R och Python till access-databaser.

* I R, den **RODBC** paketet eller **dplyr** paketet kan du fråga eller köra SQL-uttryck på databasservern.
* I Python, den **pyodbc** biblioteket ger åtkomst till databasen med ODBC som underliggande lager.  

Åtkomst **Postgres**:

* Använd paketet från R: **RPostgreSQL**.
* Använd från Python: Den **psycopg2** bibliotek.

### <a name="azure-tools"></a>Azure-verktyg
Följande Azure tools är installerat på den virtuella datorn:

* **Azure-kommandoradsgränssnittet**: I Azure CLI kan du skapa och hantera Azure-resurser via shell-kommandon. Om du vill anropa Azure-verktyg, skriver du **azure hjälp**. Mer information finns i [dokumentationssidan för Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Microsoft Azure Lagringsutforskaren**: Microsoft Azure Lagringsutforskaren är ett grafiskt verktyg som används för att bläddra igenom objekten som du har lagrat i Azure storage-konto och för att ladda upp och hämta data till och från Azure BLOB. Du kan öppna Lagringsutforskaren från ikonen genväg på skrivbordet. Du kan anropa den från Kommandotolken i gränssnittet genom att skriva **StorageExplorer**. Du måste vara inloggad från en klient X2Go eller har X11 vidarebefordran set upp.
* **Azure-bibliotek**: följande är några av de förinstallerade biblioteken.
  
  * **Python**: Azure-relaterade bibliotek i Python som installeras är **azure**, **azureml**, **pydocumentdb**, och **pyodbc**. Du kan komma åt Azure storage-tjänster, Azure Machine Learning och Azure Cosmos DB (en NoSQL-databas på Azure) med de tre första biblioteken. Fjärde biblioteket pyodbc (tillsammans med Microsoft ODBC-drivrutin för SQL Server), ger åtkomst till SQL Server, Azure SQL Database och Azure SQL Data Warehouse från Python med hjälp av en ODBC-gränssnittet. Ange **pip listan** att se alla bibliotek. Se till att köra detta kommando i 3.5 miljöer och Python 2.7.
  * **R**: Azure-relaterade bibliotek i R som installeras är **AzureML** och **RODBC**.
  * **Java**: listan över Azure Java-bibliotek finns i katalogen **/dsvm/sdk/AzureSDKJava** på den virtuella datorn. Nyckel-biblioteken har Azure lagring och hantering av API: er och Azure Cosmos DB JDBC-drivrutiner för SQL Server.  

Du kan komma åt den [Azure-portalen](https://portal.azure.com) från förinstallerade Firefox webbläsaren. På Azure-portalen kan du skapa, hantera och övervaka Azure-resurser.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning är en helt hanterad molntjänst som gör att du kan skapa, distribuera och dela förutsägelseanalyslösningar. Du kan skapa dina experiment och modeller från Azure Machine Learning Studio. Den kan nås från en webbläsare på den virtuella datorn för vetenskap av data genom att besöka [Microsoft Azure Machine Learning](https://studio.azureml.net).

När du loggar in på Azure Machine Learning Studio har du åtkomst till en experiment arbetsyta där du kan skapa en logisk flöde för de maskininlärningsalgoritmer. Du har åtkomst till en Jupyter-anteckningsbok som finns på Azure Machine Learning och fungerar sömlöst med experiment i Machine Learning Studio. Operationalisera maskininlärning modeller som du har skapat genom att omsluta dem i ett webbgränssnitt för tjänsten. Detta gör att klienter på alla språk att anropa förutsägelser från maskininlärning modeller. Mer information finns i [Machine Learning dokumentationen](https://azure.microsoft.com/documentation/services/machine-learning/).

Du kan också skapa modeller i R eller Python på den virtuella datorn och distribuerar den i produktion i Azure Machine Learning. Vi har installerat bibliotek i R (**AzureML**) och Python (**azureml**) att aktivera den här funktionen.

Information om hur du distribuerar modeller i R och Python i Azure Machine Learning finns [tio saker du kan göra på datavetenskap virtuella](vm-do-ten-things.md) (särskilt avsnittet ”Skapa modeller med R eller Python och använda dem med hjälp av Azure Machine Learning ”).

> [!NOTE]
> Dessa instruktioner skrevs för Windows-versionen av den virtuella datorn vetenskapliga Data. Men den informationen om att distribuera modeller till Azure Machine Learning finns gäller för Linux-VM.
> 
> 

### <a name="machine-learning-tools"></a>Machine learning-verktyg
Den virtuella datorn innehåller några maskininlärning verktyg och algoritmer som före kompileras och förinstallerat lokalt. Exempel på dessa är:

* **Microsoft kognitiva Toolkit** : en djup learning toolkit.
* **Vowpal Wabbit**: en snabb online inlärningsalgoritm.
* **xgboost**: ett verktyg som ger en optimerad, ökat trädet algoritmer.
* **Python**: Anaconda Python medföljer maskininlärningsalgoritmer med bibliotek som lär du dig Scikit. Du kan installera andra bibliotek med hjälp av den `pip install` kommando.
* **R**: ett omfattande Meddelandebibliotek machine learning-funktioner är tillgänglig för R. Vissa av de bibliotek som är förinstallerade är lm, glm, randomForest, rpart. Du kan installera andra bibliotek genom att köra:
  
        install.packages(<lib name>)

Här är ytterligare information om de första tre maskininlärning verktyg i listan.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
Det här är en öppen källkod djup learning toolkit. Det är ett kommandoradsverktyg (cntk) och finns redan i SÖKVÄGEN.

Kör följande kommandon i gränssnittet för att köra ett grundläggande exempel:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Mer information finns i avsnittet CNTK i [GitHub](https://github.com/Microsoft/CNTK), och [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit är machine learning-system som använder tekniker, till exempel online, hash, allreduce, minskning, learning2search, aktiv, och interaktiva learning.

Om du vill köra verktyget på en grundläggande exempel, gör du följande:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Det finns andra, större demonstrationer i katalogen. Mer information om VW finns [det här avsnittet i GitHub](https://github.com/JohnLangford/vowpal_wabbit), och [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Det här är ett bibliotek som har utformats och optimerats för ökat (trädet) algoritmer. Syftet med det här biblioteket är att push-beräkning gränserna för datorer bra som behövs för att tillhandahålla storskaliga trädet förstärkning som är skalbart, bärbar och korrekt.

Det har angetts som en kommandorad som ett R-bibliotek.

Om du vill använda det här biblioteket i R kan du starta en interaktiv R-session (genom att skriva **R** i gränssnittet), och läsa in biblioteket.

Här är ett enkelt exempel som du kan köra R-fråga:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Här är kommandona som ska köras i gränssnittet för att köra kommandoraden xgboost:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


En .model-fil skrivs till den angivna katalogen. Information om det här exemplet demo finns [på GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Mer information om xgboost finns i [xgboost dokumentationssidan](https://xgboost.readthedocs.org/en/latest/), och dess [GitHub-lagringsplatsen](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Spännen
Rattle (den **R** **A**nalytical **T**yg **T**o **L**tjäna **E** GUI-baserade datagranskning och modellering används asily). Den visar statistisk och visual sammanfattningar av data, transformeringar data som lätt kan modelleras, skapar både oövervakade och övervakat modeller från data, visar prestanda för modeller grafiskt, och anger poäng nya data. Den genererar också R-kod, replikerar åtgärder i Användargränssnittet som kan köras direkt i R eller användas som utgångspunkt för ytterligare analys.

Om du vill köra spännen måste vara i en grafisk inloggning skrivbordssession. Skriv på terminalen och ```R``` kommer R-miljön. Ange följande kommandon i Kommandotolken R:

    library(rattle)
    rattle()

Nu ett grafiskt gränssnitt som öppnas med en uppsättning flikar. Här följer stegen för Snabbstart i spännen som behövs för att använda exempeldata väder data och skapa en modell. I vissa av stegen nedan uppmanas du att installera automatiskt och läsa in vissa nödvändiga R-paket som inte redan finns på systemet.

> [!NOTE]
> Om du inte har behörighet att installera paketet i systemkatalogen (standard), kan du se en kommandotolk på din R-konsolfönster för att installera paket till ditt personliga bibliotek. Svaret *y* om du ser dessa frågor.
> 
> 

1. Klicka på **Kör**.
2. En dialogruta visas, frågar om du vill använda exempelinformationen väder. Klicka på **Ja** att läsa in exemplet.
3. Klicka på den **modellen** fliken.
4. Klicka på **Execute** att skapa en beslutsträdet.
5. Klicka på **Rita** att visa beslutsträdet.
6. Klicka på den **skog** alternativknappen och klickar på **Execute** att bygga en slumpmässig skog.
7. Klicka på den **utvärdera** fliken.
8. Klicka på den **Risk** alternativknappen och klickar på **Execute** ska visas två Risk (kumulativ) prestanda områden.
9. Klicka på den **loggen** att visa generera R-koden för föregående operationer.
   (På grund av ett fel i den aktuella versionen av spännen, måste du infoga ett *#* tecknet framför *exportera den här loggen...*  texten i loggen.)
10. Klicka på den **exportera** för att spara filen R-skript med namnet *weather_script. R* till arbetsmappen.

Du kan avsluta spännen och R. Nu kan du ändra genererade R-skriptet, eller använda den som den är att köra den när som helst om du vill upprepa allt som gjordes i Gränssnittet Rattle. Detta är ett enkelt sätt att snabbt göra analyser och maskininlärning i ett enkelt grafiskt gränssnitt vid automatisk generering av kod i R för att ändra och/eller Läs särskilt för nybörjare i R.

## <a name="next-steps"></a>Nästa steg
Här visas hur du kan fortsätta att din inlärning och undersökning:

* Den [datavetenskap på den Linux datavetenskap virtuella](linux-dsvm-walkthrough.md) genomgången visar hur du utför flera gemensamma datavetenskap uppgifter med Linux datavetenskap VM etablerats här. 
* Utforska datavetenskap verktyg på datavetenskap VM genom att försöka med de verktyg som beskrivs i den här artikeln. Du kan också köra *dsvm mer information* på gränssnittet på den virtuella datorn för en grundläggande introduktion och länkar till mer information om verktygen som installeras på den virtuella datorn.  
* Lär dig hur du skapar slutpunkt till slutpunkt Analyslösningar systematiskt med den [Team datavetenskap Process](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Besök den [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) för machine learning och analytics exempel som använder Cortana Analytics Suite.

