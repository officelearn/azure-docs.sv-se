---
title: Kontinuerlig integrering och leverans i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder kontinuerlig integrering och leverans för att flytta Data Factory-pipeliner från en miljö (utveckling, testning, produktion) till en annan.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: douglasl
ms.openlocfilehash: bfab3c94892b94eaf1c0585ee47a6dcbdb161776
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452733"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Kontinuerlig integrering och leverans (CI/CD) i Azure Data Factory

Kontinuerlig integrering är metoden att testa varje ändring som klar för att din kodbas automatiskt och så tidigt som möjligt. Kontinuerlig leverans följer testning som händer under kontinuerlig integrering och skickar ändringarna till ett system för mellanlagring eller produktion.

Innebär att flytta Data Factory-pipeliner från en miljö (utveckling, testning, produktion) till en annan för Azure Data Factory, kontinuerlig integrering och leverans. Du kan använda Användargränssnittet för Data Factory integration med Azure Resource Manager-mallar för att göra kontinuerlig integrering och leverans. Användargränssnittet för Data Factory kan generera en Resource Manager-mall när du väljer den **ARM-mallen** alternativ. När du väljer **exportera ARM-mallen**, portalen genererar Resource Manager-mall för data factory och en konfigurationsfil som innehåller alla anslutningar strängar och andra parametrar. Du måste sedan skapa en konfigurationsfil för varje miljö (utveckling, testning, produktion). Huvudfilen för Resource Manager-mall förblir densamma för alla miljöer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Titta på följande videoklipp för en nio minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Skapa en Resource Manager-mall för varje miljö
Välj **exportera ARM-mallen** att exportera Resource Manager-mallen för din datafabrik i utvecklingsmiljön.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Gå till din datafabrik för testning och produktion data factory och välj sedan **Import ARM-mallen**.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Den här åtgärden tar dig till Azure-portalen där du kan importera den exporterade mallen. Välj **skapa din egen mall i redigeringsprogrammet** och sedan **Läs in fil** och välj den genererade Resource Manager-mallen. Ange inställningarna och data factory och hela pipelinen har importerats i din produktionsmiljö.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Välj **Läs in fil** och markerar den exporterade Resource Manager-mallen som du kan få alla konfigurationsvärden (t.ex, länkade tjänster).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Anslutningssträngar**. Du hittar den information som krävs för att skapa anslutningssträngar i artiklarna om enskilda anslutningsappar. Till exempel Azure SQL Database finns i [kopiera data till och från Azure SQL Database med hjälp av Azure Data Factory](connector-azure-sql-database.md). För att kontrollera rätt anslutningssträng – för en länkad tjänst, till exempel – kan du också öppna kodvyn för resursen i Användargränssnittet för Data Factory. I kodvyn, men tas lösenord eller konto nyckeldel av anslutningssträngen bort. Välj markerad i följande skärmbild för att öppna kodvyn.

