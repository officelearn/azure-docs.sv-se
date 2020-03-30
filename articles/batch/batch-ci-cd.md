---
title: Använd Azure Pipelines för att skapa & distribuera HPC-lösningar – Azure Batch
description: Lär dig hur du distribuerar en build/release pipeline för ett HPC-program som körs på Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533138"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Använda Azure Pipelines för att skapa och distribuera HPC-lösningar

Azure DevOps-tjänster tillhandahåller en rad verktyg som används av utvecklingsteam när du skapar ett anpassat program. Verktyg som tillhandahålls av Azure DevOps kan översätta till automatiserad byggnad och testning av högpresterande beräkningslösningar. Den här artikeln visar hur du konfigurerar en kontinuerlig integrering (CI) och kontinuerlig distribution (CD) med Azure Pipelines för en högpresterande beräkningslösning som distribueras på Azure Batch.

Azure Pipelines tillhandahåller en rad moderna CI/CD-processer för att skapa, distribuera, testa och övervaka programvara. Dessa processer påskyndar din programvaruleverans, så att du kan fokusera på din kod i stället för att stödja infrastruktur och drift.

## <a name="create-an-azure-pipeline"></a>Skapa en Azure Pipeline

I det här exemplet skapar vi en pipeline för att distribuera en Azure Batch-infrastruktur och släppa ett programpaket. Förutsatt att koden utvecklas lokalt är detta det allmänna distributionsflödet:

