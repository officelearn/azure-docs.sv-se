---
title: "Självstudiekurs artikel för funktioner för förhandsgranskning av Azure Machine Learning - kommandoradsgränssnittet | Microsoft Docs"
description: "Den här självstudiekursen gå igenom steg som krävs för att slutföra en Iris klassificering slutpunkt till slutpunkt från kommandoradsgränssnittet."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: 21fb0bca08bca0fe6384bbc9ba2511f7d8b746cf
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Självstudier: Klassificera Iris med hjälp av kommandoradsgränssnittet
Azure Machine Learning (förhandsversion) är en integrerad, avancerad lösning för datavetenskap och analys, som datatekniker kan använda till att förbereda data, utveckla experiment och distribuera modeller i molnskala.

I kursen får du lära dig att använda kommandoradsgränssnittet (CLI) verktyg i Azure Machine Learning preview funktioner: 
> [!div class="checklist"]
> * Skapa ett experiment-konto och skapa en arbetsyta
> * Skapa ett projekt
> * Skicka ett experiment till flera beräknings-mål
> * Flytta upp och registrera en tränad modell
> * Distribuera en webbtjänst för att samla in nya data

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
- Du behöver åtkomst till en Azure-prenumeration och behörigheter att skapa resurser i den prenumerationen. 
- Du måste installera Azure-datorn Learing arbetsstationen programmet genom att följa den [installera och skapa Quickstart](quickstart-installation.md). 

  >[!NOTE]
  >Du behöver bara installera Azure Machine Learning arbetsstationen lokalt. Du behöver bara följer du stegen i avsnitt rätt installera Azure Machine Learning arbetsstation sedan skapa konto och skapa ett nytt projekt steg kommer att göras via kommandoraden i den här artikeln.
 
## <a name="getting-started"></a>Komma igång
Azure Machine Learning-kommandoradsgränssnittet (CLI) kan du utföra alla uppgifter som krävs för en slutpunkt till slutpunkt datavetenskap arbetsflöde. Du kan komma åt CLI-verktygen på följande sätt:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Alternativ 1. Starta Azure ML CLI från Azure ML-arbetsstationen logg i dialogrutan
När du startar Azure ML-arbetsstationen och logga in för första gången, och om du inte redan har åtkomst till ett experiment konto, visas följande skärm:

![Inget konto hittas](media/tutorial-iris-azure-cli/no_account_found.png)

Klicka på den **kommandoradsfönster** länken i dialogrutan för att öppna fönstret för kommandoraden.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Alternativ 2. Starta Azure ML CLI från Azure ML-arbetsstationen app
Om du redan har åtkomst till ett experiment-konto kan du logga in korrekt. Och du kan öppna fönstret kommandoraden genom att klicka på **filen** --> **Öppna kommandot Prompt** menyn.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Alternativ 3. Aktivera Azure ML-CLI i en godtycklig kommandoradsfönster
Du kan också aktivera Azure ML-CLI i alla kommandoradsfönster. Bara starta Kommandotolken och ange följande kommandon:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Du kan använda för att göra ändringarna permanenta `SETX` i Windows. Du kan använda för macOS, `setenv`.

>[!TIP]
>Du kan aktivera Azure CLI i din favorit terminalfönster genom att ange ovan miljövariabler.

## <a name="step-1-log-in-to-azure"></a>Steg 1. Logga in på Azure
Det första steget är att öppna CLI från AMLWorkbench App (Arkiv > Öppna Kommandotolken). På så sätt vi använder rätt python-miljön och vi har ML CLI kommandon som är tillgängliga. 

Vi måste ange rätt kontext i din CLI för att komma åt och hantera Azure-resurser.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Steg 2. Skapa en ny Azure Machine Learning-experiment konto och arbetsytan
Vi börjar med att skapa ett nytt experiment-konto och en ny arbetsyta. Se [Azure Machine Learning begrepp](overview-general-concepts.md) för mer information om undersökningar konton och arbetsytor.

> [!NOTE]
> Experiment konton kräver ett lagringskonto som används för att lagra utdata för dina experiment körs. Lagringskontonamnet måste vara globalt unikt i Azure eftersom det är en url som är kopplade till den. Om du inte anger ett befintligt lagringskonto används kontonamnet experiment för att skapa ett nytt lagringskonto. Se till att använda ett unikt namn och du får ett fel som _”lagringskontonamnet \<storage_account_name > är upptaget”._ Du kan också använda den `--storage` argumentet att ange ett befintligt lagringskonto.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Steg 2.a (valfritt) dela en arbetsyta med medarbetare
Här förklarar vi hur du dela åtkomst till en arbetsyta med en medarbetare. Steg för att dela åtkomst till ett experiment konto eller ett projekt skulle vara samma. Endast sätt för att få Azure resurs-ID måste uppdateras.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> `bob@contoso.com`i kommandot ovan måste vara en giltig Azure AD identity i katalogen när den aktuella prenumerationen tillhör.

