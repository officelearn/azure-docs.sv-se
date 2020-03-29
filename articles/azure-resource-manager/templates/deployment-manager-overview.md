---
title: Säker distribution mellan regioner – Azure Deployment Manager
description: Beskriver hur du distribuerar en tjänst över många regioner med Azure Deployment Manager. Den visar säkra distributionsmetoder för att verifiera stabiliteten i distributionen innan du distribuerar till alla regioner.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 424cd79a6c63200e1f101cf178b1fd2c9083161e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152535"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Aktivera metoder för säker distribution med Azure Deployment Manager (offentlig förhandsversion)

Om du vill distribuera din tjänst i många regioner och se till att den körs som förväntat i varje region kan du använda Azure Deployment Manager för att samordna en stegvis distribution av tjänsten. Precis som för alla Azure-distributioner definierar du resurserna för din tjänst i [Resource Manager-mallar](template-syntax.md). När du har skapat mallarna använder du Distributionshanteraren för att beskriva topologin för tjänsten och hur den ska distribueras.

Deployment Manager är en funktion i Resource Manager. Den utökar dina funktioner under distributionen. Använd Distributionshanteraren när du har en komplex tjänst som måste distribueras till flera regioner. Genom att mellanlagra distribution av tjänsten kan du upptäcka potentiella problem innan den har distribuerats till alla regioner. Om du inte behöver de extra försiktighetsåtgärderna i en stegvis distribution använder du [standarddistributionsalternativen](deploy-portal.md) för Resource Manager. Deployment Manager integreras sömlöst med alla befintliga verktyg från tredje part som stöder Resource Manager-distributioner, till exempel ci/cd-erbjudanden (continuous integration och kontinuerlig leverans).

