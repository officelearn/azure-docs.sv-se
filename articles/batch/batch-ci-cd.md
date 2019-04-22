---
title: Använd Azure Pipelines för att skapa och distribuera HPC - lösningar i Azure Batch | Microsoft Docs
description: Lär dig hur du distribuerar en version och utgåva pipeline för ett HPC-program som körs på Azure Batch.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494652"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Använd Azure Pipelines för att skapa och distribuera HPC-lösningar

Azure DevOps-tjänsterna tillhandahåller ett antal verktyg som används av utvecklingsteam när du skapar ett anpassat program. Verktygen i Azure DevOps kan översätta till automatiserade att skapa och testa av compute-lösningar med hög prestanda. Den här artikeln visar hur du ställer in en kontinuerlig integrering (CI) och kontinuerlig distribution (CD) med hjälp av Azure Pipelines för en högpresterande lösningen distribueras på Azure Batch.

Pipelines som Azure erbjuder en uppsättning moderna CI/CD-processer för att skapa, distribuera, testa och övervakningsprogram. De här processerna påskynda din programvaruleverans, så att du kan fokusera på din kod i stället för att stödja infrastrukturen och åtgärder.

## <a name="create-an-azure-pipeline"></a>Skapa en Azure-Pipeline

I det här exemplet ska vi skapa en version och släppa pipeline för att distribuera en Azure Batch-infrastruktur och släpp programpaket. Om vi antar att koden utvecklas lokalt, är detta allmänna distributionsflödet:

