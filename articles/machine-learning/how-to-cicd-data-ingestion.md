---
title: DevOps för en pipeline för datainmatning
titleSuffix: Azure Machine Learning
description: Lär dig hur du tillämpar DevOps-metoder på en implementering av datainmatningspipelines som används för att förbereda data för en modellutbildning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247188"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps för en pipeline för datainmatning

I de flesta fall är en lösning för datainmatning en sammansättning av skript, tjänstinrop och en pipeline som orkestrar alla aktiviteter. I den här artikeln får du lära dig hur du använder DevOps-metoder för utvecklingslivscykeln för en gemensam datainmatningspipeline. Pipelinen förbereder data för machine learning-modellutbildningen.

## <a name="the-solution"></a>Lösningen

Tänk på följande arbetsflöde för datainmatning:

![pipeline för datainmatning](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

I den här metoden lagras utbildningsdata i en Azure blob-lagring. En Azure Data Factory-pipeline hämtar data från en indatablolobbehållare, omvandlar den och sparar data till utdatablolobbehållaren. Den här behållaren fungerar som [datalagring](concept-data.md) för Azure Machine Learning-tjänsten. När data förbereds anropar datafabriken pipeline en pipeline för maskininlärning för att träna en modell. I det här specifika exemplet utförs dataomvandlingen av en Python-anteckningsbok som körs på ett Azure Databricks-kluster. 

## <a name="what-we-are-building"></a>Vad vi bygger

Som med alla programvarulösningar finns det ett team (till exempel Data Engineers) som arbetar med den. 

![cicd-data-intag](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

De samarbetar och delar samma Azure-resurser som Azure Data Factory, Azure Databricks, Azure Storage-konto och sådant. Insamlingen av dessa resurser är en utvecklingsmiljö. Datatekniker bidrar till samma källkodsbas. Processen för kontinuerlig integrering monterar koden, kontrollerar den med kodkvalitetstester, enhetstester och producerar artefakter som testad kod och Azure Resource Manager-mallar. Processen Kontinuerlig leverans distribuerar artefakterna till nedströmsmiljöerna. Den här artikeln visar hur du automatiserar CI- och CD-processerna med [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Hantering av källkontroll

Gruppmedlemmarna arbetar på lite olika sätt för att samarbeta med Pythons källkod för anteckningsboken och Azure Data Factory-källkoden. I båda fallen lagras dock koden i en källkontrolldatabas (till exempel Azure DevOps, GitHub, GitLab) och samarbetet baseras normalt på någon förgreningsmodell (till exempel [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)).

### <a name="python-notebook-source-code"></a>Källkod till Python-anteckningsbok

Datateknikerna arbetar med Pythons anteckningsboks källkod antingen lokalt i en IDE (till exempel [Visual Studio Code)](https://code.visualstudio.com)eller direkt på Databricks-arbetsytan. Den senare ger möjlighet att felsöka koden på utvecklingsmiljön. Koden kommer i vilket fall som helst att slås samman till databasen enligt en förgreningsprincip. Det rekommenderas starkt att lagra `.py` koden i `.ipynb` filer snarare än i Jupyter notebook format. Det förbättrar koden läsbarhet och möjliggör automatiska kontroller kodkvalitet i CI-processen.

### <a name="azure-data-factory-source-code"></a>Källkoden för Azure Data Factory

Källkoden för Azure Data Factory-pipelines är en samling json-filer som genereras av en arbetsyta. Normalt arbetar datatekniker med en visuell designer på Azure Data Factory-arbetsytan i stället för med källkodsfilerna direkt. Konfigurera arbetsytan med en källkontrolldatabas som beskrivs i [Azure Data Factory-dokumentationen](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). Med den här konfigurationen på plats kan datatekniker samarbeta i källkoden efter ett önskat förgreningsarbetsflöde.    

## <a name="continuous-integration-ci"></a>Kontinuerlig integration (CI)

Det slutliga målet med processen för kontinuerlig integrering är att samla det gemensamma teamarbetet från källkoden och förbereda det för distributionen till nedströmsmiljöerna. Precis som med källkodshanteringen är den här processen annorlunda för Python-anteckningsböcker och Azure Data Factory-pipelines. 

### <a name="python-notebook-ci"></a>Python Anteckningsbok CI

CI-processen för Python Notebooks hämtar koden från samarbetsgrenen (till exempel ***master*** eller ***develop)*** och utför följande aktiviteter:
* Kod linting
* Enhetstestning
* Spara koden som en artefakt

Följande kodavsnitt visar implementeringen av dessa steg i en Azure DevOps ***yaml-pipeline:***

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

Pipelinen använder ***flake8*** för att göra Python-koden linting. Den kör enhetstesterna som definierats i källkoden och publicerar linnings- och testresultaten så att de är tillgängliga på skärmen för Azure Pipeline-körning:

![linting-enhet-tester](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Om linning och enhetstestning lyckas kopieras källkoden till artefaktdatabasen som ska användas av de efterföljande distributionsstegen.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

CI-processen för en Azure Data Factory-pipeline är en flaskhals i hela CI/CD-historien för en datainmatningspipeline. Det finns ingen ***kontinuerlig*** integration. En distributionsbar artefakt för Azure Data Factory är en samling Azure Resource Manager-mallar. Det enda sättet att producera dessa mallar är att klicka på ***publiceringsknappen*** på arbetsytan Azure Data Factory. Det finns ingen automatisering här.
Datateknikerna sammanfogar källkoden från sina funktionsgrenar till samarbetsgrenen, till exempel ***huvud-*** eller ***utvecklar***. Sedan klickar någon med de beviljade behörigheterna på ***publiceringsknappen*** för att generera Azure Resource Manager-mallar från källkoden i samarbetsgrenen. När du klickar på knappen validerar arbetsytan pipelines (tänk på den som om linning och enhetstestning), genererar Azure Resource Manager-mallar (tänk på den som om byggnad) och sparar de genererade mallarna till en teknisk gren ***adf_publish*** i samma koddatabas (tänk på det som för publiceringsartefakter). Den här grenen skapas automatiskt av arbetsytan Azure Data Factory. Den här processen beskrivs i information i [Azure Data Factory-dokumentationen](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

Det är viktigt att se till att de genererade Azure Resource Manager-mallarna är miljöoberoende. Detta innebär att alla värden som kan skilja sig från mellan miljöer är parametrized. Azure Data Factory är smart nog att exponera de flesta av dessa värden som parametrar. I följande mall visas till exempel anslutningsegenskaperna till en Azure Machine Learning-arbetsyta som parametrar:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Du kanske vill exponera dina anpassade egenskaper som inte hanteras av Azure Data Factory-arbetsytan som standard. I den här artikeln anropar en Azure Data Factory-pipeline en Python-anteckningsbok som bearbetar data. Anteckningsboken accepterar en parameter med namnet på en indatafil.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Det här namnet är annorlunda för ***Dev-,*** ***QA-,*** ***UAT-*** och ***PROD-miljöer.*** I en komplex pipeline med flera aktiviteter kan det finnas flera anpassade egenskaper. Det är god praxis att samla in alla dessa värden på ett ställe och definiera dem som pipeline ***variabler:***

![adf-variabler](media/how-to-cicd-data-ingestion/adf-variables.png)

Rörledningsverksamheten kan avse rörledningsvariablerna när de faktiskt använder följande:

![adf-notebook-parametrar](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Arbetsytan Azure Data Factory exponerar ***inte*** pipelinevariabler som Azure Resource Manager-mallarparametrar som standard. Arbetsytan använder [standardparametimeringsmallen](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) som dikterar vilka pipeline-egenskaper som ska exponeras som Azure Resource Manager-mallparametrar. Om du vill lägga till pipelinevariabler i listan uppdaterar du avsnittet "Microsoft.DataFactory/factories/pipelines" i [standardparametriiseringsmallen](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) med följande kodavsnitt och placerar resultatjisonfilen i källmappens rot:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Om du gör det tvingas arbetsytan Azure Data Factory att lägga till variablerna i parameterlistan när ***du*** klickar på publiceringsknappen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Värdena i json-filen är standardvärden som konfigurerats i pipelinedefinitionen. De förväntas åsidosättas med målmiljövärdena när Azure Resource Manager-mallen distribueras.

## <a name="continuous-delivery-cd"></a>Kontinuerlig leverans (CD)

Processen För kontinuerlig leverans tar artefakterna och distribuerar dem till den första målmiljön. Det ser till att lösningen fungerar genom att köra tester. Om det lyckas fortsätter den till nästa miljö. CD Azure Pipeline består av flera steg som representerar miljöerna. Varje steg innehåller [distributioner](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) och [jobb](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) som utför följande steg:
* Distribuera en Python-anteckningsbok till Azure Databricks-arbetsytan
* Distribuera en Azure Data Factory-pipeline 
* Köra en pipeline
* Kontrollera resultatet för datainmatning

Pipeline-faserna kan konfigureras med [godkännanden](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) och [grindar](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) som ger ytterligare kontroll över hur distributionsprocessen utvecklas genom miljökedjan.

### <a name="deploy-a-python-notebook"></a>Distribuera en Python-anteckningsbok

Följande kodavsnitt definierar en Azure [Pipeline-distribution](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) som kopierar en Python-anteckningsbok till ett Databricks-kluster:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

De artefakter som produceras av KI kopieras automatiskt till distributionsagenten och är tillgängliga i mappen ***$(Pipeline.Workspace).*** I det här fallet refererar distributionsuppgiften till artefakten ***för di-notebooks*** som innehåller Python-anteckningsboken. Den här [distributionen](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) använder [tillägget Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) för att kopiera anteckningsboksfilerna till Databricks-arbetsytan.
Den ***Deploy_to_QA*** fasen innehåller en referens till ***variabelgruppen devops-ds-qa-vg*** som definierats i Azure DevOps-projektet. Stegen i det här steget refererar till variablerna från den här variabelgruppen (till exempel $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). Tanken är att nästa steg (till exempel ***Deploy_to_UAT***) ska fungera med samma variabelnamn som definieras i den egna variabelgruppen UAT-scoped.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Distribuera en Azure Data Factory-pipeline

En distributionsbar artefakt för Azure Data Factory är en Azure Resource Manager-mall. Därför kommer den att distribueras med Azure ***Resource Group Deployment-aktiviteten*** som det visas i följande utdrag:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
Värdet för parametern datafilnamn kommer från variabeln $(DATA_FILE_NAME) som definierats i en qa-stegvariabelgrupp. På samma sätt kan alla parametrar som definieras i ***ARMTemplateForFactory.json*** åsidosättas. Om de inte är det används standardvärdena.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Kör pipelinen och kontrollera resultatet för datainmatning

Nästa steg är att se till att den distribuerade lösningen fungerar. Följande jobbdefinition kör en Azure Data Factory-pipeline med ett [PowerShell-skript](https://github.com/microsoft/DataOps/tree/master/adf/utils) och kör en Python-anteckningsbok i ett Azure Databricks-kluster. Anteckningsboken kontrollerar om data har intagits korrekt och validerar resultatdatafilen med namnet på $(bin_FILE_NAME).

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

Den sista aktiviteten i jobbet kontrollerar resultatet av körningen av anteckningsboken. Om ett fel returneras anges statusen för pipelinekörningen.

## <a name="putting-pieces-together"></a>Sätta ihop bitar

Resultatet av den här artikeln är en CI/CD Azure Pipeline som består av följande steg:
* CI
* Distribuera till QA
    * Distribuera till Databricks + Distribuera till ADF
    * Integrationstest

Den innehåller ett antal ***distribuera*** stadier som motsvarar antalet målmiljöer du har. Varje ***distributionsfas*** innehåller två [distributioner](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) som körs parallellt och ett [jobb](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) som körs efter distributioner för att testa lösningen på miljön.

Ett exempel på pipelinen monteras ***yaml*** i följande yaml-kodavsnitt:

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Nästa steg

* [Källkontroll i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Kontinuerlig integrering och leverans i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps för Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
