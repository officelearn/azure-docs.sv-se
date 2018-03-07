---
title: "Självstudieartikel för förhandsfunktioner i Azure Machine Learning – Kommandoradsgränssnitt | Microsoft Docs"
description: "Den här självstudien visar alla steg som krävs för att slutföra en Iris-klassificering från slutpunkt till slutpunkt från kommandoradsgränssnittet."
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
ms.openlocfilehash: ad81cd02ba0c46cbe58de7071d2164aaefea6514
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Självstudie: Klassificera Iris med hjälp av kommandoradsgränssnittet
Azure Machine Learning-tjänsterna (förhandsversionen) är en integrerad lösning för datavetenskap och analys från slutpunkt till slutpunkt som datatekniker kan använda för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

I självstudien får du lära dig att använda CLI-verktyg (kommandoradsgränssnitt) i Azure Machine Learnings förhandsversionsfunktioner: 
> [!div class="checklist"]
> * Konfigurera ett experimenteringskonto och skapa en arbetsyta
> * Skapa ett projekt
> * Skicka ett experiment till flera beräkningsmål
> * Flytta upp och registrera en träningsmodell
> * Distribuera en webbtjänst för att rangordna nya data

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här kursen behöver du:
- Åtkomst till en Azure-prenumeration och behörighet att skapa resurser i prenumerationen. 
  
  Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Azure Machine Learning Workbench-programmet installeras enligt beskrivningen i [Snabbstart: Installera och starta Azure Machine Learning-tjänster](quickstart-installation.md). 

  >[!IMPORTANT]
  >Skapa inte Azure Machine Learning-tjänstkonton. Du kommer att göra det med hjälp av CLI:n i den här artikeln.
 
## <a name="getting-started"></a>Komma igång
Med Azure Machine Learning-kommandoradsgränssnittet (CLI) kan du utföra alla uppgifter som krävs för ett arbetsflöde för datavetenskap från slutpunkt till slutpunkt. Du kan komma åt CLI-verktygen på följande sätt:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Alternativ 1. starta Azure ML CLI från dialogrutan för Azure ML Workbench-inloggning
När du startar Azure ML Workbench och loggar in för första gången, och om du inte redan har åtkomst till ett experimenteringskonto redan, visas följande skärm:

![inget konto hittas](media/tutorial-iris-azure-cli/no_account_found.png)

Klicka på länken **Kommandoradsfönster** i dialogrutan för att starta kommandotolken.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Alternativ 2. starta Azure ML CLI från Azure ML Workbench-programmet
Om du redan har åtkomst till ett experimenteringskonto kan du logga in direkt. Du kan sedan öppna kommandoradsfönstret genom att klicka på **Arkiv** --> **Öppna kommandotolken**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Alternativ 3. aktivera Azure ML CLI i ett godtyckligt kommandoradsfönster
Du kan också aktivera Azure ML CLI i alla kommandoradsfönster. Gör detta genom att starta kommandotolken och ange följande kommandon:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Du kan använda `SETX` i Windows för att göra ändringen permanent. I macOS kan du använda `setenv`.

>[!TIP]
>Du kan aktivera Azure CLI i din favoritterminalfönster genom att ange föregående miljövariabler.

## <a name="step-1-log-in-to-azure"></a>Steg 1. Logga in på Azure
Det första steget är att öppna CLI:n från AMLWorkbench (Arkiv > Öppna kommandotolken). Detta innebär att du har rätt Python-miljö och att dina ML CLI-kommandon är tillgängliga. 

Nu kan du ange rätt kontext i din CLI för att komma åt och hantera Azure-resurser.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Steg 2. Skapa ett konto och en arbetsyta för Azure Machine Learning-experimentering

I det här steget skapar du ett nytt experimenteringskonto och en ny arbetsyta. Se [Azure Machine Learning-begrepp](overview-general-concepts.md) för mer information om experimenteringskonton och arbetsytor.

> [!NOTE]
> Experimenteringskonton kräver ett lagringskonto som används till att lagra utdata från dina experimentkörningar. Lagringskontonamnet måste vara globalt unikt i Azure eftersom det finns en URL som är kopplad till det. Om du inte anger något befintligt lagringskonto används experimenteringskontots namn till att skapa ett nytt lagringskonto. Om du inte använder ett unikt namn visas felet _”Lagringskontonamnet \<storage_account_name > är upptaget”._ Du kan också använda argumentet `--storage` för ett befintligt lagringskonto.

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

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Steg 2.a (valfritt) Dela en arbetsyta med medarbetarna
Här kan du se hur du delar åtkomst till en arbetsyta med en medarbetare. Stegen för att dela åtkomst till ett experimenteringskonto eller ett projekt är likadana. Det enda sättet för att få ett Azure-resurs-ID är att vara uppdaterad.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> `bob@contoso.com` i kommandot ovan måste vara en giltig Azure AD-identitet i den katalog som den aktuella prenumerationen tillhör.

