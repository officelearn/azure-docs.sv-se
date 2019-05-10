---
title: Använda Azure Deployment Manager med Resource Manager-mallar | Microsoft Docs
description: Använda Resource Manager-mallar med Azure Deployment Manager för att distribuera Azure-resurser.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/06/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4f3f43ba063ed25389860183576c4dbd94e2bfcd
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466335"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Självstudier: Hälsokontrollen för användning i Azure Deployment Manager (förhandsversion)

Lär dig hur du integrerar hälsokontroll i [Azure Deployment Manager](./deployment-manager-overview.md). Den här självstudien är baserad på den [Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md) självstudien. Innan du fortsätter med den här måste du slutföra självstudien.

I mallen för distribution som används i [Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md), du har använt ett vänta steg. I den här självstudiekursen ersätter du vänta steg med ett hälsotillstånd steg.

> [!IMPORTANT]
> Om din prenumeration har markerats för Kanarieöarna att testa nya Azure-funktioner, kan du bara använda Distributionshanteraren för Azure för att distribuera till kontrollvärde regioner. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en hälsotillstånd Kontrollera service simulator
> * Redigera mallen för distribution
> * Distribuera topologin
> * Distribuera distributionen med skadad status
> * Kontrollera distributionen distributionen
> * Distribuera distributionen med hälsostatus
> * Kontrollera distributionen distributionen
> * Rensa resurser

