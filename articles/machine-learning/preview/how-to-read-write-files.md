---
title: "Läsa och skriva stora datafiler | Microsoft Docs"
description: "Läsa och skriva stora filer i Azure Machine Learning-experiment."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: f5c75b95d9019c15bb402313ce7407fa9abb81d4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="persisting-changes-and-working-with-large-files"></a>Spara ändringarna och arbetar med stora filer
Du kan konfigurera olika mål för körning med tjänsten Azure Machine Learning-experiment. Vissa mål är lokal kan till exempel en lokal dator eller en dockerbehållare på en lokal dator. Andra användare är fjärranslutna till exempel en dockerbehållare på en fjärrdator eller ett HDInsight-kluster. Mer information finns i [översikt av Azure Machine Learning experimentera körningstjänsten](experimentation-service-configuration.md). 

Innan du kan köra på ett mål, måste du kopiera projektmappen beräknings-målet. Du måste göra det även med en lokal körning som använder en lokal tillfällig mapp för detta ändamål. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Körningen isolering, överföring och reproducerbara
Syftet med den här designen är att kontrollera isolering, reproducerbara och överföring av körningen. Om du kör skriptet för samma två gånger på samma eller en annan för att beräkna mål, du får samma resultat. De ändringar som gjorts under den första körningen bör inte påverkar de andra körning. Med den här designen kan hantera du beräknings-mål som tillståndslös beräkningsresurser som har ingen tillhörighet till jobb som körs när de är klar.

## <a name="challenges"></a>Utmaningar
Även om den här designen ger dig fördelarna med portability och repeterbarhet, medför det också vissa unika utmaningar.

### <a name="persisting-state-changes"></a>Spara tillståndsändringar
Om skriptet ändrar status för beräknings-kontext, ändringarna sparas inte för din nästa körning och de inte har spridits till klientdatorn automatiskt. 

Mer specifikt om skriptet skapar en undermapp eller skriver en fil, mappen eller filen inte finns i projektkatalogen efter körning. Filerna lagras i en tillfällig mapp i målmiljön beräkning. Du kan använda dem för felsökning, men du kan inte förlita dig på permanent sådana.

### <a name="working-with-large-files-in-the-project-folder"></a>Arbeta med stora filer i projektmappen

Om din projektmapp innehåller stora filer kan medföra fördröjning när mappen kopieras till beräkning målmiljön i början av en körning. Även om körningen sker lokalt, är det fortfarande onödiga disk-trafik för att undvika. Därför cap vi för närvarande maximala projektet storleken på 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Alternativ 1: Använd den *matar ut* mapp
Det här alternativet är bättre om samtliga följande villkor gäller:
* Skriptet ger filer.
* Du förväntar dig att filerna ska ändras med varje test.
* Du vill behålla en historik av dessa filer. 

Vanliga användningsområden är:
* En modell
* Skapa en datamängd
* Ett diagram skrivs ut som en bild som en del av modellen utbildning-körning 

Dessutom kan vill du jämföra utdata över körs väljer en utdatafil (till exempel en modell) som producerats av ett tidigare kör och sedan använda den för en efterföljande aktivitet (till exempel resultatfunktioner).

Du kan skriva filer till en mapp med namnet *matar ut* som är relativ till rotkatalogen. Mappen får särskild behandling av tjänsten experiment. Något skriptet skapas i mappen under körningen, till exempel en modellfil, data eller ritade avbildningsfil (kallas gemensamt _artefakter_), kopieras till Azure Blob storage-konto som är associerad med din experiment konto när körningen har slutförts. Filerna blir en del av din kör historikpost.

Här är en kort exempel kod för att lagra en modell i den *matar ut* mapp:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Du kan hämta någon artefakt genom att bläddra till den **utdatafilerna** avsnittet av detaljsidan kör för en viss körs i Azure Machine Learning-arbetsstationen. Välj bara kör och välj sedan den **hämta** knappen. Alternativt kan du ange den `az ml asset download` i fönstret kommandoradsgränssnittet (CLI).

En mer komplett exempel finns i `iris_sklearn.py` Python skript i den _klassificera Iris_ exempelprojektet.

## <a name="option-2-use-the-shared-folder"></a>Alternativ 2: Använd den delade mappen
Om du inte behöver sparas varje körning filer kan med hjälp av en delad mapp som kan nås över oberoende körs vara ett bra alternativ för följande scenarier: 
- Skriptet behöver läsa in data från lokala filer, till exempel CSV-filer eller en katalog med text- eller bilddata filer som utbildnings- eller data. 
- Skriptet bearbetar rådata och skriver ut mellanresultat, till exempel featurized utbildningsdata från text- eller bilddata filer som används i ett efterföljande utbildning kör. 
- Skriptet spits ut en modell och efterföljande bedömningsprofil skriptet måste hämta modellen och använda den för utvärdering. 

Det är viktigt att Observera att mappen finns lokalt på valda beräknings-målet. Därför måste det här alternativet fungerar bara om alla dina skript körs som refererar till den delade mappen körs på samma compute mål och beräknings-målet är inte återanvändas mellan körs.

