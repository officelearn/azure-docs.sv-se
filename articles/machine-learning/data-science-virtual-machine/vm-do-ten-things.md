---
title: Utforska data och modeller i Windows
titleSuffix: Azure Data Science Virtual Machine
description: Utföra data utforsknings-och modellerings uppgifter på Windows-Data Science Virtual Machine.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: tracking-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 7d9aced42efefc8651605be44f0091b2f4f2815e
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959287"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Tio saker du kan göra på Windows-Data Science Virtual Machine

Windows Data Science Virtual Machine (DSVM) är en kraftfull utvecklings miljö för data vetenskap där du kan utföra data utforsknings-och modellerings uppgifter. Miljön har redan byggts och paketerats med flera populära verktyg för data analys som gör det enkelt att komma igång med din analys för lokala, molnbaserade eller hybrid distributioner. 

DSVM fungerar nära Azure-tjänster. Den kan läsa och bearbeta data som redan finns lagrade på Azure, i Azure SQL Data Warehouse Azure Data Lake, Azure Storage eller Azure Cosmos DB. Det kan också dra nytta av andra analys verktyg, till exempel Azure Machine Learning och Azure Data Factory.

I den här artikeln får du lära dig hur du använder din DSVM för att utföra data vetenskaps uppgifter och interagera med andra Azure-tjänster. Här följer några av de saker du kan göra på DSVM:

- Utforska data och utveckla modeller lokalt på DSVM med hjälp av Microsoft Machine Learning Server och python.
- Använd en Jupyter Notebook för att experimentera med dina data i en webbläsare med hjälp av python 2, python 3 och Microsoft R. (Microsoft R är en företags färdig version av R utformad för prestanda.)
- Distribuera modeller som skapats via R och python på Azure Machine Learning så att klient program kan komma åt dina modeller med hjälp av ett enkelt webb tjänst gränssnitt.
- Administrera dina Azure-resurser med hjälp av Azure Portal eller PowerShell.
- Utöka ditt lagrings utrymme och dela storskaliga data uppsättningar/kod i hela teamet genom att skapa en Azure Files resurs som en monterad enhet på din DSVM.
- Dela kod med ditt team genom att använda GitHub. Få åtkomst till lagrings platsen med hjälp av de förinstallerade git-klienterna: git bash och git GUI.
- Få åtkomst till Azure data-och analys tjänster som Azure Blob Storage, Azure Data Lake, Azure Cosmos DB, Azure SQL Data Warehouse och Azure SQL Database.
- Bygg rapporter och en instrument panel med hjälp av Power BI Desktop instansen som är förinstallerad på DSVM och distribuera dem i molnet.
- Skala dina DSVM dynamiskt efter projektets behov.
- Installera ytterligare verktyg på den virtuella datorn.   

