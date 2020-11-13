---
title: Använd Azure-pipelines för att bygga & distribuera HPC-lösningar
description: Lär dig hur du distribuerar en pipeline för att bygga/släppa för ett HPC-program som körs på Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: e87be0db65cf12a265566e0c05815722ce3cc609
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578883"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Använd Azure-pipelines för att bygga och distribuera HPC-lösningar

Azure DevOps Services tillhandahåller en mängd verktyg som används av utvecklings grupper när du skapar ett anpassat program. Verktyg som tillhandahålls av Azure DevOps kan översätta till automatiserad utveckling och testning av beräknings lösningar med höga prestanda. Den här artikeln visar hur du konfigurerar en kontinuerlig integrering (CI) och kontinuerlig distribution (CD) med Azure-pipelines för en beräknings lösning med hög prestanda som distribueras på Azure Batch.

Azure-pipeliner innehåller en mängd moderna CI/CD-processer för att skapa, distribuera, testa och övervaka program. Dessa processer påskyndar din program varu leverans så att du kan fokusera på din kod i stället för att stödja infrastruktur och åtgärder.

## <a name="create-an-azure-pipeline"></a>Skapa en Azure-pipeline

I det här exemplet ska vi skapa en pipeline för build och release för att distribuera en Azure Batch-infrastruktur och lansera ett programpaket. Förutsatt att koden utvecklas lokalt är detta det allmänna distributions flödet:

