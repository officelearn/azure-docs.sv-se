---
title: Metoder för säker distribution i flera regioner - Distributionshanteraren för Azure
description: Beskriver hur du distribuerar en tjänst via många regioner med Azure Deployment Manager. Den visar säker distribution metoder för att kontrollera stabiliteten för distributionen innan du distribuerar till alla regioner.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: a615ab26e4ea046ced70ce2c154a0c304b741986
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550926"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-private-preview"></a>Aktivera säker distribution metoder med Azure Deployment Manager (privat förhandsversion)

För att distribuera tjänsten i många regioner och kontrollera att den körs som förväntat i varje region, kan du använda Distributionshanteraren för Azure för att samordna en stegvis distribution av tjänsten. Precis som för alla Azure-distribution, du definierar resurser för din tjänst i [Resource Manager-mallar](resource-group-authoring-templates.md). När du har skapat mallarna använda Deployment Manager för att beskriva topologin för din tjänst och hur den ska distribueras.

Deployment Manager är en funktion i Resource Manager. Det utökar din funktioner under distributionen. Använd Deployment Manager när du har en komplex tjänst som måste distribueras till flera regioner. Genom att mellanlagra distribution av tjänsten kan du upptäcka potentiella problem innan den har distribuerats till alla regioner. Om du inte behöver extra säkerhetsåtgärder för en stegvis distribution kan använda standarden [distributionsalternativ](resource-group-template-deploy-portal.md) för Resource Manager. Deployment Manager integreras sömlöst med alla befintliga verktyg från tredje part som har stöd för Resource Manager-distributioner, till exempel kontinuerlig integrering och kontinuerlig leverans (CI/CD)-erbjudanden. 

