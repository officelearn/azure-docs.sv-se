---
title: Klassificering av Flygfoto | Microsoft Docs
description: Innehåller instruktioner för verkligt scenario om klassificering av Flygfoto
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
ms.component: core
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 5ca47c8234239b56a2d829903828dda8220d53cb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967616"
---
# <a name="aerial-image-classification"></a>Klassificering av Flygfoto

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Det här exemplet visar hur du använder Azure Machine Learning Workbench för att samordna distribuerade utbildnings- och driftsättning av avbildningsklassificeringsmodeller. Vi använder två metoder för utbildning: (i) förfina en djupa neurala nätverk med hjälp av en [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) GPU-kluster och (ii) med hjälp av den [Microsoft Machine Learning för Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) paket till funktionalisera bilder med hjälp av tränats CNTK-modeller och för att träna klassificerare härledda funktioner. Vi sedan använda de tränade modellerna i parallella sätt för stor bild uppsättningar i molnet med en [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) kluster, vilket gör att vi kan skala hastighet utbildnings-och driftsättning genom att lägga till eller ta bort arbetsnoder.

Det här exemplet visar två metoder för att överföra learning, som utnyttjar tränats modeller för att undvika kostnader för djupa neurala nätverk från grunden. Träna ett djupa neurala nätverk normalt kräver GPU-beräkning, men kan leda till större noggrannhet när är tillräckligt stort. Träna en enkel klassificerare på trädmodell avbildningar kräver inte GPU-beräkning, sin natur snabbt och godtyckligt skalbara och passar färre parametrar. Den här metoden är därför ett utmärkt val när få utbildning exemplen finns tillgängliga – vilket ofta är fallet för anpassade användningsfall. 

Spark-kluster som används i det här exemplet har 40 arbetsnoder och kostar ~$40/hr ska fungera; Batch AI-klusterresurser omfattar åtta GPU: er och kostnad ~$10/hr ska fungera. Den här genomgången tar cirka tre timmar. När du är klar följer du anvisningarna rensa ta bort resurserna du har skapat och stoppa ådrar sig några kostnader.

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Offentliga GitHub-lagringsplatsen för den här verkligt scenario innehåller allt material, inklusive kodexempel som behövs för det här exemplet:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Case användarbeskrivningen

I detta scenario tränar vi machine learning-modeller att klassificera datatypen för mark som visas i Flygfoto bilder av 224 mätaren x 224 mätaren områden. Mark Använd klassificering modeller kan användas för att spåra urbanization, kalhuggning, förlust av våtmarker och andra större miljön trender med regelbundet samlas in Flygfoto bilder. Vi har förberett utbildnings- och verifiering bild uppsättningar som bygger på bilder från USA Nationella jordbruk bilder Program och mark använda etiketter som publicerats av USA: s National mark Cover-databasen. Exempel-avbildningar i varje land användningsklass visas här:

![Använder exemplet regioner för varje land](media/scenario-aerial-image-classification/example-labels.PNG)

När du utbildning och verifiera klassificerare modellen, ska vi användas för Flygfoto avbildningar utsträckning Middlesex County, MA, USA – start av Microsofts New England forskning och utveckling (NERD) Center – för att demonstrera hur dessa modeller kan användas för att studera trender i städer utveckling.

För att skapa en avbildning klassificerare med induktiv inlärning, dataexperter ofta konstruera flera modeller med flera olika metoder och välj mest högpresterande modell. Azure Machine Learning Workbench kan hjälpa data forskare koordinera utbildning mellan olika beräkningsmiljöer, spåra och jämföra prestanda för flera modeller och tillämpa en vald modell på stora datauppsättningar i molnet.

## <a name="scenario-structure"></a>Scenario-struktur

I det här exemplet förvaras avbildningsdata och tränats modeller i ett Azure storage-konto. Ett Azure HDInsight Spark-kluster läser dessa filer och skapar en modell för klassificering av avbildning med hjälp av MMLSpark. Den tränade modellen och dess förutsägelser skrivs sedan till storage-konto, där de kan analyseras och visualiseras av en Jupyter-anteckningsbok som körs lokalt. Azure Machine Learning Workbench samordnar fjärrkörning av skript i Spark-klustret. Den spårar också Precision mått för flera modeller tränas med olika metoder, så att användaren kan välja den mest högpresterande modellen.