![Diagram över distributions flödet i vår pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Installation

För att följa stegen i den här artikeln behöver du en Azure DevOps-organisation och ett grup projekt.

* [Skapa en Azure DevOps-organisation](/azure/devops/organizations/accounts/create-organization)
* [Skapa ett projekt i Azure DevOps](/azure/devops/organizations/projects/create-project)

### <a name="source-control-for-your-environment"></a>Käll kontroll för din miljö

Med käll kontroll kan grupper spåra ändringar som gjorts i kodbasen och granska tidigare versioner av koden.

Normalt betraktas käll kontroll med hand med program varu kod. Hur är det underliggande infrastrukturen? Detta leder till infrastruktur som kod, där vi kommer att använda Azure Resource Manager mallar eller andra alternativ med öppen källkod för att definiera vår underliggande infrastruktur.

Det här exemplet är mycket beroende av ett antal Resource Manager-mallar (JSON-dokument) och befintliga binärfiler. Du kan kopiera dessa exempel till din lagrings plats och skicka dem till Azure-DevOps.

Kodbas-strukturen som används i det här exemplet liknar följande:

* En mapp för **arm-mallar** som innehåller ett antal Azure Resource Manager mallar. Mallarna förklaras i den här artikeln.
* En mapp för **klient program** , som är en kopia av [Azure Batch .net-fil bearbetning med ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) -exempel. Detta behövs inte för den här artikeln.
* En **HPC-** programmapp, som är Windows 64-bitars versionen av [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
* En **pipeline** -mapp. Detta innehåller en YAML-fil som beskriver vår build-process. Detta beskrivs i artikeln.

Det här avsnittet förutsätter att du är bekant med versions kontroll och hur du utformar Resource Manager-mallar. Om du inte är bekant med dessa begrepp kan du se följande sidor för mer information.

* [Vad är käll kontroll?](/azure/devops/user-guide/source-control)
* [Förstå strukturen och syntaxen för Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Det här exemplet utnyttjar flera Resource Manager-mallar för att distribuera vår lösning. För att göra detta använder vi ett antal Capability-mallar (liknar enheter eller moduler) som implementerar en viss funktion. Vi använder också en mall från slut punkt till slut punkt som ansvarar för att sätta samman de underliggande funktionerna. Det finns några fördelar med den här metoden:

* De underliggande kapacitets mallarna kan testas separat.
* De underliggande kapacitets mallarna kan definieras som standard i en organisation och återanvändas i flera lösningar.

I det här exemplet finns en mall för heltäckande lösningar (deployment.jspå) som distribuerar tre mallar. De underliggande mallarna är Capabilities som ansvarar för att distribuera en speciell aspekt av lösningen.

![Exempel på länkad mall struktur med Azure Resource Manager mallar](media/batch-ci-cd/ARMTemplateHierarchy.png)

Den första mallen som vi ska titta på är för ett Azure Storage-konto. Vår lösning kräver ett lagrings konto för att distribuera programmet till vårt batch-konto. Det är värt att känna till [referens guiden för Resource Manager-mallar för Microsoft. lagrings resurs typer](/azure/templates/microsoft.storage/allversions) när du skapar Resource Manager-mallar för lagrings konton.

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

Härnäst ska vi titta på mallen för Azure Batch konto. Azure Batch-kontot fungerar som en plattform för att köra flera program i pooler (grupperingar av datorer). Det är värt att vara medveten om Resource [Manager-mallens referens guide för Microsoft.BatCH-resurs typer](/azure/templates/microsoft.batch/allversions) när du skapar Resource Manager-mallar för batch-konton.

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

Nästa mall visar ett exempel på hur du skapar en Azure Batch pool (backend-datorerna för att bearbeta våra program). Det är värt att vara medveten om Resource [Manager-mallens referens guide för Microsoft.BatCH-resurs typer](/azure/templates/microsoft.batch/allversions) när du skapar Resource Manager-mallar för batch-konto-pooler.

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

Slutligen har vi en mall som fungerar ungefär som en Orchestrator. Den här mallen ansvarar för distributionen av Capabilities-mallarna.

Du kan också få mer information om hur du [skapar länkade Azure Resource Manager mallar](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) i en separat artikel.

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

Infrastrukturen och program varan kan definieras som kod och samplacerade i samma lagrings plats.

I den här lösningen används ffmpeg som programpaket. Ffmpeg-paketet kan laddas ned [här](https://www.videohelp.com/software?d=ffmpeg-3.3.4-win64-static.zip).

![Exempel struktur för git-lagringsplats](media/batch-ci-cd/git-repository.jpg)

Det finns fyra huvud avsnitt i den här lagrings platsen:

* Mappen **arm-mallar** som lagrar vår infrastruktur som kod
* Den **HPC-** programmapp som innehåller binärfilerna för ffmpeg
* Mappen **pipelines** som innehåller definitionen för vår build-pipeline.
* **Valfritt** : mappen **klient-app** som lagrar kod för .NET-program. Vi använder inte detta i exemplet, men i ditt eget projekt kanske du vill köra körningar av HPC-batch-programmet via ett klient program.

> [!NOTE]
> Detta är bara ett exempel på en struktur för en kodbas. Den här metoden används för att demonstrera att program, infrastruktur och pipeline-kod lagras i samma lagrings plats.

Nu när käll koden har kon figurer ATS kan vi börja den första versionen.

## <a name="continuous-integration"></a>Kontinuerlig integrering

[Azure-pipeliner](/azure/devops/pipelines/get-started/), inom Azure DevOps Services, hjälper dig att implementera en pipeline för build, test och distribution för dina program.

I det här steget av din pipeline körs testerna vanligt vis för att validera kod och bygga lämpliga delar av program varan. Antalet och typerna av tester och eventuella ytterligare aktiviteter som du kör beror på din bredare version av version och version.

## <a name="preparing-the-hpc-application"></a>Förbereda HPC-programmet

I det här exemplet ska vi fokusera på mappen **HPC-Application** . Mappen **HPC-Application** är den ffmpeg-programvara som kommer att köras inifrån det Azure Batch kontot.

1. Gå till avsnittet versioner i Azure-pipelines i din Azure DevOps-organisation. Skapa en **ny pipeline**.

    ![Skapa en ny versions pipeline](media/batch-ci-cd/new-build-pipeline.jpg)

1. Det finns två alternativ för att skapa en pipeline för bygge:

    a. [Använda den visuella designern](/azure/devops/pipelines/get-started-designer). Om du vill använda detta klickar du på "Använd den visuella designern" på sidan **ny pipeline** .

    b. [Använda yaml-versioner](/azure/devops/pipelines/get-started-yaml). Du kan skapa en ny YAML-pipeline genom att klicka på alternativet Azure databaser eller GitHub på sidan ny pipeline. Du kan också lagra exemplet nedan i käll kontrollen och referera till en befintlig YAML-fil genom att klicka på Visual designer och sedan använda YAML-mallen.

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

1. När versionen har kon figurer ATS vid behov väljer du **spara & kö**. Om du har kontinuerlig integrering aktive rad (i avsnittet **utlösare** ) utlöses build automatiskt när en ny incheckning av lagrings platsen görs, vilket uppfyller de villkor som anges i versionen.

    ![Exempel på en befintlig versions pipeline](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visa direktsända uppdateringar av förloppet för din version av Azure DevOps genom att gå till **Bygg** avsnittet i Azure-pipeliner. Välj lämplig version från bygg definitionen.

    ![Visa Live-utdata från din version](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Om du använder ett klient program för att köra ditt HPC-batchjobb måste du skapa en separat build-definition för programmet. Du kan hitta ett antal instruktions guider i dokumentationen för [Azure pipeline](/azure/devops/pipelines/get-started/index) .

## <a name="continuous-deployment"></a>Kontinuerlig distribution

Azure-pipelines används också för att distribuera ditt program och den underliggande infrastrukturen. [Versions pipelines](/azure/devops/pipelines/release) är den komponent som möjliggör kontinuerlig distribution och automatiserar din versions process.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Distribuera ditt program och den underliggande infrastrukturen

Det finns ett antal steg som ingår i distributionen av infrastrukturen. Som vi har använt [länkade mallar](../azure-resource-manager/templates/linked-templates.md)måste de mallarna vara tillgängliga från en offentlig slut punkt (http eller https). Detta kan vara en lagrings plats på GitHub, ett Azure Blob Storage-konto eller en annan lagrings plats. Artefakter för överförda mallar kan vara skyddade, eftersom de kan lagras i ett privat läge, men nås med hjälp av någon form av SAS-token (signatur för delad åtkomst). Följande exempel visar hur du distribuerar en infrastruktur med mallar från en Azure Storage-blob.

1. Skapa en **ny versions definition** och välj en tom definition. Sedan måste du byta namn på den nyligen skapade miljön till något som är relevant för vår pipeline.

    ![Första versions pipeline](media/batch-ci-cd/Release-0.jpg)

1. Skapa ett beroende på Build-pipeline för att hämta utdata för vårt HPC-program.

    > [!NOTE]
    > Lägg märke till **käll Ali Aset** igen, eftersom det behövs när aktiviteter skapas i versions definitionen.

    ![Skapa en artefakt länk till HPCApplicationPackage i lämplig build-pipeline](media/batch-ci-cd/Release-1.jpg)

1. Skapa en länk till en annan artefakt, den här gången, en Azure-lagrings platsen. Detta krävs för att komma åt Resource Manager-mallarna som lagras i din lagrings plats. Eftersom Resource Manager-mallar inte kräver kompilering behöver du inte skicka dem via en pipeline för bygge.

    > [!NOTE]
    > Lägg märke till **käll Ali Aset** igen, eftersom det behövs när aktiviteter skapas i versions definitionen.

    ![Skapa en artefakt länk till Azure-databaser](media/batch-ci-cd/Release-2.jpg)

1. Navigera till avsnittet **Variables** . Vi rekommenderar att du skapar ett antal variabler i pipelinen, så att du inte skickar samma information till flera aktiviteter. Detta är de variabler som används i det här exemplet och hur de påverkar distributionen.

    * **applicationStorageAccountName** : namnet på lagrings kontot där binärfiler för HPC-program ska undantas
    * **batchAccountApplicationName** : namnet på programmet i Azure Batch-kontot
    * **batchAccountName** : namnet på det Azure Batch kontot
    * **batchAccountPoolName** : namnet på poolen med virtuella datorer som utför bearbetningen
    * **batchApplicationId** : unikt ID för det Azure Batch programmet
    * **batchApplicationVersion** : semantisk version av batch-programmet (det vill säga binärfilerna för ffmpeg)
    * **plats** : plats för de Azure-resurser som ska distribueras
    * **resourceGroupName** : namnet på den resurs grupp som ska skapas och var dina resurser ska distribueras
    * **storageAccountName** : namnet på det lagrings konto som ska innehålla länkade Resource Manager-mallar

    ![Exempel på variabler som ställts in för Azure pipelines-versionen](media/batch-ci-cd/Release-4.jpg)

1. Navigera till aktiviteterna för utvecklings miljön. I ögonblicks bilden nedan kan du se sex uppgifter. Dessa uppgifter kommer att: Hämta de zippade ffmpeg-filerna, distribuera ett lagrings konto som värd för de kapslade Resource Manager-mallarna, kopiera dessa Resource Manager-mallar till lagrings kontot, distribuera batch-kontot och nödvändiga beroenden, skapa ett program i Azure Batch-kontot och ladda upp programpaketet till Azure Batch-kontot.

    ![Exempel på de uppgifter som används för att frigöra HPC-programmet för Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Lägg till aktiviteten **Hämta pipeline artefakt (för hands version)** och ange följande egenskaper:
    * **Visnings namn:** Ladda ned ApplicationPackage till agent
    * **Namnet på den artefakt som ska laddas ned:** HPC-Application
    * **Sökväg att ladda ned till** : $ (system. DefaultWorkingDirectory)

1. Skapa ett lagrings konto för att lagra dina artefakter. Ett befintligt lagrings konto från lösningen kan användas, men för det självständiga exemplet och isolering av innehåll, gör vi ett dedikerat lagrings konto för våra artefakter (särskilt Resource Manager-mallarna).

    Lägg till **distributions uppgiften Azure resurs grupp** och ange följande egenskaper:
    * **Visnings namn:** Distribuera lagrings konto för Resource Manager-mallar
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Åtgärd** : skapa eller uppdatera resurs grupp
    * **Resurs grupp** : $ (resourceGroupName)
    * **Plats** : $ (plats)
    * **Mall** : $ (system. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.jspå
    * **Åsidosätt mallparametrar** :-accountName $ (storageAccountName)

1. Ladda upp artefakterna från käll kontrollen till lagrings kontot. Det finns en Azure pipeline-uppgift för att utföra detta. Som en del av den här uppgiften kan lagrings kontots behållar-URL och SAS-token överföras till en variabel i Azure-pipeliner. Det innebär att den kan återanvändas i den här agent fasen.

    Lägg till **Azure File Copy** -aktiviteten och ange följande egenskaper:
    * **Källa:** $ (system. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Azure-Anslutnings typ** : Azure Resource Manager
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Måltyp** : Azure-Blob
    * **RM-lagrings konto** : $ (storageAccountName)
    * **Behållar namn** : mallar
    * **URI för lagrings behållare** : templateContainerUri
    * **SAS-token för lagrings behållare** : templateContainerSasToken

1. Distribuera Orchestrator-mallen. Återkalla Orchestrator-mallen från tidigare kommer du att märka att det fanns parametrar för lagrings kontots behållar-URL, förutom SAS-token. Observera att de variabler som krävs i Resource Manager-mallen antingen finns i avsnittet variabler i versions definitionen eller ställts in från en annan Azure pipeline-uppgift (till exempel en del av Azure Blob Copy-aktiviteten).

    Lägg till **distributions uppgiften Azure resurs grupp** och ange följande egenskaper:
    * **Visnings namn:** Distribuera Azure Batch
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Åtgärd** : skapa eller uppdatera resurs grupp
    * **Resurs grupp** : $ (resourceGroupName)
    * **Plats** : $ (plats)
    * **Mall** : $ (system. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.jspå
    * **Åsidosätt mallparametrar** : ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

En vanlig metod är att använda Azure Key Vault uppgifter. Om tjänstens huvud namn (anslutning till din Azure-prenumeration) har rätt åtkomst principer, kan den Hämta hemligheter från en Azure Key Vault och användas som variabler i din pipeline. Namnet på hemligheten kommer att anges med det associerade värdet. Till exempel kan en hemlighet för sshPassword refereras till $ (sshPassword) i versions definitionen.

1. Nästa steg är att anropa Azure CLI. Den första används för att skapa ett program i Azure Batch. och ladda upp associerade paket.

    Lägg till **Azure CLI** -aktiviteten och ange följande egenskaper:
    * **Visnings namn:** Skapa program i Azure Batch konto
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Skript plats** : infogat skript
    * **Infogat skript** : ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Det andra steget används för att överföra associerade paket till programmet. I vårt fall, ffmpeg-filerna.

    Lägg till **Azure CLI** -aktiviteten och ange följande egenskaper:
    * **Visnings namn:** Ladda upp paket till Azure Batch konto
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Skript plats** : infogat skript
    * **Infogat skript** : ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Programpaketets versions nummer har angetts till en variabel. Detta är praktiskt om du skriver över tidigare versioner av paketet som passar dig, och om du vill styra paketets versions nummer manuellt till Azure Batch.

1. Skapa en ny version genom att välja **versions > skapa en ny version**. När den har utlösts väljer du länken till den nya versionen för att visa statusen.

1. Du kan visa Live-utdata från agenten genom att välja knappen **loggar** under din miljö.

    ![Visa status för din version](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testa miljön

När miljön har kon figurer ATS bekräftar du att följande test kan slutföras.

Anslut till det nya Azure Batch kontot med hjälp av Azure CLI från en PowerShell-kommandotolk.

* Logga in på ditt Azure-konto med `az login` och följ instruktionerna för att autentisera.
* Autentisera nu batch-kontot: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Visa en lista över tillgängliga program

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Kontrol lera att poolen är giltig

```azurecli
az batch pool list
```

Anteckna värdet för `currentDedicatedNodes` kommandots utdata. Värdet justeras vid nästa test.

#### <a name="resize-the-pool"></a>Ändra storlek på poolen

Ändra storlek på poolen så att det finns tillgängliga Compute-noder för jobb-och uppgifts testning, kontrol lera med kommandot pool List att visa aktuell status tills storleks ändringen har slutförts och det finns tillgängliga noder

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Nästa steg

Utöver den här artikeln finns det två självstudier som använder ffmpeg, med .NET och python. I de här självstudierna finns mer information om hur du interagerar med ett batch-konto via ett enkelt program.

* [Köra en parallell arbetsbelastning med Azure Batch med hjälp av Python API](tutorial-parallel-python.md)
* [Köra en parallell arbetsbelastning med Azure Batch med hjälp av .NET API:et](tutorial-parallel-dotnet.md)
