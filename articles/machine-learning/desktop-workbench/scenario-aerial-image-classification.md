---
title: Flygfoto avbildningen klassificering | Microsoft Docs
description: Innehåller instruktioner för verkligt scenario på Flygfoto avbildningen klassificering
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: 74cb72b4ddfbeace5e2409dfac4f4b6bf21ffa25
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="aerial-image-classification"></a>Flygfoto avbildningen klassificering

Det här exemplet visar hur du använder Azure Machine Learning-arbetsstationen för att koordinera distribuerade utbildnings- och operationalization av avbildningen klassificering modeller. Vi använder två metoder för träning: (i) förfina en djupa neurala nätverket med hjälp av en [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) GPU-kluster och (ii) med hjälp av den [Microsoft Machine Learning för Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) paketet till featurize bilder med pretrained CNTK modeller och för att träna klassificerare använder härledda funktioner. Vi använder sedan tränade modeller i parallella sätt stor bild anger i molnet med hjälp av en [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) klustret, så att vi kan skala på utbildning och operationalization genom att lägga till eller ta bort arbetarnoder.

Det här exemplet visar två metoder för att överföra learning som utnyttjar pretrained modeller för att undvika kostnader för djupa neurala nätverk från grunden. Omtränings djupa neurala nätverk vanligtvis kräver GPU beräkning, men kan leda till större precision när träningsmängden är tillräckligt stor. Tränar en enkel klassificerare på featurized bilder kräver inte GPU beräkning, natur snabb och skalbar godtyckligt och passar färre parametrar. Den här metoden är därför ett utmärkt val när utbildning exemplen är tillgängliga--vilket ofta är fallet för anpassade användningsfall. 

Spark-kluster som används i det här exemplet har 40 arbetarnoder och kostnader för ~$40/hr ska fungera; klusterresurser Batch AI omfattar åtta GPU-kort och kostnaden ~$10/hr ska fungera. Slutför den här genomgången tar cirka tre timmar. När du är klar följer du anvisningarna rensa och ta bort resurser som du har skapat och stoppa medför kostnader.

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Offentliga GitHub-lagringsplatsen för den här verkligt scenario innehåller alla material, inklusive kodexempel som behövs för det här exemplet:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Använd case beskrivning

I det här scenariot träna vi machine learning-modeller att klassificera typ av mark som visas i Flygfoto avbildningar av 224 mätaren x 224 mätaren områden. Mark Använd klassificering modeller som kan användas för att spåra urbanization, kalhuggning, förlust av våtmarker och andra viktiga miljön trender som regelbundet använder samlas in Flygfoto bilder visar färgerna. Vi har förberett utbildning och validering bilden anger baserat på bilder visar färgerna från USA Nationella jordbruket bilder visar färgerna Program och mark använda etiketter som publicerats av USA: s Nationella mark omfattar databas. Exempel bilder i varje mark användningsklass visas här:

![Exempel regioner för varje mark använder etikett](media/scenario-aerial-image-classification/example-labels.PNG)

Efter utbildnings- och verifiera klassificerare modellen ska vi användas för Flygfoto bilder utsträckning Middlesex region, MA--home av Microsofts New England forskning och utveckling (NERD) Center – för att demonstrera hur dessa modeller kan användas för att studera trender i Urbant utveckling.

För att skapa en avbildning klassificerare med överföring Lär dataanalytiker ofta skapa flera modeller med olika metoder och välj de mest performant modell. Azure Machine Learning arbetsstationen kan hjälpa data forskare samordna utbildning mellan olika beräknings-miljöer, spåra och jämför prestanda för flera modeller och använda en vald modell för stora datamängder i molnet.

## <a name="scenario-structure"></a>Scenario-struktur

I det här exemplet sparades bilddata och pretrained modeller i ett Azure storage-konto. Ett Azure HDInsight Spark-kluster läser filerna och skapar en modell för klassificering av avbildning med hjälp av MMLSpark. Den tränade modellen och dess förutsägelser skrivs sedan till lagringskontot, där de kan analyseras och visualiseras av en Jupyter-anteckningsbok körs lokalt. Azure Machine Learning arbetsstationen samordnar fjärrkörning av skript i Spark-klustret. Den spårar också noggrannhet mätvärden för flera modeller tränas med olika metoder, vilket gör att användaren kan välja de mest performant modell.