Distributionshanteraren i Azure finns i en privat förhandsgranskning. För att använda Azure Deployment Manager måste slutföra den [fyllt i registreringsformuläret](https://aka.ms/admsignup). Hjälp dig förbättra funktionen genom att tillhandahålla [feedback](https://aka.ms/admfeedback).

Om du vill använda Deployment Manager, måste du skapa fyra filer:

* Topologi mall
* Mall för distribution
* Parameterfilen för topologi
* Parameterfilen för distribution

Du kan distribuera mallen topologin innan du distribuerar mallen distributionen.

Azure Deployment Manager REST API-referensen finns [här](https://docs.microsoft.com/rest/api/deploymentmanager/).

## <a name="supported-locations"></a>Platser som stöds

För förhandsversionen stöds Deployment Manager-resurser i centrala USA och östra USA 2. När du definierar resurser i din topologi och distribuera mallar, som serviceenheter, artefaktkällor och distributioner som beskrivs i den här artikeln måste du ange en av dessa regioner för platsen. De resurser som du distribuerar för att skapa din tjänst, till exempel virtuella datorer, lagringskonton och webbappar, stöds dock i alla sina [standardplatserna](https://azure.microsoft.com/global-infrastructure/services/?products=all).  

## <a name="identity-and-access"></a>Identitet och åtkomst

Med Deployment Manager, en [användartilldelade hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) utför distributionsåtgärder för. Du kan skapa den här identiteten för innan du påbörjar distributionen. Den måste ha åtkomst till den prenumerationen som du distribuerar tjänsten och tillräcklig behörighet för att slutföra distributionen. Information om de åtgärder som beviljas genom roller finns i [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md).

Identiteten måste finnas i något av platserna som stöds för distribution Manager och den måste finnas på samma plats som distributionen.

## <a name="topology-template"></a>Topologi mall

Mallen topologi beskriver Azure-resurser som utgör din tjänst och var du vill distribuera. Följande bild visar topologin för en exempel-tjänst:

![Hierarkin från tjänsten topologi till tjänster till serviceenheter](./media/deployment-manager-overview/service-topology.png)

Topologi mallen innehåller följande resurser:

* Artefakt-källan – där dina Resource Manager-mallar och parametrar lagras
* Tjänsten topologi - pekar artefakt källa
  * Tjänster – anger platsen och Azure-prenumerations-ID
    * Tjänsten enheter - anger resursgruppen, distributionsläget och sökvägen till filen för mallen och parameterfilerna

För att förstå vad som händer på varje nivå, är det bra att se vilka värden som du anger.

![Värden för varje nivå](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Artefakten källa för mallar

I din topologi-mall skapar du en artefakt-källa som innehåller filerna mallar och parametrar. Artefakten källan är ett sätt att hämta filer för distribution. Du ser en annan artefakt källa för binärfiler senare i den här artikeln.

I följande exempel visas det allmänna formatet för artefakten-källan.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

Mer information finns i [artifactSources mallreferensen](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Tjänsttopologi

I följande exempel visas det allmänna formatet för topologi tjänstresurs. Du kan ange resurs-ID för artefakten källan som innehåller mallar och parameterfilerna. Tjänsten topologin innehåller alla service-resurser. För att säkerställa artefakt-källan är tillgänglig, service-topologin är beroende av den.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

Mer information finns i [serviceTopologies mallreferensen](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Tjänster

I följande exempel visas det allmänna formatet för resursen för tjänster. I varje tjänst anger du plats och Azure prenumerations-ID du använder för att distribuera din tjänst. Om du vill distribuera till flera regioner måste definiera du en tjänst för varje region. Tjänsten är beroende av tjänsten-topologi.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

Mer information finns i [services mallreferensen](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Tjänstenheter

I följande exempel visas det allmänna formatet för tjänstresurs för enheter. I varje tjänsteenhet ska du ange resursgruppen, den [distributionsläget](deployment-modes.md) ska användas för distribution och sökvägen till filen mall- och parameterfilerna. Om du anger en relativ sökväg för mallen och parametrarna, skapas den fullständiga sökvägen från rotmappen i käll-artefakter. Du kan ange en absolut sökväg till mall och parametrar, men du förlora möjligheten att enkelt version utgåvorna. Tjänsteenhet beror på tjänsten.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

Varje mall ska inkludera relaterade resurser som du vill distribuera i ett enda steg. En tjänsteenhet kan till exempel ha en mall som distribuerar alla resurser för klientsidan för din tjänst.

Mer information finns i [serviceUnits mallreferensen](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Mall för distribution

Distribuera mallen beskriver stegen för att ta när du distribuerar din tjänst. Anger du tjänsten topologin för att använda och definiera ordningen för att distribuera tjänsteenheter. Den innehåller en artefakt källa för lagring av binärfiler för distributionen. I mallen för distribution kan du definiera följande hierarki:

* Artefakten källa
* Steg
* Distributionen
  * Steg-grupper
    * Distributionsåtgärder

Följande bild visar hierarkin för mall för distributionen:

![Hierarkin från distributionen till steg](./media/deployment-manager-overview/Rollout.png)

Varje distribution kan ha många steg grupper. Varje steg gruppen har en Distributionsåtgärden som pekar på en tjänsteenhet för i topologin av tjänsten.

### <a name="artifact-source-for-binaries"></a>Artefakten källa för binärfiler

I mallen för distribution skapar du en artefakt källan för de binärfiler som du behöver distribuera till tjänsten. Den här artefakten källan liknar den [artefakt källa för mallar](#artifact-source-for-templates), förutom att den innehåller skript, webbsidor, kompilerad kod eller andra filer som krävs av din tjänst.

### <a name="steps"></a>Steg

Du kan definiera ett steg för att utföra före eller efter distributionen igen. För närvarande endast den `wait` steg är tillgänglig. Vänta steg pausar distributionen innan du fortsätter. Det kan du kontrollera att tjänsten körs som förväntat innan du distribuerar den nästa service-enheten. I följande exempel visas det allmänna formatet för en wait-steg.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Egenskapen varaktighet har [ISO 8601-standarden](https://en.wikipedia.org/wiki/ISO_8601#Durations). I föregående exempel anger en vänta en minut.

Mer information finns i [steg mallreferensen](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Distributioner

För att säkerställa artefakt-källan är tillgänglig, beror detta på den. Detta definierar grupper av steg för varje tjänst som har distribuerats. Du kan definiera åtgärder som ska vidtas innan eller efter distributionen. Du kan till exempel ange att distributionen ska vänta efter tjänsteenhet har distribuerats. Du kan definiera ordningen för steg-grupper.

Objektet identitet anger den [användartilldelade hanterad identitet](#identity-and-access) som utför distributionsåtgärder för.

I följande exempel visas det allmänna formatet för distributionen.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

Mer information finns i [distributioner mallreferensen](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Parameterfilen

Du skapar två parameterfiler. En parameterfil används när du distribuerar service-topologi och den andra används för distributionen av distributionen. Det finns vissa värden att du måste se till att är desamma i båda parameterfilerna.  

## <a name="containerroot-variable"></a>containerRoot variabel

Med version distributioner ändrar sökvägen till artefakterna med varje ny version. Första gången du kör en distribution sökvägen kan vara `https://<base-uri-blob-container>/binaries/1.0.0.0`. Den andra gången kan det vara `https://<base-uri-blob-container>/binaries/1.0.0.1`. Distributionshanteraren förenklar får rätt rotsökvägen för den aktuella distributionen med hjälp av den `$containerRoot` variabeln. Det här värdet ändras med varje version och är inte känt före distributionen.

Använd den `$containerRoot` variabel i parameterfilen för mall för att distribuera Azure-resurser. Den här variabeln ersätts med de faktiska värdena från distributionen vid tidpunkten för distribution. 

Du kan till exempel skapa en artefakt källa för binära artefakter under distributionen.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

Observera den `artifactRoot` och `sasUri` egenskaper. Artefakten roten kan anges till ett värde som `binaries/1.0.0.0`. SAS-URI är URI: N till ditt storage-behållare med en SAS-token för åtkomst. Distribution Manager skapar automatiskt värdet för den `$containerRoot` variabeln. Den kombinerar dessa värden i formatet `<container>/<artifactRoot>`.

Din mall och parametern-fil måste du veta rätt sökväg för att hämta version binärfiler. Skapa till exempel följande parameterfilen för att distribuera filer för en webbapp, med variabeln $containerRoot. Du måste använda två omvända snedstreck (`\\`) för sökvägen eftersom först är ett escape-tecken.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

Sedan använder du parametern i mallen:

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

Du kan hantera version distributioner genom att skapa nya mappar och skicka samma rot under distributionen. Sökvägen som flödar genom att den mall som distribuerar resurserna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Deployment Manager. Gå vidare till nästa artikel om du vill lära dig hur du distribuerar med Deployment Manager.

> [!div class="nextstepaction"]
> [Självstudie: Använda Distributionshanteraren för Azure med Resource Manager-mallar](./deployment-manager-tutorial.md)