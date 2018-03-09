---
title: "Tio saker som du kan göra på den datavetenskap virtuella datorn i Azure | Microsoft Docs"
description: "Utför olika datagranskning och modellering aktivitet på datavetenskap virtuella datorn."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: gokuma;weig;bradsev
ms.openlocfilehash: 71745260f45c5a5c150fdd3eac4e9a8221b8bce4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="ten-things-you-can-do-on-the-data-science-virtual-machine"></a>Tio saker som du kan göra med datavetenskap, virtuell dator

Microsoft Data vetenskap virtuell dator (DSVM) är en kraftfull datavetenskap utvecklingsmiljö där du kan utföra olika uppgifter för data från kartläggning av naturresurser och modellering. Miljön kommer redan inbyggda och anpassade med flera populära data analytics verktyg som gör det lättare att snabbt komma igång med din analys för lokalt, molnet eller hybriddistribution. DSVM användas tillsammans med många Azure-tjänster och kan läsa och bearbeta data som redan har sparats på Azure, Azure SQL Data Warehouse, Azure Data Lake, Azure Storage eller Azure Cosmos DB. Det kan också använda andra verktyg för analys, till exempel Azure Machine Learning och Azure Data Factory.

I den här artikeln får du lära dig hur du använder din DSVM att utföra olika uppgifter i datavetenskap och interagera med andra Azure-tjänster. Här följer några av de saker som du kan göra på DSVM:

1. Utforska data och utveckla modeller lokalt på DSVM med hjälp av Microsoft ML Server, Python
2. Använd en Jupyter-anteckningsbok för att experimentera med dina data i en webbläsare med Python 2, 3 Python Microsoft R redo enterprise-version av R som utformats för prestanda
3. Distribuera modeller som skapats med R och Python i Azure Machine Learning så att klientprogram kan komma åt modeller med hjälp av ett enkelt Webbgränssnitt för tjänsten
4. Administrera Azure-resurser med Azure-portalen eller Powershell
5. Utöka ditt lagringsutrymme och dela stora datauppsättningar / kod över hela teamet genom att skapa ett Azure File storage som en enhet som kan monteras på din DSVM
6. Dela kod med din grupp med GitHub och komma åt databasen med förinstallerade Git klienterna - Git Bash Git GUI.
7. Komma åt olika Azure data och analyser tjänster som Azure blob storage, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB Azure SQL Data Warehouse och databaser
8. Skapa rapporter och instrumentpanel med hjälp av Power BI Desktop förinstallerat på DSVM och distribuera dem till molnet
9. Dynamiskt skala din DSVM dina projekt behov
10. Installera ytterligare verktyg på den virtuella datorn   

