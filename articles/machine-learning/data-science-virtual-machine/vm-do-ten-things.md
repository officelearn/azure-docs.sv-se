---
title: Utforska data och modeller i Windows
titleSuffix: Azure Data Science Virtual Machine
description: Utför datautforskning och modelleringsuppgifter på den virtuella datorn för Data Science.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8da8cd7110cd17d0aadd52cce1263c7c0fcfdf5c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632160"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Tio saker du kan göra på Virtual Machine för Windows Data Science

Windows Data Science Virtual Machine (DSVM) är en kraftfull utvecklingsmiljö för datavetenskap där du kan utföra datautforskning och modelleringsuppgifter. Miljön levereras redan och levereras med flera populära dataanalysverktyg som gör det enkelt att komma igång med din analys för lokala distributioner, moln- eller hybriddistributioner. 

DSVM har ett nära samarbete med Azure-tjänster. Den kan läsa och bearbeta data som redan lagras på Azure, i Azure SQL Data Warehouse, Azure Data Lake, Azure Storage eller Azure Cosmos DB. Det kan också dra nytta av andra analysverktyg, till exempel Azure Machine Learning och Azure Data Factory.

I den här artikeln får du lära dig hur du använder din DSVM för att utföra datavetenskapsuppgifter och interagera med andra Azure-tjänster. Här är några av de saker du kan göra på DSVM:

- Utforska data och utveckla modeller lokalt på DSVM med hjälp av Microsoft Machine Learning Server och Python.
- Använd en Jupyter-anteckningsbok för att experimentera med dina data i en webbläsare med hjälp av Python 2, Python 3 och Microsoft R. (Microsoft R är en företagsklar version av R som utformats för prestanda.)
- Distribuera modeller som skapats via R och Python på Azure Machine Learning så att klientprogram kan komma åt dina modeller med hjälp av ett enkelt webbtjänstgränssnitt.
- Administrera dina Azure-resurser med hjälp av Azure-portalen eller PowerShell.
- Utöka lagringsutrymmet och dela storskaliga datauppsättningar/kod i hela teamet genom att skapa en Azure Files-resurs som en monteringsbar enhet på din DSVM.
- Dela kod med ditt team med hjälp av GitHub. Öppna databasen med hjälp av de förinstallerade Git-klienterna: Git Bash och Git GUI.
- Få åtkomst till Azure-data- och analystjänster som Azure Blob storage, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse och Azure SQL Database.
- Skapa rapporter och en instrumentpanel med hjälp av Power BI Desktop-instansen som är förinstallerad på DSVM och distribuera dem i molnet.
- Skala din DSVM dynamiskt så att den uppfyller projektets behov.
- Installera ytterligare verktyg på den virtuella datorn.   

