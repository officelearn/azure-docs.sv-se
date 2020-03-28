---
title: Använd hälsokontroll av Azure Deployment Manager
description: Använd hälsokontroll för att distribuera Azure-resurser på ett säkert sätt med Azure Deployment Manager.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 765c73a3ab8d5fa8939abe597d0141b24b59ac52
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76152485"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Självstudiekurs: Använd hälsokontroll i Azure Deployment Manager (offentlig förhandsversion)

Lär dig hur du integrerar hälsokontroll i [Azure Deployment Manager](./deployment-manager-overview.md). Den här självstudien baseras på självstudien [Använd Azure Deployment Manager med Resource Manager-mallar.](./deployment-manager-tutorial.md) Du måste slutföra den självstudien innan du fortsätter med den här.

I den distributionsmall som används i [Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md)använde du ett väntesteg. I den här självstudien ersätter du väntesteget med ett hälsokontrollsteg.

> [!IMPORTANT]
> Om din prenumeration är markerad för Canary för att testa nya Azure-funktioner kan du bara använda Azure Deployment Manager för att distribuera till Kanarieöarna. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en hälsokontrollservicesimulator
> * Ändra distributionsmallen
> * Distribuera topologin
> * Distribuera distributionen med felstatus
> * Verifiera distributionen av distributionen
> * Distribuera distributionen med felfri status
> * Verifiera distributionen av distributionen
> * Rensa resurser

Ytterligare resurser:

* [REST API-referens för Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Ett exempel på Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Slutför [Användning av Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Installera artefakterna

Ladda ner [mallarna och artefakterna](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) och packa upp den lokalt om du inte har gjort det. Och kör sedan PowerShell-skriptet som hittades vid [Förbered artefakterna](./deployment-manager-tutorial.md#prepare-the-artifacts). Skriptet skapar en resursgrupp, skapar en lagringsbehållare, skapar en blob-behållare, överför de nedladdade filerna och skapar sedan en SAS-token.

Gör en kopia av webbadressen med SAS-token. Den här URL:en krävs för att fylla i ett fält i de två parameterfilerna, filen med topologiparametrar och filen med distributionsparametrar.

Öppna CreateADMServiceTopology.Parameters.json och uppdatera värdena för **projectName** och **artifactSourceSASLocation**.

Öppna CreateADMRollout.Parameters.json och uppdatera värdena för **projectName** och **artifactSourceSASLocation**.

## <a name="create-a-health-check-service-simulator"></a>Skapa en hälsokontrollservicesimulator

I produktion använder du vanligtvis en eller flera övervakningsleverantörer. För att göra hälsointegrering så enkelt som möjligt har Microsoft arbetat med några av de bästa servicehälsoövervakningsföretagen för att ge dig en enkel lösning för att kopiera/klistra in för att integrera hälsokontroller med dina distributioner. En lista över dessa företag finns i Leverantörer av [hälsoövervakning](./deployment-manager-health-check.md#health-monitoring-providers). I den här självstudien skapar du en [Azure-funktion](/azure/azure-functions/) för att simulera en hälsoövervakningstjänst. Den här funktionen tar en statuskod och returnerar samma kod. Din Azure Deployment Manager-mall använder statuskoden för att avgöra hur du ska gå vidare med distributionen.

Följande två filer används för att distribuera Azure-funktionen. Du behöver inte ladda ner dessa filer för att gå igenom handledningen.

* En Resource Manager-mall som finns på [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json). Du distribuerar den här mallen för att skapa en Azure-funktion.
* En zip-fil med Azure [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip)Function-källkoden. Den här zip-dragkedjan anropas av mallen Resource Manager.

Om du vill distribuera Azure-funktionen väljer du **Prova det** för att öppna Azure Cloud-skalet och klistra sedan in följande skript i skalfönstret.  Om du vill klistra in koden högerklickar du på skalfönstret och väljer sedan **Klistra in**.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Så här verifierar och testar du Azure-funktionen:

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Öppna resursgruppen.  Standardnamnet är projektnamnet med **rg** bifogat.
1. Välj apptjänsten från resursgruppen.  Standardnamnet på apptjänsten är projektnamnet med **bifogad webbapp.**
1. Expandera **funktioner**och välj sedan **HttpTrigger1**.

    ![Hälsokontroll av Azure Deployment Manager-funktionen Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Välj ** &lt;/> Hämta funktions-URL**.
1. Välj **Kopiera** om du vill kopiera URL:en till Urklipp.  Webbadressen liknar:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Ersätt `{healthStatus}` i URL:en med en statuskod. I den här självstudien använder du **felfritt** för att testa det felaktiga scenariot och använd antingen **felfri** eller **varning** för att testa det felfria scenariot. Skapa två webbadresser, en med felstatus och den andra med felfri status. För exempel:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Du behöver båda webbadresserna för att slutföra den här självstudien.

1. Om du vill testa hälsoövervakningssimulatorn öppnar du webbadresserna som du skapade i det sista steget.  Resultaten för den felaktiga statusen skall likna

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Ändra distributionsmallen

Syftet med det här avsnittet är att visa hur du inkluderar ett hälsokontrollsteg i distributionsmallen.

1. Öppna **CreateADMRollout.json** som du skapade i [Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md). Denna JSON fil är en del av nedladdningen.  Se [Förutsättningar](#prerequisites).
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

1. Ersätt resursdefinitionen för väntesteg med en resursdefinition för hälsokontrollsteg:

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

    Baserat på definitionen fortsätter distributionen om hälsostatusen *är* felfri eller *varning*.

1. Uppdatera **berorON** för distributionsdefinitionen så att det omfattar det nyligen definierade hälsokontrollsteget:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Uppdatera **stegGrupper** för att inkludera hälsokontrollsteget. **HealthCheckStep** anropas i **postDeploymentSteps** of **stepGroup2**. **stepGroup3** och **stepGroup4** distribueras endast om felstatusen är *felfri* eller *varning*.

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

    Om du jämför **avsnittet stepGroup3** före och efter att det har reviderats beror det här avsnittet nu på **stepGroup2**.  Detta är nödvändigt när **stegGrupp3** och efterföljande steggrupper är beroende av resultaten av hälsoövervakning.

    Följande skärmbild illustrerar de områden som har ändrats och hur hälsokontrollsteget används:

    ![Hälsokontrollmall för Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Distribuera topologin

Kör följande PowerShell-skript för att distribuera topologin. Du behöver samma **CreateADMServiceTopology.json** och **CreateADMServiceTopology.Parameters.json** som du använde i [Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Kontrollera att tjänsttopologin och de angivna resurserna har skapats på Azure-portalen:

![Azure Deployment Manager-självstudie om resurser för en distribuerad tjänsttopologi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

**Visa dolda typer** måste väljas för att resurserna ska visas.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Distribuera distributionen med felstatus

Använd url:en för felstatus som du skapade i [Create a health check service simulator](#create-a-health-check-service-simulator). Du behöver den reviderade **CreateADMServiceTopology.json** och samma **CreateADMServiceTopology.Parameters.json** som du använde i [Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment`är ett asynkront samtal. Framgångsmeddelandet betyder bara att distributionen har börjat. Om du vill `Get-AZDeploymentManagerRollout`verifiera distributionen använder du .  Se nästa procedur.

Så här kontrollerar du utrullningsstatusen med följande PowerShell-skript:

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

Följande exempelutdata visar att distributionen misslyckades på grund av felstatus:

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

När distributionen har slutförts ska du se ytterligare en resursgrupp som skapats för västra USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Distribuera distributionen med felfritt läge

Upprepa det här avsnittet om du vill distribuera om distributionen med url:en för felstatus.  När distributionen har slutförts visas ytterligare en resursgrupp som skapats för östra USA.

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Bläddra till de nyligen skapade webbprogrammen under de nya resursgrupperna som skapades i samband med distributionen.
3. Öppna webbprogrammet i en webbläsare. Kontrollera platsen och versionen för index.html-filen.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Använd fältet **Filtrera efter namn** för att visa resursgrupperna som skapats i den här självstudien. De bör vara 3–4:

    * projectName>rg : innehåller distributionshanterarens resurser. ** &lt;**
    * projectName>ServiceWUSrg : innehåller de resurser som definieras av ServiceWUS. ** &lt;**
    * projectName>ServiceEUSrg : innehåller de resurser som definieras av ServiceEUS. ** &lt;**
    * Resursgruppen för den användardefinierade hanterade identiteten.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.
5. Upprepa de två sista stegen för att ta bort andra resursgrupper som skapats av den här självstudien.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder hälsokontrollfunktionen i Azure Deployment Manager. Mer information finns i [dokumentationen om Azure Resource Manager](/azure/azure-resource-manager/).