![Skiss för Flygfoto avbildningen klassificering verkligt scenario](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Dessa stegvisa anvisningar börjar du med guidar dig genom att skapa och förberedelse av en Azure storage-konto och Spark-kluster, inklusive data transfer och beroende installation. De kan sedan beskrivs hur du starta utbildning jobb och jämför prestanda för de resulterande modellerna. Slutligen illustrerar de hur du kan tillämpa en vald modell till en stor bild i Spark-klustret och analysera resultaten förutsägelse lokalt.


## <a name="set-up-the-execution-environment"></a>Ställ in körningsmiljön

Följande instruktionerna leder dig igenom processen för att konfigurera körningsmiljö för det här exemplet.

### <a name="prerequisites"></a>Förutsättningar
- En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns)
    - Du skapar ett HDInsight Spark-kluster med 40 arbetarnoder (168 kärnor totala). Kontrollera att ditt konto har tillräckligt med tillgängliga kärnor genom att granska ”användning + kvoter” för din prenumeration på Azure-portalen.
       - Om du har färre kärnor som är tillgängliga kan du ändra mallen HDInsight-kluster för att minska antalet personer som har etablerats. Instruktioner för detta visas under avsnittet ”Skapa HDInsight Spark-kluster”.
    - Det här exemplet skapar en Batch AI utbildning-kluster med två NC6 (1 GPU, 6 vCPU) virtuella datorer. Kontrollera att ditt konto har tillräckligt med tillgängliga kärnor i östra USA genom att granska ”användning + kvoter” för din prenumeration på Azure-portalen.
- [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)
    - Följ den [installera och skapa Quickstart](../service/quickstart-installation.md) att installera Azure Machine Learning arbetsstationen och skapa undersökningar och konton för hantering av modellen.
