---
title: Tio saker som du kan göra på den virtuella datorn för datavetenskap på Azure | Microsoft Docs
description: Utföra olika datagranskning och modellering aktiviteten på den virtuella datorn för datavetenskap.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: df9edfee9d8a6a0736a040924bac736cfcb3633c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250925"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Tio saker som du kan göra på den Windows virtuell dator för datavetenskap

Windows Data Science Virtual Machine (DSVM) är en kraftfull data science-utvecklingsmiljö där du kan utföra olika uppgifter som data utforskning och modellering. Miljön kommer redan inbyggda och tillsammans med flera populära analytics-verktyg som gör det enkelt att snabbt komma igång med din analys för lokala, moln eller hybrid-distributioner. DSVM har ett nära samarbete med många Azure-tjänster och kan läsa och bearbeta data som redan finns på Azure, i Azure SQL Data Warehouse, Azure Data Lake, Azure Storage eller Azure Cosmos DB. Det kan även använda andra verktyg som Azure Machine Learning och Azure Data Factory.

I den här artikeln får du lära dig hur du använder din DSVM att utföra olika data science-åtgärder och interagera med andra Azure-tjänster. Här följer några av de saker som du kan göra på DSVM:

1. Utforska data och utveckla modeller lokalt på DSVM med hjälp av Microsoft ML Server, Python
2. Använd en Jupyter-anteckningsboken för att experimentera med dina data i en webbläsare med hjälp av Python 2, Python 3 R Microsoft enterprise-redo version av R som utformats för prestanda
3. Distribuera modeller som skapats med R och Python på Azure Machine Learning så att klientprogram kan komma åt dina modeller med hjälp av en enkel web service-gränssnittet
4. Administrera Azure-resurser med hjälp av Azure portal eller Powershell
5. Utöka ditt lagringsutrymme och dela datauppsättningar i stor skala / code över hela gruppen genom att skapa en Azure-fillagring som en monteras enhet på din DSVM
6. Dela kod med ditt team med GitHub och få åtkomst till databasen med hjälp av de förinstallerade Git klienterna – Git Bash, Git GUI.
7. Få åtkomst till olika Azure data- och -tjänster som Azure blob-lagring, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse och databaser
8. Skapa rapporter och instrumentpanel med hjälp av Power BI Desktop förinstallerat på DSVM och distribuera dem till molnet
9. Dynamiskt skala din DSVM efter dina behov för projektet
10. Installera ytterligare verktyg på den virtuella datorn   