![Skiss för Flygfoto klassificering verkligt scenario](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Dessa stegvisa anvisningar börjar guidar dig genom skapandet och förberedelsen av en Azure storage-konto och Spark-kluster, inklusive installation för överföring och beroende av data. De sedan beskrivs hur du startar upplärningsjobb och jämföra prestanda för de resulterande modellerna. Slutligen illustrerar de hur du kan tillämpa en vald modell till en stor bild uppsättning i Spark-klustret och analysera resultatet lokalt.


## <a name="set-up-the-execution-environment"></a>Konfigurera körningsmiljö

Följande anvisningar hjälper dig genom processen för att ställa in körningsmiljö för det här exemplet.

### <a name="prerequisites"></a>Förutsättningar
- En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga)
    - Du skapar ett HDInsight Spark-kluster med 40 arbetsnoder (totalt antal 168 kärnor). Kontrollera att ditt konto har tillräckligt många tillgängliga kärnor genom att granska ”användning + kvoter” fliken för din prenumeration i Azure-portalen.
       - Om du har färre kärnor tillgängliga, kan du ändra mallen för HDInsight-kluster för att minska antalet arbetare som etablerats. Instruktioner för detta visas under avsnittet ”Skapa HDInsight Spark-kluster”.
    - Det här exemplet skapar ett Batch AI Training-kluster med två NC6 (1 GPU, 6 vCPU) virtuella datorer. Kontrollera att ditt konto har tillräckligt många tillgängliga kärnor i regionen USA, östra genom att granska ”användning + kvoter” fliken för din prenumeration i Azure-portalen.
- [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)
    - Följ den [installera och skapa Quickstart](../desktop-workbench/quickstart-installation.md) att installera Azure Machine Learning Workbench och skapa experimentering och konton för modellhantering.