Azure Deployment Manager är i förhandsversion. Hjälp oss att förbättra funktionen genom att ge [feedback](https://aka.ms/admfeedback).

Om du vill använda Distributionshanteraren måste du skapa fyra filer:

* Mall för topologi
* Mall för distribution
* Parameterfil för topologi
* Parameterfil för distribution

Du distribuerar topologimallen innan du distribuerar distributionsmallen.

Ytterligare resurser:

- [REST API-referens för Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Självstudiekurs: Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).
- [Självstudiekurs: Använd hälsokontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Ett exempel på Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identitet och åtkomst

Med Deployment Manager utför en [användartilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) distributionsåtgärderna. Du skapar den här identiteten innan du startar distributionen. Den måste ha åtkomst till prenumerationen som du distribuerar tjänsten till och tillräcklig behörighet för att slutföra distributionen. Information om de åtgärder som beviljas via roller finns [i Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

Identiteten måste finnas på samma plats som distributionen.

## <a name="topology-template"></a>Mall för topologi

Topologimallen beskriver de Azure-resurser som utgör din tjänst och var du kan distribuera dem. Följande bild visar topologin för en exempeltjänst:

![Hierarki från tjänsttopologi till tjänster till serviceenheter](./media/deployment-manager-overview/service-topology.png)

Topologimallen innehåller följande resurser:

* Artefaktkälla – där resource manager-mallar och parametrar lagras
* Servicetopologi - pekar på artefaktkälla
  * Tjänster - anger plats och Azure-prenumerations-ID
    * Serviceenheter - anger resursgrupp, distributionsläge och sökväg till mall- och parameterfil

För att förstå vad som händer på varje nivå är det bra att se vilka värden du anger.

![Värden för varje nivå](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Artefaktkälla för mallar

I topologimallen skapar du en artefaktkälla som innehåller mallarna och parameterfilerna. Artefaktkällan är ett sätt att hämta filerna för distribution. Du ser en annan artefaktkälla för binärfiler senare i den här artikeln.

I följande exempel visas artefaktkällans allmänna format.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
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

Mer information finns i [artefaktKällamallreferens](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Tjänsttopologi

I följande exempel visas det allmänna formatet för tjänsttopologiresursen. Du anger resurs-ID för artefaktkällan som innehåller mallarna och parameterfilerna. Tjänsttopologin innehåller alla serviceresurser. För att se till att artefaktkällan är tillgänglig beror tjänsttopologin på den.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Mer information finns i [serviceTopologies mallreferens](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Tjänster

I följande exempel visas det allmänna formatet för tjänsteresursen. I varje tjänst anger du den plats och Azure-prenumerations-ID som ska användas för att distribuera din tjänst. Om du vill distribuera till flera regioner definierar du en tjänst för varje region. Tjänsten är beroende av tjänsttopologin.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
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

Mer information finns i [referens för tjänstmall .](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services)

### <a name="service-units"></a>Tjänstenheter

I följande exempel visas det allmänna formatet för serviceenheternas resurs. I varje tjänstenhet anger du resursgruppen, [distributionsläget](deployment-modes.md) som ska användas för distribution och sökvägen till mall- och parameterfilen. Om du anger en relativ sökväg för mallen och parametrarna skapas den fullständiga sökvägen från rotmappen i artefaktkällan. Du kan ange en absolut sökväg för mallen och parametrarna, men du förlorar möjligheten att enkelt version dina versioner. Serviceenheten är beroende av tjänsten.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
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

Varje mall ska innehålla de relaterade resurser som du vill distribuera i ett steg. En serviceenhet kan till exempel ha en mall som distribuerar alla resurser för tjänstens klientdel.

Mer information finns i [serviceUnits mallreferens](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Mall för distribution

Distributionsmallen beskriver de steg du bör vidta när du distribuerar tjänsten. Du anger den tjänsttopologi som ska användas och definierar ordern för distribution av serviceenheter. Den innehåller en artefaktkälla för lagring av binärfiler för distributionen. I distributionsmallen definierar du följande hierarki:

* Artefaktkälla
* Steg
* Utbyggnaden
  * Steggrupper
    * Driftsättningsåtgärder

Följande bild visar distributionsmallens hierarki:

![Hierarki från distribution till steg](./media/deployment-manager-overview/Rollout.png)

Varje distribution kan ha många steggrupper. Varje steggrupp har en distributionsåtgärd som pekar på en serviceenhet i tjänsttopologin.

### <a name="artifact-source-for-binaries"></a>Artefaktkälla för binärfiler

I distributionsmallen skapar du en artefaktkälla för de binärfiler som du behöver distribuera till tjänsten. Den här artefaktkällan liknar [artefaktkällan för mallar,](#artifact-source-for-templates)förutom att den innehåller skript, webbsidor, kompilerad kod eller andra filer som behövs av tjänsten.

### <a name="steps"></a>Steg

Du kan definiera ett steg som ska utföras antingen före eller efter distributionen. För närvarande `wait` är endast steget och hälsokontrollerna tillgängliga.

Väntesteget pausar distributionen innan du fortsätter. Det gör att du kan kontrollera att din tjänst körs som förväntat innan du distribuerar nästa serviceenhet. I följande exempel visas det allmänna formatet för ett väntesteg.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
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

Egenskapen duration använder [ISO 8601-standard](https://en.wikipedia.org/wiki/ISO_8601#Durations). I föregående exempel anges en väntande minut.

Mer information om hälsokontrollsteget finns i [Införa distributionsdistribution av hälsointegrering i Azure Deployment Manager](./deployment-manager-health-check.md) och [självstudiekurs: Använd hälsokontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Mer information finns i [stegmallreferens](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Distributioner

För att se till att artefaktkällan är tillgänglig beror distributionen på den. Distributionen definierar steggrupper för varje tjänstenhet som distribueras. Du kan definiera åtgärder som ska vidtas före eller efter distributionen. Du kan till exempel ange att distributionen väntar efter att serviceenheten har distribuerats. Du kan definiera ordningen på steggrupperna.

Identitetsobjektet anger den [användartilldelade hanterade identitet](#identity-and-access) som utför distributionsåtgärderna.

I följande exempel visas det allmänna formatet för distributionen.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
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

Mer information finns i [mallreferens för distributioner](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Parameterfil

Du skapar två parameterfiler. En parameterfil används vid distribution av tjänsttopologin och den andra används för distributionen av distributionen. Det finns vissa värden som du måste se till att de är desamma i båda parameterfilerna.

## <a name="containerroot-variable"></a>behållareRot variabel

Med versionsdistributioner ändras sökvägen till dina artefakter med varje ny version. Första gången du kör en `https://<base-uri-blob-container>/binaries/1.0.0.0`distribution kan sökvägen vara . Andra gången kan `https://<base-uri-blob-container>/binaries/1.0.0.1`det vara . Deployment Manager förenklar att du får rätt rotsökväg för den aktuella distributionen med hjälp av variabeln. `$containerRoot` Det här värdet ändras med varje version och är inte känt före distributionen.

Använd `$containerRoot` variabeln i parameterfilen för mall för att distribuera Azure-resurserna. Vid distributionen ersätts den här variabeln med de faktiska värdena från distributionen.

Till exempel under distributionen skapar du en artefaktkälla för de binära artefakterna.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
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

Lägg `artifactRoot` märke `sasUri` till egenskaperna och egenskaperna. Artefaktroten kan vara `binaries/1.0.0.0`inställd på ett värde som . SAS URI är URI till din lagringsbehållare med en SAS-token för åtkomst. Deployment Manager konstruerar automatiskt `$containerRoot` variabelns värde. Den kombinerar dessa värden `<container>/<artifactRoot>`i formatet .

Mall- och parameterfilen måste känna till rätt sökväg för att hämta de versionsbehedrade binärfilerna. Om du till exempel vill distribuera filer för en webbapp skapar du följande parameterfil med variabeln $containerRoot. Du måste använda två omvänt`\\`snedstreck ( ) för banan eftersom det första är ett escape-tecken.

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

Använd sedan den parametern i mallen:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
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

Du hanterar versionsdistributioner genom att skapa nya mappar och skicka in roten under distributionen. Sökvägen flödar fram till mallen som distribuerar resurserna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig mer om Deployment Manager. Gå vidare till nästa artikel och lär dig hur du distribuerar med Deployment Manager.

> [!div class="nextstepaction"]
> [Självstudiekurs: Använda Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md)
>
> [Snabbstart: Prova Azure Deployment Manager på bara några minuter](https://github.com/Azure-Samples/adm-quickstart)
