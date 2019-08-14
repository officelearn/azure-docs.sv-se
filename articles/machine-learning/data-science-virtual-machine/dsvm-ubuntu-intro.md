---
title: Skapa en Ubuntu Linux virtuell dator för datavetenskap
titleSuffix: Azure
description: Konfigurera och skapa en Data Science Virtual Machine för Linux (Ubuntu) på Azure för att göra analyser och maskininlärning.
services: machine-learning
documentationcenter: ''
author: vijetajo
ms.author: vijetaj
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/13/2019
ms.openlocfilehash: 6d79bfa8f390d145bfd963b40497030140a3d135
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013577"
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Etablera den virtuella datorn för datavetenskap för Linux (Ubuntu)

Den virtuella datorn för datavetenskap för Linux är en Ubuntu-baserad VM-avbildning som gör det enkelt att komma igång med machine learning, inklusive djupinlärning på Azure. Deep learning-verktyg är:

* [Caffe](https://caffe.berkeleyvision.org/): Ett ramverk för djup inlärning som skapats för hastighet, expressivity och modulärhet
* [Caffe2](https://github.com/caffe2/caffe2): En plattforms oberoende version av Caffe
* [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK): En djup studie av program verktyg från Microsoft Research
* [H2O](https://www.h2o.ai/): En stor data plattform med öppen källkod och grafiskt användar gränssnitt
* [Keras](https://keras.io/): Ett neurala nätverks-API med hög nivå i python för TensorFlow, Microsoft Cognitive Toolkit och Theano
* [MXNet](https://mxnet.io/): Ett flexibelt och effektivt djup inlärnings bibliotek med många språk bindningar
* [NVIDIA-SIFFROR](https://developer.nvidia.com/digits): Ett grafiskt system som fören klar vanliga djup inlärnings uppgifter
* [PyTorch](https://pytorch.org/): Ett hög nivå python-bibliotek med stöd för dynamiska nätverk
* [TensorFlow](https://www.tensorflow.org/): Ett bibliotek med öppen källkod för Machine Intelligence från Google
* [Theano](http://deeplearning.net/software/theano/): Ett Python-bibliotek för att definiera, optimera och effektivt utvärdera matematiska uttryck som involverar flerdimensionella matriser
* [Torch](http://torch.ch/): Ett ramverk för vetenskapliga data behandling med bred support för Machine Learning-algoritmer
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
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Ett snabbt Machine Learning-system som stöder tekniker som online, hashing, allreduce, reduktioner, learning2search, aktiv och interaktiv utbildning
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Ett verktyg som ger snabb och korrekt utökat träd implementering
  * [Rattle](https://togaware.com/rattle/): Ett grafiskt verktyg som hjälper dig att komma igång med data analys och maskin inlärning i R Easy
  * [LightGBM](https://github.com/Microsoft/LightGBM): Ett snabbt, distribuerat, högpresterande tonings förstärknings ramverk
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

1. Navigera till den virtuella datorn på den [Azure-portalen](https://portal.azure.com/#create/microsoft-dsvm.linux-data-science-vm-ubuntulinuxdsvmubuntu). Du kan uppmanas att logga in på ditt Azure-konto om du inte redan har loggat in. 
1. Klicka på **skapa** för att öppna guiden.
    ![Konfigurera-data-vetenskap-VM](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
1. I följande avsnitt finns indata för varje steg i guiden som används för att skapa Microsoft Data Science Virtual Machine. Här följer de indata som behövs för att konfigurera var och en av de här stegen:

    a. **Grunderna**:
    
    * **Prenumeration**: Om du har mer än en prenumeration väljer du det som datorn är skapas och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
    * **Resursgrupp**: Du kan skapa en ny eller Använd en befintlig grupp.
    * **Namn på virtuell dator**: Namnet på den data vetenskaps server som du skapar.
    * **Region**: Välj det data Center som passar bäst. Vanligtvis är det datacenter som har de flesta av dina data, eller så är närmast dina fysiska platsen för snabbaste nätverksåtkomst.
    * **Tillgänglighetsalternativ**: Ange detta om du vill använda den här virtuella datorn i tillgänglighets uppsättningar/zoner, annars lämnar du standardvärdet.
    * **Bild**: Låt standardvärdet vara kvar
    * **Storlek**: Välj en av de servertyper som uppfyller dina funktionella krav och begränsningar för kostnad. Välj en NC-eller ND-serien VM för GPU-baserade VM-instanser. 
    * **Användarnamn**: Administratörens användar namn
    * **Offentlig SSH-nyckel**: Offentlig RSA-nyckel i enkel linje format (du kan använda lösen ord i stället för SSH-nyckel).
    
    b. **Diskar**:
    
    * **Typ av operativ system disk**: Välj **Premium SSD** om du föredrar en SSD-enhet (Solid-State Drive). Annars väljer du **Standard HDD**.
    
    c. Du kan bara använda standardvärdena för resten av inställningarna. Att tänka på icke-standardvärden, hovrar du över informationsmeddelande länken Hjälp om specifika fält.
    
    Slutligen väljer du **Granska + skapa**
    
    d. **Granska + skapa**:
    
    * När verifieringen har godkänts kontrollerar du att all information som du har angett är korrekt. Det finns en länk till användningsvillkoren. Den virtuella datorn har inga ytterligare avgifter utöver beräkningen för Server storleken som du valde i den angivna storleken. Starta etableringen, klicka på **skapa**.
    
    Etableringen tar cirka 5 minuter. Status för etableringen visas på Azure portal.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Hur du kommer åt den virtuella datorn för datavetenskap för Linux

Du kan komma åt Ubuntu DSVM på tre sätt:

1. SSH för terminal sessioner
1. X2Go för grafiska sessioner
1. JupyterHub och JupyterLab för Jupyter-anteckningsböcker

Du kan också bifoga en Data Science VM till Azure Notebooks för att köra Jupyter-anteckningsböcker på den virtuella datorn och kringgå begränsningarna i den kostnads fria tjänst nivån. Mer information finns i [Hantera och konfigurera antecknings projekt – beräknings nivå](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

När den virtuella datorn har skapats kan logga du in till den med hjälp av SSH. Använda de autentiseringsuppgifter som du skapade i den **grunderna** avsnittet i steg 3 för text shell-gränssnittet. I Windows kan du ladda ned ett SSH-klientverktyg som [Putty](https://www.putty.org). Du kan använda X11 vidarebefordran på Putty eller installera X2Go klienten om du föredrar en grafisk desktop (X Windows System).

> [!NOTE]
> Klienten X2Go presterade bättre än X11 vidarebefordran i testet. Vi rekommenderar att du använder X2Go-klienten för ett grafiskt gränssnitt för fjärrskrivbord.

### <a name="x2go"></a>X2Go

Linux VM är redan upprättad med X2Go server och redo att ta emot klientanslutningar. Slutför följande procedur på klienten för att ansluta till Linux VM grafiska skrivbordet:

1. Ladda ned och installera klienten för din klientplattform från X2Go [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Kör X2Go-klienten och välj **ny Session**. Det öppnar en konfiguration med flera flikar. Ange följande konfigurationsparametrar:
   * **Sessionen fliken**:
     * **Värd**: Ditt Linux-Data Science VM värd namn eller IP-adress.
     * **Inloggning**: Användar namn på den virtuella Linux-datorn.
     * **SSH-port**: Lämna det på 22 standardvärdet.
     * **Typ av session**: Ändra värdet till XFCE. Linux VM stöder för närvarande endast XFCE desktop.
   * **Fliken Media**: Du kan stänga av ljud supporten och klient utskrift om du inte behöver använda dem.
   * **Delade mappar**: Om du vill att kataloger från klient datorerna ska monteras på den virtuella Linux-datorn lägger du till de klient dator kataloger som du vill dela med den virtuella datorn på den här fliken.

När du loggar in till den virtuella datorn med hjälp av SSH-klient eller XFCE grafiska desktop via X2Go klienten, är du redo att börja använda de verktyg som är installerade och konfigurerade på den virtuella datorn. På XFCE, kan du se genvägar på Program-menyn och ikoner på skrivbordet för många av verktygen.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub och JupyterLab

Ubuntu DSVM körs [JupyterHub](https://github.com/jupyterhub/jupyterhub), en Jupyter-server med flera användare. Om du vill ansluta bläddrar du\/till https:/Your-VM-IP: 8000 på din bärbara dator eller skriv bord, anger det användar namn och lösen ord som du använde för att skapa den virtuella datorn och loggar in. Många exempelanteckningsböcker som är tillgängliga för dig att söka och prova att använda.

JupyterLab, är nästa generations Jupyter notebooks och JupyterHub, också tillgängligt. För att få åtkomst till den loggar du in på JupyterHub och bläddrar sedan till\/URL: en https:/Your-VM-IP: 8000/User/din-username/Lab. Du kan ange JupyterLab som standard server för bärbara datorer genom att lägga till den här raden till */etc/jupyterhub/jupyterhub_config.py*:

```python
c.Spawner.default_url = '/lab'
```

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Verktygen som installeras på den virtuella datorn för datavetenskap för Linux

### <a name="deep-learning-libraries"></a>Deep Learning-bibliotek

#### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit är en öppen källkod, deep learning toolkit. Python-bindningar är tillgängliga i Conda rot- och py35. Det har även ett kommandoradsverktyg (cntk) som redan finns i SÖKVÄGEN.

Python exempelanteckningsböcker är tillgängliga i JupyterHub. Kör ett grundläggande exempel på kommando raden genom att köra följande kommandon i gränssnittet:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Mer information finns i avsnittet CNTK i [GitHub](https://github.com/Microsoft/CNTK), och [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe

Caffe är ett ramverk för djupinlärning från Berkeley Vision och Learning Center. Det är tillgängligt i /opt/caffe. Exempel finns i /opt/caffe/examples.

#### <a name="caffe2"></a>Caffe2

Caffe2 är ett ramverk för djupinlärning från Facebook som bygger på Caffe. Det är tillgängliga i Python 2.7 i Conda rot-miljön. Om du vill aktivera den kör du följande kommando från gränssnittet:

```bash
source /anaconda/bin/activate root
```

Vissa exempel bärbara datorer är tillgängliga i JupyterHub.

#### <a name="h2o"></a>H2O

H2O är en snabb, i minnet och distribuerade maskininlärning och förutsägande analyser. En Python-paketet har installerats i rot- och py35 Anaconda-miljöer. Ett R-paket installeras även. Starta H2O från kommandoraden genom att köra `java -jar /dsvm/tools/h2o/current/h2o.jar`; det finns olika [kommandoradsalternativ](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) som du kan vilja konfigurera. Flow-Webbgränssnittet kan nås genom att bläddra till http://localhost:54321 att komma igång. Exempelanteckningsböcker är också tillgängliga i JupyterHub.

#### <a name="keras"></a>Keras

Keras är en neurala nätverks-API med hög nivå i python som kan köras ovanpå TensorFlow, Microsoft Cognitive Toolkit eller Theano. Det är tillgängligt i rot- och py35 Python.

#### <a name="mxnet"></a>MXNet

MXNet är ett ramverk för djupinlärning som utformats för både effektivitet och flexibilitet. Den har R och Python-bindningar på DSVM. Exempelanteckningsböcker som ingår i JupyterHub och exempelkod är tillgänglig i /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>NVIDIA SIFFROR

NVIDIA Deep Learning GPU utbildning systemet, kallas SIFFROR, är ett system för att förenkla vanliga deep learning-aktiviteter som att hantera data, designa och neurala nätverk på GPU-system och övervakning av prestanda i realtid med avancerade visualisering.

SIFFROR är tillgänglig som en tjänst som kallas siffror. Starta tjänsten och bläddra till http://localhost:5000 att komma igång.

SIFFROR installeras även som en Python-modul i Conda rot-miljö.

#### <a name="tensorflow"></a>TensorFlow

TensorFlow är Googles deep learning-biblioteket. Det är ett program bibliotek med öppen källkod för numerisk beräkning med data flödes diagram. Vissa exempelanteckningsböcker som ingår i JupyterHub TensorFlow är tillgängliga i py35 Python-miljön.

#### <a name="theano"></a>Theano

Theano är ett Python-bibliotek för effektiv numeriska beräkning. Det är tillgängligt i rot- och py35 Python. 

#### <a name="torch"></a>Torch

Torch är ett vetenskapliga databehandling ramverk med brett stöd för machine learning-algoritmer. Den är tillgänglig i /dsvm/tools/torch och th interaktiva sessionen och luarocks Pakethanteraren är tillgängliga på kommandoraden. Exempel finns i /dsvm/samples/torch.

PyTorch är också tillgängliga i roten Anaconda-miljön. Exempel är i /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Microsoft R Server

R är ett av de mest populära språk för dataanalys och maskininlärning. Om du vill använda R för analys, har den virtuella datorn Microsoft R Server (FRU) med Microsoft R Open (MRO) och matematiska Kernel-biblioteket (MKL). MKL optimerar matematiska åtgärder som är vanliga i analytiska algoritmer. MRO är 100 procent kompatibel med R CRAN och någon av R-bibliotek som publicerats i CRAN kan installeras på MRO. FRU ger dig skalning och driftsättning av R-modellerna till webbtjänster. Du kan redigera din R-program i ett standard-redigerare som RStudio, vi och Emacs. Om du föredrar att använda redigeraren Emacs, har det varit förinstallerade. Paketets Emacs ESS (Emacs talar statistik) förenklar arbeta med R-filer i Emacs redigeraren.

Starta R-konsolen, skriver du bara **R** i gränssnittet. Det här kommandot tar dig till en interaktiv miljö. Du använder vanligtvis en redigerare som Emacs eller vi hjälper dig för att utveckla ditt R-program, och sedan köra skript i R. Med RStudio har du ett fullständigt grafiskt IDE-miljö för att utveckla ditt R-program.

Det finns också ett R-skript att installera den [översta 20 R-paket](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) om du vill. Det här skriptet kan köras när du är i det interaktiva gränssnittet R, som du kan ange (som tidigare nämnts) genom att skriva **R** i gränssnittet.  

### <a name="python"></a>Python

Anaconda Python är installerat med Python 2.7 och 3,5 miljöer. 2\.7 miljön kallas _rot_, och 3,5 miljön kallas _py35_. Den här distributionen innehåller grundläggande Python tillsammans med ungefär 300 av de mest populära analyspaket matematiska, teknik och data.

Py35 miljön är standardinställningen. Aktivera rot (2.7)-miljö:

```bash
source activate root
```

Aktivera py35 miljön igen:

```bash
source activate py35
```

Om du vill anropa en interaktiv session Python, skriver du **python** i gränssnittet. 

Installera ytterligare Python-bibliotek med ```conda``` eller ```pip``` . För pip, aktivera rätt miljö först om du inte vill att standard:

```bash
source activate root
pip install <package>
```

Eller ange den fullständiga sökvägen till pip:

```bash
/anaconda/bin/pip install <package>
```

För conda, bör du alltid ange miljönamnet (_py35_ eller _rot_):

```bash
conda install <package> -n py35
```

Om du är på ett grafiskt gränssnitt eller har X11 vidarebefordran set upp kan du skriva **pycharm** starta PyCharm Python IDE. Du kan använda standard-textredigerare. Du kan dessutom använda Spyder, en Python IDE som har paketerats med Anaconda Python-distributioner. Spyder måste en grafisk desktop eller X11 vidarebefordran. En genväg till Spyder tillhandahålls i grafiska desktop.

### <a name="jupyter-notebook"></a>Jupyter Notebook

Anaconda distribution levereras också med en Jupyter-anteckningsbok, en miljö för att dela kod och analys. Jupyter-anteckningsboken sker via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn och lösenord.

Jupyter notebook-server har konfigurerats före med Python 2, Python 3 och R-kärnor. Det finns en skrivbordsikonen med namnet ”Jupyter Notebook” för att starta webbläsaren för att komma åt notebook-server. Om du är på den virtuella datorn via SSH- eller X2Go-klient kan du kan också besöka [ https://localhost:8000/ ](https://localhost:8000/) till Jupyter notebook-server.

> [!NOTE]
> Fortsätt om du får några certifikatvarningar.

Du kan komma åt Jupyter notebook-server från valfri värddator. Skriv bara *https://\<VM DNS-namn eller IP-adress\>: 8000 /*

> [!NOTE]
> Port 8000 öppnas i brandväggen som standard när den virtuella datorn etableras. 

Vi har förpackat exempelanteckningsböcker – en i Python och en i R. Du kan se länken till exemplen på startsidan för bärbar dator när du autentiserar till Jupyter-anteckningsbok med ditt lokala Linux-användarnamn och lösenord. Du kan skapa en ny anteckningsbok genom att välja **New**, och sedan språket kernel. Om du inte ser den **New** knapp, klickar du på den **Jupyter** ikonen uppe till vänster att gå till startsidan för notebook-server.

### <a name="apache-spark-standalone"></a>Apache Spark-fristående

En fristående instans av Apache Spark är förinstallerade på Linux DSVM till hjälper dig att utveckla lokalt Spark-program först innan du testa och distribuera stora kluster. Du kan köra PySpark program via Jupyter-kernel. När du öppnar Jupyter, klickar du på den **New** knappen och du bör se en lista över tillgängliga kärnor. ”Spark – Python” är PySpark-kerneln som låter dig skapa Spark-program med Python-språk. Du kan också använda en python IDE som pycharm med eller Spyder för att bygga Spark-programmet. I den här fristående instansen körs Spark-stacken i det anropande klient programmet, vilket gör det snabbare och enklare att felsöka problem jämfört med att utveckla ett Spark-kluster.

En bärbar dator PySpark exemplet finns på Jupyter som du hittar i katalogen ”SparkML” under arbetskatalogen för Jupyter ($HOME/anteckningsböcker/SparkML/pySpark). 

Om du använder programmering i R för Spark kan du använda Microsoft R Server, sparker eller sparklyr. 

Innan du kör i Spark-kontexten i Microsoft R Server som du behöver göra en en gång installationsprogrammet steg för att aktivera en lokal nod HDFS Hadoop och Yarn-instans. Som standard är Hadoop-tjänster installerat men inaktiverat på DSVM. För att kunna aktivera den måste du köra följande kommandon som rot första gången:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Du kan stoppa de Hadoop-relaterade tjänsterna när du inte behöver dem genom att köra```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```

Ett exempel på hur du utvecklar och testar fru i fjärr-Spark-kontext (som är den fristående Spark-instansen på DSVM) finns i och är tillgänglig i katalogen */dsvm/samples/Mrs* .

### <a name="ides-and-editors"></a>IDE: er och redigeringsprogram

Du kan välja mellan flera kod redigerare, inklusive vi/VIM, emacs, pycharm med, RStudio och IntelliJ. IntelliJ, RStudio och PyCharm finns grafiska redigerare du måste vara inloggad på ett grafiskt skrivbord kan använda dem. Dessa redigerare har skrivbord och programtjänster menyn genvägar att starta dem.

**VIM** och **Emacs** är textbaserade redigerare. Vi har installerat ett tillägg-paket som heter Emacs talar statistik (ESS) som underlättar arbetet med R i redigeraren Emacs Emacs. Mer information finns på [ESS](https://ess.r-project.org/).

**LaTex** har installerats via texlive-package tillsammans med tillägget Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) paket, vilket förenklar redigering LaTex dokument inom Emacs.  

### <a name="databases"></a>Databaser

#### <a name="graphical-sql-client"></a>Grafisk SQL-klient

**SQuirrel SQL**, en grafisk SQL-klient har angetts för att ansluta till olika databaser (till exempel Microsoft SQL Server och MySQL) och köra SQL-frågor. Du kan köra SQuirrel SQL från en grafisk fjärrskrivbordssession (med X2Go-klienten, till exempel) med hjälp av en Skriv bords ikon eller med hjälp av följande kommando i gränssnittet:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Ställa in din drivrutiner och databasen alias innan den första användningen. JDBC-drivrutiner finns på:

*/usr/Share/Java/jdbcdrivers*

Mer information finns i [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Kommandoradsverktyg för att komma åt Microsoft SQL Server

Paketets ODBC-drivrutinen för SQL Server levereras med två kommandoradsverktyg:

**BCP**: BCP-verktyget kopierar data mellan en instans av Microsoft SQL Server och en datafil i ett användardefinierat format. Bcp-verktyget för att importera stora mängder nya rader till SQL Server-tabeller eller exportera data från tabeller till datafiler. Om du vill importera data till en tabell, måste antingen använda en formatfil som skapats för tabellen eller förstå strukturen för tabellen och vilka typer av data som är giltiga för sina kolumner.

Mer information finns i [ansluter med bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Du kan ange Transact-SQL-uttryck med verktyget sqlcmd, samt system procedurer och skript filer i kommando tolken. Det här verktyget använder ODBC för att köra Transact-SQL-satser.

Mer information finns i [ansluter med sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Det finns vissa skillnader i det här verktyget mellan Linux och Windows-plattformar. Se dokumentationen för mer information.

#### <a name="database-access-libraries"></a>Bibliotek för åtkomst av databasen

Det finns bibliotek som är tillgängliga i R och Python till access-databaser.

* I R, den **RODBC** paketet eller **dplyr** paketet kan du fråga eller köra SQL-uttryck på databasservern.
* I Python, den **pyodbc** biblioteket ger åtkomst till databasen med ODBC som underliggande lager.  

### <a name="azure-tools"></a>Azure-verktyg

Följande Azure-verktygen är installerade på den virtuella datorn:

* **Kommando rads gränssnitt för Azure**: Med Azure CLI kan du skapa och hantera Azure-resurser via Shell-kommandon. För att anropa Azure-verktygen, skriver du **azure hjälp**. Mer information finns i den [dokumentationssidan för Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Microsoft Azure Storage Explorer**: Microsoft Azure Storage Explorer är ett grafiskt verktyg som används för att bläddra bland de objekt som du har lagrat på ditt Azure Storage-konto och för att ladda upp och ladda ned data till och från Azure-blobbar. Du kan komma åt Storage Explorer från ikonen genvägen på skrivbordet. Du kan anropa det från en shell-prompten genom att skriva **StorageExplorer**. Du måste vara inloggad från en X2Go-klient eller konfigurera begäran om x11-vidarebefordring.
* **Azure-bibliotek**: Här följer några av de förinstallerade biblioteken.
  
  * **Python**: Azure-relaterade bibliotek i python som är installerade är **Azure**, **azureml**, **pydocumentdb**och **pyodbc**. Med de tre första biblioteken kan du komma åt Azure storage-tjänster, Azure Machine Learning och Azure Cosmos DB (en NoSQL-databas på Azure). Fjärde biblioteket pyodbc (tillsammans med Microsoft ODBC-drivrutinen för SQL Server), ger åtkomst till SQL Server, Azure SQL Database och Azure SQL Data Warehouse från Python med hjälp av en ODBC-gränssnittet. Ange **pip lista** att se alla bibliotek. Glöm inte att köra det här kommandot i både Python 2.7 och 3,5 miljöer.
  * **R**: Azure-relaterade bibliotek i R som är installerade är **azureml** och **RODBC**.
  * **Java**: Listan med Azure Java-bibliotek finns i katalogen **/dsvm/SDK/AzureSDKJava** på den virtuella datorn. Viktiga bibliotek är Azure lagring och hantering av API: er, Azure Cosmos DB och JDBC-drivrutiner för SQL Server.  

Du kan komma åt den [Azure-portalen](https://portal.azure.com) från den förinstallerade webbläsaren Firefox. På Azure-portalen kan du skapa, hantera och övervaka Azure-resurser.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning är en fullständigt hanterad molntjänst som hjälper dig att bygga, distribuera och dela lösningar med förutsägelseanalys. Du bygger dina experiment och modeller från Azure Machine Learning Studio. Den kan nås från en webbläsare på den virtuella datorn för datavetenskap genom att besöka [Microsoft Azure Machine Learning](https://studio.azureml.net).

När du loggar in på Azure Machine Learning Studio har du åtkomst till en arbetsyta för experimentering där du kan skapa ett logiskt flöde för machine learning-algoritmer. Du har åtkomst till en Jupyter-anteckningsbok som finns på Azure Machine Learning och fungerar sömlöst med experiment i Machine Learning Studio. Operationalisera maskininlärningsmodeller som du har skapat genom att omsluta dem i ett webbgränssnitt för tjänsten. Genom att använda Machine Learning-modeller kan klienter skrivna på valfritt språk för att anropa förutsägelser från dessa modeller. Mer information finns i den [dokumentation om Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Du kan också skapa dina modeller i R eller Python på den virtuella datorn och sedan distribuera den i produktion i Azure Machine Learning. Vi har installerat bibliotek i R (**AzureML**) och Python (**azureml**) aktivera den här funktionen.

Information om hur du distribuerar modeller i R och Python i Azure Machine Learning finns i [tio saker som du kan göra på den virtuella datorn för datavetenskap](vm-do-ten-things.md) (särskilt avsnittet ”Skapa modeller med R eller Python och Operationalisera dem med hjälp av Azure Machine Learning ”).

> [!NOTE]
> De här instruktionerna har skrivits för Windows-versionen av den virtuella datorn för datavetenskap. Men informationen om att distribuera modeller till Azure Machine Learning finns tillämplig för Linux-VM.

### <a name="machine-learning-tools"></a>Machine learning-verktyg

Den virtuella datorn kommer med några machine learning-verktyg och algoritmer som har förväg kompilerad och förinstallerade lokalt. Exempel på dessa är:

* **Vowpal Wabbit**: En snabb online-inlärnings algoritm.
* **xgboost**: Ett verktyg som tillhandahåller optimerade, förstärkta träd algoritmer.
* **Rattle**: Ett R-baserat grafiskt verktyg för enkel utforskning och modellering av data.
* **Python**: Anaconda python levereras med Machine Learning-algoritmer med bibliotek som Scikit-lär. Du kan installera andra bibliotek med hjälp av den `pip install` kommando.
* **LightGBM**: Ett snabbt, distribuerat, högpresterande tonings ramverk som bygger på algoritmer för besluts träd.
* **R**: Ett omfattande bibliotek med Machine Learning-funktioner är tillgängligt för R. Några av de bibliotek som är förinstallerade är lm, GLM, randomForest, rpart. Andra bibliotek kan installeras genom att köra:
  
        install.packages(<lib name>)

Här är ytterligare information om de första tre machine learning-verktyg i listan.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit är en machine learning-system som använder tekniker som online, hash, allreduce, sänkt, learning2search, aktiv, och interaktiva utbildningar.

Om du vill köra verktyget på ett Basic-exempel använder du följande kommandon:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Det finns andra, större demonstrationer i den katalogen. Läs mer på VW [det här avsnittet av GitHub](https://github.com/JohnLangford/vowpal_wabbit), och [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost

Det här är ett bibliotek som är utformad och optimerad för bättre () algoritmer. Målet med det här biblioteket är att skicka beräkning gränserna för datorer bra som behövs för att tillhandahålla storskaliga trädet boosting som är skalbara, bärbar och korrekt.

Den tillhandahålls som en kommandorad samt ett R-bibliotek.

Om du vill använda det här biblioteket i R, du kan starta en interaktiv R-session (genom att skriva **R** i shell), och läsa in biblioteket.

Här är ett enkelt exempel som du kan köra R-prompten:

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

Om du vill köra kommandoraden xgboost visas nedan kommandona i gränssnittet för att köra:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

En .model fil skrivs till den angivna katalogen. Information om det här demo-exemplet hittar [på GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Mer information om xgboost finns i den [xgboost dokumentationssidan](https://xgboost.readthedocs.org/en/latest/), och dess [GitHub-lagringsplatsen](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Spännen

Rattle (den **R** **A**nalysdatamängd **T**yg **T**o **L**tjäna **E** asily) använder GUI-baserade datagranskning och modellering. Det utgör statistiska och visuella sammanfattningar av data, transformeringar data som kan modelleras lätt, bygger både ej kontrollerade och övervakade modeller från data, visar prestanda för modeller grafiskt, och ställer in poäng nya data. Det genererar även R-kod, replikeras de åtgärder i Användargränssnittet som kan köra direkt i R eller användas som utgångspunkt för vidare analys.

Om du vill köra spännen måste finnas i en grafisk inloggning skrivbordssession. Skriv på terminalen ```R``` kommer R-miljön. Ange följande kommandon i R-Kommandotolken:

```R
library(rattle)
rattle()
```

Nu ett grafiskt gränssnitt som öppnas med en uppsättning flikar. Här är de snabb starts steg i Rattle som behövs för att använda en exempel data uppsättning för väder och bygga en modell. I vissa av stegen nedan uppmanas du att automatiskt installera och läsa in vissa nödvändiga R-paket som inte redan finns på systemet.

> [!NOTE]
> Om du inte har behörighet att installera paketet i systemkatalogen (standard), kan du se Kommandotolken på din R-konsolfönstret för att installera paket till ditt personliga bibliotek. Svar *y* om du ser de här anvisningarna.

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
