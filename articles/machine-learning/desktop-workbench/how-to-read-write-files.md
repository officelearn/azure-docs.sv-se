---
title: Läsa och skriva stora datafiler | Microsoft Docs
description: Läsa och skriva stora filer i Azure Machine Learning-experiment.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 4a2dff4dd57bdb0b010bbb4568d796f1e197a728
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971509"
---
# <a name="persisting-changes-and-working-with-large-files"></a>Spara ändringarna och arbeta med stora filer

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Du kan konfigurera en mängd olika körningsmål med tjänsten Azure Machine Learning-experimentering. Vissa riktar sig lokala, till exempel en lokal dator eller en Docker-behållare på en lokal dator. Andra är fjärransluten, till exempel en Docker-behållare på en fjärrdator eller ett HDInsight-kluster. Mer information finns i [översikt av Azure Machine Learning experimentera körningstjänsten](experimentation-service-configuration.md). 

Innan du kan köra på ett mål, måste du kopiera projektmappen till beräkningsmål. Du måste göra och med en lokal körning som använder en lokal temporär mapp för detta ändamål. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Körningen isolering, portabilitet och reproducerbarhet
Syftet med den här designen är att se till att den isolering och reproducerbarhet blir mer flexibel med körningen. Om du kör samma skript två gånger, på samma eller en annan compute mål, du får samma resultat. Ändringar som görs under den första körningen inte ska påverka de i den andra körningen. Med den här designen kan du hantera beräkningsmål som tillståndslösa beräkningsresurser som vart och ett har ingen tillhörighet ifråga om jobb som körs när de är klar.

## <a name="challenges"></a>Utmaningar
Även om den här designen ger dig fördelarna med portabilitet och repeterbarhet, skapar den även vissa unika utmaningar.

### <a name="persisting-state-changes"></a>Bevara tillståndsändringar
Om skriptet ändrar tillståndet för beräkningskontexten, ändringarna sparas inte för din nästa körning och de inte har spritts till klientdatorn automatiskt. 

Mer specifikt om skriptet skapar en undermapp eller skriver en fil, visas mappen eller filen inte i projektkatalogen efter körningen. Filerna lagras i en tillfällig mapp i målmiljön beräkning. Du kan använda dem för felsökning, men du förlita dig inte på permanenta sådana.

### <a name="working-with-large-files-in-the-project-folder"></a>Arbeta med stora filer i projektmappen

Om din projektmapp, till exempel innehåller några stora filer kan medföra fördröjning när mappen kopieras till målet beräkningsmiljö i början av en körning. Även om körningen sker lokalt, finns det fortfarande onödiga disktrafik att undvika. Därför cap vi för närvarande maximalt projekt storleken på 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Alternativ 1: Använd den *matar ut* mapp
Det här alternativet är bäst om samtliga följande villkor gäller:
* Skriptet ger filer.
* Du förväntar dig filer att ändra med varje experiment.
* Vill du behålla historiken för de här filerna. 

Vanliga användningsområden är:
* Träna en modell
* Skapa en datauppsättning
* Ett diagram skrivs ut som en bildfil som en del av modellträning körningar 

>[!Note]
> Maxstorlek på spårade fil i mappen för utdata efter en körning är 512 MB. Det innebär att om skriptet genererar en fil som är större än 512 MB i mappen utdata, den inte har samlats in det. 

Dessutom kan du jämföra utdata från körningarna väljer en utdatafil (till exempel en modell) som tillhandahålls av en tidigare kör och sedan använder den för en efterföljande aktivitet (till exempel bedömning).

Du kan skriva filer till en mapp med namnet *matar ut* som är relativ till rotkatalogen. Mappen får särskild behandling av tjänsten experimentering. Något skriptet skapas i mappen under körning, till exempel en modellfilen, datafilen eller ritade bildfil (benämns gemensamt kallas _artefakter_), kopieras till Azure Blob storage-kontot som är associerad med din konto för experimentering när körningen har slutförts. Filerna blir en del av din post i körningshistoriken.

Här är ett kort exempel kod för att lagra en modell i den *matar ut* mapp:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Du kan ladda ned alla artefakter genom att bläddra till den **utdatafilerna** på Detaljvyn sidan av en viss körs i Azure Machine Learning Workbench. Välj bara kör och välj sedan den **hämta** knappen. Alternativt kan du ange den `az ml asset download` i fönstret kommandoradsgränssnittet (CLI).

En mer komplett exempel finns i den `iris_sklearn.py` Python-skript i den _klassificera Iris_ exempelprojektet.

## <a name="option-2-use-the-shared-folder"></a>Alternativ 2: Använd den delade mappen
Om du inte behöver behålla historiken för varje körning filer kan med hjälp av en delad mapp som kan nås från oberoende körningarna vara ett bra alternativ för följande scenarier: 
- Skriptet måste läsa in data från lokala filer, till exempel CSV-filer eller en katalog med filer på text- eller bildfil som utbildnings- eller data. 
- Skriptet bearbetar rådata och skriver ut mellanresultat, till exempel trädmodell träningsdata från text- eller bildfil filer som används i ett efterföljande utbildnings kör. 
- Skriptet spits ut en modell och din efterföljande bedömningsskriptet måste hämta modellen och använda den för utvärdering. 