## <a name="step-3-create-a-new-project"></a>Steg 3. Skapa ett nytt projekt
Nästa steg är att skapa ett nytt projekt. Det finns flera sätt att komma igång med ett nytt projekt.

### <a name="create-a-new-blank-project"></a>Skapa ett nytt tomt projekt

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Skapa ett nytt projekt med en standardprojektmall
Du kan skapa ett nytt projekt med en standardmall.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Skapa ett nytt projekt som är associerat med en Git-lagringsplats i molnet
Du kan skapa ett nytt projekt som är associerat med en VSTS (Visual Studio Team Service) Git-lagringsplats. Varje gång ett experiment skickas, allokeras en ögonblicksbild av hela projektmappen till den fjärranslutna Git-lagringsplatsen. Se [Använda en Git-lagringsplats med ett Azure Machine Learning Workbench-projekt](using-git-ml-project.md) för mer information.

> [!NOTE]
> Azure Machine Learning stöder endast tomma Git-lagringsplatser som skapas i VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Om du får felet ”Lagringsplatsens URL kan vara ogiltig eller användaren kanske inte har åtkomst”, kan du skapa en säkerhetstoken i VSTS (under _Säkerhet_, _Lägg till personliga åtkomsttokens_) och använda `--vststoken`-argumentet när du skapar ditt projekt. 

### <a name="sample_create"></a>Skapa ett nytt projekt från ett exempel
I det här exemplet skapar du ett nytt projekt med ett projektexempel som mall.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
När projektet har skapats kan du använda `cd`-kommandot för att ange projektkatalogen.

## <a name="step-4-run-the-training-experiment"></a>Steg 4 Kör träningsexperimentet 
Följande steg förutsätter att du har ett projekt med Iris-exemplet (se [Skapa ett nytt projekt från ett online-exempel](#sample_create)).

### <a name="prepare-your-environment"></a>Förbered din miljö 
Du måste installera matplotlib för Iris-exemplet.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Skicka experimentet

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Iterera ditt experiment med fallande regulariseringspriser
Med lite kreativitet är det enkelt att sätta ihop ett Python-skript som skickar experiment med olika regulariseringspriser. (Du kan behöva redigera filen så att den pekar till rätt projektsökväg.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Steg 5. Visa körningshistorik
Följande kommando visar alla tidigare körningar som utförts. 

```azure-cli
$ az ml history list -o table
```
Om du kör föregående kommando visas en lista med alla körningar som hör till det här projektet. Du kan även se mått för precision och regulariseringshastighet. Detta gör det enkelt att identifiera den bästa körningen i listan.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Steg 5.a Visa den bifogade fil som skapas av en viss körning 
Du kan använda informationskommandot i körningshistoriken om du vill se den bifogade fil som är associerad med en viss körning. Hitta ett körnings-ID för en specifik körning i listan ovan.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Du kan använda nedanstående kommando för att ladda ned artefakter från en körning:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Steg 6. Flytta upp artefakter i en körning 
En av körningarna har en bättre AUC, så vi använder den när vi skapar en bedömning av webbtjänsten som ska distribueras till produktionen. För att kunna göra det måste du först uppgradera artefakterna till en tillgång.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Detta skapar en `assets`-mapp i projektkatalogen med en `model.pkl.link`-fil. Länkfilen används för att referera till en upphöjd tillgång.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Steg 7. Ladda ned filerna som ska operationaliseras
Ladda ned den uppflyttade modellen så att du kan använda den för att skapa en webbtjänst för förutsägelser. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>Steg 8. Konfigurera din modellhanteringsmiljö 
Skapa en miljö för att distribuera webbtjänster. Du kan köra webbtjänsten på den lokala datorn med Docker. Eller distribuera den till ett ACS-kluster för åtgärder med hög skalbarhet. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Steg 9. Skapa ett modellhanteringskonto 
Ett modellhanteringskonto krävs för att distribuera och spåra modeller i produktionen. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Steg 10. Skapa en webbtjänst
Skapa en webbtjänst som returnerar en förutsägelse med hjälp av modellen som du distribuerade. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>Steg 11. Köra webbtjänsten
Använd webbtjänst-ID:t från utdatan i föregående steg till att anropa webbtjänsten och testa den. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>Steg 12. Ta bort alla resurser 
Låt oss avsluta den här självstudien genom att ta bort alla resurser som har skapats, såvida du inte vill fortsätta att arbeta med dem. 

Gör detta genom att ta bort resursgruppen som innehåller resurserna. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig att använda Azure Machine Learning för att: 
> [!div class="checklist"]
> * Konfigurera ett experimenteringskonto och skapa en arbetsyta
> * Skapa projekt
> * Skicka experiment till flera beräkningsmål
> * Flytta upp och registrera en träningsmodell
> * Skapa ett modellhanteringskonto för modellhantering
> * Skapa en miljö för att distribuera webbtjänster
> * Distribuera en webbtjänst och rangordna nya data