![Diagram som visar flödet av utbyggnad i vår pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Installation

Om du vill följa stegen i den här artikeln behöver du en Azure DevOps-organisation och ett teamprojekt.

* [Skapa en Azure DevOps-organisation](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Skapa ett projekt i Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Källkontroll för din miljö

Källkontroll gör det möjligt för team att spåra ändringar som gjorts i kodbasen och inspektera tidigare versioner av koden.

Vanligtvis är källkontroll tänkt på hand i hand med programvarukod. Vad sägs om den underliggande infrastrukturen? Detta leder oss till Infrastruktur som kod, där vi kommer att använda Azure Resource Manager-mallar eller andra alternativ med öppen källkod för att deklarativt definiera vår underliggande infrastruktur.

Det här exemplet är starkt beroende av ett antal JSON-dokument (Resource Manager-mallar) och befintliga binärfiler. Du kan kopiera dessa exempel till din databas och skicka dem till Azure DevOps.

Den kodbasstruktur som används i det här exemplet liknar följande:

* En **mapp med armmallar** som innehåller ett antal Azure Resource Manager-mallar. Mallarna förklaras i den här artikeln.
* En **mapp för klientprogram,** som är en kopia av [Azure Batch .NET-filbearbetning med ffmpeg-exempel.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Detta behövs inte för den här artikeln.
* En **hpc-program** mapp, som är Windows 64-bitars version av [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* En **pipelinemapp.** Detta innehåller en YAML-fil som beskriver vår byggprocess. Detta diskuteras i artikeln.

Det här avsnittet förutsätter att du är bekant med versionskontrollen och designar Resource Manager-mallar. Om du inte är bekant med dessa begrepp läser du följande sidor för mer information.

* [Vad är källkontroll?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Det här exemplet utnyttjar flera Resource Manager-mallar för att distribuera vår lösning. För att göra detta använder vi ett antal funktionsmallar (liknande enheter eller moduler) som implementerar en viss funktion. Vi använder också en heltäckande lösningsmall som ansvarar för att sammanföra dessa underliggande funktioner. Det finns ett par fördelar med detta tillvägagångssätt:

* De underliggande funktionsmallarna kan testas individuellt.
* De underliggande funktionsmallarna kan definieras som en standard inom en organisation och återanvändas i flera lösningar.

I det här exemplet finns det en heltäckande lösningsmall (deployment.json) som distribuerar tre mallar. De underliggande mallarna är funktionsmallar som ansvarar för att distribuera en viss aspekt av lösningen.

![Exempel på länkad mallstruktur med Azure Resource Manager-mallar](media/batch-ci-cd/ARMTemplateHierarchy.png)

Den första mallen som vi ska titta på är för ett Azure Storage-konto. Vår lösning kräver ett lagringskonto för att distribuera programmet till vårt batchkonto. Det är värt att vara medveten om [resurshanterarens mallreferensguide för Microsoft.Storage-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) när du skapar Resource Manager-mallar för lagringskonton.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Därefter tittar vi på Azure Batch-kontomallen. Azure Batch-konto fungerar som en plattform för att köra många program över pooler (grupperingar av datorer). Det är värt att känna till [resurshanterarens mallreferensguide för Microsoft.Batch-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) när du skapar Resource Manager-mallar för batchkonton.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Nästa mall visar ett exempel på att skapa en Azure Batch Pool (backend-datorerna för att bearbeta våra program). Det är värt att vara medveten om [resource manager-mallreferensguiden för Microsoft.Batch-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) när du skapar Resource Manager-mallar för batchkontopooler.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Slutligen har vi en mall som fungerar som liknar en orchestrator. Den här mallen ansvarar för att distribuera funktionsmallarna.

Du kan också läsa mer om [hur du skapar länkade Azure Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) i en separat artikel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>HPC-lösningen

Infrastrukturen och programvaran kan definieras som kod och samlokaliseras i samma databas.

För den här lösningen används ffmpeg som programpaket. Den ffmpeg paketet kan laddas ner [här](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Exempel på Git-databasstruktur](media/batch-ci-cd/git-repository.jpg)

Det finns fyra huvudavsnitt i den här databasen:

* Mappen **armmallar** som lagrar vår infrastruktur som kod
* Mappen **hpc-application** som innehåller binärfilerna för ffmpeg
* **Pipelines-mappen** som innehåller definitionen för vår build pipeline.
* **Valfritt**: Den **klientprogrammapp** som skulle lagra kod för .NET-programmet. Vi använder inte detta i exemplet, men i ditt eget projekt kanske du vill köra körningar av HPC Batch Application via ett klientprogram.

> [!NOTE]
> Detta är bara ett exempel på en struktur till en kodbas. Den här metoden används för att visa att program-, infrastruktur- och pipeline-kod lagras i samma databas.

Nu när källkoden är inställd, kan vi börja den första bygga.

## <a name="continuous-integration"></a>Kontinuerlig integrering

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), inom Azure DevOps Services, hjälper dig att implementera en pipeline för att skapa, testa och distribuera för dina program.

I det här skedet av pipelinen körs tester vanligtvis för att validera kod och skapa lämpliga delar av programvaran. Antalet och typerna av tester och eventuella ytterligare uppgifter som du kör beror på din bredare bygg- och utgivningsstrategi.

## <a name="preparing-the-hpc-application"></a>Förbereda HPC-programmet

I det här exemplet fokuserar vi på **hpc-programmappen.** **HPC-programmappen** är ffmpeg-programvaran som körs inifrån Azure Batch-kontot.

1. Navigera till avsnittet Bygg i Azure Pipelines i din Azure DevOps-organisation. Skapa en **ny pipeline**.

    ![Skapa en pipeline för nybyggnation](media/batch-ci-cd/new-build-pipeline.jpg)

1. Du har två alternativ för att skapa en Build pipeline:

    a. [Använda Visual Designer](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Om du vill använda detta klickar du på "Använd den visuella designern" på sidan **Ny pipeline.**

    b. [Använda YAML Builds](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Du kan skapa en ny YAML-pipeline genom att klicka på alternativet Azure Repos eller GitHub på sidan Ny pipeline. Du kan också lagra exemplet nedan i källkontrollen och referera till en befintlig YAML-fil genom att klicka på Visual Designer och sedan använda YAML-mallen.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. När versionen har konfigurerats efter behov väljer du **Spara & kö**. Om du har aktiverat kontinuerlig integrering (i avsnittet **Utlösare) utlöses** bygget automatiskt när ett nytt åtagande till databasen görs och uppfyller villkoren som anges i versionen.

    ![Exempel på en befintlig build pipeline](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visa liveuppdateringar om hur du skapar i Azure DevOps genom att navigera till avsnittet **Bygg** i Azure Pipelines. Välj lämplig version från din byggdefinition.

    ![Visa liveutdata från ditt bygge](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Om du använder ett klientprogram för att köra ditt HPC Batch-program måste du skapa en separat byggdefinition för det programmet. Du hittar ett antal instruktioner i [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) Azure Pipelines-dokumentationen.

## <a name="continuous-deployment"></a>Löpande distribution

Azure Pipelines används också för att distribuera ditt program och underliggande infrastruktur. [Släpp pipelines](https://docs.microsoft.com/azure/devops/pipelines/release) är den komponent som möjliggör kontinuerlig distribution och automatiserar din utgivningsprocess.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Distribuera ditt program och underliggande infrastruktur

Det finns ett antal steg inblandade i utbyggnaden av infrastrukturen. Eftersom vi har använt [länkade mallar](../azure-resource-manager/templates/linked-templates.md)måste dessa mallar vara tillgängliga från en offentlig slutpunkt (HTTP eller HTTPS). Detta kan vara en databas på GitHub, eller ett Azure Blob Storage-konto eller en annan lagringsplats. De uppladdade mallartefakterna kan förbli säkra eftersom de kan hållas i ett privat läge men nås med någon form av SAS-token (Shared Access Signature). I följande exempel visas hur du distribuerar en infrastruktur med mallar från en Azure Storage-blob.

1. Skapa en **ny versionsdefinition**och välj en tom definition. Vi måste sedan byta namn på den nyskapade miljön till något som är relevant för vår pipeline.

    ![Inledande utgivningspipeline](media/batch-ci-cd/Release-0.jpg)

1. Skapa ett beroende av Build Pipeline för att få utdata för vårt HPC-program.

    > [!NOTE]
    > Observera återigen **källaliaset**, eftersom detta kommer att behövas när aktiviteter skapas inuti versionsdefinitionen.

    ![Skapa en artefaktlänk till HPCApplicationPackage i lämplig byggpipeline](media/batch-ci-cd/Release-1.jpg)

1. Skapa en länk till en annan artefakt, den här gången en Azure Repo. Detta krävs för att komma åt Resource Manager-mallarna som lagras i databasen. Eftersom Resource Manager-mallar inte kräver kompilering behöver du inte driva dem genom en byggpipeline.

    > [!NOTE]
    > Observera återigen **källaliaset**, eftersom detta kommer att behövas när aktiviteter skapas inuti versionsdefinitionen.

    ![Skapa en artefaktlänk till Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Navigera till **variables** variabelavsnittet. Vi rekommenderar att du skapar ett antal variabler i pipelinen, så att du inte matar in samma information i flera aktiviteter. Dessa är de variabler som används i det här exemplet och hur de påverkar distributionen.

    * **applicationStorageAccountName**: Namnet på lagringskontot som ska innehålla binärfiler för HPC-program
    * **batchAccountApplicationName**: Namnet på programmet i Azure Batch-konto
    * **batchAccountName**: Namn på Azure Batch-konto
    * **batchAccountPoolName**: Namnet på poolen av virtuella datorer som utför bearbetningen
    * **batchApplicationId**: Unikt ID för Azure Batch-programmet
    * **batchApplicationVersion**: Semantisk version av ditt parti program (det vill ha ffmpeg binärfiler)
    * **plats**: Plats för de Azure-resurser som ska distribueras
    * **resourceGroupName**: Namnet på den resursgrupp som ska skapas och var dina resurser ska distribueras
    * **storageAccountName**: Namnet på lagringskontot som ska innehålla länkade Resource Manager-mallar

    ![Exempel på variabler som angetts för Azure Pipelines-versionen](media/batch-ci-cd/Release-4.jpg)

1. Navigera till uppgifterna för utvecklingsmiljön. I ögonblicksbilden nedan kan du se sex aktiviteter. Dessa uppgifter kommer: hämta de zippade ffmpeg-filerna, distribuera ett lagringskonto som värd för de kapslade Resource Manager-mallarna, kopiera dessa Resource Manager-mallar till lagringskontot, distribuera batchkontot och nödvändiga beroenden, skapa ett program i Azure Batch-konto och ladda upp programpaketet till Azure Batch-konto.

    ![Exempel på de uppgifter som används för att släppa HPC-programmet till Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Lägg till aktiviteten **Hämta pipelineartefakt (förhandsversion)** och ange följande egenskaper:
    * **Visningsnamn:** Ladda ner ApplicationPackage till Agent
    * **Namnet på artefakten att ladda ner:** hpc-ansökan
    * **Sökväg att hämta till:**$(System.DefaultWorkingDirectory)

1. Skapa ett lagringskonto för att lagra dina artefakter. Ett befintligt lagringskonto från lösningen kan användas, men för det fristående exemplet och isoleringen av innehåll gör vi ett dedikerat lagringskonto för våra artefakter (särskilt Resource Manager-mallarna).

    Lägg till **azure Resource Group Deployment-aktiviteten** och ange följande egenskaper:
    * **Visningsnamn:** Distribuera lagringskonto för Resource Manager-mallar
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Åtgärd**: Skapa eller uppdatera resursgrupp
    * **Resursgrupp**: $(resourceGroupName)
    * **Plats**: $(plats)
    * **Mall**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Åsidosätta mallparametrar**: -accountName $(storageAccountName)

1. Ladda upp artefakterna från källkontrollen till lagringskontot. Det finns en Azure Pipeline-uppgift för att utföra detta. Som en del av den här uppgiften kan URL:en för lagringskontobehållare och SAS-token matas ut till en variabel i Azure Pipelines. Detta innebär att den kan återanvändas under hela denna agentfas.

    Lägg till azure **file copy-uppgiften** och ange följande egenskaper:
    * **Källa:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Azure-anslutningstyp:** Azure Resource Manager
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Måltyp**: Azure Blob
    * **RM-lagringskonto**: $(storageAccountName)
    * **Behållarens namn**: mallar
    * **Lagringsbehållare URI**: templateContainerUri
    * **SAS-token för lagringsbehållare**: templateContainerSasToken

1. Distribuera orchestratormallen. Återkalla orchestrator-mallen från tidigare, kommer du att märka att det fanns parametrar för storage account container URL, förutom SAS-token. Du bör märka att de variabler som krävs i Resource Manager-mallen antingen finns i variabelavsnittet i versionsdefinitionen eller har angetts från en annan Azure Pipelines-aktivitet (till exempel en del av Azure Blob Copy-aktiviteten).

    Lägg till **azure Resource Group Deployment-aktiviteten** och ange följande egenskaper:
    * **Visningsnamn:** Distribuera Azure Batch
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Åtgärd**: Skapa eller uppdatera resursgrupp
    * **Resursgrupp**: $(resourceGroupName)
    * **Plats**: $(plats)
    * **Mall**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Åsidosätta mallparametrar:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

En vanlig metod är att använda Azure Key Vault-uppgifter. Om tjänsthuvudmannen (anslutning till din Azure-prenumeration) har en lämplig åtkomstprincipuppsättning kan den hämta hemligheter från ett Azure Key Vault och användas som variabler i pipelinen. Namnet på hemligheten kommer att anges med det associerade värdet. Till exempel kan en hemlighet av sshPassword refereras med $(sshPassword) i releasedefinitionen.

1. I nästa steg anropas Azure CLI. Den första används för att skapa ett program i Azure Batch. och ladda upp tillhörande paket.

    Lägg till **Azure CLI-uppgiften** och ange följande egenskaper:
    * **Visningsnamn:** Skapa program i Azure Batch-konto
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Skriptplats:** Infogat skript
    * **Infogat skript:**```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Det andra steget används för att överföra associerade paket till programmet. I vårt fall, ffmpeg filer.

    Lägg till **Azure CLI-uppgiften** och ange följande egenskaper:
    * **Visningsnamn:** Ladda upp paket till Azure Batch-konto
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Skriptplats:** Infogat skript
    * **Infogat skript:**```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Versionsnumret för programpaketet är inställt på en variabel. Detta är praktiskt om överskrivning av tidigare versioner av paketet fungerar för dig, och om du vill styra versionsnumret för paketet manuellt till Azure Batch.

1. Skapa en ny version genom att välja **Release > Skapa en ny version**. När du har utlöst den väljer du länken till den nya versionen för att visa statusen.

1. Du kan visa liveutdata från agenten genom att välja knappen **Loggar** under din miljö.

    ![Visa status för din release](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testa miljön

När miljön har konfigurerats bekräftar du att följande tester kan slutföras.

Anslut till det nya Azure Batch-kontot med hjälp av Azure CLI från en PowerShell-kommandotolk.

* Logga in på ditt `az login` Azure-konto med och följ instruktionerna för att autentisera.
* Autentisera nu batchkontot:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Lista tillgängliga program

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Kontrollera att poolen är giltig

```azurecli
az batch pool list
```

Observera värdet `currentDedicatedNodes` för från utdata från det här kommandot. Det här värdet justeras i nästa test.

#### <a name="resize-the-pool"></a>Ändra storlek på poolen

Ändra storlek på poolen så att det finns beräkningsnoder tillgängliga för jobb- och uppgiftstestning, kontrollera med kommandot poollista för att se aktuell status tills storleksändringen har slutförts och det finns tillgängliga noder

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Nästa steg

Förutom den här artikeln finns det två självstudier som använder ffmpeg, med .NET och Python. Se dessa självstudier för mer information om hur du interagerar med ett Batch-konto via ett enkelt program.

* [Köra en parallell arbetsbelastning med Azure Batch med hjälp av Python API](tutorial-parallel-python.md)
* [Köra en parallell arbetsbelastning med Azure Batch med hjälp av .NET API:et](tutorial-parallel-dotnet.md)