## <a name="step-3-create-a-new-project"></a>Steg 3. Skapa ett nytt projekt
Vår nästa steg är att skapa ett nytt projekt. Det finns flera sätt att komma igång med ett nytt projekt.

### <a name="create-a-new-blank-project"></a>Skapa ett nytt tomt projekt

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Skapa ett nytt projekt med en standardmall för projektet
Du kan skapa ett nytt projekt med en standardmall.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Skapa ett nytt projekt som är kopplade till ett moln Git-lagringsplats
Vi kan skapa ett nytt projekt som är associerade med en VSTS (Visual Studio Team Service) Git-lagringsplats. Varje gång ett experiment skickas, värnar en ögonblicksbild av hela projektmappen fjärransluten Git-lagringsplatsen. Se [med Git-lagringsplats med ett projekt för Azure Machine Learning arbetsstationen](using-git-ml-project.md) för mer information.

> [!NOTE]
> Azure Machine Learning stöder endast tomt Git repor som skapats i VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Om du får ett fel ”databasen url kan vara ogiltig eller användaren kanske inte har åtkomst”, kan du skapa en säkerhetstoken i VSTS (under _säkerhet_, _lägger till personliga åtkomsttoken_ menyn) och använda `--vststoken`argumentet när du skapar ditt projekt. 

### <a name="sample_create"></a>Skapa ett nytt projekt från ett prov
I det här exemplet skapar vi ett nytt projekt med ett exempel på projekt som en mall.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
När projektet har skapats kan du använda `cd` kommando för att ange projektkatalogen.

## <a name="step-4-run-the-training-experiment"></a>Steg 4 kör experimentet utbildning 
Stegen nedan förutsätter att du har ett projekt med exemplet Iris (se [skapa ett nytt projekt från en online exemplet](#sample_create)).

### <a name="prepare-your-environment"></a>Förbered din miljö 
Vi behöver installera matplotlib för Iris-exemplet.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Skicka experimentet

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Iterera ditt experiment med fallande regularization priser
Med vissa Kreativitet är det enkelt att sätta ihop Python-skriptet som skickar experiment med olika regularization priser. (Du kan behöva redigera filen så att den pekar till rätt projektsökvägen.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Steg 5. Visa körningshistorik
Följande kommando visar alla tidigare körs utförs. 

```azure-cli
$ az ml history list -o table
```
Kör kommandot ovan visas en lista över alla körningar som hör till det här projektet. Du kan se att Precision och regularization hastighet mått visas för. Den här funktionen för att göra det lättare att identifiera bäst köras i listan.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Steg 5.a visa bifogad fil skapas av en viss körning 
Vi kan använda kommandot information av körningshistorik om du vill visa den bifogade filen som är associerad med en viss körning. Hitta en kör id för en specifik körning i listan ovan.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Du kan använda nedan kommando för att ladda ned artefakter från en kördes:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Steg 6. Befordra artefakter av en körning 
En av körs som vi har gjort är bättre AUC, så vi vill använda den för att skapa en bedömningsprofil webbtjänst för att distribuera till produktion. För att göra så vi måste först uppgradera artefakter till en tillgång.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Detta skapar en `assets` mappen i projektkatalogen med en `model.pkl.link` fil. Den här länkfilen används för att referera till en upphöjt tillgång.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Steg 7. Hämta filerna som ska vara operationalized
Vi behöver nu hämta upphöjt modellen, så vi kan använda dem för att skapa våra förutsägelse-webbtjänsten. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>Steg 8. Konfigurera din miljö för hantering av modellen 
Vi skapa en miljö för att distribuera webbtjänster. Vi kan köra webbtjänsten på den lokala datorn med Docker. Eller distribuera den till en ACS-kluster för hög skalningsåtgärder. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Steg 9. Skapa ett konto för hantering av modellen 
Ett konto för hantering av modellen krävs för att distribuera och spåra modeller i produktionen. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Steg 10. Skapa en webbtjänst
Vi kan sedan skapa en webbtjänst som returnerar en förutsägelse med hjälp av modellen som vi har distribuerats. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-10-run-the-web-service"></a>Steg 10. Kör webbtjänst
Med hjälp av web service-id från utdata från föregående steg, vi anropa webbtjänsten och testa den. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Ta bort alla resurser 
Vi den här kursen genom att ta bort alla resurser som vi har skapat, såvida du inte vill fortsätta att arbeta på det! 

Gör vi helt enkelt ta bort resursgruppen som innehåller alla våra resurser. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Nästa steg
I kursen får lärt du dig att använda funktioner i Azure Machine Learning-preview till 
> [!div class="checklist"]
> * Skapa ett experiment konto, skapa arbetsyta
> * Skapa projekt
> * Skicka experiment till flera beräknings-mål
> * Flytta upp och registrera en tränad modell
> * Skapa ett konto för hantering av modellen för modellhantering av
> * Skapa en miljö för att distribuera en webbtjänst
> * Distribuera ett webbtjänsten och poängsätta nya data