![Diagram över flödet av distribution i vår Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Konfiguration

Om du vill följa stegen i den här artikeln behöver du en Azure DevOps-organisation och ett teamprojekt.

* [Skapa en Azure DevOps-organisation](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Skapa ett projekt i Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Källkontrollen för din miljö

Källkontroll kan grupper att spåra ändringar i kodbasen och inspektera tidigare versioner av koden.

Källkontroll är vanligtvis betraktas hand i hand med programkoden. Vad sägs om den underliggande infrastrukturen? Därmed avslutar vi detta infrastruktur som kod, där vi använder Azure Resource Manager-mallar eller andra alternativ för öppen källkod för att deklarativt definiera vår underliggande infrastruktur.

Det här exemplet beroende kraftigt av ett antal Resource Manager-mallar (JSON-dokument) och befintliga program. Du kan kopiera de här exemplen i din databas och skicka dem till Azure DevOps.

Kodbas-strukturen som används i det här exemplet liknar följande:

* En **arm-mallar** mappen som innehåller ett antal Azure Resource Manager-mallar. Mallarna beskrivs i den här artikeln.
* En **-klientprogram** -mappen, som är en kopia av den [Azure Batch .NET-filen bearbetning med ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) exemplet. Detta behövs inte för den här artikeln.
* En **hpc-program** -mappen, som är Windows 64-bitars version av [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* En **pipelines** mapp. Innehåller en YAML-fil som beskriver våra skapandeprocessen. Detta beskrivs i artikeln.

Det här avsnittet förutsätter att du är bekant med version kontroll och designa Resource Manager-mallar. Om du inte är bekant med de här koncepten finns på följande sidor för mer information.

* [Vad är källkontroll?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Det här exemplet använder flera Resource Manager-mallar för att distribuera vår lösning. Detta gör använder vi ett antal funktionen-mallar (liknar enheter eller moduler) som implementerar en viss typ av funktioner. Vi kan också använda en lösning från slutpunkt till slutpunkt-mall som ansvarar för att leverera de underliggande funktioner tillsammans. Det finns några fördelar med att den här metoden:

* De underliggande funktionen mallarna kan vara individuellt enhet som testas.
* Underliggande funktionen mallarna kan definieras som en standard i en organisation och återanvändas i flera lösningar.

Det finns en lösning från slutpunkt till slutpunkt-mall (deployment.json) som distribuerar tre mallar för det här exemplet. De underliggande mallarna är funktionen mallar, ansvarar för att distribuera en viss aspekt av lösningen.

![Exempel på den länkade mallen strukturen med hjälp av Azure Resource Manager-mallar](media/batch-ci-cd/ARMTemplateHierarchy.png)

Den första mallen som vi ska titta på används för ett Azure Storage-konto. Vår lösning kräver ett lagringskonto för att distribuera programmet till vår Batch-konto. Det är värt att märker den [referensguiden för Resource Manager-mall för Microsoft.Storage-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) när du skapar Resource Manager-mallar för Storage-konton.

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

Nu ska ska vi titta på mallen för Azure Batch-konto. Azure Batch-kontot fungerar som en plattform för att köra flera program i pooler (grupperingar av datorer). Det är värt att märker den [referensguiden för Resource Manager-mall för Microsoft.Batch-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) när du skapar Resource Manager-mallar för Batch-konton.

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

Nästa mallen visas ett exempel som skapar en Azure Batch-Pool (backend-datorer att bearbeta våra program). Det är värt att märker den [referensguiden för Resource Manager-mall för Microsoft.Batch-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) när du skapar Resource Manager-mallar för Batch-konto-pooler.

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

Slutligen har vi en mall som fungerar ungefär som en initierare. Den här mallen är ansvariga för att distribuera funktionen mallar.

Du kan också läsa mer om [Skapa länkade Azure Resource Manager-mallar](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) i en separat artikel.

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

Infrastruktur och programvara kan definieras som kod och samordnat på samma lagringsplats.

För den här lösningen används ffmpeg som programpaketet. Ffmpeg-paketet kan laddas ned [här](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Exempel Git-lagringsplats struktur](media/batch-ci-cd/git-repository.jpg)

Det finns fyra huvudsakliga delar den här databasen:

* Den **arm-mallar** mapp som innehåller vår infrastruktur som kod
* Den **hpc-program** mapp som innehåller binärfiler för ffmpeg
* Den **pipelines** mapp som innehåller en definition för vår build-pipeline.
* **Valfritt**: Den **-klientprogram** mapp som ska lagra kod för .NET-program. Vi använder inte det här i det här exemplet, men i ett eget projekt kan du köra körningar av HPC-Batch-program via ett klientprogram.

> [!NOTE]
> Det här är bara ett exempel på en struktur till en kodbas. Den här metoden används för att demonstrera att program, infrastruktur och pipeline-kod lagras på samma lagringsplats.

Nu när källkoden är konfigurerat kan börja vi den första versionen.

## <a name="continuous-integration"></a>Kontinuerlig integrering

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), inom Azure DevOps Services hjälper dig att implementera en bygga, testa och distribuera pipelinen för dina program.

I den här fasen av din pipeline körs vanligtvis tester för att verifiera kod och skapa lämpliga delar av programvaran. Antalet och typerna av tester och alla ytterligare uppgifter som du kör beror på bygget bredare och viktig strategi.

## <a name="preparing-the-hpc-application"></a>Förbereda HPC-programmet

I det här exemplet ska vi fokusera på den **hpc-program** mapp. Den **hpc-program** mappen är ffmpeg-program som körs från inom Azure Batch-kontot.

1. Gå till avsnittet versioner av Azure Pipelines i din Azure DevOps-organisation. Skapa en **ny pipeline**.

    ![Skapa en ny Build-Pipeline](media/batch-ci-cd/new-build-pipeline.jpg)

1. Du har två alternativ för att skapa en Build-pipeline:

    a. [Den visuella designern](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Om du vill använda det, klickar du på ”använda den visuella designern” på den **ny pipeline** sidan.

    b. [Med hjälp av YAML bygger](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Du kan skapa en ny YAML-pipeline genom att klicka på alternativet Azure-databaser eller GitHub på sidan Ny pipeline. Du kan också lagra exemplet nedan i din källkontroll och referera till en befintlig YAML-fil genom att klicka på visuell Designer och sedan använda YAML-mallen.

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

1. När versionen konfigureras efter behov, väljer **spara och köa**. Om du har kontinuerlig integrering aktiverat (i den **utlösare** avsnittet), versionen utlöser automatiskt när en ny incheckning till lagringsplatsen görs, uppfyller krav som i versionen.

    ![Exempel på en befintlig skapa Pipeline](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visa live uppdateringar på förloppet för bygget i Azure DevOps genom att navigera till den **skapa** avsnitt av Azure-Pipelines. Välj lämplig version från build-definition.

    ![Visa live utdata från bygget](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Om du använder ett klientprogram för att köra ett Batch HPC-program kan behöva du skapa en separat byggesdefinition för programmet. Du kan hitta ett antal guider i den [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) dokumentation.

## <a name="continuous-deployment"></a>Kontinuerlig distribution

Azure Pipelines används också för att distribuera dina program och den underliggande infrastrukturen. [Släppa pipelines](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) är den komponent som möjliggör kontinuerlig distribution och automatiserar dina lanseringsprocessen.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Distribuera programmet och underliggande infrastruktur

Det finns ett antal steg ingår i distributionen av infrastrukturen. Eftersom vi har använt [länkade mallar](../azure-resource-manager/resource-group-linked-templates.md), måste de mallarna ska vara tillgängliga från en offentlig slutpunkt (HTTP eller HTTPS). Detta kan vara en arkivet på GitHub, eller ett Azure Blob Storage-konto eller en annan lagringsplats. Överförda mall artefakter kan förblir säkra, eftersom de kan lagras i privat läge men nås med hjälp av någon form av signaturtoken för delad åtkomst (SAS). I följande exempel visar hur du distribuerar en infrastruktur med mallar från en Azure Storage-blob.

1. Skapa en **nya Versionsdefinition**, och välj en tom definition. Vi måste du byta namn på den nya miljön till något som är relevanta för vår pipeline.

    ![Första versionen Pipeline](media/batch-ci-cd/Release-0.jpg)

1. Skapa ett beroende till skapa pipelinen för att hämta utdata för vårt HPC-program.

    > [!NOTE]
    > Observera återigen det **källa Alias**, eftersom detta krävs när aktiviteter har skapats i Versionsdefinitionen.

    ![Skapa en artefakt-länk till HPCApplicationPackage i lämplig build-pipeline](media/batch-ci-cd/Release-1.jpg)

1. Skapa en länk till en annan artefakt nu en Azure-lagringsplats. Detta krävs för att få åtkomst till Resource Manager-mallar som lagras i databasen. Eftersom Resource Manager-mallar inte kräver kompilering, behöver du inte skicka dem via en build-pipeline.

    > [!NOTE]
    > Observera återigen det **källa Alias**, eftersom detta krävs när aktiviteter har skapats i Versionsdefinitionen.

    ![Skapa en artefakt-länk till Azure-databaser](media/batch-ci-cd/Release-2.jpg)

1. Navigera till den **variabler** avsnittet. Vi rekommenderar för att skapa ett antal variabler i din pipeline, så att du inte mata in samma information i flera aktiviteter. Det här är de variabler som används i det här exemplet och hur de påverkar distributionen.

    * **applicationStorageAccountName**: Namnet på Storage-konto för att lagra binärfilerna för HPC-programmet
    * **batchAccountApplicationName**: Namnet på programmet i Azure Batch-konto
    * **batchAccountName**: Namnet på Azure Batch-konto
    * **batchAccountPoolName**: Namnet på poolen med virtuella datorer gör bearbetning
    * **batchApplicationId**: Unikt ID för Azure Batch-program
    * **batchApplicationVersion**: Semantisk version av ett batch-program (det vill säga ffmpeg binära)
    * **Plats**: Plats för Azure-resurser som ska distribueras
    * **resourceGroupName**: Namnet på resursgruppen som ska skapas, och där resurserna ska distribueras
    * **storageAccountName**: Namnet på Storage-konto för att lagra länkade Resource Manager-mallar

    ![Exempel på variabler som släpps för Azure-Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Gå till aktiviteterna för Dev-miljö. I de nedanstående ögonblicksbild, kan du se sex uppgifterna. Dessa uppgifter kommer: ladda ned de komprimerade ffmpeg-filerna, distribuera ett lagringskonto för att vara värd för de kapslade Resource Manager-mallarna, kopiera dessa Resource Manager-mallar till lagringskontot, distribuera batch-konto och nödvändiga beroenden, skapa ett program i Azure Batch-konto och ladda upp programpaket till Azure Batch-konto.

    ![Exempel på uppgifter som används för att frisläppa HPC-programmet till Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Lägg till den **hämta Pipeline artefakten (förhandsversion)** uppgift och ange följande egenskaper:
    * **Visningsnamn:** Ladda ned ApplicationPackage-agenten
    * **Namnet på artefakten att ladda ned:** hpc-program
    * **Sökväg för nedladdning till**: $(System.DefaultWorkingDirectory)

1. Skapa ett Lagringskonto för att lagra dina artefakter. Ett befintligt lagringskonto från lösningen skulle kunna användas, men för fristående exempel och isolering av innehåll kan vi gör ett dedikerat storage-konto för vår artefakter (särskilt Resource Manager-mallar).

    Lägg till den **Azure Resursgruppsdistribution** uppgift och ange följande egenskaper:
    * **Visningsnamn:** Distribuera Storage-konto för Resource Manager-mallar
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Åtgärd**: Skapa eller uppdatera resursgrupp
    * **Resursgrupp**: $(resourceGroupName)
    * **Plats**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Åsidosätt mallparametrar**: - accountName $(storageAccountName)

1. Ladda upp artefakter från källkontrollen till Lagringskontot. Det finns en Azure-Pipeline-aktivitet för att utföra detta. Som en del av den här uppgiften, den Molnlagringskontots behållar-URL och SAS-Token kan vara för utdata till en variabel i Azure-Pipelines. Det innebär att den kan återanvändas i hela den här fasen av agenten.

    Lägg till den **Azure File Copy** uppgift och ange följande egenskaper:
    * **Källa:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates /
    * **Azure anslutningstypen**: Azure Resource Manager
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Måltypen**: Azure-blobb
    * **RM Lagringskonto**: $(storageAccountName)
    * **Behållarnamn**: mallar
    * **Storage behållar-URI**: templateContainerUri
    * **Storage-behållare SAS-Token**: templateContainerSasToken

1. Distribuera orchestrator-mallen. Kom ihåg orchestrator-mall från tidigare, kommer du att märka att det fanns parametrar för den Molnlagringskontots behållar-URL, förutom SAS-token. Du bör märka att variablerna som krävs i Resource Manager-mallen lagras antingen i avsnittet variabler i versionsdefinitionen eller har ställts in från en annan Azure-Pipelines aktivitet (till exempel att en del av Azure Blob Copy-aktiviteten).

    Lägg till den **Azure Resursgruppsdistribution** uppgift och ange följande egenskaper:
    * **Visningsnamn:** Distribuera Azure Batch
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Åtgärd**: Skapa eller uppdatera resursgrupp
    * **Resursgrupp**: $(resourceGroupName)
    * **Plats**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Åsidosätt mallparametrar**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

En vanlig metod är att använda Azure Key Vault-uppgifter. Om tjänstens huvudnamn (anslutning till din Azure-prenumeration) har en lämplig åtkomst-principer som angetts, den kan hämta hemligheter från ett Azure Key Vault och kan användas som variabler i din pipeline. Namnet på hemligheten att skapas med det associerade värdet. Exempelvis kan en hemlighet i sshPassword refereras med $(sshPassword) i versionsdefinitionen.

1. Nästa steg anropar Azure CLI. Först används för att skapa ett program i Azure Batch. och ladda upp associerade paket.

    Lägg till den **Azure CLI** uppgift och ange följande egenskaper:
    * **Visningsnamn:** Skapa program i Azure Batch-konto
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Skriptplats**: Infogat skript
    * **Infogat skript**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Det andra steget används för att ladda upp associerade paket till programmet. I vårt fall ffmpeg-filer.

    Lägg till den **Azure CLI** uppgift och ange följande egenskaper:
    * **Visningsnamn:** Ladda upp paketet till Azure Batch-konto
    * **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    * **Skriptplats**: Infogat skript
    * **Infogat skript**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Det lägre versionsnumret för programpaketet har angetts till en variabel. Detta är praktiskt om att skriva över tidigare versioner av paketet passar dig, och om du vill manuellt kontrollera det lägre versionsnumret för det paket som skickas till Azure Batch.

1. Skapa en ny version genom att välja **viktig > Skapa en ny version**. När utlöses, väljer du länken till din nya version att visa status.

1. Du kan visa live utdata från agenten genom att välja den **loggar** knappar under din miljö.

    ![Visa status för din version](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testa miljön

När miljön har ställts in, kan du kontrollera följande tester kan slutföras.

Ansluta till den nya Azure Batch-konto, med hjälp av Azure CLI från en PowerShell-kommandotolk.

* Logga in på ditt Azure-konto med `az login` och följ instruktionerna för att autentisera.
* Autentiseras Batch-kontot: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>En lista med tillgängliga program

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Kontrollera att poolen är giltigt

```azurecli
az batch pool list
```

Anteckna värdet för `currentDedicatedNodes` från kommandots utdata. Det här värdet har ändrats i nästa testet.

#### <a name="resize-the-pool"></a>Ändra storlek på poolen

Ändra storlek på poolen så att det finns compute-noder som är tillgängliga för jobbet och uppgiften testning, kontrollera med kommandot pool listan att visa aktuell status tills åtgärden har slutförts och det finns tillgängliga noder

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Nästa steg

Förutom den här artikeln finns det två självstudier som använder ffmpeg, med .NET och Python. Se de här självstudierna för mer information om hur du interagerar med ett Batch-konto via ett enkelt program.

* [Kör en parallell arbetsbelastning med Azure Batch med hjälp av Python API](tutorial-parallel-python.md)
* [Kör en parallell arbetsbelastning med Azure Batch med hjälp av .NET-API](tutorial-parallel-dotnet.md)