> [!NOTE]
> Ytterligare användningsavgifter tillkommer för många av de datalagrings- och analystjänster som anges i den här artikeln. Mer information finns på [sidan Azure-priser.](https://azure.microsoft.com/pricing/)
> 
> 

## <a name="prerequisites"></a>Krav

* Du behöver en Azure-prenumeration. Du kan [registrera dig för en gratis provperiod](https://azure.microsoft.com/free/).
* Instruktioner för att etablera en virtuell datavetenskapdator på Azure-portalen är tillgängliga i [Skapa en virtuell dator](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Utforska data och utveckla modeller med Microsoft Machine Learning Server
Du kan använda språk som R och Python för att göra dataanalys direkt på DSVM.

För R kan du använda en IDE som RStudio som finns på startmenyn eller på skrivbordet. Eller så kan du använda R Tools för Visual Studio. Microsoft har tillhandahållit ytterligare bibliotek ovanpå cran r-r med öppen källkod för att möjliggöra skalbar analys och möjligheten att analysera data som är större än den minnesstorlek som tillåts i parallell chunked-analys. 

För Python kan du använda en IDE som Visual Studio Community Edition, som har tillägget Python Tools for Visual Studio (PTVS) förinstallerat. Som standard är endast Python 3.6, roten Conda-miljön, konfigurerad på PTVS. Så här aktiverar du Anaconda Python 2.7:

1. Skapa anpassade miljöer för varje version genom att gå till **Python** > **Tools** > **Python-miljöer**och sedan välja + **Anpassad** i Visual Studio Community Edition.
1. Ge en beskrivning och ange miljöprefixsökvägen som **c:\anaconda\envs\python2** för Anaconda Python 2.7.
1. Välj **Identifiera automatiskt** > **om** du vill spara miljön.

Mer information om hur du skapar Python-miljöer finns i [PTVS-dokumentationen.](https://aka.ms/ptvsdocs)

Nu är du inställd på att skapa ett nytt Python-projekt. Gå till **File** > **New** > **Project** > **Python** och välj vilken typ av Python-program du skapar. Du kan ställa in Python-miljön för det aktuella projektet till önskad version (Python 2.7 eller 3.6) genom att högerklicka på **Python-miljöer** och sedan välja **Lägg till/ta bort Python-miljöer**. Mer information om hur du arbetar med PTVS finns i [produktdokumentationen](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Jupyter Notebook
Den Jupyter Notebook ger en webbläsarbaserad IDE för datautforskning och modellering. Du kan använda Python 2, Python 3 eller R (både öppen källkod och Microsoft R Server) i en Jupyter-anteckningsbok.

Om du vill starta jupyteranteckningsboken väljer du ikonen **Jupyter Notebook** på **Start-menyn** eller på skrivbordet. I kommandotolken DSVM kan du ```jupyter notebook``` också köra kommandot från katalogen där du har befintliga anteckningsböcker eller där du vill skapa nya anteckningsböcker.  

När du har börjat jupyter bör du se en katalog som innehåller några exempel anteckningsböcker som är förpaketerade till DSVM. Nu kan du:

* Välj anteckningsboken om du vill visa koden.
* Kör varje cell genom att markera Skift+Retur.
* Kör hela anteckningsboken genom att välja **Cell** > **Run**.
* Skapa en ny anteckningsbok genom att välja Jupyter-ikonen (övre vänstra hörnet), välja knappen **Nytt** till höger och sedan välja det anteckningsboksspråk (kallas även kärnor).   

> [!NOTE]
> För närvarande stöds Python 2.7-, Python 3.6-, R-, Julia- och PySpark-kärnor i Jupyter. R-kärnan stöder programmering i både R med öppen källkod och Microsoft R.   
> 
> 

När du är i anteckningsboken kan du utforska dina data, skapa modellen och testa modellen med hjälp av ditt val av bibliotek.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Träna och distribuera modeller med hjälp av Azure Machine Learning
När du har skapat och validerat din modell är nästa steg vanligtvis att distribuera den i produktion. Med det här steget kan klientprogrammen anropa modellförutsägelserna i realtid eller batchläge. Azure Machine Learning tillhandahåller en mekanism för att operationalisera en modell som är inbyggd i antingen R eller Python.

När du fungerar din modell i Azure Machine Learning exponeras en webbtjänst. Det gör det möjligt för klienter att göra REST-anrop som passerar i indataparametrar och ta emot förutsägelser från modellen som utdata.

### <a name="build-and-operationalize-python-models"></a>Skapa och operationalisera Python-modeller
Här är ett kodavsnitt som utvecklats i en Python Jupyter anteckningsbok som bygger en enkel modell med hjälp av Scikit-learn biblioteket:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Den metod som används för att distribuera dina Python-modeller till Azure Machine Learning avslutar förutsägelsen av modellen till en funktion och dekorerar den med attribut som tillhandahålls av det förinstallerade Azure Machine Learning Python-biblioteket. Attributen anger ditt Azure Machine Learning-arbetsyte-ID, API-nyckeln och indata- och returparametrarna.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

En klient kan nu ringa samtal till webbtjänsten. Bekvämlighetsomslag skapar REST API-begäranden. Här är exempelkod för att använda webbtjänsten:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> För närvarande stöds Azure Machine Learning-biblioteket endast på Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Bygga och operationalisera R-modeller
Du kan distribuera R-modeller som är byggda på den virtuella datorn för datavetenskap eller någon annanstans till Azure Machine Learning på ett sätt som liknar hur det görs för Python. Här är stegen:

1. Skapa en settings.json-fil för att ange ditt arbetsområdes-ID och autentiseringstoken. 
2. Skriv ett omslag för modellens prediktningsfunktion.
3. Anropa ```publishWebService``` i Azure Machine Learning-biblioteket för att skicka in funktionsomslaget.  

Använd följande procedur och kodavsnitt för att konfigurera, skapa, publicera och använda en modell som en webbtjänst i Azure Machine Learning.

#### <a name="set-up"></a>Konfigurera

Skapa en settings.json-fil ```.azureml``` under en katalog som anropas under din hemkatalog. Ange parametrarna från arbetsytan Azure Machine Learning.

Här är filen settings.json filstruktur:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Skapa en modell i R och publicera den i Azure Machine Learning

```r
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
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Använda modellen som distribueras i Azure Machine Learning
Om du vill använda modellen från ett klientprogram använder du Azure Machine Learning-biblioteket för att slå upp den publicerade webbtjänsten efter namn. Använd `services` API-anropet för att bestämma slutpunkten. Då ringer du `consume` bara funktionen och skickar in dataramen som ska förutsägas.

Använd följande kod för att använda modellen som publiceras som en Azure Machine Learning-webbtjänst:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Se mer information om [R-paket i Machine Learning Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Hantera Azure-resurser
DSVM tillåter inte bara att du kan bygga din analyslösning lokalt på den virtuella datorn. Du kan också komma åt tjänster på Azure-molnplattformen. Azure tillhandahåller flera beräknings-, lagrings-, dataanalys- och andra tjänster som du kan administrera och komma åt från din DSVM.

Om du vill administrera din Azure-prenumeration och molnresurser har du två alternativ:
+ Använd din webbläsare och gå till [Azure-portalen](https://portal.azure.com).

+ Använd PowerShell-skript. Kör Azure PowerShell från en genväg på skrivbordet eller på **Start-menyn.** Mer information finns i [Microsoft Azure PowerShell-dokumentationen.](../../powershell-azure-resource-manager.md) 

## <a name="extend-storage-by-using-shared-file-systems"></a>Utöka lagringen med hjälp av delade filsystem
Dataexperter kan dela stora datauppsättningar, kod eller andra resurser inom teamet. DSVM har ca 45 GB ledigt utrymme. Om du vill utöka lagringsutrymmet kan du använda Azure-filer och antingen montera den på en eller flera DSVM-instanser eller komma åt den via ett REST API. Du kan också använda [Azure-portalen](../../virtual-machines/windows/attach-managed-disk-portal.md) eller använda [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) för att lägga till extra dedikerade datadiskar. 

> [!NOTE]
> Det maximala utrymmet på Azure Files-resursen är 5 TB. Storleksgränsen för varje fil är 1 TB. 

Du kan använda det här skriptet i Azure PowerShell för att skapa en Azure Files-resurs:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Nu när du har skapat en Azure Files-resurs kan du montera den på vilken virtuell dator som helst i Azure. Vi rekommenderar att du placerar den virtuella datorn i samma Azure-datacenter som lagringskontot, för att undvika svarstid och dataöverföringsavgifter. Här är Azure PowerShell-kommandona för att montera enheten på DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Nu kan du komma åt den här enheten som du skulle någon vanlig enhet på den virtuella datorn.

## <a name="share-code-in-github"></a>Dela kod i GitHub
GitHub är en koddatabas där du kan hitta kodexempel och källor för olika verktyg med hjälp av tekniker som delas av utvecklarcommunityn. Den använder Git som teknik för att spåra och lagra versioner av kodfilerna. GitHub är också en plattform där du kan skapa en egen databas för att lagra teamets delade kod och dokumentation, implementera versionskontroll och styra vem som har åtkomst till visa och bidra med kod. 

Besök [GitHubs hjälpsidor](https://help.github.com/) för mer information om hur du använder Git. Du kan använda GitHub som ett av sätten att samarbeta med ditt team, använda kod som utvecklats av communityn och bidra med kod tillbaka till communityn.

DSVM levereras med klientverktyg på kommandoraden och på gränssnittet för att komma åt GitHub-databasen. Kommandoradsverktyget som fungerar med Git och GitHub kallas Git Bash. Visual Studio är installerat på DSVM och har Git-tilläggen. Du hittar ikoner för dessa verktyg på **Start-menyn** och på skrivbordet.

Om du vill hämta kod från en ```git clone``` GitHub-databas använder du kommandot. Om du till exempel vill hämta databasen för datavetenskap som microsoft har publicerat i den aktuella katalogen kan du köra följande kommando i Git Bash:

    git clone https://github.com/Azure/DataScienceVM.git

I Visual Studio kan du göra samma kloning. Följande skärmbild visar hur du kommer åt Git- och GitHub-verktyg i Visual Studio:

![Skärmbild av Visual Studio med GitHub-anslutningen visad](./media/vm-do-ten-things/VSGit.PNG)

Du hittar mer information om hur du använder Git för att arbeta med din GitHub-databas från resurser som är tillgängliga på github.com. [Lathunden](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) är en användbar referens.

## <a name="access-azure-data-and-analytics-services"></a>Få tillgång till Azure-data- och analystjänster
### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob storage är en tillförlitlig, ekonomisk molnlagringstjänst för stora och små data. I det här avsnittet beskrivs hur du kan flytta data till Blob-lagring och komma åt data som lagras i en Azure-blob.

#### <a name="prerequisites"></a>Krav

* Skapa ditt Azure Blob-lagringskonto från [Azure-portalen](https://portal.azure.com).

   ![Skärmbild av processen för att skapa lagringskonto i Azure-portalen](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Bekräfta att kommandorads-AzCopy-verktyget är ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```förinstallerat: . Katalogen som innehåller azcopy.exe finns redan på din PATH-miljövariabel, så du kan undvika att skriva hela kommandosökvägen när du kör det här verktyget. Mer information om AzCopy-verktyget finns i [Aoskopidokumentationen](../../storage/common/storage-use-azcopy.md).
* Starta verktyget Azure Storage Explorer. Du kan hämta den från [webbsidan Storage Explorer](https://storageexplorer.com/). 

   ![Skärmbild av Azure Storage Explorer som använder ett lagringskonto](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Flytta data från en virtuell dator till en Azure-blob: AzCopy

Om du vill flytta data mellan lokala filer och Blob-lagring kan du använda AzCopy på kommandoraden eller i PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Ersätt **C:\myfolder** med sökvägen där filen lagras, **mittkontokonto** med ditt Blob-lagringskontonamn, **mincontainer** med behållarens namn och **lagringskontonyckel** med åtkomstnyckeln för Blob-lagring. Du hittar dina lagringskontouppgifter i [Azure-portalen](https://portal.azure.com).

Kör kommandot AzCopy i PowerShell eller från en kommandotolk. Här är några exempel användning av AzCopy kommandot:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

När du har kört kommandot AzCopy för att kopiera till en Azure-blob visas filen i Azure Storage Explorer.

![Skärmbild av lagringskontot, visar den uppladdade CSV-filen](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Flytta data från en virtuell dator till en Azure-blob: Azure Storage Explorer

Du kan också överföra data från den lokala filen i den virtuella datorn med hjälp av Azure Storage Explorer:

* Om du vill överföra data till en behållare markerar du målbehållaren och väljer **knappen Ladda upp.** ![Skärmbild av överföringsknappen i Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Välj ellips (**...**) till höger om **rutan Filer,** välj en eller flera filer att ladda upp från filsystemet, och välj **Ladda upp** för att börja ladda upp filerna. ![Skärmbild av dialogrutan Ladda upp filer](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Läsa data från en Azure-blob: Machine Learning-läsarmodul

I Azure Machine Learning Studio kan du använda modulen Importera data för att läsa data från din blob.

![Skärmbild av modulen Importera data i Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Läsa data från en Azure-blob: Python ODBC

Du kan använda BlobService-biblioteket för att läsa data direkt från en blob i en Jupyter-anteckningsbok eller i ett Python-program.

Importera först de paket som krävs:

```python
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
```

Anslut sedan dina Blob-lagringskontouppgifter och läsa data från blobben:

```python
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

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Data läss som en dataram:

![Skärmbild av de första 10 raderna med data](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage är en hyperskala lagringsplats för arbetsbelastningar för stordataanalys och är kompatibel med Hadoop Distributed File System (HDFS). Det fungerar med Hadoop, Spark och Azure Data Lake Analytics. I det här avsnittet får du lära dig hur du kan flytta data till Azure Data Lake Storage och köra analyser med hjälp av Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Krav

* Skapa din Azure Data Lake Analytics-instans i [Azure-portalen](https://portal.azure.com).

   ![Skärmbild av hur du skapar en DataSjöanalys-instans från Azure-portalen](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Plugin-programmet Azure Data Lake och Stream Analytics för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) är redan installerat i Visual Studio Community Edition på den virtuella datorn. När du har startat Visual Studio och loggat in på din Azure-prenumeration bör du se ditt Azure Data Analytics-konto och -lagring på den vänstra panelen i Visual Studio.

   ![Skärmbild av plugin-programmet för datasjöverktyg i Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Flytta data från en virtuell dator till DataSjö: Azure Data Lake Explorer

Du kan använda Azure Data Lake Explorer för att [överföra data från de lokala filerna på den virtuella datorn till Data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Du kan också skapa en datapipeline för att operationalize din dataförflyttning till eller från Azure Data Lake med hjälp av [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Den här artikeln](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) guidar dig genom stegen för att skapa datapipelderna.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Läsa data från en Azure-blob till Data Lake: U-SQL

Om dina data finns i Azure Blob-lagring kan du direkt läsa data från en Azure-blob i en U-SQL-fråga. Innan du skriver din U-SQL-fråga kontrollerar du att ditt Blob-lagringskonto är länkat till din Azure Data Lake-instans. Gå till Azure-portalen, hitta instrumentpanelen i Azure Data Lake Analytics, välj **Lägg till datakälla,** välj en lagringstyp av **Azure Storage**och anslut ditt Azure-lagringskontonamn och -nyckel. Sedan kan du referera till data som lagras i lagringskontot.

![Skärmbild av dialogrutan Lägg till datakälla](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

I Visual Studio kan du läsa data från Blob-lagring, manipulera data, identifiera funktioner och skicka resulterande data till antingen Azure Data Lake eller Azure Blob storage. När du refererar till data i Blob-lagring använder **du wasb://**. När du refererar till data i Azure Data Lake använder **du swbhdfs://**.

Du kan använda följande U-SQL-frågor i Visual Studio:

```usql
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
```

När frågan har skickats till servern visas jobbets status i ett diagram.

![Skärmbild av jobbstatusdiagrammet](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Frågedata i Datasjö: U-SQL

När datauppsättningen har förtärs i Azure Data Lake kan du använda [U-SQL-språk](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) för att fråga och utforska data. U-SQL-språket liknar T-SQL, men kombinerar vissa funktioner från C# så att användarna kan skriva anpassade moduler och användardefinierade funktioner. Du kan använda skripten i föregående steg.

När frågan har skickats till servern tripdata_summary. CSV visas i Azure Data Lake Explorer. Du kan förhandsgranska data genom att högerklicka på filen.

![Skärmbild av CSV-filen i DataSjöutforskaren](./media/vm-do-ten-things/USQL_create_summary.png)

Filinformationen visas:

![Skärmbild av filsammanfattningsinformationen](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop kluster
Azure HDInsight är en hanterad Apache Hadoop-, Spark-, HBase- och Storm-tjänst i molnet. Du kan enkelt arbeta med Azure HDInsight-kluster från virtual machine för datavetenskap.

#### <a name="prerequisites"></a>Krav

* Skapa ditt Azure Blob-lagringskonto från [Azure-portalen](https://portal.azure.com). Det här lagringskontot används för att lagra data för HDInsight-kluster.

   ![Skärmbild av hur du skapar ett lagringskonto från Azure-portalen](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Anpassa Azure HDInsight Hadoop-kluster från [Azure-portalen](../team-data-science-process/customize-hadoop-cluster.md).
  
   Länka lagringskontot som skapats med ditt HDInsight-kluster när det skapas. Det här lagringskontot används för åtkomst till data som kan bearbetas i klustret.

   ![Val för länkning av lagringskontot som skapats med ett HDInsight-kluster](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Aktivera åtkomst till fjärrskrivbord till huvudnoden i klustret när den har skapats. Kom ihåg de fjärråtkomstautentiseringsuppgifter som du anger här, eftersom du behöver dem i den efterföljande proceduren.

   ![Knapp för fjärrskrivbord för att aktivera fjärråtkomst till HDInsight-klustret](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Skapa en Azure Machine Learning-arbetsyta. Dina Machine Learning-experiment lagras på den här machine learning-arbetsytan. Välj de markerade alternativen i portalen, som visas i följande skärmbild:

   ![Skapa en Azure Machine Learning-arbetsyta](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Ange parametrarna för arbetsytan.

   ![Ange parametrar för maskininlärningsarbetsyta](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Ladda upp data med hjälp av IPython Notebook. Importera nödvändiga paket, koppla in autentiseringsuppgifter, skapa en databas i ditt lagringskonto och läs sedan in data i HDI-kluster.

```python
# Import required packages
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


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
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


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Alternativt kan du följa [den här genomgången](../team-data-science-process/hive-walkthrough.md) för att ladda upp NYC Taxi-data till HDI-klustret. Viktiga steg är:
  
* Använd AzCopy för att hämta zippade CVS från den offentliga blob till din lokala mapp.
* Använd AzCopy för att ladda upp uppackade CSV:er från den lokala mappen till ett HDI-kluster.
* Logga in på huvudnoden för Hadoop-klustret och förbered dig för dataanalys.

När data har lästs in i HDI-klustret kan du kontrollera dina data i Azure Storage Explorer. Och nyctaxidb databasen har skapats i HDI-klustret.

#### <a name="data-exploration-hive-queries-in-python"></a>Datautforskning: Hive-frågor i Python

Eftersom data finns i ett Hadoop-kluster kan du använda pyodbc-paketet för att ansluta till Hadoop-kluster och frågedatabaser med hjälp av Hive för att göra utforskning och funktionsteknik. Du kan visa de befintliga tabeller som du skapade i det nödvändiga steget.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Visa befintliga tabeller](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Låt oss titta på antalet poster i varje månad och frekvenserna av tippade eller inte i resetabellen:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Diagram över antal poster i varje månad](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Tomt av spetsfrekvenser](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Du kan också beräkna avståndet mellan upphämtningsplats och avlämningsplats och sedan jämföra det med resans avstånd.

```python
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
results = pd.read_sql(queryString, connection)
results.head(5)
```

![De översta raderna i upphämtnings- och avlämningstabellen](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Tomt av pickup / drop-off avstånd till resan avstånd](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nu ska vi förbereda en nedsamplad (1 procent) uppsättning data för modellering. Du kan använda dessa data i machine learning-läsarmodulen.

```python
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
```

Infoga nu innehållet i kopplingen i den föregående interna tabellen.

```python
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
```

Efter ett tag kan du se att data har lästs in i Hadoop-kluster:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Översta rader med data från tabellen](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse och databaser
Azure SQL Data Warehouse är ett elastiskt datalager som en tjänst med en SQL Server-upplevelse i företagsklass.

Du kan etablera ditt Azure SQL-informationslager genom att följa instruktionerna i den [här artikeln](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). När du har etablerat ditt SQL-informationslager kan du använda [den här genomgången](../team-data-science-process/sqldw-walkthrough.md) för att göra dataöverföring, utforskning och modellering med hjälp av data i SQL-informationslagret.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB är en NoSQL-databas i molnet. Du kan använda den för att arbeta med dokument som JSON och för att lagra och fråga dokumenten.

Använd följande nödvändiga steg för att komma åt Azure Cosmos DB från DSVM:

1. Azure Cosmos DB Python SDK är redan installerat på DSVM. Om du vill ```pip install pydocumentdb --upgrade``` uppdatera den körs den från en kommandotolk.
2. Skapa ett Azure Cosmos DB-konto och -databas från [Azure-portalen](https://portal.azure.com).
3. Hämta Azure Cosmos DB Data Migration Tool från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) och extrahera till en valfri katalog.
4. Importera JSON-data (vulkandata) som lagras i en [offentlig blob](https://cahandson.blob.core.windows.net/samples/volcano.json) till Azure Cosmos DB med följande kommandoparametrar till migreringsverktyget. (Använd dtui.exe från katalogen där du installerade Azure Cosmos DB Data Migration Tool.) Ange käll- och målplatsen med följande parametrar:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

När du har importerat data kan du gå till Jupyter och öppna anteckningsboken *DocumentDBSample*. Den innehåller Python-kod för att komma åt Azure Cosmos DB och göra några grundläggande frågor. Du kan läsa mer om Azure Cosmos DB genom att besöka tjänstens [dokumentationssida](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="use-power-bi-reports-and-dashboards"></a>Använda Power BI-rapporter och instrumentpaneler 
Du kan visualisera Volcano JSON-filen från föregående Azure Cosmos DB-exempel i Power BI Desktop för att få visuella insikter i data. Detaljerade steg finns i [Power BI-artikeln](../../cosmos-db/powerbi-visualize.md). Här är stegen på hög nivå:

1. Öppna Power BI Desktop och välj **Hämta data**. Ange URL:en `https://cahandson.blob.core.windows.net/samples/volcano.json`som: .
2. Du bör se JSON-posterna importerade som en lista. Konvertera listan till en tabell så att Power BI kan arbeta med den.
4. Expandera kolumnerna genom att markera expanderingsikonen (pilen).
5. Observera att platsen är ett **postfält.** Expandera posten och markera bara koordinaterna. **Koordinat** är en listkolumn.
6. Lägg till en ny kolumn för att konvertera listkoordinatkoldinnen till en kommaavgränsad **LatLong-kolumn.** Sammanfoga de två elementen i koordinatlistefältet med hjälp av formeln ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Konvertera kolumnen **Höjd** till decimal och markera knapparna **Stäng** och **Använd.**

I stället för föregående steg kan du klistra in följande kod. Det skript ut de steg som används i Advanced Editor i Power BI för att skriva dataomvandlingar i ett frågespråk.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Du har nu data i din Power BI-datamodell. Power BI Desktop-instansen ska visas på följande sätt:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Du kan börja skapa rapporter och visualiseringar med hjälp av datamodellen. Du kan följa stegen i den [här Power BI-artikeln](../../cosmos-db/powerbi-visualize.md#build-the-reports) för att skapa en rapport.

## <a name="scale-the-dsvm-dynamically"></a>Skala DSVM dynamiskt 
Du kan skala upp och ned DSVM för att uppfylla projektets behov. Om du inte behöver använda den virtuella datorn på kvällen eller på helgerna kan du stänga av den virtuella datorn från [Azure-portalen](https://portal.azure.com).

> [!NOTE]
> Du ådrar dig beräkningsavgifter om du bara använder avstängningsknappen för operativsystemet på den virtuella datorn.  
> 
> 

Du kan behöva hantera en storskalig analys och behöva mer cpu, minne eller diskkapacitet. Om så är fallet kan du hitta ett urval av VM-storlekar när det gäller CPU-kärnor, GPU-baserade instanser för djupinlärning, minneskapacitet och disktyper (inklusive solid state-enheter) som uppfyller dina beräknings- och budgetbehov. Den fullständiga listan över virtuella datorer, tillsammans med deras timberäkningspriser, är tillgänglig på [azure-pristabellen för virtuella datorer.](https://azure.microsoft.com/pricing/details/virtual-machines/)

På samma sätt kan ditt behov av vm-bearbetningskapacitet minska. (Du har till exempel flyttat en större arbetsbelastning till ett Hadoop- eller Spark-kluster.) Du kan sedan skala ned klustret från [Azure-portalen](https://portal.azure.com) och gå till inställningarna för din VM-instans. 

## <a name="add-more-tools"></a>Lägg till fler verktyg
Verktyg som är fördefinierade i DSVM kan tillgodose många vanliga dataanalysbehov. Detta sparar tid eftersom du inte behöver installera och konfigurera dina miljöer en efter en. Det sparar också pengar, eftersom du betalar för endast resurser som du använder.

Du kan använda andra Azure-data- och analystjänster som är profilerade i den här artikeln för att förbättra din analysmiljö. I vissa fall kan du behöva ytterligare verktyg, inklusive några egna partnerverktyg. Du har fullständig administrativ åtkomst på den virtuella datorn för att installera nya verktyg som du behöver. Du kan också installera ytterligare paket i Python och R som inte är förinstallerade. För Python kan du ```conda``` ```pip```använda antingen eller . För R kan ```install.packages()``` du använda i R-konsolen eller använda IDE och välja **Paketinstallationspaket** > **Install Packages**.

## <a name="deep-learning"></a>Deep learning

Förutom de rambaserade exemplen kan du få en uppsättning omfattande genomgångar som har validerats på DSVM. Dessa genomgångar hjälper dig att få fart på din utveckling av djupinlärningsprogram i domäner som bild- och text- och språkförståelse.   


- [Köra neurala nätverk över olika ramverk](https://github.com/ilkarman/DeepLearningFrameworks): Den här genomgången visar hur man migrerar kod från ett ramverk till ett annat. Det visar också hur man jämför modeller och körtidsprestanda över ramverk. 

- [En hur-till guide för att bygga en end-to-end-lösning för att upptäcka produkter i bilder:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)Bildidentifiering är en teknik som kan lokalisera och klassificera objekt i bilder. Denna teknik har potential att ge stora belöningar i många verkliga affärsområden. Återförsäljare kan till exempel använda den här tekniken för att avgöra vilken produkt en kund har plockat upp från hyllan. Den här informationen hjälper i sin tur butiker att hantera produktlager. 

- [Djupinlärning för ljud:](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)Den här självstudien visar hur man tränar en djupinlärningsmodell för ljudhändelsedetektering på den [urbana ljuddatauppsättningen](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Det ger också en översikt över hur du arbetar med ljuddata.

- [Klassificering av textdokument](https://github.com/anargyri/lstm_han): Den här genomgången visar hur man bygger och tränar två neurala nätverksarkitekturer: Hierarkiskt attention network och LSTM-nätverk (Long Short Term Memory). Dessa neurala nätverk använder Keras API för djupinlärning för att klassificera textdokument. Keras är en front till tre av de mest populära djupinlärningsramverken: Microsoft Cognitive Toolkit, TensorFlow och Theano.

## <a name="summary"></a>Sammanfattning
I den här artikeln beskrivs några av de saker du kan göra på den virtuella datorn för Microsoft Data Science. Det finns många fler saker du kan göra för att göra DSVM till en effektiv analysmiljö.