Azure Deployment Manager REST API-referensen finns [här](https://docs.microsoft.com/rest/api/deploymentmanager/).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* Fullständig [Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).
* Ladda ned [mallar och artefakter](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) som används av den här självstudien. 

## <a name="create-a-health-check-service-simulator"></a>Skapa en hälsotillstånd Kontrollera service simulator

I produktion använder du vanligtvis en eller flera övervakning providers. Microsoft har arbetat med några av företag så att du får en enkel kopiera och klistra in-lösning för att integrera hälsokontroller dina distributioner av övre service hälsoövervakning för att göra hälsotillstånd integration så enkel som möjligt. En lista över dessa företag finns i [hälsoövervakning providers](./deployment-manager-health-check.md#health-monitoring-providers). I den här självstudien skapar du en [Azure Function](/azure/azure-functions/) att simulera en hälsoövervakning för tjänsten. Den här funktionen tar en statuskod och returnerar samma kod. Distributionshanteraren för Azure-mall använder statuskoden för att avgöra hur du fortsätter med distributionen. 

Följande två filer används för att distribuera Azure-funktion. Du behöver inte ladda ned filerna för att gå igenom självstudien.

* Resource Manager-mall finns i [ https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json ](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Du distribuerar den här mallen om du vill skapa en Azure-funktion.  
* En zip-fil på Azure Function-källkod [ https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip ](https://armtutorials.blob.core.windows.net/admtutorial/RestHealthTest.zip). Den här zip kallas anropas av Resource Manager-mallen.

För att distribuera Azure-funktion, Välj **prova** att öppna Azure Cloud shell och klistra in följande skript i fönstret shell.  Om du vill klistra in koden, högerklicka på shell-fönstret och välj sedan **klistra in**. 

> [!IMPORTANT]
> **projectName** i PowerShell skript används för att generera namn för Azure-tjänster som distribueras i den här självstudien. Olika Azure-tjänster har olika krav på namnen. För att säkerställa att distributionen är klar, väljer du ett namn med färre än 12 tecken innehåller bara gemena bokstäver och siffror.
> Spara en kopia av projektnamnet. Du kan använda samma projektnamn självstudien.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Att kontrollera och testa Azure-funktion:

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Öppna resursgruppen.  Standardnamnet är projektnamnet på med **rg** sist.
1. Välj app service från resursgruppen.  Standardnamnet för app Service är projektnamn med **webapp** sist.
1. Expandera **Functions**, och välj sedan **HttpTrigger1**. 

    ![Azure Deployment Manager-hälsokontroll Azure-funktion](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Välj  **&lt;/ > hämta Funktionswebbadress**.
1. Välj **kopia** att kopiera Webbadressen till Urklipp.  URL: en är ungefär:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Ersätt `{healthStatus}` i URL: en med en statuskod. I den här självstudien **feltillstånd** att testa det felaktiga scenariot och använda antingen **felfri** eller **varning** att testa felfri scenariot. Skapa två URL: er, en med skadad status och den andra med hälsostatus. Exempel:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Du måste båda URL: er för att slutföra den här självstudien.

1. Öppna URL: er som du skapade i det sista steget för att testa hälsotillstånd övervakning simulatorn.  Resultat för skadad status bör likna följande:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Redigera mallen för distribution

Syftet med det här avsnittet är att hur du kan ta ett hälsotillstånd steg i mallen för distributionen. Du behöver skapa en egen CreateADMRollout.json-fil för att slutföra den här självstudien. Mallen ändrade distributionen delas i ett lagringskonto som används i följande avsnitt.

1. Öppna **CreateADMRollout.json**. Det här JSON-fil är en del av nedladdningen.  Se [Förutsättningar](#prerequisites).
1. Lägg till ytterligare två parametrar:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Ersätt resursdefinitionen vänta steg med en resursdefinition för health Kontrollera steg:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    Baserat på definitionen, fortsätter distributionen om hälsotillståndet är antingen *felfri* eller *varning*. 

1. Uppdatera den **dependsON** av distributionen definition att inkludera nya hälsotillstånd Kontrollera steg:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Uppdatera **stepGroups** att inkludera hälsotillstånd Kontrollera steg. Den **healthCheckStep** kallas i **postDeploymentSteps** av **stepGroup2**. **stepGroup3** och **stepGroup4** distribueras endast om hälsostatus är antingen *felfri* eller *varning*. 

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Om du jämför de **stepGroup3** avsnittet före och efter den ändras i det här avsnittet nu beror på **stepGroup2**.  Detta är nödvändigt när **stepGroup3** och grupperna efterföljande steg beroende av resultaten med hälsoövervakning.

    Följande skärmbild illustrerar de områden som ändras och hur hälsotillståndet Kontrollera steg används:

    ![Azure Deployment Manager health Kontrollera-mall](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Distribuera topologin

För att förenkla självstudien som topologi mall och artefakter delas på följande platser så att du inte behöver att förbereda din egna kopia. Om du vill använda din egen, följer du anvisningarna i [självstudien: Använda Distributionshanteraren för Azure med Resource Manager-mallar](./deployment-manager-tutorial.md).

* Topologi mall: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Artefakter store: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Om du vill distribuera topologin, Välj **prova** öppna cloudshell och klistra in PowerShell-skriptet.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Kontrollera att tjänsttopologin och de angivna resurserna har skapats på Azure-portalen:

![Azure Deployment Manager-självstudie om resurser för en distribuerad tjänsttopologi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

**Visa dolda typer** måste väljas för att resurserna ska visas.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Distribuera distributionen med skadad status

För att förenkla självstudien kan delas mallen reviderade distribution på följande plats så att du inte behöver att förbereda din egna kopia. Om du vill använda din egen, följer du anvisningarna i [självstudien: Använda Distributionshanteraren för Azure med Resource Manager-mallar](./deployment-manager-tutorial.md).

* Topologi mall: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Artefakter store: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Använd skadad status-URL som du skapade i [skapa hälsotillstånd Kontrollera service simulator](#create-a-health-check-service-simulator). För **managedIdentityID**, se [skapa den hanterade Användartilldelad identitet](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` är ett asynkront anrop. Lyckades visas endast innebär att distributionen har har startat. Verifiera distributionen genom att använda `Get-AZDeploymentManagerRollout`.  Finns i nästa procedur.

Så här kontrollerar du förloppet distributionen med hjälp av följande PowerShell-skript:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

Följande exempel på utdata visar distributionen misslyckades på grund av felaktiga status:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

När distributionen är klar kan se du en ytterligare resursgruppen som skapades för USA, västra.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Distribuera distributionen med hälsostatus

Upprepa det här avsnittet om du vill distribuera om distributionen med URL: en för hälsostatus.  När distributionen är klar kan se du en mer resursgruppen som skapades för östra USA.

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Bläddra till de nyligen skapade webbprogrammen under de nya resursgrupperna som skapades i samband med distributionen.
3. Öppna webbprogrammet i en webbläsare. Kontrollera platsen och versionen för index.html-filen.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Använd fältet **Filtrera efter namn** för att visa resursgrupperna som skapats i den här självstudien. De bör vara 3–4:

    * **&lt;namePrefix>rg**: Innehåller Deployment Manager-resurserna.
    * **&lt;namePrefix>ServiceWUSrg**: Innehåller resurserna som definieras av ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**: Innehåller resurserna som definieras av ServiceEUS.
    * Resursgruppen för den användardefinierade hanterade identiteten.
3. Välj resursgruppens namn.  
4. Välj **Ta bort resursgrupp** från menyn längst upp.
5. Upprepa de två sista stegen för att ta bort andra resursgrupper som skapats av den här självstudien.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda funktionen Kontrollera hälsotillståndet för Azure Deployment Manager. Mer information finns i [dokumentationen om Azure Resource Manager](/azure/azure-resource-manager/).