> [!NOTE]
> Ytterligare användnings kostnader gäller för många av tjänsterna för data lagring och analys som anges i den här artikeln. Mer information finns på sidan med [priser för Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Krav

* Du behöver en Azure-prenumeration. Du kan [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/free/).
* Instruktioner för hur du konfigurerar en Data Science Virtual Machine på Azure Portal finns i [skapa en virtuell dator](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Utforska data och utveckla modeller med Microsoft Machine Learning Server
Du kan använda språk som R och python för att utföra data analyser direkt på DSVM.

För R kan du använda en IDE-liknande RStudio som du hittar på Start-menyn eller på Skriv bordet. Du kan också använda R-verktyg för Visual Studio. Microsoft har tillhandahållit ytterligare bibliotek ovanpå CRAN R med öppen källkod för att möjliggöra skalbar analys och möjlighet att analysera data som är större än den minnes storlek som tillåts i parallell segment analys. 

För python kan du använda en IDE som Visual Studio Community Edition, som har tillägget Python Tools for Visual Studio (PTVS) förinstallerat. Som standard konfigureras bara python 3,6, rot Conda-miljön på PTVS. Gör så här för att aktivera Anaconda python 2,7:

1. Skapa anpassade miljöer för varje version genom att gå till **verktyg**  >  **python-verktyg**  >  **python-miljöer**och sedan välja **+ anpassad** i Visual Studio Community Edition.
1. Ange en beskrivning och ange sökvägen till miljöprefixet som **c:\anaconda\envs\python2** för anaconda python 2,7.
1. Välj **Använd automatisk identifiering**  >  **Apply** för att spara miljön.

Mer information om hur du skapar python-miljöer finns i [PTVS-dokumentationen](https://aka.ms/ptvsdocs) .

Nu har du ställt in för att skapa ett nytt python-projekt. Gå till **filen**  >  **nytt**  >  **projekt**  >  **python** och välj den typ av python-program som du skapar. Du kan ställa in python-miljön för det aktuella projektet till önskad version (python 2,7 eller 3,6) genom att högerklicka på **python-miljöer** och sedan välja **Lägg till/ta bort python-miljöer**. Du hittar mer information om hur du arbetar med PTVS i [produkt dokumentationen](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Jupyter Notebook
Jupyter Notebook tillhandahåller en webbläsarbaserad IDE för data utforskning och modellering. Du kan använda python 2, python 3 eller R (både öppen källkod och Microsoft R Server) i en Jupyter Notebook.

Om du vill starta Jupyter Notebook väljer du ikonen **Jupyter Notebook** på **Start** -menyn eller på Skriv bordet. I kommando tolken DSVM kan du också köra kommandot ```jupyter notebook``` från den katalog där du har befintliga antecknings böcker eller där du vill skapa nya antecknings böcker.  

När du har startat Jupyter navigerar du till `/notebooks` katalogen till exempel antecknings böcker som förpackas i DSVM. Nu kan du:

* Välj den bärbara datorn om du vill se koden.
* Kör varje cell genom att välja SKIFT + RETUR.
* Kör hela antecknings boken genom att välja **cell**  >  **körning**.
* Skapa en ny antecknings bok genom att välja ikonen Jupyter (övre vänstra hörnet), klicka på knappen **nytt** till höger och sedan välja språket för antecknings boken (även kallat kernels).   

> [!NOTE]
> För närvarande stöds python 2,7, python 3,6, R, Julia och PySpark-kerneler i Jupyter. R-kärnan stöder programmering i både öppen källkod R och Microsoft R.   
> 
> 

När du är i antecknings boken kan du utforska dina data, bygga modellen och testa modellen med hjälp av ditt val av bibliotek.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Träna och distribuera modeller med hjälp av Azure Machine Learning
När du har skapat och validerat din modell är nästa steg vanligt vis att distribuera den till produktions miljön. Det här steget gör det möjligt för dina klient program att anropa modell förutsägelserna i real tid eller i ett batch-läge. Azure Machine Learning tillhandahåller en mekanism för att operationalisera en modell som skapats antingen i R eller python.

När du operationalisera din modell i Azure Machine Learning visas en webb tjänst. Det gör det möjligt för klienter att göra REST-anrop som skickar in indataparametrar och tar emot förutsägelser från modellen som utdata.

### <a name="build-and-operationalize-python-models"></a>Bygg och operationalisera python-modeller
Här är ett kodfragment med kod som har utvecklats i en python Jupyter Notebook som skapar en enkel modell med hjälp av Scikit-biblioteket:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Metoden som används för att distribuera python-modeller till Azure Machine Learning omsluter förutsägelsen av modellen till en funktion och dekorerar den med attribut som tillhandahålls av det förinstallerade Azure Machine Learning python-biblioteket. Attributen anger Azure Machine Learning arbetsyte-ID, API-nyckeln och parametrarna för ingångs-och retur.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

En klient kan nu ringa till webb tjänsten. Bekvämlighets omslutningar konstruerar REST API begär Anden. Här är exempel kod för att använda webb tjänsten:

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

### <a name="build-and-operationalize-r-models"></a>Build-och operationalisera R-modeller
Du kan distribuera R-modeller som bygger på Data Science Virtual Machine eller någon annan stans på Azure Machine Learning på ett sätt som liknar hur det är för python. Gör så här:

1. Skapa en settings.jspå en fil för att ange arbets ytans ID och autentiseringstoken. 
2. Skriv en omslutning för modellens predict-funktion.
3. Anropa ```publishWebService``` i Azure Machine Learning-biblioteket för att skicka in funktions omslutningen.  

Använd följande procedur och kodfragment för att konfigurera, bygga, publicera och använda en modell som en webb tjänst i Azure Machine Learning.

#### <a name="set-up"></a>Konfigurera

Skapa en settings.jspå en fil under en katalog som kallas för ```.azureml``` din Hem Katalog. Ange parametrarna från din Azure Machine Learning-arbetsyta.

Här är settings.jspå fil strukturen:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Bygg en modell i R och publicera den i Azure Machine Learning

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
Om du vill använda modellen från ett klient program använder du Azure Machine Learning-biblioteket för att leta upp den publicerade webb tjänsten efter namn. Använd `services` API-anropet för att fastställa slut punkten. Sedan anropar du bara `consume` funktionen och skickar data ramen så att den förväntas.

Använd följande kod för att förbruka modellen som publicerats som en Azure Machine Learning-webb tjänst:

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
DSVM låter dig inte bara bygga analys lösningen lokalt på den virtuella datorn. Du kan också få åtkomst till tjänster på Azures moln plattform. Azure tillhandahåller flera beräknings-, lagrings-, data analys-och andra tjänster som du kan administrera och få åtkomst till från din DSVM.

För att administrera din Azure-prenumeration och moln resurser har du två alternativ:
+ Använd webbläsaren och gå till [Azure Portal](https://portal.azure.com).

+ Använd PowerShell-skript. Kör Azure PowerShell från en genväg på Skriv bordet eller från **Start** -menyn. Se [Microsoft Azure PowerShell-dokumentationen](../../powershell-azure-resource-manager.md) för fullständig information. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Utöka lagring med hjälp av delade fil system
Data forskare kan dela stora data uppsättningar, kod eller andra resurser i teamet. DSVM har cirka 45 GB ledigt utrymme. Om du vill utöka lagringen kan du använda Azure Files och antingen montera den på en eller flera DSVM-instanser eller komma åt den via en REST API. Du kan också använda [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) eller använda [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) för att lägga till extra dedikerade data diskar. 

> [!NOTE]
> Det maximala utrymmet på den Azure Files resursen är 5 TB. Storleks gränsen för varje fil är 1 TB. 

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

Nu när du har skapat en Azure Files-resurs kan du montera den på en virtuell dator i Azure. Vi rekommenderar att du sätter den virtuella datorn i samma Azure-datacenter som lagrings kontot, för att undvika svars tid och data överförings kostnader. Följande är de Azure PowerShell-kommandon som används för att montera enheten på DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Nu kan du komma åt den här enheten som en normal enhet på den virtuella datorn.

## <a name="share-code-in-github"></a>Dela kod i GitHub
GitHub är en kod lagrings plats där du kan hitta kod exempel och källor för olika verktyg med hjälp av tekniker som delas av Developer community. Den använder git som teknik för att spåra och lagra versioner av filerna. GitHub är också en plattform där du kan skapa din egen lagrings plats för att lagra teamets delade kod och dokumentation, implementera versions kontroll och kontrol lera vem som har åtkomst till att visa och bidra med kod. 

Besök [GitHub-hjälp sidorna](https://help.github.com/) om du vill ha mer information om hur du använder git. Du kan använda GitHub som ett av de olika sätten att samar beta med ditt team, använda kod som har utvecklats av communityn och sedan koppla kod till communityn.

DSVM har lästs in med klient verktyg på kommando raden och i det grafiska användar gränssnittet för att komma åt GitHub-lagringsplatsen. Kommando rads verktyget som fungerar med git och GitHub kallas git bash. Visual Studio är installerat på DSVM och har git-tillägg. Du kan hitta ikoner för dessa verktyg på **Start** -menyn och på Skriv bordet.

Om du vill ladda ned kod från en GitHub-lagringsplats använder du ```git clone``` kommandot. Om du till exempel vill hämta data vetenskaps databasen som publicerats av Microsoft i den aktuella katalogen kan du köra följande kommando i git bash:

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

I Visual Studio kan du utföra samma klonings åtgärd. Följande skärm bild visar hur du kommer åt git-och GitHub-verktyg i Visual Studio:

![Skärm bild av Visual Studio med GitHub-anslutningen visas](./media/vm-do-ten-things/VSGit.PNG)

Du hittar mer information om hur du använder Git för att arbeta med din GitHub-lagringsplats från resurser som finns på github.com. [Lathund-bladet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) är en användbar referens.

## <a name="access-azure-data-and-analytics-services"></a>Få åtkomst till Azure data-och analys tjänster
### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob Storage är en tillförlitlig, ekonomisk moln lagrings tjänst för data som är stora och små. I det här avsnittet beskrivs hur du kan flytta data till Blob Storage och komma åt data som lagras i en Azure-blob.

#### <a name="prerequisites"></a>Krav

* Skapa ditt Azure Blob Storage-konto från [Azure Portal](https://portal.azure.com).

   ![Skärm bild av processen för att skapa lagrings konton i Azure Portal](./media/vm-do-ten-things/create-azure-blob.png)

* Bekräfta att kommando rads verktyget AzCopy är förinstallerat: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . Katalogen som innehåller azcopy.exe finns redan i miljövariabeln PATH, så du kan undvika att skriva den fullständiga kommando Sök vägen när du kör det här verktyget. Mer information om AzCopy-verktyget finns i AzCopy- [dokumentationen](../../storage/common/storage-use-azcopy.md).
* Starta Azure Storage Explorer-verktyget. Du kan ladda ned den från [webb sidan Storage Explorer](https://storageexplorer.com/). 

   ![Skärm bild av Azure Storage Explorer åtkomst till ett lagrings konto](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Flytta data från en virtuell dator till en Azure-Blob: AzCopy

Om du vill flytta data mellan dina lokala filer och Blob Storage kan du använda AzCopy på kommando raden eller i PowerShell:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

Ersätt **C:\MyFolder** med sökvägen till den plats där filen lagras, **Mystorageaccount** med ditt Blob Storage- **kontonamn,** behållar namnet och **lagrings konto nyckeln** med din åtkomst nyckel för Blob Storage. Du kan hitta autentiseringsuppgifterna för ditt lagrings konto i [Azure Portal](https://portal.azure.com).

Kör kommandot AzCopy i PowerShell eller från en kommando tolk. Här är några exempel på användningen av AzCopy-kommandot:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

När du har kört AzCopy-kommandot för att kopiera till en Azure-Blob visas filen i Azure Storage Explorer.

![Skärm bild av lagrings kontot som visar den överförda CSV-filen](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Flytta data från en virtuell dator till en Azure-Blob: Azure Storage Explorer

Du kan också ladda upp data från den lokala filen på den virtuella datorn med hjälp av Azure Storage Explorer:

* Om du vill överföra data till en behållare väljer du mål behållaren och klickar på knappen **överför** . ![ Skärm bild av knappen upload i Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Välj ellipsen (**...**) till höger om rutan **filer** , Välj en eller flera filer som ska överföras från fil systemet och välj **Ladda upp** för att påbörja överföring av filerna. ![ Skärm bild av dialog rutan Ladda upp filer](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Läs data från en Azure-Blob: Machine Learning Reader-modul

I Azure Machine Learning Studio kan du använda modulen importera data för att läsa data från din BLOB.

![Skärm bild av modulen importera data i Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Läsa data från en Azure-Blob: python ODBC

Du kan använda BlobService-biblioteket för att läsa data direkt från en BLOB i en Jupyter Notebook eller i ett python-program.

Importera först de nödvändiga paketen:

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

Anslut sedan dina autentiseringsuppgifter för Blob Storage-kontot och Läs data från bloben:

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

Data läses som en data ram:

![Skärm bild av de första 10 raderna med data](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage är en storskalig lagrings plats för Big data Analytics-arbetsbelastningar och är kompatibel med Hadoop Distributed File System (HDFS). Det fungerar med Hadoop, Spark och Azure Data Lake Analytics. I det här avsnittet får du lära dig hur du kan flytta data till Azure Data Lake Storage och köra analyser med hjälp av Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Krav

* Skapa din Azure Data Lake Analytics-instans i [Azure Portal](https://portal.azure.com).

   ![Skärm bild som visar hur du skapar en Data Lake Analytics instans från Azure Portal](./media/vm-do-ten-things/azure-data-lake-create-v3.png)

* [Azure Data Lake-och Stream Analytics-verktygen för Visual Studio-plugin-programmet](https://www.microsoft.com/download/details.aspx?id=49504) är redan installerat i Visual Studio Community Edition på den virtuella datorn. När du startar Visual Studio och loggar in på din Azure-prenumeration bör du se ditt Azure Data Analytics-konto och-lagring i den vänstra panelen i Visual Studio.

   ![Skärm bild av plugin-programmet för Data Lake verktyg i Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Flytta data från en virtuell dator till Data Lake: Azure Data Lake Explorer

Du kan använda Azure Data Lake Explorer för att [överföra data från de lokala filerna på den virtuella datorn till data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Du kan också bygga en datapipeline för att operationalisera data flytten till eller från Azure Data Lake med hjälp av [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Den här artikeln](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) vägleder dig genom stegen för att bygga data pipeliner.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Läs data från en Azure-blob till Data Lake: U-SQL

Om dina data finns i Azure Blob Storage kan du direkt läsa data från en Azure-blob i en U-SQL-fråga. Innan du skapar din U-SQL-fråga ska du kontrol lera att ditt Blob Storage-konto är länkat till Azure Data Lake-instansen. Gå till Azure Portal, hitta Azure Data Lake Analytics instrument panel, Välj **Lägg till data källa**, Välj en lagrings typ för **Azure Storage**och Anslut ditt Azure Storage-kontonamn och-nyckel. Sedan kan du referera till de data som lagras i lagrings kontot.

![Skärm bild av dialog rutan Lägg till data Källa](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

I Visual Studio kan du läsa data från Blob Storage, manipulera data, ingenjörs funktioner och skicka de resulterande data till antingen Azure Data Lake eller Azure Blob Storage. Använd **wasb://** när du refererar till data i Blob Storage. När du refererar till data i Azure Data Lake använder du **swbhdfs://**.

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

När din fråga har skickats till servern visar ett diagram jobbets status.

![Skärm bild av jobb status diagrammet](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Fråga efter data i Data Lake: U-SQL

När data uppsättningen har matats in i Azure Data Lake kan du använda [U-SQL-språket](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) för att fråga och utforska data. U-SQL-språket liknar T-SQL, men kombinerar vissa funktioner från C# så att användarna kan skriva anpassade moduler och användardefinierade funktioner. Du kan använda skripten i föregående steg.

När frågan har skickats till servern visas tripdata_summary.CSV i Azure Data Lake Explorer. Du kan förhandsgranska data genom att högerklicka på filen.

![Skärm bild av CSV-filen i Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Fil informationen visas:

![Skärm bild av fil sammanfattnings informationen](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse och databaser
Azure SQL Data Warehouse är ett elastiskt informations lager som en tjänst med en SQL Server upplevelse i företags klass.

Du kan etablera ditt Azure SQL Data Warehouse genom att följa anvisningarna i [den här artikeln](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). När du har etablerat ditt SQL Data Warehouse kan du använda [den här genom gången](../team-data-science-process/sqldw-walkthrough.md) för att utföra data uppladdning, utforskning och modellering med hjälp av data i SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB är en NoSQL-databas i molnet. Du kan använda den för att arbeta med dokument som JSON och för att lagra och skicka frågor till dokumenten.

Använd följande nödvändiga steg för att komma åt Azure Cosmos DB från DSVM:

1. Azure Cosmos DB python SDK har redan installerats på DSVM. Kör ```pip install pydocumentdb --upgrade``` från en kommando tolk för att uppdatera den.
2. Skapa ett Azure Cosmos DB konto och en databas från [Azure Portal](https://portal.azure.com).
3. Hämta verktyget Azure Cosmos DB datamigrering från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) och extrahera till en valfri katalog.
4. Importera JSON-data (Volcano-data) som lagras i en [offentlig BLOB](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) till Azure Cosmos dB med följande kommando parametrar till migreringsverktyget. (Använd dtui.exe från den katalog där du installerade verktyget Azure Cosmos DB datamigrering.) Ange käll-och mål platsen med följande parametrar:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

När du har importerat data kan du gå till Jupyter och öppna antecknings boken med namnet *DocumentDBSample*. Den innehåller python-kod för att komma åt Azure Cosmos DB och göra några grundläggande frågor. Du kan lära dig mer om Azure Cosmos DB genom att gå till tjänstens [dokumentations sida](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="use-power-bi-reports-and-dashboards"></a>Använda Power BI rapporter och instrument paneler 
Du kan visualisera Volcano JSON-filen från föregående Azure Cosmos DB exempel i Power BI Desktop för att få visuella insikter om data. Detaljerade anvisningar finns i [Power BI artikeln](../../cosmos-db/powerbi-visualize.md). Här följer de övergripande stegen:

1. Öppna Power BI Desktop och välj **Hämta data**. Ange URL: en: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Du bör se vilka JSON-poster som importeras som en lista. Konvertera listan till en tabell så Power BI kan arbeta med den.
4. Expandera kolumnerna genom att välja ikonen Expandera (pil).
5. Observera att platsen är ett **post** fält. Expandera posten och välj bara koordinaterna. **Koordinaten** är en List kolumn.
6. Lägg till en ny kolumn för att konvertera List koordinatens kolumn till en kommaavgränsad **LatLong** -kolumn. Sammanfoga de två elementen i koordinatens List fält med hjälp av formeln ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
7. Konvertera kolumnen **höjning** till decimal och välj knapparna **Stäng** och **tillämpa** .

I stället för föregående steg kan du klistra in följande kod. Den skriver ut stegen som används i Avancerad redigerare i Power BI för att skriva data transformationer i ett frågespråk.

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

Nu har du data i din Power BI data modell. Din Power BI Desktop instans bör se ut så här:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Du kan börja skapa rapporter och visualiseringar med hjälp av data modellen. Du kan följa stegen i [den här Power BI artikeln](../../cosmos-db/powerbi-visualize.md#build-the-reports) för att bygga en rapport.

## <a name="scale-the-dsvm-dynamically"></a>Skala DSVM dynamiskt 
Du kan skala upp och ned DSVM för att uppfylla projektets behov. Om du inte behöver använda den virtuella datorn på kvällen eller på helger kan du stänga av den virtuella datorn från [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Du ådrar dig beräknings avgifter om du bara använder knappen Stäng för operativ systemet på den virtuella datorn.  
> 
> 

Du kan behöva hantera viss storskalig analys och behöver mer processor, minne eller disk kapacitet. I så fall kan du hitta olika VM-storlekar i förhållande till processor kärnor, GPU-baserade instanser för djup inlärning, minnes kapacitet och disk typer (inklusive solid state-hårddiskar) som uppfyller dina beräknings-och budget behov. Den fullständiga listan över virtuella datorer, tillsammans med deras beräknings priser per timme, finns på sidan med [priser för Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) .

På samma sätt kan behovet av bearbetnings kapacitet för virtuella datorer minska. (Till exempel: du flyttade en större arbets belastning till ett Hadoop-eller Spark-kluster.) Du kan sedan skala ned klustret från [Azure Portal](https://portal.azure.com) och gå till inställningarna för den virtuella dator instansen. 

## <a name="add-more-tools"></a>Lägg till fler verktyg
Verktyg som är inbyggda i DSVM kan hantera många vanliga data analys behov. Detta sparar tid eftersom du inte behöver installera och konfigurera dina miljöer en i taget. Det sparar också pengar, eftersom du betalar för endast resurser som du använder.

Du kan använda andra Azure data-och Analytics-tjänster som är profilerade i den här artikeln för att förbättra din analys miljö. I vissa fall kan du behöva ytterligare verktyg, inklusive vissa patentskyddade partner verktyg. Du har fullständig administrativ åtkomst på den virtuella datorn för att installera nya verktyg som du behöver. Du kan också installera ytterligare paket i python och R som inte är förinstallerade. För python kan du använda antingen ```conda``` eller ```pip``` . För r kan du använda ```install.packages()``` i r-konsolen eller använda IDE och välja **paket**  >  **installera paket**.

## <a name="deep-learning"></a>Deep learning

Förutom de ramverkbaserade exemplen kan du få en uppsättning omfattande genom gångar som har verifierats på DSVM. Med de här genom gången kan du komma igång med att utveckla djup inlärnings program i domäner som bild och text/språk förståelse.   


- [Köra neurala-nätverk i olika ramverk](https://github.com/ilkarman/DeepLearningFrameworks): den här genom gången visar hur du migrerar kod från ett ramverk till ett annat. Det visar också hur du jämför modeller och körnings prestanda i ramverk. 

- [En instruktions guide för att bygga en komplett lösning för att identifiera produkter i bilder](https://github.com/Azure/cortana-intelligence-product-detection-from-images): bild identifiering är en teknik som kan användas för att hitta och klassificera objekt i bilder. Den här tekniken har möjlighet att få enorma förmåner i många affärs domäner i real tid. Åter försäljare kan till exempel använda den här metoden för att avgöra vilken produkt en kund har hämtat från hyllan. Den här informationen i sin tur hjälper butiker att hantera produkt inventering. 

- [Djup inlärning för ljud](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): den här självstudien visar hur du tränar en djup inlärnings modell för att upptäcka ljud händelser i [data uppsättningen för urbana ljud](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Den innehåller också en översikt över hur du arbetar med ljuddata.

- [Klassificering av text dokument](https://github.com/anargyri/lstm_han): den här genom gången visar hur du skapar och tränar två neurala-nätverks arkitekturer: hierarkiskt Attention-nätverk och LSTM-nätverk. Dessa neurala-nätverk använder keras-API: et för djup inlärning för att klassificera text dokument. Keras är en klient del till tre populära ramverk för djup inlärning: Microsoft Cognitive Toolkit, TensorFlow och Theano.

## <a name="summary"></a>Sammanfattning
I den här artikeln beskrivs några av de saker du kan göra på Microsoft Data Science Virtual Machine. Det finns många fler saker du kan göra för att göra DSVM till en effektiv analys miljö.

