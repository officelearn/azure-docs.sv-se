---
title: Skapa en Ubuntu Linux virtuell dator för datavetenskap
titleSuffix: Azure
description: Konfigurera och skapa en Data Science Virtual Machine för Linux (Ubuntu) på Azure för att göra analyser och maskininlärning.
services: machine-learning
documentationcenter: ''
author: gopitk
ms.author: gokuma
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: bbd73035993d3a981744eb5377fe0bf2c9d55b63
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268863"
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Etablera den virtuella datorn för datavetenskap för Linux (Ubuntu)

Den virtuella datorn för datavetenskap för Linux är en Ubuntu-baserad VM-avbildning som gör det enkelt att komma igång med machine learning, inklusive djupinlärning på Azure. Deep learning-verktyg är:

  * [Caffe](http://caffe.berkeleyvision.org/): Ett ramverk för djupinlärning som skapats för hastighet, expressivity och modularitet
  * [Caffe2](https://github.com/caffe2/caffe2): En plattformsoberoende version av Caffe
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK): Verktyget för programvara från Microsoft Research för djupinlärning
  * [H2O](https://www.h2o.ai/): En öppen källkod stordataplattform och grafiskt användargränssnitt
  * [Keras](https://keras.io/): Ett övergripande neural network API i Python för TensorFlow, Microsoft Cognitive Toolkit och Theano
  * [MXNet](http://mxnet.io/): En flexibla, effektiva deep learning-biblioteket med många språkbindningar
  * [NVIDIA DIGITS](https://developer.nvidia.com/digits): Ett grafiskt system som förenklar vanliga uppgifter för djupinlärning
  * [PyTorch](http://pytorch.org/): Ett övergripande Python-bibliotek med stöd för dynamisk nätverk
  * [TensorFlow](https://www.tensorflow.org/): Ett bibliotek för öppen källkod för maskinintelligens från Google
  * [Theano](http://deeplearning.net/software/theano/): Ett Python-bibliotek för att definiera, optimera och effektivt utvärderar matematiska uttryck som involverar flerdimensionella matriser
  * [Torch](http://torch.ch/): Ett vetenskapliga databehandling ramverk med brett stöd för machine learning-algoritmer
  * CUDA-, cuDNN- och NVIDIA-drivrutin
  * Många exempel Jupyter-anteckningsböcker

Alla bibliotek är GPU-versioner, även om de också körs på Processorn.

Den virtuella datorn för datavetenskap för Linux innehåller även populära verktyg för datavetenskap och utveckling, inklusive:

* Microsoft R Server Developer Edition med Microsoft R Open
* Anaconda Python-distribution (version 2.7 och 3.5), inklusive populära analysbibliotek
* JuliaPro – en granskad fördelning av Julia-språket med populära vetenskapliga och data analytics-bibliotek
* Standalone Spark-instans och nod Hadoop (HDFS, Yarn)
* JupyterHub – en flera användare Jupyter notebook server stöd för R, Python, PySpark, Julia-kärnor
* Azure Lagringsutforskaren
* Azure-kommandoradsgränssnittet (CLI) för att hantera Azure-resurser
* Machine learning-verktyg
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Ett system för snabb maskininlärning som stöd för tekniker som online hashing allreduce, sänkt, learning2search, aktiv, och interaktiv utbildning
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Ett verktyg för att tillhandahålla snabba och exakta bättre trädet-implementering
  * [Rattle](https://togaware.com/rattle/): Ett grafiskt verktyg som gör att komma igång med dataanalys och maskininlärning i R enkelt
  * [LightGBM](https://github.com/Microsoft/LightGBM): En snabb, distribuerad, högpresterande gradient boosting framework
* Azure SDK i Java, Python, node.js, Ruby, PHP
* Bibliotek i R och Python för använder i Azure Machine Learning och andra Azure-tjänster
* Utvecklingsverktyg och -redigerare (RStudio, PyCharm, IntelliJ, Emacs, vim)


Datavetenskap omfattar iteration av en serie uppgifter:

1. Sökning, läser in och bearbeta data data i förväg
1. Skapa och testa modeller
1. Distribuera modeller för användning i intelligenta program

Dataexperter använda olika verktyg för att utföra dessa uppgifter. Det kan ta ganska lång tid att hitta rätt versioner av programvaran och sedan för att hämta, kompilera och installera de här versionerna.

Den virtuella datorn för datavetenskap för Linux kan avsevärt minska det här problemet. Du kan använda den för att ge ditt analysprojekt. Det kan du arbeta på uppgifter på olika språk, inklusive R, Python, SQL, Java och C++. Azure-SDK som ingår i den virtuella datorn kan du bygga dina program med hjälp av olika tjänster på Linux för Microsofts molnplattform. Dessutom kan har du åtkomst till andra språk som Ruby, Perl, PHP och node.js som också har förinstallerat.

Det finns inga programvaruavgifter för den här data science VM-avbildning. Du betalar endast de Azure-maskinvaran Användningsavgift som utvärderas baserat på storleken på den virtuella datorn som du etablerar. Mer information om beräkning avgifter kan hittas på den [VM lista sidan på Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Andra versioner av den virtuella datorn för datavetenskap
En [CentOS](linux-dsvm-intro.md) bilden är också tillgängliga med många av samma verktyg som Ubuntu-avbildning. En [Windows](provision-vm.md) bilden är också tillgänglig.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa en virtuell dator för datavetenskap för Linux, måste du ha en Azure-prenumeration. Om du vill skaffa en Se [skaffa Azure kostnadsfria utvärderingsversionen](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Skapa din virtuella dator för datavetenskap för Linux
Här följer stegen för att skapa en instans av den virtuella datorn för datavetenskap för Linux:

1. Navigera till den virtuella datorn på den [Azure-portalen](https://portal.azure.com/#create/microsoft-dsvm.linux-data-science-vm-ubuntulinuxdsvmubuntu). Du kan uppmanas att logga in på ditt Azure-konto om du redan inte är inloggad. 
1. Klicka på **skapa** (längst ned) visas i guiden.![ Konfigurera –--virtuell dator för datavetenskap](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
1. Följande avsnitt innehåller indata för varje steg i guiden (räknas upp till höger i föregående bild) används för att skapa Microsoft Data Science Virtual Machine. Här följer de indata som behövs för att konfigurera var och en av de här stegen:
   
   a. **Grunderna**:
   
   * **Namn**: Namnet på din data science-server som du skapar.
   * **Typ av virtuell Datordisk**: Välj **Premium SSD** om du föredrar en Solid State-hårddisk (SSD). Annars väljer du **Standard HDD**. 
   * **Användarnamn**: Första kontoinloggningen-ID.
   * **Lösenord**: Första kontolösenordet (du kan använda offentlig SSH-nyckel i stället för lösenord).
   * **Prenumeration**: Om du har mer än en prenumeration väljer du det som datorn är skapas och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
   * **Resursgrupp**: Du kan skapa en ny eller Använd en befintlig grupp.
   * **Plats**: Välj det datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data, eller så är närmast dina fysiska platsen för snabbaste nätverksåtkomst.
   
   b. **Storlek**:
   
   * Välj en av de servertyper som uppfyller dina funktionella krav och begränsningar för kostnad. Välj en NC eller Virtuella ND-klassen för GPU-baserad VM-instanser. Den [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/) sidan visar en lista över regioner med GPU: er.
   
   c. **Inställningar för**:
   
   * I de flesta fall kan du bara använda standardvärdena. Att tänka på icke-standardvärden, hovrar du över informationsmeddelande länken Hjälp om specifika fält.
   
   d. **Sammanfattning av**:
   
   * Kontrollera att all information du angett är korrekt. Det finns en länk till användningsvillkoren. Den virtuella datorn har inte några ytterligare avgifter utöver beräkning för serverstorlek som du valde i den **storlek** steg. Starta etableringen, klicka på **skapa**. 
   
Etableringen tar cirka 5 minuter. Status för etableringen visas på Azure portal.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Hur du kommer åt den virtuella datorn för datavetenskap för Linux

Du kan komma åt Ubuntu DSVM på tre sätt:
1. SSH för terminal sessioner
1. X2Go för grafiska sessioner
1. JupyterHub och JupyterLab för Jupyter-anteckningsböcker

### <a name="ssh"></a>SSH

När den virtuella datorn har skapats kan logga du in till den med hjälp av SSH. Använda de autentiseringsuppgifter som du skapade i den **grunderna** avsnittet i steg 3 för text shell-gränssnittet. I Windows kan du ladda ned ett SSH-klientverktyg som [Putty](http://www.putty.org). Du kan använda X11 vidarebefordran på Putty eller installera X2Go klienten om du föredrar en grafisk desktop (X Windows System).

> [!NOTE]
> Klienten X2Go presterade bättre än X11 vidarebefordran i testet. Vi rekommenderar att du använder X2Go-klienten för ett grafiskt gränssnitt för fjärrskrivbord.
> 
> 

### <a name="x2go"></a>X2Go
Linux VM är redan upprättad med X2Go server och redo att ta emot klientanslutningar. Slutför följande procedur på klienten för att ansluta till Linux VM grafiska skrivbordet:

1. Ladda ned och installera klienten för din klientplattform från X2Go [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
1. Kör X2Go-klienten och välj **ny Session**. Det öppnar en konfiguration med flera flikar. Ange följande konfigurationsparametrar:
   * **Sessionen fliken**:
     * **Värden**: Värdnamn eller IP-adressen för din Linux virtuell dator för datavetenskap.
     * **logga in**: Användarnamn på Linux-VM.
     * **SSH Port**: Lämna det på 22, standardvärdet.
     * **Sessionstyp**: Ändra värdet till XFCE. Linux VM stöder för närvarande endast XFCE desktop.
   * **Media-flik**: Du kan inaktivera stöd för ljud och skriva ut om du inte behöver använda dem-klienten.
   * **Delade mappar**: Om du vill kataloger från dina klientdatorer som är monterad på Linux-VM, lägger du till de klienten datorn kataloger som du vill dela med den virtuella datorn på den här fliken.

När du loggar in till den virtuella datorn med hjälp av SSH-klient eller XFCE grafiska desktop via X2Go klienten, är du redo att börja använda de verktyg som är installerade och konfigurerade på den virtuella datorn. På XFCE, kan du se genvägar på Program-menyn och ikoner på skrivbordet för många av verktygen.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub och JupyterLab

Ubuntu DSVM körs [JupyterHub](https://github.com/jupyterhub/jupyterhub), en Jupyter-server med flera användare. För att ansluta, bläddra till https://your-vm-ip:8000 ange användarnamnet och lösenordet som användes för att skapa den virtuella datorn och logga in på din bärbara dator eller desktop. Många exempelanteckningsböcker som är tillgängliga för dig att söka och prova att använda.

JupyterLab, är nästa generations Jupyter notebooks och JupyterHub, också tillgängligt. Logga in till JupyterHub för att komma åt det och sedan bläddra till URL: en https://your-vm-ip:8000/user/your-username/lab. Du kan ange JupyterLab som standard notebook-server genom att lägga till den här raden /etc/jupyterhub/jupyterhub_config.py:

    c.Spawner.default_url = '/lab'

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Verktygen som installeras på den virtuella datorn för datavetenskap för Linux
### <a name="deep-learning-libraries"></a>Deep Learning-bibliotek

#### <a name="cntk"></a>CNTK
Microsoft Cognitive Toolkit är en öppen källkod, deep learning toolkit. Python-bindningar är tillgängliga i Conda rot- och py35. Det har även ett kommandoradsverktyg (cntk) som redan finns i SÖKVÄGEN.

Python exempelanteckningsböcker är tillgängliga i JupyterHub. Om du vill köra ett grundläggande exempel på kommandoraden, kör du följande kommandon i gränssnittet:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Mer information finns i avsnittet CNTK i [GitHub](https://github.com/Microsoft/CNTK), och [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe
Caffe är ett ramverk för djupinlärning från Berkeley Vision och Learning Center. Det är tillgängligt i /opt/caffe. Exempel finns i /opt/caffe/examples.

#### <a name="caffe2"></a>Caffe2
Caffe2 är ett ramverk för djupinlärning från Facebook som bygger på Caffe. Det är tillgängliga i Python 2.7 i Conda rot-miljön. Aktivera den genom att köra följande från gränssnittet:

    source /anaconda/bin/activate root

Vissa exempel bärbara datorer är tillgängliga i JupyterHub.

#### <a name="h2o"></a>H2O
H2O är en snabb, i minnet och distribuerade maskininlärning och förutsägande analyser. En Python-paketet har installerats i rot- och py35 Anaconda-miljöer. Ett R-paket installeras även. Starta H2O från kommandoraden genom att köra `java -jar /dsvm/tools/h2o/current/h2o.jar`; det finns olika [kommandoradsalternativ](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) som du kan vilja konfigurera. Flow-Webbgränssnittet kan nås genom att bläddra till http://localhost:54321 att komma igång. Exempelanteckningsböcker är också tillgängliga i JupyterHub.

#### <a name="keras"></a>Keras
Keras är ett övergripande neurala nätverk API i Python som klarar köra överst TensorFlow, Microsoft Cognitive Toolkit eller Theano. Det är tillgängligt i rot- och py35 Python. 

#### <a name="mxnet"></a>MXNet
MXNet är ett ramverk för djupinlärning som utformats för både effektivitet och flexibilitet. Den har R och Python-bindningar på DSVM. Exempelanteckningsböcker som ingår i JupyterHub och exempelkod är tillgänglig i /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>NVIDIA SIFFROR
NVIDIA Deep Learning GPU utbildning systemet, kallas SIFFROR, är ett system för att förenkla vanliga deep learning-aktiviteter som att hantera data, designa och neurala nätverk på GPU-system och övervakning av prestanda i realtid med avancerade visualisering. 

SIFFROR är tillgänglig som en tjänst som kallas siffror. Starta tjänsten och bläddra till http://localhost:5000 att komma igång.

SIFFROR installeras även som en Python-modul i Conda rot-miljö.

#### <a name="tensorflow"></a>TensorFlow
TensorFlow är Googles deep learning-biblioteket. Det är en öppen källkod programbibliotek för numeriska beräkning med hjälp av dataflödesgrafik. Vissa exempelanteckningsböcker som ingår i JupyterHub TensorFlow är tillgängliga i py35 Python-miljön.

#### <a name="theano"></a>Theano
Theano är ett Python-bibliotek för effektiv numeriska beräkning. Det är tillgängligt i rot- och py35 Python. 

#### <a name="torch"></a>Torch
Torch är ett vetenskapliga databehandling ramverk med brett stöd för machine learning-algoritmer. Den är tillgänglig i /dsvm/tools/torch och th interaktiva sessionen och luarocks Pakethanteraren är tillgängliga på kommandoraden. Exempel finns i /dsvm/samples/torch.

PyTorch är också tillgängliga i roten Anaconda-miljön. Exempel är i /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Microsoft R Server
R är ett av de mest populära språk för dataanalys och maskininlärning. Om du vill använda R för analys, har den virtuella datorn Microsoft R Server (FRU) med Microsoft R Open (MRO) och matematiska Kernel-biblioteket (MKL). MKL optimerar matematiska åtgärder som är vanliga i analytiska algoritmer. MRO är 100 procent kompatibel med R CRAN och någon av R-bibliotek som publicerats i CRAN kan installeras på MRO. FRU ger dig skalning och driftsättning av R-modellerna till webbtjänster. Du kan redigera din R-program i ett standard-redigerare som RStudio, vi och Emacs. Om du föredrar att använda redigeraren Emacs, har det varit förinstallerade. Paketets Emacs ESS (Emacs talar statistik) förenklar arbeta med R-filer i Emacs redigeraren.

Starta R-konsolen, skriver du bara **R** i gränssnittet. Detta tar dig till en interaktiv miljö. Du använder vanligtvis en redigerare som Emacs eller vi hjälper dig för att utveckla ditt R-program, och sedan köra skript i R. Med RStudio har du ett fullständigt grafiskt IDE-miljö för att utveckla ditt R-program.

Det finns också ett R-skript att installera den [översta 20 R-paket](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) om du vill. Det här skriptet kan köras när du är i det interaktiva gränssnittet R, som du kan ange (som tidigare nämnts) genom att skriva **R** i gränssnittet.  

### <a name="python"></a>Python
Anaconda Python är installerat med Python 2.7 och 3,5 miljöer. 2.7 miljön kallas _rot_, och 3,5 miljön kallas _py35_. Den här distributionen innehåller grundläggande Python tillsammans med ungefär 300 av de mest populära analyspaket matematiska, teknik och data. 

Py35 miljön är standardinställningen. Aktivera rot (2.7)-miljö:

    source activate root

Aktivera py35 miljön igen:

    source activate py35

Om du vill anropa en interaktiv session Python, skriver du **python** i gränssnittet. 

Installera ytterligare Python-bibliotek med ```conda``` eller ```pip``` . För pip, aktivera rätt miljö först om du inte vill att standard:

    source activate root
    pip install <package>

Eller ange den fullständiga sökvägen till pip:

    /anaconda/bin/pip install <package>
    
För conda, bör du alltid ange miljönamnet (_py35_ eller _rot_):

    conda install <package> -n py35

Om du är på ett grafiskt gränssnitt eller har X11 vidarebefordran set upp kan du skriva **pycharm** starta PyCharm Python IDE. Du kan använda standard-textredigerare. Du kan dessutom använda Spyder, en Python IDE som har paketerats med Anaconda Python-distributioner. Spyder måste en grafisk desktop eller X11 vidarebefordran. En genväg till Spyder har angetts i grafiska desktop.s

### <a name="jupyter-notebook"></a>Jupyter Notebook
Anaconda distribution levereras också med en Jupyter-anteckningsbok, en miljö för att dela kod och analys. Jupyter-anteckningsboken sker via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn och lösenord.

Jupyter notebook-server har konfigurerats före med Python 2, Python 3 och R-kärnor. Det finns en skrivbordsikonen med namnet ”Jupyter Notebook” för att starta webbläsaren för att komma åt notebook-server. Om du är på den virtuella datorn via SSH- eller X2Go-klient kan du kan också besöka [ https://localhost:8000/ ](https://localhost:8000/) till Jupyter notebook-server.

> [!NOTE]
> Fortsätt om du får några certifikatvarningar.
> 
> 

Du kan komma åt Jupyter notebook-server från valfri värddator. Skriv bara *https://\<VM DNS-namn eller IP-adress\>: 8000 /*

> [!NOTE]
> Port 8000 öppnas i brandväggen som standard när den virtuella datorn etableras.
> 
> 

Vi har förpackat exempelanteckningsböcker – en i Python och en i R. Du kan se länken till exemplen på startsidan för bärbar dator när du autentiserar till Jupyter-anteckningsbok med ditt lokala Linux-användarnamn och lösenord. Du kan skapa en ny anteckningsbok genom att välja **New**, och sedan språket kernel. Om du inte ser den **New** knapp, klickar du på den **Jupyter** ikonen uppe till vänster att gå till startsidan för notebook-server.

### <a name="apache-spark-standalone"></a>Apache Spark-fristående 
En fristående instans av Apache Spark är förinstallerade på Linux DSVM till hjälper dig att utveckla lokalt Spark-program först innan du testa och distribuera stora kluster. Du kan köra PySpark program via Jupyter-kernel. När du öppnar Jupyter, klickar du på den **New** knappen och du bör se en lista över tillgängliga kärnor. ”Spark – Python” är PySpark-kerneln som låter dig skapa Spark-program med Python-språk. Du kan också använda en Python IDE som PyCharm eller Spyder för att skapa du Spark-programmet. Eftersom detta är en fristående instans, Spark-stack körs inom det anropande klientprogrammet. Detta gör det snabbare och enklare att felsöka problem med jämfört med utveckling i ett Spark-kluster. 

En bärbar dator PySpark exemplet finns på Jupyter som du hittar i katalogen ”SparkML” under arbetskatalogen för Jupyter ($HOME/anteckningsböcker/SparkML/pySpark). 

Om programmering i R för Spark, kan du använda Microsoft R Server, SparkR eller sparklyr. 

Innan du kör i Spark-kontexten i Microsoft R Server som du behöver göra en en gång installationsprogrammet steg för att aktivera en lokal nod HDFS Hadoop och Yarn-instans. Som standard är Hadoop-tjänster installerat men inaktiverat på DSVM. För att kunna aktivera den måste du köra följande kommandon som rot första gången:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Du kan stoppa Hadoop-relaterade tjänster när du inte behöver dem genom att köra ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` ett exempel som visar hur du utvecklar och testar FRU i remote Spark-kontexten (som är fristående Spark-instansen på DSVM) har angetts och är tillgängliga i den `/dsvm/samples/MRS` katalogen. 

### <a name="ides-and-editors"></a>IDE: er och redigeringsprogram
Du kan välja mellan flera kod redigerare. Detta inkluderar vi/VIM, Emacs, RStudio pycharm med IntelliJ. IntelliJ, RStudio och PyCharm finns grafiska redigerare du måste vara inloggad på ett grafiskt skrivbord kan använda dem. Dessa redigerare har skrivbord och programtjänster menyn genvägar att starta dem.

**VIM** och **Emacs** är textbaserade redigerare. Vi har installerat ett tillägg-paket som heter Emacs talar statistik (ESS) som underlättar arbetet med R i redigeraren Emacs Emacs. Mer information finns på [ESS](http://ess.r-project.org/).

**LaTex** har installerats via texlive-package tillsammans med tillägget Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) paket, vilket förenklar redigering LaTex dokument inom Emacs.  

### <a name="databases"></a>Databaser

#### <a name="graphical-sql-client"></a>Grafisk SQL-klient
**SQuirrel SQL**, en grafisk SQL-klient har angetts för att ansluta till olika databaser (till exempel Microsoft SQL Server och MySQL) och köra SQL-frågor. Du kan köra det från en grafisk skrivbordssession (med X2Go-klienten, till exempel). För att anropa SQuirrel SQL, kan du starta från ikonen på skrivbordet eller kör följande kommando på gränssnittet.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Ställa in din drivrutiner och databasen alias innan den första användningen. JDBC-drivrutiner finns på:

*/usr/Share/Java/jdbcdrivers*

Mer information finns i [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Kommandoradsverktyg för att komma åt Microsoft SQL Server
Paketets ODBC-drivrutinen för SQL Server levereras med två kommandoradsverktyg:

**bcp**: Bcp-verktyget bulk kopierar data mellan en instans av Microsoft SQL Server och en datafil i ett format som angetts av användaren. Bcp-verktyget för att importera stora mängder nya rader till SQL Server-tabeller eller exportera data från tabeller till datafiler. Om du vill importera data till en tabell, måste antingen använda en formatfil som skapats för tabellen eller förstå strukturen för tabellen och vilka typer av data som är giltiga för sina kolumner.

Mer information finns i [ansluter med bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Du kan ange Transact-SQL-uttryck med sqlcmd-verktyget, samt system procedurer och skriptfiler i Kommandotolken. Det här verktyget använder ODBC för att köra Transact-SQL-satser.

Mer information finns i [ansluter med sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Det finns vissa skillnader i det här verktyget mellan Linux och Windows-plattformar. Se dokumentationen för mer information.
> 
> 

#### <a name="database-access-libraries"></a>Bibliotek för åtkomst av databasen
Det finns bibliotek som är tillgängliga i R och Python till access-databaser.

* I R, den **RODBC** paketet eller **dplyr** paketet kan du fråga eller köra SQL-uttryck på databasservern.
* I Python, den **pyodbc** biblioteket ger åtkomst till databasen med ODBC som underliggande lager.  

### <a name="azure-tools"></a>Azure-verktyg
Följande Azure-verktygen är installerade på den virtuella datorn:

* **Azure-kommandoradsgränssnittet**: Azure CLI kan du skapa och hantera Azure-resurser via shell-kommandon. För att anropa Azure-verktygen, skriver du **azure hjälp**. Mer information finns i den [dokumentationssidan för Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Microsoft Azure Storage Explorer**: Microsoft Azure Storage Explorer är ett grafiskt verktyg som används för att bläddra igenom objekten som du har lagrat i Azure storage-kontot, och för att ladda upp och ned data till och från Azure-blobar. Du kan komma åt Storage Explorer från ikonen genvägen på skrivbordet. Du kan anropa det från en shell-prompten genom att skriva **StorageExplorer**. Du måste vara inloggad på en X2Go-klient eller har X11 vidarebefordran set upp.
* **Azure-bibliotek**: Följande är några av de förinstallerade biblioteken.
  
  * **Python**: Azure-relaterade-bibliotek i Python som är installerade är **azure**, **azureml**, **pydocumentdb**, och **pyodbc**. Med de tre första biblioteken kan du komma åt Azure storage-tjänster, Azure Machine Learning och Azure Cosmos DB (en NoSQL-databas på Azure). Fjärde biblioteket pyodbc (tillsammans med Microsoft ODBC-drivrutinen för SQL Server), ger åtkomst till SQL Server, Azure SQL Database och Azure SQL Data Warehouse från Python med hjälp av en ODBC-gränssnittet. Ange **pip lista** att se alla bibliotek. Glöm inte att köra det här kommandot i både Python 2.7 och 3,5 miljöer.
  * **R**: Azure-relaterade biblioteken i R som är installerade är **AzureML** och **RODBC**.
  * **Java**: Listan över Azure Java-bibliotek finns i katalogen **/dsvm/sdk/AzureSDKJava** på den virtuella datorn. Viktiga bibliotek är Azure lagring och hantering av API: er, Azure Cosmos DB och JDBC-drivrutiner för SQL Server.  

Du kan komma åt den [Azure-portalen](https://portal.azure.com) från den förinstallerade webbläsaren Firefox. På Azure-portalen kan du skapa, hantera och övervaka Azure-resurser.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning är en fullständigt hanterad molntjänst som hjälper dig att bygga, distribuera och dela lösningar med förutsägelseanalys. Du bygger dina experiment och modeller från Azure Machine Learning Studio. Den kan nås från en webbläsare på den virtuella datorn för datavetenskap genom att besöka [Microsoft Azure Machine Learning](https://studio.azureml.net).

När du loggar in på Azure Machine Learning Studio har du åtkomst till en arbetsyta för experimentering där du kan skapa ett logiskt flöde för machine learning-algoritmer. Du har åtkomst till en Jupyter-anteckningsbok som finns på Azure Machine Learning och fungerar sömlöst med experiment i Machine Learning Studio. Operationalisera maskininlärningsmodeller som du har skapat genom att omsluta dem i ett webbgränssnitt för tjänsten. På så sätt kan klienter som är skrivna på valfritt språk att anropa förutsägelser från machine learning-modeller. Mer information finns i den [dokumentation om Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Du kan också skapa dina modeller i R eller Python på den virtuella datorn och sedan distribuera den i produktion i Azure Machine Learning. Vi har installerat bibliotek i R (**AzureML**) och Python (**azureml**) aktivera den här funktionen.

Information om hur du distribuerar modeller i R och Python i Azure Machine Learning finns i [tio saker som du kan göra på den virtuella datorn för datavetenskap](vm-do-ten-things.md) (särskilt avsnittet ”Skapa modeller med R eller Python och Operationalisera dem med hjälp av Azure Machine Learning ”).

> [!NOTE]
> De här instruktionerna har skrivits för Windows-versionen av den virtuella datorn för datavetenskap. Men informationen om att distribuera modeller till Azure Machine Learning finns tillämplig för Linux-VM.
> 
> 

### <a name="machine-learning-tools"></a>Machine learning-verktyg
Den virtuella datorn kommer med några machine learning-verktyg och algoritmer som har förväg kompilerad och förinstallerade lokalt. Exempel på dessa är:

* **Vowpal Wabbit**: En snabb online inlärningsalgoritm.
* **xgboost**: Ett verktyg som tillhandahåller optimerade, bättre algoritmer.
* **Rattle**: Ett R-baserad grafiska verktyg för enkel datagranskning och modellering.
* **Python**: Anaconda Python medföljer machine learning-algoritmer med bibliotek som lär du dig Scikit. Du kan installera andra bibliotek med hjälp av den `pip install` kommando.
* **LightGBM**: En snabb, distribuerad, högpresterande gradient boosting framework baserat på beslut algoritmer.
* **R**: Ett omfattande bibliotek med machine learning-funktioner är tillgängliga för R. Vissa av de bibliotek som är förinstallerade är lm, glm, randomForest, rpart. Andra bibliotek kan installeras genom att köra:
  
        install.packages(<lib name>)

Här är ytterligare information om de första tre machine learning-verktyg i listan.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit är en machine learning-system som använder tekniker som online, hash, allreduce, sänkt, learning2search, aktiv, och interaktiva utbildningar.

Om du vill köra verktyget på en grundläggande exempel, gör du följande:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Det finns andra, större demonstrationer i den katalogen. Läs mer på VW [det här avsnittet av GitHub](https://github.com/JohnLangford/vowpal_wabbit), och [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Det här är ett bibliotek som är utformad och optimerad för bättre () algoritmer. Målet med det här biblioteket är att skicka beräkning gränserna för datorer bra som behövs för att tillhandahålla storskaliga trädet boosting som är skalbara, bärbar och korrekt.

Den tillhandahålls som en kommandorad samt ett R-bibliotek.

Om du vill använda det här biblioteket i R, du kan starta en interaktiv R-session (genom att skriva **R** i shell), och läsa in biblioteket.

Här är ett enkelt exempel som du kan köra R-prompten:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Om du vill köra kommandoraden xgboost visas nedan kommandona i gränssnittet för att köra:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


En .model fil skrivs till den angivna katalogen. Information om det här demo-exemplet hittar [på GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Mer information om xgboost finns i den [xgboost dokumentationssidan](https://xgboost.readthedocs.org/en/latest/), och dess [GitHub-lagringsplatsen](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Spännen
Rattle (den **R** **A**nalysdatamängd **T**yg **T**o **L**tjäna **E** asily) använder GUI-baserade datagranskning och modellering. Det utgör statistiska och visuella sammanfattningar av data, transformeringar data som kan modelleras lätt, bygger både ej kontrollerade och övervakade modeller från data, visar prestanda för modeller grafiskt, och ställer in poäng nya data. Det genererar även R-kod, replikeras de åtgärder i Användargränssnittet som kan köra direkt i R eller användas som utgångspunkt för vidare analys.

Om du vill köra spännen måste finnas i en grafisk inloggning skrivbordssession. Skriv på terminalen ```R``` kommer R-miljön. Ange följande kommandon i R-Kommandotolken:

    library(rattle)
    rattle()

Nu ett grafiskt gränssnitt som öppnas med en uppsättning flikar. Här följer snabbstartsstegen i spännen som behövs för att använda en exempeldatauppsättning för väder och skapa en modell. I vissa av stegen nedan uppmanas du att automatiskt installera och läsa in vissa nödvändiga R-paket som inte redan finns på systemet.

> [!NOTE]
> Om du inte har behörighet att installera paketet i systemkatalogen (standard), kan du se Kommandotolken på din R-konsolfönstret för att installera paket till ditt personliga bibliotek. Svar *y* om du ser de här anvisningarna.
> 
> 

1. Klicka på **Kör**.
1. En dialogruta öppnas, där du uppmanas om du vill använda exempelinformationen väder. Klicka på **Ja** att läsa in exemplet.
1. Klicka på den **modellen** fliken.
1. Klicka på **kör** att skapa ett beslutsträd.
1. Klicka på **Rita** att visa beslutsträdet.
1. Klicka på den **skog** alternativknappen klicka sedan på **kör** att skapa en slumpmässig skog.
1. Klicka på den **utvärdera** fliken.
1. Klicka på den **Risk** alternativknappen klicka sedan på **kör** att visa två Risk (kumulativ) prestanda diagrammen.
1. Klicka på den **Log** flik för att visa generera R-kod för de föregående åtgärderna.
   (På grund av ett fel i den aktuella versionen av spännen du behöver att infoga en *#* tecknet framför *exportera den här loggen...*  texten i loggen.)
1. Klicka på den **exportera** för att spara R-skript-fil med namnet *weather_script. R* till arbetsmappen.

Du kan avsluta spännen och R. Du kan nu ändra genererade R-skriptet eller använda det som det är att köra det när som helst om du vill upprepa allt som gjordes i Rattle-Användargränssnittet. Det här är ett enkelt sätt att snabbt utföra analyser och maskininlärning i en enkel grafiska gränssnittet när automatisk generering av kod i R för att ändra och/eller Läs särskilt för nybörjare i R.

## <a name="next-steps"></a>Nästa steg
Här är hur du kan fortsätta din inlärning och undersökning:

* Den [datavetenskap på den virtuella datorn för datavetenskap för Linux](linux-dsvm-walkthrough.md) genomgången visar hur du utför flera vanliga datavetenskapsuppgifter med den Linux virtuell dator för datavetenskap etablerats här. 
* Utforska de olika verktyg för datavetenskap på den virtuella datorn för datavetenskap genom att prova de verktyg som beskrivs i den här artikeln. Du kan också köra *dsvm-mer-info* på gränssnittet på den virtuella datorn för en grundläggande introduktion och länkar till mer information om verktygen som installeras på den virtuella datorn.  
* Lär dig att skapa slutpunkt till slutpunkt Analyslösningar systematiskt med hjälp av den [Team Data Science Process](https://aka.ms/tdsp).
* Gå till den [Azure AI-galleriet](https://gallery.azure.ai/) för machine learning och data analytics-exempel som använder Azure AI-tjänster.