- [Batch AI](https://github.com/Azure/BatchAI) Python SDK och Azure CLI
    - Gå igenom följande avsnitt i den [Batch AI-recept README](https://github.com/Azure/BatchAI/tree/master/recipes):
        - ”Förutsättningar”
        - ”Skapa och få programmet Azure Active Directory (AAD)”
        - ”Registrera BatchAI Resursprovidrar” (under ”kör recept med hjälp av Azure CLI”)
        - ”Installera Azure Batch AI-Hanteringsklient”
        - ”Installera Azure Python SDK”
    - Post klient-ID, hemlighet och klient-ID för Azure Active Directory-programmet kommer du att skapa. Du använder dessa autentiseringsuppgifter senare i den här självstudien.
    - När detta skrivs Azure Machine Learning Workbench och Azure Batch AI kan du använda separata förgreningar av Azure CLI. För tydlighetens skull refererar vi till Workbench-versionen av CLI som ”en CLI som startas från Azure Machine Learning Workbench” och den allmänt-versionen (som innehåller Batch AI) som ”Azure CLI”.
- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy), ett kostnadsfritt verktyg för att samordna filöverföring mellan Azure storage-konton
    - Kontrollera att mappen som innehåller den AzCopy körbara filen finns på systemets PATH-miljövariabeln. (Instruktioner om hur du ändrar miljövariabler finns [här](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- En SSH-klient; Vi rekommenderar att [PuTTY](http://www.putty.org/).

Det här exemplet har testats på en Windows 10-dator; du ska kunna köra den från alla Windows-datorn, inklusive Azure virtuella datorer för datavetenskap. Azure CLI har installerats från en MSI enligt [instruktionerna](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Mindre ändringar kan krävas (t.ex, ändringar i filepaths) när du kör det här exemplet på macOS.

### <a name="set-up-azure-resources"></a>Ställa in Azure-resurser

Det här exemplet kräver ett HDInsight Spark-kluster och ett Azure storage-konto till värd relevanta filer. Följ dessa instruktioner för att skapa dessa resurser i en ny Azure resursgrupp:

#### <a name="create-a-new-workbench-project"></a>Skapa ett nytt Workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:
1.  Öppna Azure Machine Learning Workbench
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök efter projektmallar** sökrutan skriver du ”klassificering av Flygfoto avbildning” och väljer mallen
5.  Klicka på **Skapa**
 
#### <a name="create-the-resource-group"></a>Skapa en resursgrupp

1. När du läser in ditt projekt i Azure Machine Learning Workbench, öppna ett kommandoradsgränssnitt (CLI) genom att klicka på Arkiv -> Öppna Kommandotolken.
    Använd den här versionen av CLI för flesta av självstudiekursen. (Där det anges, du uppmanas att öppna en annan version av CLI för att förbereda Batch AI-resurser.)

1. Från kommandoradsgränssnittet, loggar du in på Azure-kontot genom att köra följande kommando:

    ```
    az login
    ```

    Du uppmanas att gå till en URL och ange ett angivet tillfälliga code; webbplatsen begär dina Azure-autentiseringsuppgifter.
    
1. När inloggningen är klar, gå tillbaka till CLI och kör följande kommando för att avgöra vilka Azure-prenumerationer som är tillgängliga på Azure-kontot:

    ```
    az account list
    ```

    Det här kommandot visar en lista över alla prenumerationer som är associerade med ditt Azure-konto. Hitta ID för den prenumeration som du vill använda. Skriv prenumerations-ID ange där det anges i följande kommando och sedan aktiv prenumeration genom att köra kommandot:

    ```
    az account set --subscription [subscription ID]
    ```

1. Azure-resurser skapas i det här exemplet lagras tillsammans i en Azure-resursgrupp. Välj ett unikt Resursgruppsnamn och skriva där det anges, och kör sedan bägge kommandona för att skapa Azure-resursgrupp:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Skapa lagringskontot

Nu kan vi skapa storage-konto att värdar projektfiler som måste kunna nås av HDInsight Spark.

1. Välj ett unikt namn på lagringskontot och skriva där det anges i följande `set` kommandot och sedan skapa ett Azure storage-konto genom att köra båda kommandon:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Lista över nycklar till lagringskontot genom att köra följande kommando:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Anteckna värdet för `key1` som storage-nyckel i följande kommando kör kommando för att lagra värdet.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Skapa en filresurs med namnet `baitshare` i ditt storage-konto med följande kommando:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. Läsa in i valfri textredigerare den `settings.cfg` från Azure Machine Learning Workbench-projekt ”Code” underkatalog och infoga lagringskontonamnet och nyckeln som anges. Spara och Stäng den `settings.cfg` filen.
1. Om du inte redan har gjort det, hämta och installera den [AzCopy](http://aka.ms/downloadazcopy) verktyget. Kontrollera att den AzCopy körbara filen finns på systemsökvägen genom att skriva ”AzCopy” och trycka på RETUR för att visa dess dokumentation.
1. Kör följande kommandon för att kopiera alla exempeldata, tränats modeller och modellen utbildningsskript till lämplig plats i ditt storage-konto:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Förvänta dig Filöverföring till ta ungefär en timme. Medan du väntar kan du fortsätta till följande avsnitt: du kan behöva öppna en annan kommandoradsgränssnitt via Workbench och definiera om de tillfälliga variablerna.

#### <a name="create-the-hdinsight-spark-cluster"></a>Skapa HDInsight Spark-kluster

Våra rekommenderade metoden för att skapa ett HDInsight-kluster använder HDInsight Spark-kluster resource manager-mallen i undermappen ”Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning” i det här projektet.

1. HDInsight Spark-kluster-mallen är filen ”template.json” under undermappen ”Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning” i det här projektet. Som standard skapar mallen ett Spark-kluster med 40 arbetsnoder. Om du måste justera det numret, öppna mallen i valfri textredigerare och Ersätt alla instanser av ”40” med worker noden antalet ditt val.
    - Minnet är slut fel kan inträffa senare om antalet arbetsnoder som du väljer är mindre. För att bekämpa minnesfel kan du köra skripten utbildnings- och driftsättning för en delmängd av alla tillgängliga data som beskrivs senare i det här dokumentet.
2. Välj ett unikt namn och lösenord för HDInsight-kluster och skriva dem där det anges i följande kommando: skapa klustret genom att utfärda kommandon:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Ditt kluster-distributionen kan ta upp till 30 minuter (inklusive körningen på etablering och skript).

### <a name="set-up-batch-ai-resources"></a>Konfigurera Batch AI-resurser

Du kan förbereda Batch AI Network File Server (NFS) och GPU-kluster medan du väntar Filöverföring för Storage-konto och distribution av Spark-kluster för att slutföra. Öppna en kommandotolk för Azure CLI och kör följande kommando:

```
az --version 
```

Bekräfta att `batchai` visas i listan med installerade moduler. Om inte, kanske du använder en annan Command Line Interface (till exempel en öppnas via Workbench).

Därefter kontrollerar du att registreringen har slutförts. (Providerregistrering tar upp till 15 minuter och kan fortfarande vara pågående om du nyligen har slutfört den [Batch AI konfigurationsanvisningar](https://github.com/Azure/BatchAI/tree/master/recipes).) Bekräfta att både ”Microsoft.Batch” och ”Microsoft.BatchAI” visas med statusen ”registrerad” i utdata från följande kommando:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Om inte, kör följande provider registrering kommandon och vänta ~ 15 minuter för registrering för att slutföra.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Ändra följande kommandon för att ersätta uttrycken inom hakparenteser med de värden som du använde tidigare när resursen grupp och storage-kontot skapades. Sedan kan lagra värdena som variabler genom att köra kommandona:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Identifiera den mapp som innehåller din Azure Machine Learning-projekt (till exempel `C:\Users\<your username>\AzureML\aerialimageclassification`). Ersätt värdet inom hakparenteser med mappens filepath (med utan avslutande snedstreck) och kör kommandot:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Du är nu redo att skapa Batch AI-resurser som behövs för den här självstudien.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Förbereda Batch AI-Network-filserver

Batch AI-kluster har åtkomst till dina utbildningsdata på en filserver för nätverket. Åtkomst till data kan vara several-fold snabbare när åtkomst till filer från en NFS jämfört med en Azure-filresurs eller Azure Blob Storage.

1. Kör följande kommando för att skapa en filserver för nätverk:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p "Dem0Pa$$w0rd" --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Kontrollera Etableringsstatus för ditt nätverk med hjälp av följande kommando:

    ```
    az batchai file-server list
    ```

    När ”provisioningState” på filservern nätverk med namnet ”landuseclassifier” är ”lyckades”, är det redo för användning. Förvänta dig etablering för att ta ungefär fem minuter.
1. Hitta IP-adressen för din NFS i utdata från föregående kommando (egenskapen ”fileServerPublicIp” under ”mountSettings”). Skriv IP-adress där det anges i följande kommando och sedan lagra värdet genom att köra kommandot:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Med ditt favorit SSH (följande exempel kommandot använder [PuTTY](http://www.putty.org/)), köra det här projektet `prep_nfs.sh` skriptet på NFS att överföra avbildningen utbildnings- och verifiering anger det.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Behöver inte oroa om data nedladdning och extrahering statusuppdateringar bläddrar i fönstret shell så snabbt att de är oläsligt.

Om du vill kan du bekräfta att dataöverföringen har fortsatte som planerat genom att logga in på servern med din favorit SSH-verktyg och kontrollera den `/mnt/data` kataloginnehållet. Du bör hitta två mappar, training_images och validation_images, var och en innehåller med undermapparna som namnges enligt mark använda kategorier.  Uppsättningar för träning och verifiering ska innehålla ~ 44 k och ~ 11 k bilder, respektive.

#### <a name="create-a-batch-ai-cluster"></a>Skapa ett Batch AI-kluster

1. Skapa klustret genom att följande kommando:

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p "Dem0Pa$$w0rd" --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. Använd följande kommando för att kontrollera etableringsstatusen för ditt kluster:

    ```
    az batchai cluster list
    ```

    När allokeringstillståndet för klustret med namnet ”landuseclassifier” ändringar från ändrar storlek så att konstant, är det möjligt att skicka jobb. Dock startar jobb inte köras tills alla virtuella datorer i klustret har kvar ”förberedande” status. Om egenskapen ”fel” för klustret inte är null, ett fel uppstod när klustret skapas och det bör inte användas.

#### <a name="record-batch-ai-training-credentials"></a>Registrera Batch AI Training autentiseringsuppgifter

Medan du väntar kluster allokering att slutföra, öppna den `settings.cfg` filen från ”Code” underkatalog i det här projektet i valfri textredigerare. Uppdatera följande variabler med dina autentiseringsuppgifter:
- `bait_subscription_id` (36 tecken Azure prenumerations-ID)
- `bait_aad_client_id` (Azure Active Directory-program/klient-ID anges i avsnittet ”förutsättningar”)
- `bait_aad_secret` (Azure Active Directory programhemlighet nämns i avsnittet ”förutsättningar”)
- `bait_aad_tenant` (Azure Active Directory klient-ID anges i avsnittet ”förutsättningar”)
- `bait_region` (när detta skrivs är det enda alternativet: eastus)
- `bait_resource_group_name` (den resursgrupp du valde tidigare)

När du har tilldelat dessa värden, bör de ändrade raderna i filen settings.cfg likna följande:

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

Du kan nu stänga CLI-fönstret där du kör kommandona Batch AI-resurs skapas. Alla ytterligare steg i den här självstudien använder en CLI som startas från Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Förbereda miljön för Azure Machine Learning Workbench-körning

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Registrera dig i HDInsight-klustret som en Azure Machine Learning Workbench beräkningsmål

När HDInsight-kluster har skapats registrerar du klustret som beräkningsmål för ditt projekt på följande sätt:

1.  Kör följande kommando från Azure Machine Learning kommandoradsgränssnittet:

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    Det här kommandot lägger till två filer, `myhdi.runconfig` och `myhdi.compute`, i projektets `aml_config` mapp.

1. Öppna den `myhdi.compute` filen i valfri textredigerare. Ändra den `yarnDeployMode: cluster` raden till `yarnDeployMode: client`, spara och stäng filen.
1. Kör följande kommando för att förbereda din miljö för användning för HDInsight:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Installera lokala beroenden

Öppna en CLI från Azure Machine Learning Workbench och installera beroenden som krävs för lokal körning av att följande kommando:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Förvärv och förståelse av data

Det här scenariot använder offentligt tillgängliga Flygfoto bilder data från den [nationella jordbruk bilder programmet](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) med 1-mätaren upplösning. Vi har genererat filuppsättningar 224 pixel x 224 pixel PNG beskärs från den ursprungliga NAIP informationen och sorterade enligt mark Använd etiketter från den [nationella mark täcker Database](https://www.mrlc.gov/nlcd2011.php). En exempelbild med etiketten ”utvecklade” visas i full storlek:

![En exempel-panel utvecklade mark](media/scenario-aerial-image-classification/sample-tile-developed.png)

Klass-belastningsutjämnade uppsättningar av ~ 44 k och 11 k-avbildningar används för modellträning och validering, respektive. Vi visar distribution av modeller på en ~ 67 k bild ange färdiga Middlesex County, MA, USA – start av Microsofts New England forskning och utveckling (NERD) center. Mer information om hur dessa bild uppsättningar konstruerades finns i den [Embarrassingly Parallel Bildklassificering git-lagringsplats](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Platsen för Middlesex regionen, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Flygfoto-uppsättningarna som används i det här exemplet har överförts till det lagringskonto som du skapade under installationen. Utbildning, validering och driftsättning avbildningar är alla 224 pixel x 224 pixel PNG-filer med en upplösning på en bildpunkt per kvadratmeter. Utbildning och verifiering avbildningar har tagits uppdelade i undermappar baserat på deras mark Använd etikett. (Mark Använd etiketter driftsättning avbildning är okända och i många fall tvetydig; vissa av dessa avbildningar innehåller flera mark-typer.) Mer information om hur dessa bild uppsättningar konstruerades finns i den [Embarrassingly Parallel Bildklassificering git-lagringsplats](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Visa exempel avbildningar i Azure storage-konto (valfritt):
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter namnet på ditt lagringskonto i sökfältet längst upp på skärmen. Klicka på ditt lagringskonto i sökresultaten.
2. Klicka på länken ”BLOB” i huvudfönstret för storage-konto.
3. Klicka på behållaren med namnet ”train”. Du bör se en lista med kataloger som heter enligt mark använder.
4. Klicka på någon av dessa kataloger att läsa in listan över avbildningar som den innehåller.
5. Klicka på valfri bild och ladda ned det för att visa bilden.
6. Om du vill kan du klicka på behållare med namnet ”test” och ”middlesexma2016” om du vill visa samt deras innehåll.

## <a name="modeling"></a>Modellering

### <a name="training-models-with-azure-batch-ai"></a>Utbildning modeller med Azure Batch AI

Den `run_batch_ai.py` skript i undermappen ”Code\02_Modeling” i Workbench-projekt som används för att utfärda ett Batch AI Training jobb. Det här jobbet retrains en bild-klassificerare DNN som valts av användaren (AlexNet eller ResNet 18 tränats på ImageNet). Djupet för att träna kan också anges: träna bara det sista lagret för nätverket kan minska overfitting när utbildning exemplen är tillgängliga, när du finjusterar hela nätverket (eller för AlexNet, fullständigt anslutna lager) kan leda till större modell prestanda när den är tillräckligt stort.

När utbildningsjobbet är klar sparar det här skriptet modellen (tillsammans med en fil som beskriver mappningen mellan modellens heltal utdata och etiketter för sträng) och förutsägelserna till blob storage. Loggfilen för jobbets BAIT analyseras för att extrahera timecourse av fel hastighet förbättring jämfört med utbildning epoker. Fel hastighet improvement timecourse loggas AML-arbetsstationen kör historik-funktionen för att visa den senare.

Välj ett namn för din tränad modell, en tränats modelltyp och ett omtränings djup. Skriv dina val där det anges i följande kommando och sedan börjar träna genom att köra kommandot från en Azure ML Command Line Interface:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Förvänta dig Azure Machine Learning körs för att ta ungefär en halvtimme för att slutföra. Vi rekommenderar att du kör några liknande kommandon (varierande modellnamn utdata och tränats modelltypen omtränings djupet) så att du kan jämföra prestanda för modeller tränas med olika metoder.

### <a name="training-models-with-mmlspark"></a>Utbildning modeller med MMLSpark

Den `run_mmlspark.py` skript i undermappen ”Code\02_Modeling” i Workbench-projekt som används för att träna en [MMLSpark](https://github.com/Azure/mmlspark) modeller för klassificering av avbildning. Skriptet första featurizes utbildningen ange bilder med hjälp av en avbildning klassificerare DNN tränats på ImageNet-datauppsättning (AlexNet eller en 18 lager ResNet). Skriptet använder sedan trädmodell avbildningar för att träna en MMLSpark-modell (en slumpmässig skog eller en logistic regression-modellen) att klassificera bilderna. Bild testmängd är sedan trädmodell och poängsätts med den tränade modellen. Precisionen i modellens förutsägelser på test-uppsättning beräknas och inloggade Azure Machine Learning Workbench körningshistoriken funktionen. Slutligen sparas den tränade modellen MMLSpark och dess förutsägelser utifrån test-datauppsättning till blob storage.

Välj ett unikt utdata modellnamn för din tränad modell, en tränats modelltyp och en MMLSpark modelltypen. Skriv dina val där det anges i följande kommando mallen sedan börjar träna genom att köra kommandot från en Azure ML Command Line Interface:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Ytterligare `--sample_frac` parametern kan användas för att träna och testa modellen med en delmängd av tillgängliga data. Om du använder ett litet antal bråkvärde minskar körningen och minneskraven på bekostnad av Precision tränade modellen. (Till exempel en körning med `--sample_frac 0.1` förväntas ta ungefär 20 minuter.) Mer information om den här och andra parametrar kör `python Code\02_Modeling\run_mmlspark.py -h`.

Användarna uppmanas att köra det här skriptet flera gånger med olika indataparametrar. Prestanda för de resulterande modellerna kan sedan jämföras i Azure Machine Learning Workbench Körningshistorik funktionen.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Jämföra modellprestanda med funktionen Workbench Körningshistorik

När du har genomfört två eller fler utbildning körs båda typer av, går du till funktionen Körningshistoriken i Workbench genom att klicka på klockikonen längs vänstra menyraden. Välj `run_mmlspark.py` i listan med skript till vänster. Ett fönster som läser in jämföra test set Precision för alla körningar. Om du vill se mer information, rulla nedåt och klicka på namnet på en enskild körning.

## <a name="deployment"></a>Distribution

Infoga dina önskade modellnamn i följande kommando om du vill använda en av dina anpassade modeller till Flygfoto avbildningar sida vid sida med fjärrkörning på HDInsight Middlesex County, MA, och kör den:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Ytterligare `--sample_frac` parametern kan användas för att operationalisera modellen med en delmängd av tillgängliga data. Om du använder ett litet antal bråkvärde minskar körningen och minneskraven på bekostnad av förutsägelse kompletta. Mer information om den här och andra parametrar kör `python Code\03_Deployment\batch_score_spark -h`.

Det här skriptet skriver modellens förutsägelser till ditt lagringskonto. Förutsägelserna granskas enligt beskrivningen i nästa avsnitt.

## <a name="visualization"></a>Visualisering

”Modellera förutsägande analys” Jupyter-anteckningsboken i undermappen ”Code\04_Result_Analysis” i Workbench-projekt att visualisera en modellens förutsägelser. Läsa in och köra anteckningsboken på följande sätt:
1. Öppna projektet i Workbench och klicka på mappen (”filer”) ikonen längs den vänstra menyn för att läsa in kataloglistan.
2. Navigera till undermappen ”Code\04_Result_Analysis” och klicka på den bärbara datorn med namnet ”modell förutsägande analys”. En förhandsversion återgivningen av anteckningsboken ska visas.
3. Klicka på ”Starta Notebook Server” för att läsa in anteckningsboken.
4. Ange namnet på modellen vars resultat du vill analysera där det anges i den första cellen.
5. Klicka på ”Cell -> Kör alla” att köra alla celler i anteckningsboken.
6. Läsa tillsammans med anteckningsboken mer information om analyser och visualiseringar som det visas.

## <a name="cleanup"></a>Rensa
När du har slutfört exemplet, rekommenderar vi att du tar bort alla resurser som du har skapat genom att köra följande kommando från Azure-kommandoradsgränssnitt:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Referenser

- [Lagringsplatsen Embarrassingly Parallel Bildklassificering](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Beskriver datauppsättningen konstruktion från gratis bilder och etiketter
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub-lagringsplats
   - Innehåller ytterligare exempel på modellinlärning och utvärdering med MMLSpark

## <a name="conclusions"></a>Slutsatser

Azure Machine Learning Workbench kan dataforskare enkelt distribuera sin kod på fjärranslutna beräkningsmål. I det här exemplet distribuerades lokal kod i MMLSpark-utbildning för fjärrkörning på ett HDInsight-kluster och ett lokalt skript startas ett utbildningsjobb i ett Azure Batch AI GPU-kluster. Azure Machine Learning Workbench körningshistoriken funktionen spåras prestanda för flera modeller och hjälpt oss att identifiera den bästa modellen. Workbench Jupyter-anteckningsböcker funktionen hjälpte visualisera våra modeller förutsägelser i en interaktiv grafisk miljö.

## <a name="next-steps"></a>Nästa steg
Att gå på djupet i det här exemplet:
- I Azure Machine Learning Workbench Körningshistorik funktionen klickar du på kugghjulet symboler för att välja vilka diagram och mått som visas.
- Granska exempelskript för uttryck som anropar den `run_logger`. Kontrollera att du förstår hur varje mått registreras.
- Granska exempelskript för uttryck som anropar den `blob_service`. Kontrollera att du förstår hur tränade modeller och förutsägelser lagras och hämtas från molnet.
- Utforska innehållet i de behållare som skapats i blob storage-kontot. Kontrollera att du förstår vilka skriptet eller kommandot är ansvarig för att skapa varje grupp av filer.
- Ändra skriptet utbildning för att träna en annan MMLSpark modelltyp eller ändra justeringsmodeller. Använd funktionen körningshistorik för att avgöra om ändringarna ökar eller minskar modellens precision.