![Öppna kodvyn för att se anslutningssträngen](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Livscykel för kontinuerlig integrering
Här är hela livscykeln för kontinuerlig integrering och leverans som du kan använda när du har aktiverat Azure lagringsplatser Git-integrering i Användargränssnittet för Data Factory:

1.  Konfigurera en data factory för utveckling med Azure-lagringsplatser där alla utvecklare kan skapa Data Factory-resurser som pipelines, datauppsättningar och så vidare.

1.  Utvecklare kan sedan ändra resurser, till exempel pipelines. När de gör ändringarna kan de välja **felsöka** att se hur pipelinekörningarna med de senaste ändringarna.

1.  När utvecklare är nöjd med ändringarna, kan de skapa en pull-begäran från deras gren till mastergrenen (eller grenen samarbete) för att hämta ändringarna granskas av peer-datorer.

1.  När det finns ändringar i huvudgrenen, de publicerar till utveckling factory genom att välja **publicera**.

1.  När teamet är redo att göra ändringar i fabriken för testning och produktion fabriken, kan de exportera Resource Manager-mallen från huvudgrenen eller från andra grenen om deras huvudgrenen säkerhetskopierar live utvecklingen Data Factory.

1.  Den exporterade Resource Manager-mallen kan distribueras med olika parameterfiler fabriken för testning och produktion fabriken.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatisera kontinuerlig integrering med Azure Pipelines versioner

Här följer stegen för att konfigurera en Azure-Pipelines version så kan du automatisera distributionen av en data factory till flera miljöer.

![Diagram över kontinuerlig integrering med Azure-Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Krav

-   En Azure-prenumeration som är länkad till Team Foundation Server eller Azure-databaser med hjälp av den [*Azure Resource Manager-tjänstslutpunkt*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   En Datafabrik med Azure-lagringsplatser Git-integrering som konfigurerats.

-   En [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som innehåller hemligheterna.

### <a name="set-up-an-azure-pipelines-release"></a>Konfigurera en Azure-Pipelines-version

1.  Gå till din Azure-lagringsplatser sida i samma projekt som konfigurerats med Data Factory.

1.  Klicka på den översta menyn **Azure Pipelines** &gt; **versioner** &gt; **skapa versionsdefinition**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Välj den **tom process** mall.

1.  Ange namnet på din miljö.

1.  Lägg till en Git-artefakt och välj på samma lagringsplats som konfigurerats med Data Factory. Välj `adf_publish` som standardgren med senaste standardversionen.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Lägg till en uppgift för Azure Resource Manager-distribution:

    a.  Skapa en ny uppgift, Sök efter **Azure Resursgruppsdistribution**, och lägger till den.

    b.  Välj den prenumeration, resursgrupp och plats för mål Data Factory i aktiviteten distribution och ange autentiseringsuppgifter om det behövs.

    c.  Välj den **skapa eller uppdatera resursgruppen** åtgärd.

    d.  Välj **...** i den **mall** fält. Bläddra efter Resource Manager-mall (*ARMTemplateForFactory.json*) som har skapats av publiceringsåtgärden i portalen. Leta efter den här filen i mappen `<FactoryName>` av den `adf_publish` gren.

    e.  Gör samma sak för parameterfilen. Välj rätt fil beroende på om du har skapat en kopia eller du använder standardfilen *ARMTemplateParametersForFactory.json*.

    f.  Välj **...** bredvid den **åsidosätta mallparametrar** fältet och Fyll i informationen för målet Data Factory. För de autentiseringsuppgifter som kommer från key vault använder samma namn för hemligheten i följande format: förutsatt att den hemliga namn är `cred1`, ange `"$(cred1)"` (mellan citattecken).

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Välj den **inkrementella** Distributionsläget.

    > [!WARNING]
    > Om du väljer **Slutför** distributionsläget befintliga resurser kan tas bort, inklusive alla resurser i målresursgruppen som inte har definierats i Resource Manager-mallen.

1.  Spara versionspipelinen.

1.  Skapa en ny version från den här releasepipeline.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Valfritt – som får hemligheterna från Azure Key Vault

Om du har hemligheter för att skicka in en Azure Resource Manager-mall, bör du använda Azure Key Vault med Azure-Pipelines.

Det finns två sätt att hantera hemligheterna:

1.  Lägg till hemligheterna i parameterfilen. Mer information finns i [använda Azure Key Vault för att skicka säkra parametervärdet under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Skapa en kopia av filen parametrar som har överförts till grenen publicera och ange värden för parametrar som du vill hämta från key vault med följande format:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   När du använder den här metoden hämtas hemligheten från nyckelvalvet automatiskt.

    -   Parameterfilen måste vara i grenen publicera.

1.  Lägg till en [Azure Key Vault uppgift](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) innan Azure Resource Manager-distribution som beskrivs i föregående avsnitt:

    -   Välj den **uppgifter** fliken, skapa en ny uppgift, Sök efter **Azure Key Vault** och lägga till den.

    -   I Key Vault-uppgiften väljer du den prenumeration där du skapade nyckelvalvet, ange autentiseringsuppgifter om det behövs och välj sedan nyckelvalvet.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Bevilja behörigheter till Pipelines med Azure-agenten
Azure Key Vault-aktiviteten misslyckas fIntegration Runtime tid med ett felmeddelande om nekad. Hämta loggar för versionen och leta upp den `.ps1` filen med kommandot för att ge behörighet till Pipelines med Azure-agenten. Du kan köra kommandot direkt, eller du kan kopiera huvudkonto-ID från filen och lägga till åtkomstprincipen manuellt i Azure-portalen. (*Hämta* och *lista* är den lägsta behörigheten som krävs).

### <a name="update-active-triggers"></a>Uppdatera active utlösare
Distributionen kan misslyckas om du försöker uppdatera active utlösare. För att uppdatera active utlösare, måste du manuellt stoppa dem och starta dem efter distributionen. Du kan lägga till en Azure Powershell-uppgift för detta ändamål som visas i följande exempel:

1.  I fliken uppgifter i versionen, söker du efter **Azure Powershell** och lägga till den.

1.  Välj **Azure Resource Manager** som anslutningen skriver och välj din prenumeration.

1.  Välj **infogat skript** som skriptet som skriver och ange sedan din kod. Följande exempel stoppar utlösare:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Du kan följa liknande steg och använda liknande kod (med den `Start-AzDataFactoryV2Trigger` funktionen) starta om utlösarna efter distributionen.

> [!IMPORTANT]
> Integration Runtime-typen i olika miljöer som i kontinuerlig integrering och distributionsscenarier, måste vara samma. Exempel: Om du har en *Egenvärdbaserade* Integration Runtime (IR) i utvecklingsmiljön, samma IR måste vara av typen *Egenvärdbaserade* i andra miljöer, till exempel testning och produktion också. På samma sätt, om du delar integreringskörningar i flera steg kan du behöva konfigurera Integreringskörningar som *länkade Egenvärdbaserade* i alla miljöer som utveckling, testning och produktion.

## <a name="sample-deployment-template"></a>Exempelmall för distribution

Här är en exempelmall för distribution som du kan importera i Azure-Pipelines.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Exempel på skript att stoppa och starta om utlösare och rensa

Här är ett exempelskript som du vill sluta utlösare före distributionen och starta om utlösare efteråt. Skriptet innehåller också kod för att ta bort resurser som har tagits bort. Om du vill installera den senaste versionen av Azure PowerShell, se [installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Använda anpassade parametrar med Resource Manager-mall

Om du använder GIT-läge kan åsidosätta du standardegenskaperna i Resource Manager-mall för att ange egenskaper som parametriseras i mallen och egenskaper som är hårdkodad. Du kanske vill åsidosätta standardmallen för parameterisering i dessa scenarier:

* Du använder automatisk CI/CD och du vill ändra vissa egenskaper under distribution av resurshanteraren, men egenskaperna innehåller parametrar inte som standard.
* Din datafabrik är så stora som standard Resource Manager-mallen är ogiltig eftersom den har fler än det högsta tillåtna parametrar (256).

Skapa en fil med namnet under dessa villkor om du vill åsidosätta parameterisering standardmallen *arm-mall-parametrarna-definition.json* i rotmappen på lagringsplatsen. Filnamnet måste vara en exakt matchning. Data Factory försöker läsa den här filen från den gren som du har för närvarande i Azure Data Factory-portalen, inte bara från grenen samarbete. Du kan skapa eller redigera filen från en privat gren där du kan testa dina ändringar med hjälp av den **exportera ARM-mallen** i Användargränssnittet. Sedan kan du koppla filen till grenen samarbete. Om ingen fil hittas, används standardmallen.


### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxen för en fil med anpassade parametrar

Här följer några riktlinjer ska användas när du skapar anpassade parameterfilen. Filen innehåller ett avsnitt för varje enhetstyp: utlösaren, pipeline, linkedservice, dataset, integrationruntime och så vidare.
* Ange egenskapssökvägen till under den relevanta entitetstypen.
* När du anger ett egenskapsnamn till '\*'', du ange att du vill Parameterisera alla egenskaper under den (endast till den första nivån inte rekursivt). Du kan också ange undantag till detta.
* När du anger värdet för en egenskap som en sträng kan ange du att du vill Parameterisera egenskapen. Använd formatet `<action>:<name>:<stype>`.
   *  `<action>` kan vara något av följande tecken:
      * `=` innebär att behålla det aktuella värdet som standardvärde för parametern.
      * `-` innebär har inte standardvärdet för parametern.
      * `|` är ett specialfall för hemligheter från Azure Key Vault för anslutningssträngar eller nycklar.
   * `<name>` är namnet på parametern. Om den är tom, som det tar att namnet på egenskapen. Om värdet börjar med en `-` tecknet namnet har kortats ned. Till exempel `AzureStorage1_properties_typeProperties_connectionString` skulle kortats ned till `AzureStorage1_connectionString`.
   * `<stype>` är typ av parameter. Om `<stype>` är tom, standardtypen är `string`. Värden som stöds: `string`, `bool`, `number`, `object`, och `securestring`.
* När du anger en matris i definitionsfilen, anger du att egenskapen matchande i mallen är en matris. Data Factory går igenom alla objekt i matrisen med hjälp av den definition som har angetts i Integration Runtime-objektet i matrisen. Det andra objektet, en sträng, blir namnet på egenskapen, som används som namn på parametern för varje iteration.
* Det går inte att ha en definition som är specifik för en resursinstans. Alla definitionen gäller för alla resurser av den typen.
* Som standard innehåller alla säkra strängar, till exempel hemligheter i Key Vault och säker strängar, till exempel anslutningssträngar, nycklar och token, parametrar.
 
## <a name="sample-parameterization-template"></a>Parameterisering exempelmall

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

### <a name="explanation"></a>Förklaring:

#### <a name="pipelines"></a>Pipelines
    
* Alla egenskaper i sökvägen aktiviteter/typeProperties/waitTimeInSeconds parametriserade. Detta innebär att alla aktiviteter i en pipeline med en kod på servernivå egenskap med namnet `waitTimeInSeconds` (till exempel den `Wait` aktivitet) är som innehåller parametrar som ett tal med ett standardnamn. Men det kommer inte ha ett standardvärde i Resource Manager-mallen. Det är en obligatorisk indata under Resource Manager-distribution.
* På samma sätt kan en egenskap som kallas `headers` (till exempel i en `Web` aktivitet) parameteriserat med typen `object` (JObject). Den har ett standardvärde, vilket är samma värde som källa fabriken.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Endast egenskaper och alla egenskaper under sökvägen `typeProperties` parametriseras med sina respektive standardvärden. Till exempel från och med dagens schema, det finns två egenskaper under **IntegrationRuntimes** strängtypsegenskaper: `computeProperties` och `ssisProperties`. Båda egenskapstyperna av skapas med sina respektive standardvärden och typer (objekt).

#### <a name="triggers"></a>Utlösare

* Under `typeProperties`, två egenskaper parametriseras. Den första är `maxConcurrency`som anges för att ha ett standardvärde och typen skulle vara `string`. Den har parametern standardnamn `<entityName>_properties_typeProperties_maxConcurrency`.
* Den `recurrence` egenskapen också parameteriserat. Under det har alla egenskaper på den nivån angetts till parametriseras som strängar med standardvärden och parameternamn. Ett undantag är den `interval` egenskapen, som innehåller parametrar som nummertypen och med parameternamnet suffix med `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. På samma sätt kan den `freq` egenskapen är en sträng och är som innehåller parametrar som en sträng. Men den `freq` egenskapen parameteriserat utan ett standardvärde. Namnet har kortats ned och suffix. Till exempel `<entityName>_freq`.

#### <a name="linkedservices"></a>linkedServices

* Länkade tjänster är unikt. Eftersom länkade tjänster och datauppsättningar kan vara av flera typer, kan du ange typspecifika anpassning. Du kan till exempel säga att för alla länkade tjänster av typen `AzureDataLakeStore`, en särskild mall kommer att tillämpas, och för alla andra (via \*) en annan mall kommer att tillämpas.
* I föregående exempel, den `connectionString` egenskapen kommer parametriseras som en `securestring` värde, den inte har något standardvärde och den har en förkortade parameternamnet som suffix med `connectionString`.
* Egenskapen `secretAccessKey`, men råkar vara en `AzureKeyVaultSecret` (till exempel en `AmazonS3` länkad tjänst). Därför är automatiskt innehåller parametrar som en Azure Key Vault-hemlighet och hämtas från nyckelvalvet som den är konfigurerad med i käll-factory. Du kan också Parameterisera nyckelvalvet, sig själv.

#### <a name="datasets"></a>Datauppsättningar

* Även om typspecifika anpassning är tillgänglig för datauppsättningar konfiguration kan tillhandahållas utan uttryckligen en \*-konfiguration på nivå. I föregående exempel, alla egenskaper för datamängd under `typeProperties` parametriseras.

Parameterisering standardmallen kan ändra, men det här är den aktuella mallen. Det här är användbart om du behöver bara lägga till ytterligare en egenskap som en parameter, utan även om du inte vill förlora befintliga parameterizations och måste du återskapa dem.


```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Länkade Resource Manager-mallar

Om du har konfigurerat kontinuerlig integrering och distribution (CI/CD) för din Datafabriker kan du se när din datafabrik växer större kan köras i Resource Manager-mall gränser, t.ex. det maximala antalet resurser eller den maximala nyttolasten i en resurs Manager-mall. För scenarier som dessa tillsammans med genererar den fullständiga Resource Manager-mallen för en fabrik genererar Data Factory nu även länkade Resource Manager-mallar. Därför kan ha du hela factory nyttolasten delas upp i flera filer så att du inte stöter på gränserna som anges.

Om du har konfigurerat Git länkade mallar genereras och sparas tillsammans med de fullständiga Resource Manager-mallarna i den `adf_publish` gren, under en ny mapp med namnet `linkedTemplates`.

![Länkade mappen för Resource Manager-mallar](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Länkade Resource Manager-mallar har vanligtvis en Huvudmall och en uppsättning underordnade mallar som är länkad till huvudservern. Den överordnade mallen kallas `ArmTemplate_master.json`, och underordnade mallar kallas med mönstret `ArmTemplate_0.json`, `ArmTemplate_1.json`och så vidare. Om du vill flytta över från att använda den fullständiga Resource Manager-mallen till att använda länkade mallar, uppdatera dina CI/CD-aktivitet så att den pekar till `ArmTemplate_master.json` i stället för att peka på `ArmTemplateForFactory.json` (det vill säga fullständiga Resource Manager-mallen). Resource Manager måste du överföra de länkade mallarna till ett lagringskonto så att de kan nås av Azure under distributionen. Mer information finns i [distribuera länkade ARM-mallar med VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Kom ihåg att lägga till Data Factory-skript i CI/CD-pipeline före och efter distributionen uppgiften.

Om du inte har konfigurerat Git länkade mallar är tillgängliga via den **exportera ARM-mallen** gest.

## <a name="best-practices-for-cicd"></a>Metodtips för CI/CD

Om du använder Git-integrering med din data factory och du har en CI/CD-pipeline som flyttar dina ändringar från utveckling till testmiljön och sedan till produktion, rekommenderar vi följande metoder:

-   **Git-integrering**. Du behöver bara konfigurera din data factory för utveckling med Git-integrering. Ändringar av Test- och produktionsmiljöer distribueras via CI/CD och de behöver inte ha Git-integrering.

-   **Data Factory CI/CD skriptet**. Innan steget för Resource Manager-distribution i CI/CD måste du ta hand om saker som att stoppa utlösare och annan typ av factory rensning. Vi rekommenderar att du använder [det här skriptet](#sample-script-to-stop-and-restart-triggers-and-clean-up) som den tar hand om alla dessa saker. Kör skriptet en gång innan distributionen, och en gång efter, med korrekta flaggor.

-   **Integreringskörningar och dela**. Integration Runtime är en av infrastrukturen komponenterna i din datafabrik som förändras mindre ofta och som liknar varandra för alla steg i din CI/CD. Därför kan Data Factory förväntar sig att du har samma namn och samma typ av Integreringskörningar i alla led i CI/CD. Om du vill dela Integreringskörningar i alla led - exempelvis den lokala Integration Runtime – är ett sätt att dela som som är värd för lokal IR Ternär fabrik, bara för som innehåller delade Integreringskörningar. Du kan sedan använda dem i Dev/Test/Prod som en länkad IR-typ.

-   **Key Vault**. När du använder den rekommenderade Azure Key Vault baserat länkade tjänster, kan du dra dess fördelar en nivå ytterligare genom att potentiellt hålla separat nyckelvalv för Dev/Test/Prod. Du kan också konfigurera separata behörighetsnivåer för respektive scenario. Du kanske inte vill att gruppmedlemmarna har behörigheter till produktion hemligheter. Vi rekommenderar också att du kan hålla samma hemliga namn i alla faser. Om du behåller samma namn som du inte behöver ändra dina Resource Manager-mallar i CI/CD, eftersom det enda som behöver ändras är namn på key vault som är en av Resource Manager-mallens parametrar.

## <a name="unsupported-features"></a>Funktioner som inte stöds

-   Du kan inte publicera enskilda resurser, eftersom data factory-entiteter som är beroende av varandra. Till exempel utlösare är beroende av pipelines, pipelines beror på datauppsättningar och andra rörledningar osv. Det är svårt att spåra föränderliga beroenden. Om det var möjligt att välja resurserna du publicerar manuellt, skulle det vara möjligt att välja endast en delmängd av hela uppsättningen ändringar, vilket skulle leda till oväntade resultat för saker efter publicering.

-   Du kan inte publicera från privata grenar.

-   Du kan inte vara värd för projekt på Bitbucket.