> [!NOTE]
> Ytterligare användning kan tillkomma för många av ytterligare data lagring och analys tjänster som visas i den här artikeln. Referera till den [priser för Azure](https://azure.microsoft.com/pricing/) för information.
> 
> 

**Förutsättningar**

* Du behöver en Azure-prenumeration. Du kan registrera dig för en kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/free/).
* Instruktioner för att etablera en virtuell dator för datavetenskap på Azure portal finns på [skapar en virtuell dator](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Utforska data och utveckla modeller med hjälp av Microsoft ML Server eller Python
Du kan använda språk som R och Python för att göra din Dataanalys på DSVM.

För R, kan du använda en IDE som RStudio som finns på start-menyn eller skrivbordet eller R Tools för Visual Studio. Microsoft tillhandahåller ytterligare bibliotek ovanpå den öppen-källa/CRAN-R för att aktivera skalbar analys och möjligheten att analysera data som är större än maxstorleken för minne genom att göra parallella segmenterat analysis. 

För Python, kan du använda en IDE som Visual Studio Community Edition, som har Python-verktygen för Visual Studio (PTVS)-tillägg som redan är installerat. Som standard endast Python 3.6, har rot conda-miljö konfigurerats på PTVS. För att aktivera Anaconda Python 2.7, måste du göra följande:

* Skapa anpassade miljöer för varje version genom att gå till **verktyg** -> **Python Tools** -> **Python-miljöer** och sedan klicka på ” **+ Anpassad**”i Visual Studio Community Edition
* Ge en beskrivning och ange miljö prefixsökvägen som *c:\anaconda\envs\python2* för Anaconda Python 2.7
* Klicka på **automatisk identifiering** och sedan **tillämpa** att spara miljön.

Här är vad inställningen för anpassad miljö ser ut som i Visual Studio.

![PTVS installationen](./media/vm-do-ten-things/PTVSSetup.png)

Se den [dokumentationen till PTVS](https://aka.ms/ptvsdocs) för ytterligare information om hur du skapar Python-miljöer.

Du är nu ställa in för att skapa ett nytt Python-projekt. Gå till **filen** -> **New** -> **projekt** -> **Python** och välj typ av Python-program som du skapar. Du kan ange Python-miljön för det aktuella projektet till den önskade versionen (Python 2.7 eller 3.6) genom att högerklicka på den **Python-miljöer**, välja **Lägg till/ta bort Python miljöer**, och sedan välja önskad miljö. Du hittar mer information om hur du arbetar med PTVS i produkten [dokumentation](https://aka.ms/ptvsdocs).

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Använder en Jupyter-anteckningsbok för att utforska och modellera dina data med Python eller R
Jupyter-anteckningsboken är en kraftfull miljö som ger ett webbläsarbaserat ”IDE” för datagranskning och modellering. Du kan använda Python 2, Python 3 eller R (öppen källkod och Microsoft R Server) i en Jupyter-anteckningsbok.

Klicka på ikonen start-menyn om du vill starta Jupyter-anteckningsboken / skrivbordsikonen benämnt **Jupyter Notebook**. I Kommandotolken för DSVM kan du också köra kommandot ```jupyter notebook``` från den katalog där du har befintliga anteckningsböcker eller om du vill skapa nya anteckningsböcker.  

När du har startat Jupyter, bör du se en katalog som innehåller några exempel anteckningsböcker som är kompletta i DSVM. Nu kan du:

* Klicka på anteckningsboken för att se koden.
* Kör varje cell genom att trycka på **SKIFT-ange**.
* Köra anteckningsboken genom att klicka på **Cell** -> **kör**
* Skapa en ny anteckningsbok genom att klicka på ikonen Jupyter (övre vänstra hörnet) och sedan klicka på **New** knappen på höger och sedan välja notebook-språk (även kallat kernlar).   

> [!NOTE]
> Python 2.7, Python 3.6, R, Julia och PySpark kernlar i Jupyter stöds för närvarande. R-kernel har stöd för programmering i såväl R med öppen källkod som högpresterande Microsoft R.   
> 
> 

När du är i anteckningsboken som du kan utforska dina data, skapa modellen, testa modellen med ditt val av bibliotek.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Skapa modeller med R eller Python och utför åtgärder dem med hjälp av Azure Machine Learning
När du har skapat och verifieras din modell är vanligtvis nästa steg att distribuera den till produktionen. Detta gör att ditt klientprogram att anropa modellens förutsägelser på en i realtid eller på basis av batch-läge. Azure Machine Learning är en mekanism för att operationalisera en modell som skapats i R eller Python.

När du driftsätta modellen i Azure Machine Learning, visas en webbtjänst som tillåter klienter att göra REST-anrop som skickar in indataparametrar och ta emot förutsägelser från modellen som utdata.   

> [!NOTE]
> Om du inte har har registrerat dig för Azure Machine Learning, kan du hämta en kostnadsfri arbetsyta eller en standardarbetsytan genom att besöka den [Azure Machine Learning Studio](https://studio.azureml.net/) startsidan och klicka på ”Kom igång”.   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Skapa och Operationalisera Python modeller
Här är ett kodstycke som utvecklats i en Python Jupyter-anteckningsbok som bygger en enkel modell med SciKit-Läs-biblioteket.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

Metoden används för att distribuera python-modeller till Azure Machine Learning radbryter förutsägelser av modellen in i en funktion och decorates med attribut som tillhandahålls av förinstallerade python-bibliotek för Azure Machine Learning som anger Azure Machine Learning Arbetsyte-ID, API-nyckel och parametrarna indata och gå tillbaka.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

En klient kan nu göra anrop till webbtjänsten. Det finns bekvämlighet omslutningar som konstruerar REST API-begäranden. Här är en exempelkod som förbrukar webbtjänsten.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Azure Machine Learning-biblioteket stöds bara på Python 2.7 för närvarande.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Skapa och Operationalisera R-modeller
Du kan distribuera R-modeller som skapats på den virtuella datorn för datavetenskap eller någon annanstans till Azure Machine Learning på ett sätt som liknar hur man gör för Python. Här är stegen:

* Skapa en settings.json-fil för att ange ditt arbetsyte-ID och auth-token 
* skriva en Omslutning för modellens förutsäga funktion.
* anropa ```publishWebService``` i Azure Machine Learning-biblioteket för att skicka in Omslutning för funktionen.  

Här är de proceduren och kod kodavsnitt som kan användas för att konfigurera, skapa, publicera och Använd en modell som en webbtjänst i Azure Machine Learning.

#### <a name="setup"></a>Konfiguration

* Skapa en settings.json fil under en katalog med namnet ```.azureml``` under arbetskatalogen och ange parametrar från Azure Machine Learning-arbetsyta:

Settings.JSON filstruktur:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Skapa en modell i R och publicera den i Azure Machine Learning
    library(AzureML)
    ws <- workspace(config="~/.azureml/settings.json")

    if(!require("lme4")) install.packages("lme4")
    library(lme4)
    set.seed(1)
    train <- sleepstudy[sample(nrow(sleepstudy), 120),]
    m <- lm(Reaction ~ Days + Subject, data = train)

    # Define a prediction function to publish based on the model:
    sleepyPredict <- function(newdata){
          predict(m, newdata=newdata)
    }

    ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Förbruka modellen distribueras i Azure Machine Learning
Om du vill använda modellen från ett klientprogram skickar vi använder Azure Machine Learning-biblioteket för att leta upp den publicerad webbtjänsten med hjälp av namnet på `services` API-anrop för att fastställa slutpunkten. Du bara anropa den `consume` fungera och skicka in dataram till att förutse.
Följande kod används för att förbruka modellen som publiceras som en Azure Machine Learning-webbtjänst.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Mer information om Azure Machine Learning R-bibliotek finns [här](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Administrera Azure-resurser med hjälp av Azure portal eller Powershell
DSVM inte bara kan du skapa en analyslösning lokalt på den virtuella datorn, men även ger dig tillgång till tjänster på Microsoft Azure-molnet. Azure tillhandahåller flera beräkning, lagring, tjänster för analys av data och andra tjänster som du kan administrera och komma åt från din DSVM.

Att administrera dina Azure-prenumeration och cloud-resurser du kan använda din webbläsare och peka på den [Azure-portalen](https://portal.azure.com). Du kan också använda Azure Powershell för att administrera Azure-prenumeration och resurser via ett skript.
Du kan köra Azure Powershell från en genväg på skrivbordet eller start-menyn med titeln ”Microsoft Azure Powershell”. Referera till [Microsoft Azure Powershell-dokumentationen](../../powershell-azure-resource-manager.md) mer information om hur du kan administrera din Azure-prenumeration och resurser med hjälp av Windows Powershell-skript.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Utöka ditt lagringsutrymme med ett delat filsystem
Dataexperter kan dela stora datauppsättningar, kod eller andra resurser i teamet. DSVM själva har cirka 45GB tillgängligt utrymme. Du kan använda Azure File Service för att utöka din lagring och antingen montera den på en eller flera instanser av DSVM eller åtkomst till den via ett REST-API.  Du kan också använda [Azure-portalen](../../virtual-machines/windows/attach-managed-disk-portal.md) eller Använd [Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) att lägga till extra dedikerade datadiskar. 

> [!NOTE]
> Det maximala utrymmet Azure File Service-resursen är 5 TB och storleksgränsen för enskilda filer är 1 TB. 
> 
> 

Du kan använda Azure Powershell för att skapa en filresurs i Azure File Service. Här är skriptet som ska köras under Azure PowerShell för att skapa en Azure-tjänsten filresurs.

    # Authenticate to Azure.
    Connect-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


Nu när du har skapat en Azure-filresurs kan montera du den i valfri virtuell dator i Azure. Vi rekommenderar starkt att Virtuellt datorn i samma Azure-datacenter som lagringskontot för att undvika kostnader för överföring av latens och data. Här är kommandon för att montera enheten på DSVM som du kan köra på Azure Powershell.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Du kan nu komma åt den här enheten precis som med alla vanliga enheter på den virtuella datorn.

## <a name="6-share-code-with-your-team-using-github"></a>6. Dela kod med ditt team med GitHub
GitHub är ett kodcentrallager som var du hittar många exempelkoden och källor för olika verktyg med hjälp av olika tekniker som delas av vårt community för utvecklare. Git använder tekniken som att spåra och lagra versioner av kodfiler. GitHub är också en plattform där du kan skapa en egen databas att lagra din grupps delad kod och dokumentation, implementera versionskontroll och också styra vem som har åtkomst till Visa och bidra kod. Gå till den [GitHub hjälpsidor](https://help.github.com/) för mer information om hur du använder Git. Du kan använda GitHub som ett sätt att samarbeta med ditt team, Använd kod som har utvecklats av communityn och bidra kod tillbaka till diskussionsgruppen.

DSVM levereras med klientverktyg på både kommandoraden som väl GUI för att få åtkomst till GitHub-lagringsplatsen. Kommandoradsverktyget för att arbeta med Git och GitHub kallas Git Bash. Visual Studio installerat på DSVM har Git-tillägg. Du kan hitta Start ikoner för dessa verktyg på start-menyn och skrivbordet.

Om du vill hämta kod från en GitHub-lagringsplats som du använder den ```git clone``` kommando. Till exempel för att hämta data science-lagringsplats som publicerats av Microsoft i den aktuella katalogen du kan köra följande kommando när du är i ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

I Visual Studio kan du göra samma kopieringen. Följande skärmbild visar hur du kommer åt Git och GitHub-verktygen i Visual Studio.

![Git i Visual Studio](./media/vm-do-ten-things/VSGit.PNG)

Du hittar mer information om hur du använder Git för att arbeta med din GitHub-lagringsplats från flera resurser som är tillgängliga på github.com. Den [facit](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) är användbar som referens.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Få åtkomst till olika Azure-data- och tjänster
### <a name="azure-blob"></a>Azure-blobb
Azure blob är en pålitlig, ekonomisk molnlagring för stora och små. Det här avsnittet beskrivs hur du kan flytta data till Azure Blob- och komma åt data som lagras i en Azure-Blob.

**Krav**

* **Skapa Azure Blob storage-kontot från [Azure-portalen](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Bekräfta att de förinstallerade kommandoradsverktyget AzCopy påträffades vid ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Den katalog som innehåller azcopy.exe finns redan i miljövariabeln PATH för att undvika att skriva den fullständiga kommandosökvägen när du kör det här verktyget. Mer information om AzCopy-verktyget finns i [AzCopy-dokumentationen](../../storage/common/storage-use-azcopy.md)
* Starta verktyget Azure Storage Explorer. Den kan hämtas från [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Flytta data från virtuell dator till Azure Blob: AzCopy**

För att flytta data mellan din lokala filer och blob storage med AzCopy kan du i kommandoraden eller PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Ersätt **C:\myfolder** till den sökväg där filen lagras, **mystorageaccount** till ditt blob storage-kontonamn, **mycontainer** till behållarens namn **lagringskontonyckel** till din åtkomstnyckel för blob storage. Du kan hitta autentiseringsuppgifterna för ditt lagringskonto i [Azure-portalen](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

Kör AzCopy-kommandot i PowerShell eller från en kommandotolk. Här är några exempel på användning av AzCopy-kommandot:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



När du kör AzCopy-kommandot för att kopiera till en Azure-blob, visas din fil som visas i Azure Storage Explorer inom kort.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Flytta data från virtuell dator till Azure Blob: Azure Storage Explorer**

Du kan också ladda upp data från den lokala filen i den virtuella datorn med Azure Storage Explorer:

* Välj en målbehållare för att ladda upp data till en behållare, och klicka på den **överför** knappen.![ Ladda upp i Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Klicka på den **...**  till höger om den **filer** väljer du en eller flera filer att ladda upp från filsystemet och klicka på **överför** att påbörja uppladdningen av filerna.![ Ladda upp filer till blob](./media/vm-do-ten-things/upload-files-to-blob.png)

**Läsa data från Azure Blob: modul för dataläsare för Machine Learning**

Du kan använda i Azure Machine Learning Studio, en **importdata modulen** att läsa data från din blob.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Läsa data från Azure Blob: Python ODBC**

Du kan använda **BlobService** -biblioteket för att läsa data direkt från blob i ett Jupyter-anteckningsbok eller Python.

Först importera paket som krävs:

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random

Sedan ansluter autentiseringsuppgifterna för ditt Azure-Blob och läsa data från Blob:

    CONTAINERNAME = 'xxx'
    STORAGEACCOUNTNAME = 'xxxx'
    STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
    BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
    localfilename = 'trip_data_1.csv'
    LOCALDIRECTORY = os.getcwd()
    LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

    #download from blob
    t1 = time.time()
    blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
    t2 = time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

Data läses i som en dataram:

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage är en storskalig lagringsplats för stordataanalyser och kompatibel med Hadoop Distributed File System (HDFS). Det fungerar med Hadoop, Spark och Azure Data Lake Analytics. I det här avsnittet får du lära dig hur du kan flytta data till Azure Data Lake Store och kör analytics med hjälp av Azure Data Lake Analytics.

**Krav**

* Skapa Azure Data Lake Analytics i [Azure-portalen](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Den **Azure Data Lake Tools** i **Visual Studio** hittades på den här [länk](https://www.microsoft.com/download/details.aspx?id=49504) redan är installerad på den Visual Studio Community Edition som finns på den virtuella datorn. När du startar Visual Studio och loggning i din Azure-prenumeration kan bör du se din Azure Data Analytics-konto och lagringsutrymme i den vänstra panelen av Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Flytta data från virtuell dator till Data Lake: Azure Data Lake Explorer**

Du kan använda **Azure Data Lake Explorer** att överföra data från de lokala filerna i den virtuella datorn till Data Lake-lagring.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Du kan också skapa en datapipeline för att operationalisera dina data flyttas till eller från Azure Data Lake med hjälp av den [Azure Data Factory(ADF)](https://azure.microsoft.com/services/data-factory/). Referera till denna [artikeln](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) att guida dig genom stegen för att skapa data pipelines.

**Läsa data från Azure Blob till Data Lake: U-SQL**

Om dina data finns i Azure Blob storage, kan du direkt läsa data från Azure storage blob i U-SQL-fråga. Kontrollera att blob storage-kontot är länkat till din Azure Data Lake innan du skriver U-SQL-fråga. Gå till **Azure-portalen**, hitta din Azure Data Lake Analytics-instrumentpanelen, klicka på **Lägg till datakälla**, Välj lagringstyp som ska **Azure Storage** och Anslut i Azure Storage-konto Namn och nyckel. Är du kunna referera till de data som lagras i lagringskontot.

![Ange lagringskontot och nyckeln](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

I Visual Studio kan du läsa data från blob storage, göra vissa datamanipulering, funktionstekniker och den resulterande utdata till Azure Data Lake eller Azure Blob Storage. När du refererar till data i blob storage, använda **wasb: / /**; när du refererar till data i Azure Data Lake, Använd **swbhdfs: / /**

![Dataram](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Du kan använda följande U-SQL-frågor i Visual Studio:

    @a =
        EXTRACT medallion string,
                hack_license string,
                vendor_id string,
                rate_code string,
                store_and_fwd_flag string,
                pickup_datetime string,
                dropoff_datetime string,
                passenger_count int,
                trip_time_in_secs double,
                trip_distance double,
                pickup_longitude string,
                pickup_latitude string,
                dropoff_longitude string,
                dropoff_latitude string

        FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
        USING Extractors.Csv();

    @b =
        SELECT vendor_id,
        COUNT(medallion) AS cnt_medallion,
        SUM(passenger_count) AS cnt_passenger,
        AVG(trip_distance) AS avg_trip_dist,
        MIN(trip_distance) AS min_trip_dist,
        MAX(trip_distance) AS max_trip_dist,
        AVG(trip_time_in_secs) AS avg_trip_time
        FROM @a
        GROUP BY vendor_id;

    OUTPUT @b   
    TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
    USING Outputters.Csv();

    OUTPUT @b   
    TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
    USING Outputters.Csv();



När din fråga har skickats till servern, visas ett diagram som visar status för jobbet.

![Status för jobbdiagram](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Fråga efter data i Data Lake: U-SQL**

När datauppsättningen matas in i Azure Data Lake, kan du använda [U-SQL-språket](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) att fråga efter och utforska data. U-SQL-språket liknar T-SQL, men kombinerar vissa funktioner i C# så att användarna kan skriva anpassade moduler, användardefinierade funktioner och osv. Du kan använda skripten i föregående steg.

När frågan har skickats till servern, tripdata_summary. CSV finns snart i **Azure Data Lake Explorer**, du kan förhandsgranska data genom att högerklicka på filen.

![Filen i Azure Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Visa filinformationen:

![Fil-sammanfattning](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop-kluster
Azure HDInsight är en hanterad Apache Hadoop, Spark, HBase och Storm-tjänst i molnet. Du kan arbeta enkelt med Azure HDInsight-kluster från den virtuella datorn för datavetenskap.

**Krav**

* Skapa Azure Blob storage-kontot från [Azure-portalen](https://portal.azure.com). Det här lagringskontot används för att lagra data för HDInsight-kluster.

![Skapa Azure Blob storage-konto](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Anpassa Azure HDInsight Hadoop-kluster från [Azure-portalen](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Länka storage-konto som skapats med ditt HDInsight-kluster när den skapas. Det här lagringskontot används för att komma åt data som kan bearbetas i klustret.

![Länka till storage-konto som skapats med HDInsight-kluster](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Aktivera **fjärråtkomst** till huvudnoden i klustret när den har skapats. Kom ihåg fjärråtkomst-autentiseringsuppgifter som du anger här eftersom du behöver dem i den efterföljande proceduren.

![Aktivera fjärråtkomst](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Skapa en Azure Machine Learning-arbetsyta. Machine Learning-experiment lagras i den här Machine Learning-arbetsytan. Välj de markerade alternativ i portalen, enligt följande skärmbild:

![Skapa en Azure Machine Learning-arbetsyta](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Ange parametrar för din arbetsyta

![Ange parametrar för Machine Learning-arbetsyta](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Överföra data med hjälp av IPython Notebook. Först importera paket som krävs, plugin-autentiseringsuppgifter, skapa en databas i ditt storage-konto och sedan läsa in data till HDI-kluster.

        #Import required Packages
        import pyodbc
        import time as time
        import json
        import os
        import urllib
        import urllib2
        import warnings
        import re
        import pandas as pd
        import matplotlib.pyplot as plt
        from azure.storage.blob import BlobService
        warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
        queryString = "create database if not exists nyctaxidb;"
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.trip
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            rate_code string,
                            store_and_fwd_flag string,
                            pickup_datetime string,
                            dropoff_datetime string,
                            passenger_count int,
                            trip_time_in_secs double,
                            trip_distance double,
                            pickup_longitude double,
                            pickup_latitude double,
                            dropoff_longitude double,
                            dropoff_latitude double)  
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.fare
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            pickup_datetime string,
                            payment_type string,
                            fare_amount double,
                            surcharge double,
                            mta_tax double,
                            tip_amount double,
                            tolls_amount double,
                            total_amount double)
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


* Alternativt kan du följa den här [genomgången](../team-data-science-process/hive-walkthrough.md) överföra NYC Taxi-data till HDI-kluster. Viktigaste stegen är:
  
  * AzCopy: hämta komprimerade CSV från offentlig blob till den lokala mappen
  * AzCopy: ladda upp uppzippade CSV: N från lokal mapp för HDI-kluster
  * Logga in på huvudnoden för Hadoop-kluster och förbereda för undersökande analys

När data har lästs in HDI-kluster, kan du kontrollera dina data i Azure Storage Explorer. Och du har en databas nyctaxidb som skapats i HDI-kluster.

**Datagranskning: Hive-frågor i Python**

Eftersom data är i Hadoop-kluster kan använda du pyodbc-paketet för att ansluta till Hadoop-kluster och fråga databasen med Hive för att göra utforskning och funktionstekniker. Du kan visa de befintliga tabellerna som vi skapade i nödvändiga steg.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Visa befintliga tabeller](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Låt oss titta på hur många poster i varje månad och frekvenser på lutad eller inte i resetabellen:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Exempeldiagram av antalet poster i varje månad](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![Exempeldiagram av tips frekvenser](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Du kan också beräkna avståndet mellan upphämtning och dropoff plats och jämför den med avståndet resa.

    queryString = """
                    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                        *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                        pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                        from nyctaxidb.trip
                        where month=1
                            and pickup_longitude between -90 and -30
                            and pickup_latitude between 30 and 90
                            and dropoff_longitude between -90 and -30
                            and dropoff_latitude between 30 and 90;
                """
    results = pd.read_sql(queryString,connection)
    results.head(5)


![Hämtning och dropoff tabell](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Exempeldiagram av upphämtning/dropoff avståndet till resans avstånd](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nu ska vi förbereda en ned samplas (1%) uppsättning data för modellering. Du kan använda dessa data i modul för dataläsare för Machine Learning.

        queryString = """
        create  table if not exists nyctaxi_downsampled_dataset_testNEW (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\\n'
        stored as textfile;
        """
        cursor.execute(queryString)

        --- now insert contents of the join into the preceding internal table

        queryString = """
        insert overwrite table nyctaxi_downsampled_dataset_testNEW
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
        rand() as sample_key

        from trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01
        """
        cursor.execute(queryString)

Du kan se data har lästs in i Hadoop-kluster efter ett tag:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Tabell med data](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Läsa data från HDI med hjälp av Machine Learning: läsmodulen**

Du kan också använda den **läsare** modul i Machine Learning Studio för att få åtkomst till databasen i Hadoop-kluster. Plugin-autentiseringsuppgifter för HDI-kluster och Azure Storage-konto för att skapa maskininlärningsmodeller med databasen i HDI-kluster.

![Modulen läsaregenskaper](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Poängsatta datauppsättningen kan sedan visas:

![Visa poängsatta datauppsättningen](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse och databaser
Azure SQL Data Warehouse är ett Elastiskt informationslager som en tjänst med SQL Server-miljö i företagsklass.

Du kan etablera Azure SQL Data Warehouse genom att följa instruktionerna i den här [artikeln](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). När du etablerar Azure SQL Data Warehouse kan du använda det här [genomgången](../team-data-science-process/sqldw-walkthrough.md) göra ladda upp data, utforska och modellering med hjälp av data i SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB är en NoSQL-databas i molnet. Det gör att du kan arbeta med dokument som JSON och gör att du kan lagra och fråga efter dokument.

Följande är per komponenter stegen för att få åtkomst till Azure Cosmos DB från DSVM:

1. Azure Cosmos DB Python SDK redan är installerad på DSVM (kör ```pip install pydocumentdb --upgrade``` från Kommandotolken för att uppdatera)
2. Skapa ett Azure Cosmos DB-konto och en databas från [Azure-portalen](https://portal.azure.com)
3. Ladda ned ”Azure Cosmos DB Migreringsverktyget” från [här](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) och extrahera till en valfri katalog
4. Importera JSON-data (vulkanen data) som lagras på en [offentlig blob](https://cahandson.blob.core.windows.net/samples/volcano.json) till Cosmos DB med följande parametrar för att Migreringsverktyget (dtui.exe från den katalog där du installerade Cosmos DB Migration Tool). Ange platsen för källa och mål med följande parametrar:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

När du importerar data kan du gå till Jupyter och öppna anteckningsboken med rubriken *DocumentDBSample* som innehåller python-kod för åtkomst till Azure Cosmos DB och utföra vissa grundläggande frågor. Du kan lära dig mer om Cosmos DB genom att gå till tjänsten [dokumentationssidan](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Skapa rapporter och instrumentpanel med hjälp av Power BI Desktop
Du kan visualisera vulkanen JSON-filen från föregående Cosmos DB-exempel i Power BI visuella insikter om data. Detaljerade anvisningar finns i den [Power BI-artikeln](../../cosmos-db/powerbi-visualize.md). Här är de övergripande stegen:

1. Öppna Power BI Desktop och ”hämta Data”. Ange URL: en som: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Du bör se JSON-poster som har importerats som en lista
3. Konvertera listan till en tabell så att Power BI kan arbeta med samma
4. Expandera kolumnerna genom att klicka på ikonen (ett med ikonen ”pilen till vänster och en högerpil” till höger om kolumnen)
5. Observera att platsen är ett ””-postfält. Visa posten och välj endast koordinaterna. Koordinaten är en kolumn
6. Lägg till en ny kolumn för att konvertera koordinaten listkolumnen till en kommaavgränsad separat LatLong kolumn sammanfoga två element i fältet koordinaten lista med formeln ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Slutligen konvertera den ```Elevation``` kolumnen till decimaltal och välj den **Stäng** och **tillämpa**.

I stället för föregående steg, kan du klistra in följande kod att skript ut de steg som används i avancerade redigeraren i Power BI där du kan skriva dataomvandlingarna i ett frågespråk.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Nu har du data i Power BI-datamodell. Power BI desktop ska visas på följande sätt:

![Power BI desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Du kan börja skapa rapporter och visualiseringar med datamodellen. Du kan följa stegen i den här [Power BI-artikeln](../../cosmos-db/powerbi-visualize.md#build-the-reports) att skapa en rapport. Utdata är en rapport som ser ut som följande.

![Power BI Desktop rapportvyn - anslutningsprogrammet för Powerbi](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Dynamiskt skala din DSVM efter dina behov för projektet
Du kan skala upp och ned DSVM som uppfyller dina behov för projektet. Om du inte behöver använda den virtuella datorn på kvällar eller helger, kan du bara att stänga av den virtuella datorn från den [Azure-portalen](https://portal.azure.com).

> [!NOTE]
> Du debiteras beräkning om du använder bara operativsystemet knappen Avsluta på den virtuella datorn.  
> 
> 

Om du behöver att hantera storskaliga analyser och behöver mer CPU eller minne eller disk-kapacitet kan du hitta ett stort urval av VM-storlekar när det gäller processorkärnor, GPU-baserade instanser för djupinlärning, minneskapaciteten och disktyper (inklusive SSD-enheter) som uppfyller dina beräknings- och budgeten behov. En fullständig lista över virtuella datorer tillsammans med deras per timme beräkningspriser är tillgänglig på den [prissättning för Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) sidan.

På samma sätt, om ditt behov av VM-bearbetningskapacitet minskar (till exempel: du flyttat en större arbetsbelastning till ett Hadoop- eller ett Spark-kluster), du kan skala ned klustret från den [Azure-portalen](https://portal.azure.com) och gå till inställningar för VM-instansen. Här är en skärmbild.

![Inställningar för VM-instans](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Installera ytterligare verktyg på den virtuella datorn
Det finns flera verktyg som är färdiga i DSVM som kan lösa många av de vanliga data analytics behöver. Detta sparar tid genom att inte behöva installera och konfigurera dina miljöer i taget och spara pengar genom att betala endast för resurser som du använder.

Du kan använda andra tjänster för Azure och analys som har profileras i den här artikeln för att förbättra miljön analytics. I vissa fall kanske du behöver ytterligare verktyg, bland annat vissa tillverkarspecifika verktyg från tredje part. Du har fullständig administratörsbehörighet på den virtuella datorn att installera nya verktyg som du behöver. Du kan också installera ytterligare paket i Python- och R som inte redan är installerad. För Python kan du använda antingen ```conda``` eller ```pip```. Du kan använda för R i ```install.packages()``` i R-konsolen eller Använd IDE och välj ”**paket** -> **installera paket...** ".

## <a name="summary"></a>Sammanfattning
Det här är några av de saker du kan göra på Microsoft Data Science Virtual Machine. Det finns många fler saker som du kan göra så att den blir en effektiv analytics-miljö.