Det är viktigt att notera att den delade mappen ligger lokalt på din valda beräkningsmål. Därför kan det här alternativet fungerar bara om alla dina skript-körningar som refererar till den delade mappen körs på samma beräkningsmålet och beräkningsmål är inte återanvänds mellan körningar.

Genom att utnyttja funktionen delad mapp, kan du läsa från eller skriva till mappen som har identifierats av en miljövariabel `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Exempel
Här är några Python-exempelkod för att använda den här mapp för distributionsresursen för att läsa och skriva till en textfil:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "w") as f1:
    f1.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "r") as f2:
    text = f2.read()
```

En mer komplett exempel finns i den *iris_sklearn_shared_folder.py* fil i den _klassificera Iris_ exempelprojektet.

Innan du kan använda den här funktionen måste du behöva konfigurera den *.compute* filen några enkla konfigurationer som representerar den aktuella körningskontexten i den *aml_config* mapp. Den faktiska sökvägen till mappen kan variera beroende på beräkningsmål som du väljer och det värde som du konfigurerar.

### <a name="configure-local-compute-context"></a>Konfigurera lokal beräkningskontext

Om du vill aktivera den här funktionen i en lokal beräkningskontext, lägger du till i din *.compute* filen följande rad som representerar den _lokala_ miljö (vanligen namnet *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

Sökvägen ~/.azureml/share är basmapp standardsökvägen. Du kan ändra den till någon lokal absolut sökväg som kan nås genom att köra skriptet. Kontonamnet för experimentering, Arbetsytenamn och projektnamn läggs automatiskt till baskatalogen namn, som blir den fullständiga sökvägen till den delade katalogen. Till exempel filer kan skrivas till (och hämtas från) på följande sökväg om du använder föregående Standardvärde:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Konfigurera Docker-beräkningskontexten (lokal eller fjärransluten)
Om du vill aktivera den här funktionen på en beräkningskontext för Docker, måste du lägga till följande två rader till din lokala eller fjärranslutna Docker *.compute* fil.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>Den **sharedVolumes** egenskapen måste anges till *SANT* när du använder den `AZUREML_NATIVE_SHARE_DIRECTORY` miljövariabeln för att få åtkomst till den delade mappen. Annars misslyckas körningen.

Den kod som körs i Docker-behållare alltid ser den delade mappen som */azureml-share /*. Sökvägen till mappen kan som visas av Docker-behållare inte konfigureras. Använd inte den här mappnamn i din kod. Använd i stället alltid miljövariabeln `AZUREML_NATIVE_SHARE_DIRECTORY` att referera till den här mappen. Det är kopplat till en lokal mapp på Docker-värdens machine eller beräkningskontexten. Grundläggande katalogen för den här lokala mappen är konfigurerbart värde på den `nativeSharedDirectory` i den *.compute* fil. Den lokala sökvägen på den delade mappen på värddatorn, är om du använder standardvärdet följande:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>Sökvägen till den delade mappen på den lokala disken är samma oavsett om det är en lokal beräkningskontext eller en lokal Docker-beräkningskontext. Det innebär att du kan också dela filer mellan en lokal körning och en lokal Docker-körning.

Du kan placera inmatade data direkt i dessa mappar och förväntar sig att lokal- eller Docker körs på datorn kan hämta den. Du kan också skriva filer till den här mappen från din lokala eller Docker körs och räknar filer får sparas i den mappen kvarvarande livscykel för körning.

Mer information finns i [konfigurationsfiler för Azure Machine Learning Workbench körning](experimentation-service-configuration-reference.md).

>[!NOTE]
>Den `AZUREML_NATIVE_SHARE_DIRECTORY` miljövariabeln stöds inte i ett HDInsight-beräkningskontexten. Det är enkelt att göra detta genom att uttryckligen använda en absolut sökväg i Azure Blob storage att läsa från och skriva till anslutna blob-lagringen.

## <a name="option-3-use-external-durable-storage"></a>Alternativ 3: Använd extern varaktig lagring

Du kan använda extern varaktig lagring för att bevara tillstånd under körning. Det här alternativet är användbart i följande scenarier:
- Dina indata lagras redan i varaktig lagring som kan nås från beräkning målmiljön.
- Filerna behöver inte vara en del av körningshistorik-poster.
- Filerna måste delas av körningar i olika beräkningsmiljöer.
- Filerna måste kunna överleva beräkningskontexten själva.

En sådan metod är att använda Azure Blob storage från Python eller PySpark-kod. Här är ett kort exempel:

```python
from azure.storage.blob import BlockBlobService
from azure.storage.blob.models import PublicAccess
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
blob_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Här är ett kort exempel för att bifoga alla godtyckliga Azure Blob storage till en HDI Spark-körningen:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Sammanfattning
Eftersom Azure Machine Learning kör skript genom att kopiera hela projektmappen till mål-beräkningskontext kan ta särskild försiktighet med stora indata, utdata och mellanliggande filer. För stora filer transaktioner, du kan använda mappen särskilda utdata, den delade mappen som kan nås via det `AZUREML_NATIVE_SHARE_DIRECTORY` miljövariabeln eller extern varaktig lagring. 

## <a name="next-steps"></a>Nästa steg
- Granska den [konfigurationsfiler för Azure Machine Learning Workbench körning](experimentation-service-configuration-reference.md) artikeln.
- Se hur [klassificera Iris](tutorial-classifying-iris-part-1.md) självstudieprojektet använder utdata-mappen för att spara en tränad modell.