- [Batch-AI](https://github.com/Azure/BatchAI) Python SDK och Azure CLI 2.0
    - Gå igenom följande avsnitt i den [Batch AI recept viktigt](https://github.com/Azure/BatchAI/tree/master/recipes):
        - ”Krav”
        - ”Skapa och få programmet Azure Active Directory (AAD)”
        - ”Registrera BatchAI Resursproviders” (under ”kör recept med hjälp av Azure CLI 2.0”)
        - ”Installera Azure Batch AI Management-klienten”
        - ”Installera Azure Python SDK”
    - Klient-ID-post, secret och klient-ID för Azure Active Directory-program kommer du att skapa. Dessa autentiseringsuppgifter används senare i den här kursen.
    - När detta skrivs Azure Machine Learning arbetsstationen och Azure Batch AI kan du använda separata fildelar av Azure CLI 2.0. För tydlighetens skull kallar vi i arbetsstationen version av CLI som ”en CLI som startas från Azure Machine Learning arbetsstationen” och allmänna-versionen (som innehåller Batch AI) ”Azure CLI 2.0”.
- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)en kostnadsfria verktyg för att koordinera filöverföring mellan Azure storage-konton
    - Kontrollera att mappen som innehåller den körbara filen AzCopy finns på systemets PATH-miljövariabeln. (Instruktioner för att ändra miljövariabler finns [här](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- En SSH-klienten. Vi rekommenderar [PuTTY](http://www.putty.org/).

Det här exemplet har testats på en Windows 10-dator; du ska kunna köra den från alla Windows-datorer, inklusive Azure datavetenskap virtuella datorer. Azure CLI 2.0 har installerats från en MSI enligt [instruktionerna](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Mindre ändringar kan krävas (t.ex, ändringar av filepaths) när du kör det här exemplet på macOS.

### <a name="set-up-azure-resources"></a>Konfigurera Azure-resurser

Det här exemplet kräver ett HDInsight Spark-kluster och ett Azure storage-konto för värden relevanta filer. Följ instruktionerna för att skapa dessa resurser i en ny Azure resursgrupp:

#### <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:
1.  Öppna Azure Machine Learning-arbetsstationen
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök projektmallar** sökrutan, Skriv ”Flygfoto avbildningen klassificering” och välj mallen
5.  Klicka på **Skapa**
 
#### <a name="create-the-resource-group"></a>Skapa en resursgrupp

1. När du läser in ditt projekt i Azure Machine Learning arbetsstationen, öppna en kommandoradsgränssnittet (CLI) genom att klicka på Arkiv -> Öppna Kommandotolken.
    Använd den här versionen av CLI för flesta av kursen. (Där anges, du uppmanas att öppna en annan version av CLI för att förbereda Batch AI-resurser.)

1. Från kommandoradsgränssnittet, loggar du in på Azure-konto genom att köra följande kommando:

    ```
    az login
    ```

    Du uppmanas att besöka en URL och ange en tillfällig angivna kod. webbplatsen begär dina Azure-autentiseringsuppgifter.
    
1. När inloggningen är klar, gå tillbaka till CLI och kör följande kommando för att avgöra vilka Azure-prenumerationer som är tillgängliga för ditt Azure-konto:

    ```
    az account list
    ```

    Det här kommandot visar alla prenumerationer som är kopplade till ditt Azure-konto. Hitta ID för den prenumeration som du vill använda. Skriv prenumerations-ID ange där det anges i följande kommando och sedan aktiv prenumeration genom att köra kommandot:

    ```
    az account set --subscription [subscription ID]
    ```

1. Azure-resurser skapas i det här exemplet lagras tillsammans i en Azure-resursgrupp. Välj ett unikt resursgruppens namn och skriver den där anges, kör du båda kommandon för att skapa Azure-resursgrupp:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Skapa lagringskontot

Nu kan vi skapa storage-konto att värdar projektfiler som måste kunna nås av HDInsight Spark.

1. Välj ett unikt lagringskontonamn och skriver den där det anges i följande `set` kommandot och sedan skapa ett Azure storage-konto genom att köra båda kommandon:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Lista nycklar till lagringskontot genom att köra följande kommando:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Registrera värdet för `key1` som lagringsnyckel i kommandot Kör kommando för att lagra värdet.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Skapa en filresurs med namnet `baitshare` i ditt lagringskonto med följande kommando:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. I valfri textredigerare, läsa in den `settings.cfg` från projektet Azure Machine Learning arbetsstationen ”Code” underkatalog och infoga lagringskontonamn och nyckel som anges. Spara och Stäng den `settings.cfg` filen.
1. Om du inte redan har gjort det hämtar och installerar den [AzCopy](http://aka.ms/downloadazcopy) verktyget. Kontrollera att den körbara filen AzCopy finns på systemsökvägen genom att skriva ”AzCopy” och trycka på RETUR för att visa dess dokumentation.
1. Utfärda följande kommandon för att kopiera alla exempeldata, pretrained modeller och modellen utbildning skript till lämplig plats i ditt lagringskonto:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Förvänta dig filöverföring ta ungefär en timme. Medan du väntar kan du fortsätta till följande avsnitt: du kan behöva öppna en annan kommandoradsgränssnittet via arbetsstationen och definiera om det tillfälliga variabler.

#### <a name="create-the-hdinsight-spark-cluster"></a>Skapa HDInsight Spark-kluster

Vår metod som rekommenderas för att skapa ett HDInsight-kluster använder HDInsight Spark-kluster resource manager-mallen finns i undermappen ”Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning” i det här projektet.

1. Mallen HDInsight Spark-klustret är i ”template.json” filen undermappen ”Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning” i det här projektet. Som standard skapar mallen ett Spark-kluster med 40 arbetsnoderna. Om du måste justera numret öppna mallen i valfri textredigerare och Ersätt alla förekomster av ”40” med worker nodnumret på ditt val.
    - Minnet är slut fel kan inträffa senare om antalet arbetarnoder som du väljer är mindre. För att bekämpa minnesfel, kan du köra skript utbildning och operationalization för en delmängd med tillgängliga data som beskrivs senare i det här dokumentet.
2. Välj ett unikt namn och lösenord för till HDInsight-kluster och skriva dem där det anges i följande kommando: skapa klustret genom att utfärda kommandon:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Ditt kluster-distributionen kan ta upp till 30 minuter (inklusive körningen på etablering och skript).

### <a name="set-up-batch-ai-resources"></a>Ange Batch AI-resurser

Du kan förbereda Batch AI Network File Server (NFS) och GPU-klustret medan du väntar Filöverföring för Storage-konto och Spark-kluster-distribution för att slutföra. Öppna en kommandotolk med Azure CLI 2.0 och kör följande kommando:

```
az --version 
```

Bekräfta att `batchai` visas i listan med installerade moduler. Om inte, kan du använda en annan kommandoradsgränssnittet (till exempel en öppnas via arbetsstationen).

Kontrollera sedan att registreringen har slutförts. (Registreringen av providern tar upp till femton minuter och eventuellt fortfarande pågående om du nyligen har slutfört den [Batch AI instruktioner](https://github.com/Azure/BatchAI/tree/master/recipes).) Kontrollera att både ”Microsoft.Batch” och ”Microsoft.BatchAI” visas med statusen ”registrerad” i utdata av följande kommando:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Om inte, kör följande provider registrering kommandon och vänta ~ 15 minuter för registrering att slutföra.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Ändra följande kommandon för att ersätta uttrycken inom hakparenteser med värden som du använde tidigare under skapande av resurs grupp och storage-konto. Lagra sedan värdena som variabler genom att köra kommandon:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Identifiera den mapp som innehåller din Azure Machine Learning-projektet (till exempel `C:\Users\<your username>\AzureML\aerialimageclassification`). Ersätt värdet inom hakparenteser med mappens filepath (med inga avslutande omvänt snedstreck) och kör kommandot:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Du är nu redo att skapa Batch AI-resurser som krävs för den här kursen.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Förbereda nätverksserver för AI i Batch

Batch AI-klustret har åtkomst till din utbildning data på en filserver i nätverket. Dataåtkomst kanske several-fold snabbare vid åtkomst till filer från en NFS jämfört med en Azure-filresursen eller Azure Blob Storage.

1. Utfärda följande kommando för att skapa en filserver i nätverket:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p "Dem0Pa$$w0rd" --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Kontrollera Etableringsstatus på filservern nätverk med följande kommando:

    ```
    az batchai file-server list
    ```

    När filservern nätverk med namnet ”landuseclassifier” ”provisioningState” är ”lyckades”, är det redo för användning. Förvänta dig etablering för att ta cirka fem minuter.
1. Hitta IP-adressen för din NFS i utdata från det föregående kommandot (egenskapen ”fileServerPublicIp” under ”mountSettings”). Skriv IP-adress där det anges i följande kommando och sedan lagra värdet genom att köra kommandot:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Med hjälp av din favorit SSH-verktyget (följande exempel kommando använder [PuTTY](http://www.putty.org/)), köra det här projektet `prep_nfs.sh` skriptet på NFS att överföra avbildningen utbildning och validering anger det.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Behöver inte bry om hämtning och extrahering statusuppdateringar data rulla över fönstret shell så snabbt att de är oläslig.

Om du vill kan du bekräfta att dataöverföringen har fortsatte som planerat genom att logga in på filservern med din favorit SSH-verktyget och kontrollera den `/mnt/data` kataloginnehållet. Du bör hitta två mappar, training_images och validation_images använder varje som innehåller med undermappar med namnet enligt mark kategorier.  Uppsättningar för träning och validering ska innehålla ~ 44 k och ~ 11 k bilder respektive.

#### <a name="create-a-batch-ai-cluster"></a>Skapa en Batch AI-kluster

1. Skapa klustret genom att följande kommando:

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p "Dem0Pa$$w0rd" --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. Använd följande kommando för att kontrollera Etableringsstatus för klustret:

    ```
    az batchai cluster list
    ```

    När tillståndet allokering för klustret med namnet ”landuseclassifier” ändringar från ändrar storlek så att konstant, är det möjligt att skicka jobb. Dock startar jobb inte köras tills alla virtuella datorer i klustret har lämnat tillståndet ”förbereda”. Om egenskapen ”fel” i klustret inte är null, ett fel uppstod när klustret skapas och den bör inte användas.

#### <a name="record-batch-ai-training-credentials"></a>Registrera Batch AI utbildning autentiseringsuppgifter

Medan du väntar för klustret tilldelning att slutföra, öppna den `settings.cfg` filen från underkatalogen ”kod” för det här projektet i textredigeraren önskat. Uppdatera följande variabler med dina autentiseringsuppgifter:
- `bait_subscription_id` (36 tecken Azure-prenumeration ID)
- `bait_aad_client_id` (Azure Active Directory-program/klient-ID anges i avsnittet ”förutsättningar”)
- `bait_aad_secret` (Azure Active Directory programhemlighet anges i avsnittet ”förutsättningar”)
- `bait_aad_tenant` (Azure Active Directory klient-ID anges i avsnittet ”förutsättningar”)
- `bait_region` (när detta skrivs är det enda alternativet: eastus)
- `bait_resource_group_name` (resursgrupp som du valde tidigare)

När du har tilldelat dessa värden bör ändrade raderna i filen settings.cfg likna följande:

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Spara och Stäng `settings.cfg`.

Du kan stänga fönstret CLI där du kör kommandona Batch AI resursen skapas. Alla ytterligare steg i den här kursen använder en CLI som startas från Azure Machine Learning-arbetsstationen.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Förbereda körningsmiljö Azure Machine Learning arbetsstationen

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Registrera HDInsight-klustret som ett mål för beräkning av Azure Machine Learning arbetsstationen

När HDInsight-klustret har skapats, registrerar du klustret som beräkning mål för projektet enligt följande:

1.  Kör du följande kommando från Azure Machine Learning kommandoradsgränssnittet:

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    Detta kommando lägger till två filer, `myhdi.runconfig` och `myhdi.compute`, i ditt projekt `aml_config` mapp.

1. Öppna den `myhdi.compute` filen i valfri textredigerare. Ändra den `yarnDeployMode: cluster` raden till `yarnDeployMode: client`, spara och stäng filen.
1. Kör följande kommando för att förbereda miljön för HDInsight:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Installera lokala beroenden

Öppna en CLI från Azure Machine Learning arbetsstationen och installera beroenden som krävs för lokal körning av följande kommando:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Förvärv och förståelse av data

Det här scenariot använder offentligt tillgängliga Flygfoto bilder visar färgerna data från den [nationella jordbruket bilder visar färgerna programmet](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) med 1 mätaren upplösning. Vi har genererat filuppsättningar 224 pixel x 224 pixel PNG beskäras från de ursprungliga NAIP data och sorteras enligt mark Använd etiketter från den [nationella mark omfattar databasen](https://www.mrlc.gov/nlcd2011.php). En exempelbild med etiketten ”utvecklade” visas i full storlek:

![En exempel-panel utvecklade mark](media/scenario-aerial-image-classification/sample-tile-developed.png)

Klassen belastningsutjämnade uppsättningar med ~ 44 k och 11 k bilder används för modellen utbildning och verifiering, respektive. Vi visar modellen distribution på en ~ 67 k avbildning ange färdiga Middlesex region, MA--home av Microsofts New England forskning och utveckling (NERD) center. Mer information om hur dessa uppsättningar av avbildningen konstruerades finns i [Embarrassingly parallella avbildningen klassificering git-lagringsplats](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Platsen för Middlesex regionen, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

De Flygfoto bild som används i det här exemplet har överförts till lagringskontot som du skapade under installationen. Utbildning, verifiering och operationalization avbildningar är alla 224 pixel x 224 pixel PNG-filer med en upplösning på en bildpunkt per kvadratisk mätaren. Utbildning och validering bilder har tagits ordnade i undermappar baserat på deras mark Använd etikett. (Mark Använd etiketter operationalization avbildningar är okända och i många fall tvetydig; vissa av dessa avbildningar innehåller flera mark-typer.) Mer information om hur dessa uppsättningar av avbildningen konstruerades finns i [Embarrassingly parallella avbildningen klassificering git-lagringsplats](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Visa exempel bilder i ditt Azure storage-konto (valfritt):
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter namnet på ditt lagringskonto i sökfältet överst på skärmen. Klicka på ditt lagringskonto i sökresultaten.
2. Klicka på länken ”BLOB” i huvudfönstret för storage-konto.
3. Klicka på behållaren med namnet ”train”. Du bör se en lista över kataloger som heter enligt mark använder.
4. Klicka på någon av de här katalogerna att läsa in listan över bilder som den innehåller.
5. Klicka på någon bild och ladda ned för att visa bilden.
6. Om du vill kan du klicka på behållare med namnet ”test” och ”middlesexma2016” för att visa innehållet samt.

## <a name="modeling"></a>Modellering

### <a name="training-models-with-azure-batch-ai"></a>Utbildning modeller med Azure Batch AI

Den `run_batch_ai.py` skriptet i mappen ”Code\02_Modeling” i projektet arbetsstationen används för att utfärda ett Batch AI utbildning jobb. Det här jobbet retrains image-klassificerare DNN som valts av användaren (AlexNet eller ResNet 18 pretrained på ImageNet). Djupet för omtränings kan också anges: omtränings det sista säkerhetslagret i nätverket kan minska overfitting när utbildning exemplen är tillgängliga när finjustera hela nätverket (eller för AlexNet fullt ansluten lager) kan leda till större modellen prestanda när träningsmängden är tillräckligt stor.

När utbildning jobbet är slutfört, sparar det här skriptet modellen (tillsammans med en fil som beskriver mappningen mellan modellens heltal utdata och etiketter sträng) och förutsägelser till blob storage. Jobbet BETE loggfilen analyseras för att extrahera timecourse av fel hastighet förbättring jämfört med utbildning epoker. Fel hastighet improvement timecourse loggas till AML arbetsstationen kör funktionen för visning av senare.

Välj ett namn för din tränad modell och en pretrained modelltyp omtränings djup. Skriv dina val där det anges i följande kommando och sedan börjar via programmering genom att köra kommandot från en Azure ML kommandoradsgränssnittet:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Förvänta dig Azure Machine Learning som körs för att ta ungefär en halvtimme för att slutföra. Vi rekommenderar att du kör några liknande kommandon (varierande modellnamnet utdata och pretrained modelltypen omtränings djup) så att du kan jämföra prestanda i modeller tränas med olika metoder.

### <a name="training-models-with-mmlspark"></a>Utbildning modeller med MMLSpark

Den `run_mmlspark.py` skriptet i mappen ”Code\02_Modeling” i projektet arbetsstationen används för att träna en [MMLSpark](https://github.com/Azure/mmlspark) modellen för klassificering av avbildningen. Skriptet första featurizes utbildning ange bilder med en avbildning klassificerare DNN pretrained på ImageNet datauppsättningen (AlexNet eller en ResNet 18 layer). Skriptet använder sedan featurized avbildningar för att träna en MMLSpark modell (en slumpmässig skog eller en logistic regressionsmodell) att klassificera avbildningar. Uppsättningen test avbildningen är sedan featurized och bedömas med den tränade modellen. Riktighet modellens förutsägelser på uppsättningen test beräknas och inloggade Azure Machine Learning-arbetsstationens kör funktionen. Slutligen sparas den tränade modellen MMLSpark och dess förutsägelser på uppsättningen test för att blob storage.

Välj ett unikt utdata modellnamn för din tränad modell, pretrained modelltypen och en MMLSpark modelltypen. Skriv dina val där det anges i mallen följande kommando påbörja via programmering genom att köra kommandot från en Azure ML kommandoradsgränssnittet:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Ytterligare `--sample_frac` parametern kan användas för att träna och testa modellen med en delmängd av tillgängliga data. Om du använder ett litet exempel bråk minskar runtime och minne krav på bekostnad av tränade modellen noggrannhet. (Till exempel ett kör med `--sample_frac 0.1` förväntas ta ungefär 20 minuter.) Mer information om den här och andra parametrar kör `python Code\02_Modeling\run_mmlspark.py -h`.

Användarna uppmanas att köra detta skript flera gånger med olika indataparametrar. Prestanda för de resulterande modellerna kan sedan jämföras i Azure Machine Learning-arbetsstationens kör funktionen.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Jämföra modellen prestanda med hjälp av funktionen arbetsstationen kör tidigare

När du har genomfört två eller flera utbildning körs båda typer av, kan du gå till funktionen Körningshistorik i arbetsstationen genom att klicka på ikonen klockan längs vänstra menyraden. Välj `run_mmlspark.py` från listan över skript längst till vänster. En ruta läser in jämföra test ange precisionen för alla körs. Bläddra nedåt och klicka på namnet på en enskild kör om du vill se mer information.

## <a name="deployment"></a>Distribution

Om du vill använda en av dina tränade modeller Flygfoto bilder sida vid sida med fjärrkörning på HDInsight Middlesex region, MA, infoga din önskade modellnamn i följande kommando och kör det:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Ytterligare `--sample_frac` parametern kan användas för att operationalisera modellen med en delmängd av tillgängliga data. Om du använder ett litet exempel bråk minskar runtime och minne krav på bekostnad av förutsägelse är fullständig. Mer information om den här och andra parametrar kör `python Code\03_Deployment\batch_score_spark -h`.

Det här skriptet skriver modellens förutsägelser till ditt lagringskonto. Förutsägelser granskas som beskrivs i nästa avsnitt.

## <a name="visualization"></a>Visualisering

”Modellen förutsägelse analys” Jupyter-anteckningsbok i undermappen ”Code\04_Result_Analysis” i projektet arbetsstationen visualizes förutsägelser för en modell. Läsa in och kör den bärbara datorn enligt följande:
1. Öppna projektet i arbetsstationen och klicka på mappen (”filer”) ikonen längs den vänstra menyn att läsa in kataloglistan.
2. Navigera till undermappen ”Code\04_Result_Analysis” och klicka på den bärbara datorn med namnet ”modell förutsägelse analys”. En förhandsgranskning återgivningen av den bärbara datorn ska visas.
3. Klicka på ”Starta anteckningsboken Server” för att läsa in den bärbara datorn.
4. Ange namnet på modell vars resultat du vill analysera där det anges i den första cellen.
5. Klicka på ”Cell -> Kör alla” att köra alla celler i den bärbara datorn.
6. Läsa tillsammans med anteckningsboken på Läs mer om analys och visualiseringar som det visas.

## <a name="cleanup"></a>Rensa
När du har slutfört exemplet, rekommenderar vi att du tar bort alla resurser som du har skapat genom att köra följande kommando från Azure-kommandoradsgränssnittet:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Referenser

- [Databasen Embarrassingly parallella avbildningen klassificering](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Beskriver dataset konstruktion från gratis bilder visar färgerna och etiketter
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub-lagringsplatsen
   - Innehåller ytterligare exempel på modellen träning och utvärdering med MMLSpark

## <a name="conclusions"></a>Slutsatser

Azure Machine Learning arbetsstationen hjälper datavetare enkelt distribuera koden på fjärranslutna beräkning mål. I det här exemplet distribuerades lokal MMLSpark utbildning kod för fjärrkörning på ett HDInsight-kluster och lokala skriptet startas ett utbildning jobb på Azure Batch AI GPU-kluster. Azure Machine Learning-arbetsstationens kör funktionen spåras prestanda för flera modeller och hjälp oss att identifiera den mest korrekta modellen. Arbetsstationens Jupyter-anteckningsböcker funktionen hjälpt visualisera förutsägelser för våra modeller i en interaktiv, grafisk miljö.

## <a name="next-steps"></a>Nästa steg
Att gå på djupet i det här exemplet:
- Klicka på växeln symboler för att välja vilka diagram och mått som visas i Azure Machine Learning-arbetsstationens Körningshistorik funktionen.
- Granska exempelskript för instruktioner anropar den `run_logger`. Kontrollera att du förstår hur varje mått registreras.
- Granska exempelskript för instruktioner anropar den `blob_service`. Kontrollera att du förstår hur tränade modeller och förutsägelser lagras och hämtas från molnet.
- Utforska innehållet i behållare som skapats i blob storage-konto. Se till att du förstår vilka skriptet eller kommandot är ansvarig för att skapa varje grupp med filer.
- Ändra skriptet utbildning för att träna en annan MMLSpark modelltyp eller ändra justeringsmodeller. Använd funktionen körningshistoriken för att avgöra om ändringarna ökas eller minskas modellens precision.