> [!NOTE]
> Extra kostnader gäller för många ytterligare data storage analytics tjänsterna och i den här artikeln. Referera till den [priser för Azure](https://azure.microsoft.com/pricing/) information på sidan.
> 
> 

**Förutsättningar**

* Du behöver en Azure-prenumeration. Du kan registrera dig för en kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/free/).
* Instruktioner för att etablera en datavetenskap virtuell dator på Azure portal finns på [skapar en virtuell dator](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Utforska data och utveckla modeller med hjälp av Microsoft ML Server eller Python
Du kan använda språk som R och Python för att göra din dataanalys åt höger på DSVM.

Du kan använda som RStudio som finns på start-menyn eller skrivbordet eller R verktyg för Visual Studio IDE-miljö för R. Microsoft tillhandahåller ytterligare bibliotek ovanpå Öppna-källa/CRAN-R aktivera skalbara analyser och möjligheten att analysera data som är större än minnesstorleken tillåts genom att göra parallella chunked analys. 

För Python, kan du använda IDE som Visual Studio Community Edition, som har Python Tools för Visual Studio (PTVS)-tillägget förinstallerat. Som standard endast Python 3,6 konfigureras rot conda miljö på PTVS. För att aktivera Anaconda Python 2.7, måste du göra följande:

* Skapa anpassade miljöer för varje version genom att gå till **verktyg** -> **Python Tools** -> **Python-miljöer** och sedan klicka på ” **+ Anpassad**”i Visual Studio 2015 Community Edition
* Ge en beskrivning och ange sökvägen till miljön prefix som *c:\anaconda\envs\python2* för Anaconda Python 2.7
* Klicka på **automatisk identifiering** och sedan **tillämpa** spara miljön.

Här är inställningen anpassad miljö ser ut i Visual Studio.

![PTVS installationen](./media/vm-do-ten-things/PTVSSetup.png)

Finns det [dokumentationen till PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) för ytterligare information om hur du skapar Python-miljöer.

Du är nu ställa in för att skapa ett nytt Python-projekt. Gå till **filen** -> **ny** -> **projekt** -> **Python** och välj typ av Python-program som du skapar. Du kan ange Python-miljön för det aktuella projektet till den önskade versionen (Anaconda 2.7 eller 3,6) genom att högerklicka på den **Python-miljö**, välja **Lägg till/ta bort Python-miljöer**, och sedan Plocka för önskad miljö. Du hittar mer information om hur du arbetar med PTVS produkten [dokumentationen](https://github.com/Microsoft/PTVS/wiki) sidan.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Med hjälp av en Jupyter-anteckningsbok att utforska och modelldata med Python eller R
Jupyter-anteckningsbok är en kraftfull miljö som tillhandahåller en webbaserat ”IDE” för modellering och undersökning av data. Du kan använda Python 2, Python 3 eller R (öppen källkod och Microsoft R Server) i en Jupyter-anteckningsbok.

Klicka på ikonen start-menyn om du vill starta Jupyter-anteckningsbok / skrivbordsikon med titeln **Jupyter-anteckningsbok**. Kommandotolken DSVM du kan också köra kommandot ```jupyter notebook``` från katalogen där du har befintliga bärbara eller om du vill skapa nya bärbara datorer.  

När du har startat Jupyter bör du se en katalog som innehåller några exempel bärbara datorer är förhand packade till DSVM. Nu kan du:

* Klicka på anteckningsboken för att se koden.
* Köra varje cell genom att trycka på **SKIFT-ange**.
* Kör anteckningsboken genom att klicka på **Cell** -> **kör**
* Skapa en ny anteckningsbok genom att klicka på ikonen Jupyter (övre vänstra hörnet) och sedan klicka på **ny** knappen till höger och sedan välja språk för bärbara datorer (även kallat kärnor).   

> [!NOTE]
> Python 2.7, Python 3,6, R, Julia och PySpark-kernel i Jupyter stöds för närvarande. R-kernel stöder programmering i både öppen källkod R samt performant Microsoft R.   
> 
> 

När du är i den bärbara datorn som du kan utforska dina data, skapa modellen, testa modellen med ditt val av bibliotek.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Bygga modeller med R eller Python och Operationalize dem med hjälp av Azure Machine Learning
När du har skapat och verifiera din modell är vanligtvis nästa steg att distribuera till produktionen. Detta gör att ditt klientprogram att anropa förutsägelser för modellen på en realtid eller på grundval av batch-läge. Azure Machine Learning ger dig möjlighet att operationalisera en modell som skapats i R eller Python.

När du driftsätta modellen i Azure Machine Learning exponeras en webbtjänst som tillåter klienter att REST-anrop som skickar i indataparametrar och ta emot förutsägelser från modellen som utdata.   

> [!NOTE]
> Om du inte har har registrerat dig för Azure Machine Learning, kan du hämta en kostnadsfri arbetsyta eller en standard arbetsyta genom att besöka den [Azure Machine Learning Studio](https://studio.azureml.net/) startsidan och klicka på ”Kom igång”.   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Bygg- och Operationalisera Python modeller
Här är en kodavsnittet utvecklats i en Python Jupyter-anteckningsbok som skapar en enkel modell med SciKit Läs-biblioteket.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

Metoden som används för att distribuera modeller python till Azure Machine Learning radbryter förutsägelser av modellen i en funktion och decorates med attribut som tillhandahålls av förinstallerad Azure Machine Learning python-bibliotek som anger Azure Machine Learning Arbetsyte-ID, API-nyckel och indata- och returnera parametrar.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

En klient kan nu göra anrop till webbtjänsten. Det finns bekvämlighet omslutningar som skapar REST API-begäranden. Här är en exempelkod för att använda webbtjänsten.

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

### <a name="build-and-operationalize-r-models"></a>Bygg- och Operationalisera R modeller
Du kan distribuera R modeller som bygger på datavetenskap virtuella datorn eller någon annanstans på Azure Machine Learning på ett sätt som liknar hur du gör för Python. Här är stegen:

* Skapa en settings.json-fil för att ge ditt arbetsyte-ID och auth token 
* skriva en wrapper för modellens förutsäga funktion.
* anropa ```publishWebService``` i Azure Machine Learning-biblioteket för att skicka in funktionen omslutning.  

Det här är de proceduren och koden kodavsnitt som kan användas för att konfigurera, skapa, publicera och använda en modell som en webbtjänst i Azure Machine Learning.

#### <a name="setup"></a>Konfiguration

* Skapa en settings.json fil under en katalog med namnet ```.azureml``` under arbetskatalogen och ange parametrar från Azure Machine Learning-arbetsytan:

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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Använda modellen distribueras i Azure Machine Learning
Om du vill använda modellen från ett klientprogram vi använda Azure Machine Learning-biblioteket för att leta upp tjänsten publicerade webbprogram med hjälp av namnet på `services` API-anrop för att fastställa slutpunkten. Du bara anropa den `consume` fungerar och skicka in dataramen till förutsägas.
Följande kod används för att använda den modell som publiceras som en Azure Machine Learning-webbtjänst.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Mer information om Azure Machine Learning R-biblioteket finns [här](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Administrera Azure-resurser med Azure-portalen eller Powershell
DSVM inte bara kan du skapa din lösning för analytics lokalt på den virtuella datorn, men ger dig möjlighet att komma åt tjänster på Microsoft Azure-molnet. Azure tillhandahåller flera beräkning, lagring, data Analystjänster och andra tjänster som du kan administrera och åtkomst till från din DSVM.

Administrera Azure-prenumeration och moln-resurser du kan använda din webbläsare och peka på den [Azure-portalen](https://portal.azure.com). Du kan också använda Azure Powershell för att administrera Azure-prenumeration och resurser via ett skript.
Du kan köra Azure Powershell från en genväg på skrivbordet eller från start-menyn med rubriken ”Microsoft Azure Powershell”. Referera till [dokumentation för Microsoft Azure Powershell](../../powershell-azure-resource-manager.md) mer information om hur du kan administrera din Azure-prenumeration och resurser med hjälp av Windows Powershell-skript.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Utöka ditt lagringsutrymme med ett delat filsystem
Datavetare kan dela stora datamängder, kod eller andra resurser i teamet. DSVM själva har 45GB diskutrymme. Om du vill förlänga din lagring, kan du använda tjänsten för Azure och antingen montera den på en eller flera instanser av DSVM eller få åtkomst till den via ett REST-API.  Du kan också använda [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) eller använda [Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) att lägga till extra dedikerade datadiskar. 

> [!NOTE]
> Maximalt utrymme för Azures Filtjänst resursen är 5 TB storleksgränsen för enskilda filer är 1 TB. 
> 
> 

Du kan använda Azure Powershell för att skapa en Azure File Service-resurs. Här är skriptet som ska köras under Azure PowerShell för att skapa en Azure-tjänsten fildelning.

    # Authenticate to Azure.
    Login-AzureRmAccount
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


Nu när du har skapat en Azure-filresurs kan montera du den i en virtuell dator i Azure. Vi rekommenderar starkt att den virtuella datorn är i samma Azure-datacenter som lagringskontot för att undvika fördröjning och data transfer debiteringar. Här är att montera enheten på DSVM som du kan köra på Azure Powershell-kommandon.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Du kan nu komma åt enheten precis som med alla vanliga enheter på den virtuella datorn.

## <a name="6-share-code-with-your-team-using-github"></a>6. Dela kod med din grupp med GitHub
GitHub är en kod lagringsplats där du kan hitta många exempelkod och datakällor för olika verktyg med olika teknologier som delas av utvecklare. Git används som tekniken för att spåra och lagra versioner av kodfilerna. GitHub är också en plattform där du kan skapa egna lagringsplatsen för att lagra din grupps delad kod och dokumentation, implementera versionskontroll och också styra som har behörighet att visa och bidra med kod. Besök den [GitHub hjälpsidor](https://help.github.com/) för mer information om hur du använder Git. Du kan använda GitHub som ett sätt att samarbeta med din grupp, använda koden som har utvecklats av gemenskapen och bidra kod tillbaka till gruppen.

DSVM levereras inlästa med klientverktyg på både kommandoradsverktyg som korrekt GUI till GitHub-lagringsplatsen. Kommandoradsverktyg för att arbeta med Git och GitHub kallas Git Bash. Visual Studio installerat på DSVM har Git-tillägg. Du kan hitta uppstart ikoner för de här verktygen på start-menyn och skrivbordet.

Om du vill ladda ned koden från en GitHub-databas som du använder den ```git clone``` kommando. Till exempel för att hämta datavetenskap databasen som publicerats av Microsoft i den aktuella katalogen du kan köra följande kommando när du är i ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

I Visual Studio kan du göra samma åtgärd för kloning. Följande skärmbild visar hur du kommer åt Git och GitHub verktyg i Visual Studio.

![Git i Visual Studio](./media/vm-do-ten-things/VSGit.PNG)

Du hittar mer information om hur du använder Git för att arbeta med GitHub-lagringsplats från flera resurser som är tillgängliga på github.com. Den [fusklapp](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) är en bra referens.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Komma åt olika Azure data och analytics-tjänster
### <a name="azure-blob"></a>Azure-blobb
Azure blob är en tillförlitlig, ekonomiska molnlagring för data stora och små. Det här avsnittet beskrivs hur du kan flytta data till Azure Blob och åtkomst till data som lagras i Azure-Blob.

**Krav**

* **Skapa Azure Blob storage-konto från [Azure-portalen](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Bekräfta att det förinstallerade kommandoradsverktyget AzCopy påträffades vid ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Den katalog som innehåller azcopy.exe finns redan i PATH-miljövariabeln att undvika att skriva hela kommandosökvägen när du kör det här verktyget. Mer information om verktyget AzCopy avser [AzCopy-dokumentationen](../../storage/common/storage-use-azcopy.md)
* Starta verktyget Azure Lagringsutforskaren. Du kan hämta från [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Flytta data från virtuella datorer till Azure Blob: AzCopy**

Om du vill flytta data mellan din lokala filer och blob-lagring kan du använda AzCopy i kommandoraden eller PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Ersätt **C:\myfolder** till sökvägen där filen lagras **mittlagringskonto** till ditt blob storage-kontonamn, **minbehållare** att behållarens namn **lagringskontonyckel** till din lagringsåtkomstnyckel för blob. Du kan hitta autentiseringsuppgifterna för ditt lagringskonto i [Azure-portalen](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

Kör AzCopy-kommandot i PowerShell eller från en kommandotolk. Här är några exempel på användning av AzCopy-kommandot:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



När du kör AzCopy-kommandot för att kopiera till en Azure blob finns du filen visas i Azure Lagringsutforskaren inom kort.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Flytta data från virtuella datorer till Azure Blob: Azure Lagringsutforskaren**

Du kan också ladda upp data från den lokala filen i den virtuella datorn med Azure Lagringsutforskaren:

* Markera Målbehållaren som för att överföra data till en behållare, och klicka på den **överför** knappen.![ Ladda upp i Lagringsutforskaren](./media/vm-do-ten-things/storage-accounts.png)
* Klicka på den **...**  till höger om den **filer** väljer du en eller flera filer som ska överföra från filsystemet och klicka på **överför** att börja överföra filerna.![ Ladda upp filer till blob](./media/vm-do-ten-things/upload-files-to-blob.png)

**Läsa data från Azure Blob: modul för dataläsare för Machine Learning**

I Azure Machine Learning Studio, kan du använda en **importera Data modulen** att läsa data från din blob.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Läsa data från Azure Blob: Python ODBC**

Du kan använda **BlobService** biblioteket för att läsa data direkt från blob i ett Jupyter-anteckningsbok eller Python.

Först importera nödvändiga paket:

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

Sedan ansluter dina Azure Blob-autentiseringsuppgifter och läsa data från Blob:

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

Data läses i som en data-ram:

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage är en storskalig lagringsplats för analytics stordataarbetsbelastningar och kompatibla med Hadoop Distributed File System (HDFS). Den fungerar med Hadoop, Spark och Azure Data Lake Analytics. I det här avsnittet får du lära dig hur du kan flytta data till Azure Data Lake Store och kör analytics med hjälp av Azure Data Lake Analytics.

**Krav**

* Skapa Azure Data Lake Analytics i [Azure-portalen](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Den **Azure Data Lake-verktyg** i **Visual Studio** hittades på den här [länk](https://www.microsoft.com/download/details.aspx?id=49504) redan är installerad på den Visual Studio Community Edition som finns på den virtuella datorn. När du startar Visual Studio och loggning i din Azure-prenumeration kan bör du se din Azure Data Analytics-konto och lagringsutrymme i den vänstra panelen av Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Flytta data från VM till Data Lake: Azure Data Lake Explorer**

Du kan använda **Azure Data Lake Explorer** att överföra data från lokala filer i den virtuella datorn till Data Lake-lagring.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Du kan också skapa en data-pipeline för att operationalisera dina data flyttas till eller från Azure Data Lake med hjälp av den [Azure Data Factory(ADF)](https://azure.microsoft.com/services/data-factory/). Referera till den här [artikel](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) och vägleder dig genom stegen för att skapa data rörledningar.

**Läsa data från Azure Blob till Data Lake: U-SQL**

Om dina data finns i Azure Blob storage, kan du direkt läsa data från Azure storage blob i U-SQL-frågan. Kontrollera att blob storage-konto som är kopplad till ditt Azure Data Lake innan fastställdes U-SQL-fråga. Gå till **Azure-portalen**, hitta din Azure Data Lake Analytics-instrumentpanelen, klicka på **Lägg till datakälla**, Välj lagringstyp som ska **Azure Storage** och Anslut i Azure Storage-konto Namn och nyckel. Är du kan referera till data som lagras i lagringskontot.

![Ange storage-konto och nyckel](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

I Visual Studio kan du läsa data från blob storage, göra vissa datamanipulering, egenskapsval och spara resulterande data till Azure Data Lake eller Azure Blob Storage. När du refererar till data i blob storage, använda **wasb: / /**; när du refererar till data i Azure Data Lake, Använd **swbhdfs: / /**

![Data-ram](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

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



När frågan har skickats till servern, visas ett diagram som visar status för jobbet.

![Jobbet status diagram](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Fråga efter data i Data Lake: U-SQL**

När dataset inhämtas i Azure Data Lake, kan du använda [U-SQL-språket](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) att fråga efter och utforska data. U-SQL-språket liknar T-SQL, men kombinerar vissa funktioner från C# så att användarna kan skriva anpassade moduler, användardefinierade funktioner och osv. Du kan använda skripten i föregående steg.

När frågan har skickats till servern, tripdata_summary. CSV kan hittas inom kort i **Azure Data Lake Explorer**, du kan förhandsgranska data genom att högerklicka på filen.

![Filen i Azure Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Visa filinformation:

![Filen sammanfattning](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop Clusters
Azure HDInsight är en hanterad Apache Hadoop, Spark, HBase eller Storm-tjänst i molnet. Du kan arbeta enkelt med Azure HDInsight-kluster från datavetenskap virtuell dator.

**Krav**

* Skapa Azure Blob storage-konto från [Azure-portalen](https://portal.azure.com). Det här lagringskontot används för att lagra data för HDInsight-kluster.

![Skapa Azure Blob storage-konto](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Anpassa Azure HDInsight Hadoop-kluster från [Azure-portalen](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Länka lagringskonto som skapats med ditt HDInsight-kluster när den skapas. Det här lagringskontot används för att komma åt data som kan bearbetas i klustret.

![Länka till lagringskonto som skapats med HDInsight-kluster](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Aktivera **fjärråtkomst** till huvudnod i klustret när den har skapats. Kom ihåg autentiseringsuppgifterna för fjärråtkomst som du anger här eftersom du behöver dem i följande procedur.

![Aktivera fjärråtkomst](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Skapa en Azure Machine Learning-arbetsytan. Din Maskininlärningsexperiment lagras i Machine Learning-arbetsytan. Välj de markerade alternativen i portalen som visas i följande skärmbild:

![Skapa en Azure Machine Learning-arbetsyta](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Ange parametrar för din arbetsyta

![Ange parametrar för Machine Learning-arbetsytan](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Överföra data med hjälp av IPython bärbar dator. Först importera nödvändiga paket, plugin-autentiseringsuppgifter, skapa en db i ditt lagringskonto och läsa in data till HDI-kluster.

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


* Alternativt kan du följa detta [genomgången](../team-data-science-process/hive-walkthrough.md) överföra NYC Taxi data till HDI-klustret. Större stegen innefattar:
  
  * AzCopy: hämta komprimerade CSV från offentliga blob till den lokala mappen
  * AzCopy: Överför uppackade CSV: N från en lokal mapp till HDI-kluster
  * Logga in på huvudnod Hadoop-kluster och förbereda för undersökande dataanalys

Du kan kontrollera dina data i Azure Lagringsutforskaren när data läses till HDI-klustret. Och du har en databas nyctaxidb som skapats i HDI-klustret.

**Datagranskning: Hive-frågor i Python**

Eftersom data i Hadoop-kluster kan använda du paketet pyodbc för att ansluta till Hadoop-kluster och fråga databas med hjälp av Hive för att göra undersökning och egenskapsval. Du kan visa de befintliga tabellerna som vi skapade i steget nödvändiga.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Visa befintliga tabeller](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Nu ska vi titta på hur många poster i varje månad och frekvenser för lutad eller inte i tabellen resa:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Område för antalet poster i varje månad](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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


![Ritytans tips frekvenser](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Du kan även beräkna avståndet mellan där den ska hämtas och dropoff plats och jämför den med resa avståndet.

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


![Område för hämtning/dropoff avståndet i resa avstånd](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nu ska vi förbereder en ned provtagning (% 1) uppsättning data för modellering. Du kan använda dessa data i Machine Learning reader modul.

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

**Läsa data från HDI med Machine Learning: modul för dataläsare**

Du kan också använda den **reader** modul i Machine Learning Studio för att få åtkomst till databasen i Hadoop-kluster. Anslut med autentiseringsuppgifterna för HDI-kluster och Azure Storage-konto för att skapa maskininlärning modeller som använder databasen i HDI-kluster.

![Läsaren modulen egenskaper](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Poängsatta dataset kan sedan visas:

![Visa bedömas dataset](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse och databaser
Azure SQL Data Warehouse är ett datalager för elastiska som en tjänst med företagsklass SQL Server-miljön.

Du kan etablera din Azure SQL Data Warehouse genom att följa anvisningarna i det här [artikel](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). När du distribuera din Azure SQL Data Warehouse, kan du använda den [genomgången](../team-data-science-process/sqldw-walkthrough.md) att göra Datahämtningen undersökning och modellering med data i SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos-DB är en NoSQL-databas i molnet. Det kan du arbeta med som JSON-dokument och gör att du kan lagra och fråga dokumenten.

Följande är per kraven stegen för att komma åt Azure Cosmos DB från DSVM:

1. Azure Cosmos DB Python SDK är redan installerad på DSVM (kör ```pip install pydocumentdb --upgrade``` från Kommandotolken för att uppdatera)
2. Skapa ett Azure DB som Cosmos-konto och en databas från [Azure-portalen](https://portal.azure.com)
3. Ladda ned ”Azure Cosmos DB Migreringsverktyget” från [här](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) och extrahera till en katalog önskat
4. Importera JSON-data (vulkanen data) lagras på en [offentlig blob](https://cahandson.blob.core.windows.net/samples/volcano.json) i Cosmos DB med följande parametrar för att Migreringsverktyget (dtui.exe från den katalog där du installerade Cosmos DB Migration Tool). Ange platsen för källan och målet med följande parametrar:
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

När du importerar data, kan du gå till Jupyter och öppna den bärbara datorn med namnet *DocumentDBSample* som innehåller python-kod för åtkomst till Azure Cosmos DB och utföra vissa grundläggande frågor. Du kan lära dig mer om Cosmos DB genom att gå till tjänsten [dokumentationssidan](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Skapa rapporter och instrumentpanel med hjälp av Power BI Desktop
Du kan visualisera vulkanen JSON-filen i föregående exempel Cosmos-DB i Power BI och få visual insikter om data. Detaljerade anvisningar finns i den [Power BI-artikel](../../cosmos-db/powerbi-visualize.md). Här följer de övergripande stegen:

1. Öppna Power BI Desktop och ”hämta Data”. Ange en Webbadress som: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Du bör se JSON-poster som har importerats som en lista
3. Konvertera listan till en tabell så att Power BI kan arbeta med samma
4. Expandera kolumner genom att klicka på plustecknet (ett med ”vänsterpilen och en högerpil”-ikonen till höger om kolumnen)
5. Observera att platsen är ett ”post”-fält. Expandera posten och välj endast koordinater. Koordinaten är en kolumn
6. Lägg till en ny kolumn konvertera koordinaten listkolumnen till en kommatecken separat LatLong kolumn sammanfoga två element i fältet koordinaten lista med formeln för ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Slutligen konvertera den ```Elevation``` kolumn till Decimal och välj den **Stäng** och **tillämpa**.

Du kan klistra in följande kod att skript ut de steg som används i avancerade redigeraren i Power BI kan du skriva omvandlingarna data i ett frågespråk i stället för föregående steg.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Nu har du data i Power BI datamodellen. Power BI desktop ska visas på följande sätt:

![Power BI desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Du kan börja skapa rapporter och visualiseringar med hjälp av datamodellen. Du kan följa stegen i den här [Power BI-artikel](../../cosmos-db/powerbi-visualize.md#build-the-reports) att skapa en rapport. Resultatet är en rapport som liknar följande.

![Power BI Desktop rapportvyn - anslutningsprogrammet för Powerbi](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Dynamiskt skala din DSVM dina projekt behov
Du kan skala uppåt och nedåt DSVM dina projekt-behov. Om du inte behöver använda den virtuella datorn i kväll eller helger, kan du bara att stänga av den virtuella datorn från den [Azure-portalen](https://portal.azure.com).

> [!NOTE]
> Du betalar avgifter för beräkning om du använder bara operativsystemet knappen Avsluta på den virtuella datorn.  
> 
> 

Om du behöver hantera vissa storskaliga analys och behöver mer CPU eller minne eller disk kapacitet hittar du ett stort urval av VM-storlekar som CPU-kärnor, GPU-baserad instanser för djup learning, minneskapacitet och disktyper (inklusive SSD-enheter) som uppfyller dina beräknings- och budgeten behov. En fullständig lista över virtuella datorer tillsammans med deras timvis beräkning prisnivå är tillgängligt på den [priser för Azure virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) sidan.

På samma sätt om minskar behovet av VM-bearbetningskapacitet (till exempel: du har flyttat en större arbetsbelastning på en Hadoop eller ett Spark-kluster), du kan skala ned klustret från den [Azure-portalen](https://portal.azure.com) och gå till inställningarna för VM-instans. Här är en skärmbild.

![Inställningar för VM-instans](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Installera ytterligare verktyg på den virtuella datorn
Det finns flera verktyg som är inbyggd i DSVM som kan lösa många av de vanliga data analytics behöver. Detta sparar tid genom att undvika att behöva installera och konfigurera dina miljöer i taget och spara pengar genom att betala endast för resurser som du använder.

Du kan använda andra Azure data och analytics-tjänster i listan i den här artikeln för att förbättra din analytics-miljö. I vissa fall kanske du behöver ytterligare verktyg, bland annat vissa egna verktyg från tredje part. Du har fullständig administrativ åtkomst på den virtuella datorn att installera nya verktyg som du behöver. Du kan också installera ytterligare paket i Python och R som inte redan är installerad. För Python kan du använda antingen ```conda``` eller ```pip```. Du kan använda för R i ```install.packages()``` i R-konsolen eller använda IDE och välj ”**paket** -> **paket installeras...** ".

## <a name="summary"></a>Sammanfattning
Det är några av de saker som du kan göra på Microsoft datavetenskap Virtual Machine. Det finns många saker du kan göra så att den blir en effektiv analytics-miljö.