Genom att utnyttja funktionen delad mapp, kan du läsa från eller skriva till mappen som identifieras av en miljövariabel `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Exempel
Här är några exempel Python-kod för att läsa och skriva till en textfil med den här mappen för resursen:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

En mer komplett exempel finns i *iris_sklearn_shared_folder.py* filen i den _klassificera Iris_ exempelprojektet.

Innan du kan använda denna funktion kan du behöva ange den *.compute* filen några enkla konfigurationer som representerar den aktuella körningskontexten i den *aml_config* mapp. Den faktiska sökvägen till mappen kan variera beroende på beräknings-mål som du väljer och det värde som du konfigurerar.

### <a name="configure-local-compute-context"></a>Konfigurera lokala beräknings-kontexten

Om du vill aktivera den här funktionen i en kontext som lokala beräkning, lägger du till i din *.compute* filen följande raden som representerar den _lokala_ miljö (vanligen namnet *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

Sökvägen ~/.azureml/share är basmapp standardsökvägen. Du kan ändra den till en lokal absolut sökväg som är tillgänglig genom att köra skriptet. Experiment kontonamn, arbetsytans namn och projektnamn läggs automatiskt till baskatalog namn, som blir den fullständiga sökvägen till den delade katalogen. Till exempel filer kan skrivas till (och hämtas från) på följande sökväg om du använder den föregående Standardvärde:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Konfigurera Docker beräkning kontexten (lokala eller fjärranslutna)
Om du vill aktivera den här funktionen på en Docker beräkning kontext, måste du lägga till följande två rader till din lokala eller fjärranslutna Docker *.compute* fil.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>Den **sharedVolumes** egenskapen måste anges till *SANT* när du använder den `AZUREML_NATIVE_SHARE_DIRECTORY` miljövariabeln för att få åtkomst till den delade mappen. Annars misslyckas körningen.

Den kod som körs i dockerbehållare alltid ser den delade mappen som */azureml-share /*. Sökvägen till mappen kan som visas av Docker-behållaren inte konfigureras. Använd inte den här mappnamn i koden. Använd i stället alltid miljövariabeln `AZUREML_NATIVE_SHARE_DIRECTORY` att referera till den här mappen. Är mappad till en lokal mapp på Docker värden datorn eller compute-kontexten. Baskatalog för lokala mappen är konfigurerbar värdet för den `nativeSharedDirectory` i den *.compute* fil. Den lokala sökvägen till den delade mappen på värddatorn, är om du använder standardvärdet följande:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>Sökvägen till den delade mappen på den lokala disken är samma oavsett om det är en lokal beräknings-kontext eller en lokal Docker beräkning kontext. Det innebär att du kan också dela filer mellan en lokal körning och en lokal Docker-körning.

Du kan placera indata direkt i dessa mappar och räknar med att lokal- eller Docker körs på datorn kan hämta den. Du kan också skriva filer till den här mappen från din lokala eller Docker körs och räknar filer hämta kvar i mappen, kvarvarande livscykeln för körning.

Mer information finns i [Azure Machine Learning arbetsstationen körning konfigurationsfiler](experimentation-service-configuration-reference.md).

>[!NOTE]
>Den `AZUREML_NATIVE_SHARE_DIRECTORY` miljövariabeln stöds inte i en kontext för beräkning av HDInsight. Det är lätt att åstadkomma detta genom att uttryckligen med hjälp av en absolut sökväg i Azure Blob storage kan läsa från och skriva till anslutna blob-lagring.

## <a name="option-3-use-external-durable-storage"></a>Alternativ 3: Använda externa beständig lagring

Du kan använda extern beständig lagring för att bevara tillstånd under körning. Det här alternativet är användbart i följande scenarier:
- Inkommande data lagras redan i varaktiga lagring som är tillgänglig från beräkning målmiljön.
- Filerna som behöver inte vara en del av körningshistorik-poster.
- Filerna måste delas av körningar mellan olika miljöer för beräkning.
- Filerna måste kunna överleva kontexten beräkning.

En metod är att använda Azure Blob storage från Python eller PySpark koden. Här är en kort exempel:

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Här är en kort exempel för att koppla en godtycklig Azure Blob storage till en HDI Spark-körning:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Slutsats
Eftersom Azure Machine Learning kör skript genom att kopiera hela projektmappen till målet compute kontext, noggrann med stora indata, utdata och mellanliggande filer. För stora filöverföringar, kan du använda mappen särskilda utdata, den delade mappen som kan nås via de `AZUREML_NATIVE_SHARE_DIRECTORY` miljövariabeln eller extern beständig lagring. 

## <a name="next-steps"></a>Nästa steg
- Granska de [Azure Machine Learning arbetsstationen körning konfigurationsfiler](experimentation-service-configuration-reference.md) artikel.
- Se hur [klassificera Iris](tutorial-classifying-iris-part-1.md) självstudiekursen projektet använder utdata-mapp för att spara en tränad modell.
