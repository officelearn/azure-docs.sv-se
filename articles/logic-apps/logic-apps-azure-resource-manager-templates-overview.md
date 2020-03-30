---
title: Översikt – Automatisera distributionen för Azure Logic Apps
description: Lär dig mer om Azure Resource Manager-mallar för att automatisera distributionen för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 920d8bfbcef33464d528306113abe6223d752889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477756"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Översikt: Automatisera distributionen för Azure Logic Apps med hjälp av Azure Resource Manager-mallar

När du är redo att automatisera skapandet och distributionen av logikappen kan du expandera logikappens underliggande arbetsflödesdefinition till en [Azure Resource Manager-mall](../azure-resource-manager/management/overview.md). Den här mallen definierar infrastruktur, resurser, parametrar och annan information för etablering och distribution av logikappen. Genom att definiera parametrar för värden som varierar vid distributionen, även känd som *parameterisering,* kan du upprepade gånger och konsekvent distribuera logikappar baserat på olika distributionsbehov.

Om du till exempel distribuerar till miljöer för utveckling, test och produktion använder du förmodligen olika anslutningssträngar för varje miljö. Du kan deklarera mallparametrar som accepterar olika anslutningssträngar och sedan lagra dessa strängar i en separat [parameterfil](../azure-resource-manager/templates/parameter-files.md). På så sätt kan du ändra dessa värden utan att behöva uppdatera och distribuera om mallen. För scenarier där du har parametervärden som är känsliga eller måste skyddas, till exempel lösenord och hemligheter, kan du lagra dessa värden i [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) och låta dina parametrar hämta dessa värden. I dessa scenarier skulle du dock distribuera om för att hämta de aktuella värdena.

I den här översikten beskrivs attributen i en Resource Manager-mall som innehåller en logikapparbetsflödesdefinition. Både mallen och arbetsflödesdefinitionen använder JSON-syntax, men vissa skillnader finns eftersom arbetsflödesdefinitionen också följer [schemat För arbetsflödesdefinitionsspråk](../logic-apps/logic-apps-workflow-definition-language.md). Malluttryck och arbetsflödesdefinitionsuttryck skiljer sig till exempel åt i hur de [refererar till parametrar](#parameter-references) och de värden som de kan acceptera.

> [!TIP]
> Om du vill ha en ã¤ljé av en giltig parameteriserad logikappmall som oftast är klar för distribution kan du använda Visual Studio (kostnadsfri Community Edition eller senare) och Azure Logic Apps Tools for Visual Studio. Du kan sedan antingen [skapa logikappen i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) eller hitta och ladda ned en befintlig [logikapp från Azure till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Du kan också skapa logikappmallar med hjälp av [Azure PowerShell med LogicAppTemplate-modulen](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

Exempellogiken i det här avsnittet använder en [Office 365 Outlook-utlösare](/connectors/office365/) som utlöses när ett nytt e-postmeddelande anländer och en [Azure Blob Storage-åtgärd](/connectors/azureblob/) som skapar en blob för e-posttexten och överför den blob till en Azure-lagringsbehållare. Exemplen visar också hur du parameteriserar värden som varierar vid distributionen.

Mer information om Resource Manager-mallar finns i följande avsnitt:

* [Mallstruktur och syntax för Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Metodtips för Azure Resource Manager-mall](../azure-resource-manager/templates/template-best-practices.md)
* [Utveckla Azure Resource Manager-mallar för molnkonsekvens](../azure-resource-manager/templates/templates-cloud-consistency.md)

Information om exempel på logikappmallar finns i följande exempel:

* [Fullständig mall](#full-example-template) som används för det här avsnittets exempel
* [Exempel på snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) för logikapp i GitHub

Information om mallresurser som är specifika för logikappar, integrationskonton och artefakter för integrationskonto finns i [Microsoft.Logic-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="template-structure"></a>

## <a name="template-structure"></a>Mallstruktur

På den översta nivån följer en Resource Manager-mall den här strukturen, som beskrivs fullt ut i [mallstrukturen och syntaxavsnittet i Azure Resource Manager:](../azure-resource-manager/templates/template-syntax.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

För en logikappmall arbetar du främst med dessa mallobjekt:

| Attribut | Beskrivning |
|-----------|-------------|
| `parameters` | Deklarerar [mallparametrarna](../azure-resource-manager/templates/template-syntax.md#parameters) för att acceptera de värden som ska användas när resurser för distribution skapas och anpassas för distribution i Azure. Dessa parametrar accepterar till exempel värdena för logikappens namn och plats, anslutningar och andra resurser som krävs för distributionen. Du kan lagra dessa parametervärden i en [parameterfil](#template-parameter-files), som beskrivs senare i det här avsnittet. Allmän information finns i [Mallstruktur för Parametrar - Resurshanteraren och syntax](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Definierar [resurser](../azure-resource-manager/templates/template-syntax.md#resources) för att skapa eller uppdatera och distribuera till en Azure-resursgrupp, till exempel din logikapp, anslutningar, Azure-lagringskonton och så vidare. Allmän information finns i [Resurs - Resurshanterarens mallstruktur och syntax](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Logikappmallen använder det här filnamnsformatet:

**<*logik-app-namn*>.json**

> [!IMPORTANT]
> Mallsyntaxen är skiftlägeskänslig så se till att du använder konsekvent hölje. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Mallparametrar

En logikappmall `parameters` har flera objekt som finns på olika nivåer och utför olika funktioner. På den översta nivån kan du till exempel deklarera [mallparametrar](../azure-resource-manager/templates/template-syntax.md#parameters) för de värden som ska accepteras och användas vid distribution när du skapar och distribuerar resurser i Azure, till exempel:

* Din logikapp
* Anslutningar som logiken använder för att komma åt andra tjänster och system via [hanterade anslutningsappar](../connectors/apis-list.md)
* Andra resurser som logikappen behöver för distribution

  Om logikappen till exempel använder ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för B2B-scenarier (Business-to-Business) deklarerar mallens objekt på den översta nivån `parameters` den parameter som accepterar resurs-ID:t för det integrationskontot.

Här är den allmänna strukturen och syntaxen för en parameterdefinition, som beskrivs fullständigt av [parametrar - Resurshanterarens mallstruktur och syntax:](../azure-resource-manager/templates/template-syntax.md#parameters)

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

I det här exemplet visas bara mallparametrar för de värden som används för att skapa och distribuera dessa resurser i Azure:

* Namn och plats för logikappen
* ID som ska användas för ett integrationskonto som är länkat till logikappen

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Med undantag för parametrar som hanterar värden som är känsliga eller måste skyddas, till `defaultValue` exempel användarnamn, lösenord och hemligheter, innehåller alla dessa parametrar attribut, även om standardvärdena i vissa fall är tomma värden. De distributionsvärden som ska användas för dessa mallparametrar tillhandahålls av [exempelparametrarfilen](#template-parameter-files) som beskrivs senare i det här avsnittet.

Mer information om hur du skyddar mallparametrar finns i följande avsnitt:

* [Säkerhetsrekommendationer för mallparametrar](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Förbättra säkerheten för mallparametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Skicka skyddade parametervärden med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Andra mallobjekt refererar ofta till mallparametrar så att de kan använda de värden som passerar genom mallparametrar, till exempel:

* [Mallens resursobjekt,](#template-resources)som beskrivs senare i det här avsnittet, definierar varje resurs i Azure som du vill skapa och distribuera, till exempel [logikappens resursdefinition](#logic-app-resource-definition). Dessa resurser använder ofta mallparametervärden, till exempel logikappens namn och plats- och anslutningsinformation.

* På en djupare nivå i logikappens resursdefinition deklarerar [arbetsflödesdefinitionens parameterobjekt](#workflow-definition-parameters) parametrar för de värden som ska användas vid logikappens körning. Du kan till exempel deklarera parametrar för arbetsflödesdefinition för användarnamn och lösenord som en HTTP-utlösare använder för autentisering. Om du vill ange värden för `parameters` arbetsflödesdefinitionsparametrar använder du objektet som ligger *utanför* arbetsflödesdefinitionen men som fortfarande *finns i* logikappens resursdefinition. I det `parameters` här yttre objektet kan du referera till tidigare deklarerade mallparametrar, som kan acceptera värden vid distribution från en parameterfil.

När malluttryck och funktioner refererar till parametrar använder de olika syntaxen och fungerar annorlunda än uttryck och funktioner för arbetsflödesdefinition. Mer information om dessa skillnader finns i [Referenser till parametrar](#parameter-references) senare i det här avsnittet.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Metodtips - mallparametrar

Här är några metodtips för att definiera parametrar:

* Deklarera parametrar endast för värden som varierar, baserat på dina distributionsbehov. Deklarera inte parametrar för värden som förblir desamma över olika distributionskrav.

* Inkludera `defaultValue` attributet, som kan ange tomma värden, för alla parametrar utom värden som är känsliga eller måste skyddas. Använd alltid säkra parametrar för användarnamn, lösenord och hemligheter. Om du vill dölja eller skydda känsliga parametervärden följer du anvisningarna i följande avsnitt:

  * [Säkerhetsrekommendationer för mallparametrar](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Förbättra säkerheten för mallparametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Skicka skyddade parametervärden med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Om du vill skilja mallparameternamn från parameternamn för arbetsflödesdefinition kan du använda beskrivande mallparameternamn, till exempel:`TemplateFabrikamPassword`

Mer metodtips för mallar finns i [Metodtips för mallparametrar](../azure-resource-manager/templates/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Filen Mallparametrar

Om du vill ange värden för mallparametrar lagrar du dessa värden i en [parameterfil](../azure-resource-manager/templates/parameter-files.md). På så sätt kan du använda olika parameterfiler baserat på dina distributionsbehov. Här är filnamnsformatet som ska användas:

* Filnamn för logikappmall: ** < *logik-app-namn*>.json**
* Parameterfilnamn: ** < *logik-app-namn*>.parameters.json**

Här är strukturen inuti parameterfilen, som innehåller en nyckelvalvsreferens för [att skicka ett skyddat parametervärde med Azure Key Vault:](../azure-resource-manager/templates/key-vault-parameter.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Den här exempelparametren anger värdena för de mallparametrar som deklarerats tidigare i det här avsnittet:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Mallresurser

Mallen har `resources` ett objekt, som är en matris som innehåller definitioner för varje resurs som ska skapas och distribueras i Azure, till exempel [logikappens resursdefinition,](#logic-app-resource-definition)alla [definitioner av anslutningsresurser](#connection-resource-definitions)och alla andra resurser som logikappen behöver för distribution.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Mallar kan innehålla resursdefinitioner för flera logikappar, så se till att alla logikappresurser anger samma Azure-resursgrupp. När du distribuerar mallen till en Azure-resursgrupp med Visual Studio uppmanas du till vilken logikapp som du vill öppna. Dessutom kan ditt Azure-resursgruppsprojekt innehålla mer än en mall, så se till att du väljer rätt parameterfil när du uppmanas att göra det.

Allmän information om mallresurser och deras attribut finns i följande avsnitt:

* [Resurser - Resurshanterarens mallstruktur och syntax](../azure-resource-manager/templates/template-syntax.md#resources)
* [Metodtips för mallresurser](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definition av logikappresurs

Logikappens resursdefinition börjar `properties` med objektet, som innehåller den här informationen:

* Logikappens tillstånd vid distributionen
* ID:et för alla integrationskonto som används av logikappen
* Logikappens arbetsflödesdefinition
* Ett `parameters` objekt som anger de värden som ska användas vid körning
* Annan resursinformation om logikappen, till exempel namn, typ, plats och så vidare

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Här är attributen som är specifika för logikappresursdefinitionen:

| Attribut | Krävs | Typ | Beskrivning |
|-----------|----------|------|-------------|
| `state` | Ja | String | Logikappens tillstånd vid `Enabled` distributionen innebär att `Disabled` logikappen är live och innebär att logikappen är inaktiv. Om du till exempel inte är redo för logikappen att visas men vill `Disabled` distribuera en utkastversion kan du använda alternativet. |
| `integrationAccount` | Inga | Objekt | Om logikappen `id` använder ett integrationskonto som lagrar artefakter för B2B-scenarier (Business-to-Business) innehåller det här objektet attributet som anger ID:t för integrationskontot. |
| `definition` | Ja | Objekt | Logikappens underliggande arbetsflödesdefinition, som är samma objekt som visas i kodvyn och som beskrivs fullständigt i avsnittet [Schemareferens för arbetsflödesdefinitionsspråk.](../logic-apps/logic-apps-workflow-definition-language.md) I den här `parameters` arbetsflödesdefinitionen deklarerar objektet parametrar för de värden som ska användas vid logikappkörning. Mer information finns i [Arbetsflödesdefinition och parametrar](#workflow-definition-parameters). <p><p>Om du vill visa attributen i logikappens arbetsflödesdefinition växlar du från "designvy" till "kodvy" i Azure-portalen eller Visual Studio, eller med hjälp av ett verktyg som [Azure Resource Explorer](https://resources.azure.com). |
| `parameters` | Inga | Objekt | [Parametervärdena för arbetsflödesdefinition som](#workflow-definition-parameters) ska användas vid logikappkörning. Parameterdefinitionerna för dessa värden visas i [arbetsflödesdefinitionens parameterobjekt](#workflow-definition-parameters). Om logikappen använder [hanterade kopplingar](../connectors/apis-list.md) för åtkomst till andra tjänster `$connections` och system innehåller det här objektet också ett objekt som anger de anslutningsvärden som ska användas under körning. |
| `accessControl` | Inga | Objekt | För att ange säkerhetsattribut för logikappen, till exempel begränsa IP-åtkomst till begärandeutlösare eller köra historikindata och utdata. Mer information finns i [Säker åtkomst till logikappar](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

Information om mallresurser som är specifika för logikappar, integrationskonton och artefakter för integrationskonto finns i [Microsoft.Logic-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Arbetsflödesdefinition och parametrar

Logikappens arbetsflödesdefinition visas `definition` i objektet, `properties` som visas i objektet i logikappens resursdefinition. Det `definition` här objektet är samma objekt som visas i kodvyn och beskrivs fullständigt i [avsnittet Schemareferens för språk för arbetsflödesdefinition.](../logic-apps/logic-apps-workflow-definition-language.md) Arbetsflödesdefinitionen innehåller `parameters` ett inre deklarationsobjekt där du kan definiera nya eller redigera befintliga parametrar för de värden som används av arbetsflödesdefinitionen vid körning. Du kan sedan referera till dessa parametrar i utlösaren eller åtgärderna i arbetsflödet. Som standard `parameters` är det här objektet tomt om inte logikappen skapar anslutningar till andra tjänster och system via [hanterade kopplingar](../connectors/apis-list.md).

Om du vill ange värden för `parameters` arbetsflödesdefinitionsparametrar använder du objektet som ligger *utanför* arbetsflödesdefinitionen men som fortfarande *finns i* logikappens resursdefinition. I det `parameters` här yttre objektet kan du sedan referera till tidigare deklarerade mallparametrar, som kan acceptera värden vid distribution från en parameterfil.

> [!TIP]
>
> Det bästa är att inte direkt referera till mallparametrar, som utvärderas vid distributionen, inifrån arbetsflödesdefinitionen. Deklarera i stället en arbetsflödesdefinitionsparameter `parameters` som du sedan kan ange i objektet som ligger *utanför* arbetsflödesdefinitionen men som fortfarande *finns i* logikappens resursdefinition. Mer information finns i [Referenser till parametrar](#parameter-references).

Den här syntaxen visar var du kan deklarera parametrar på både mall- och arbetsflödesdefinitionsnivåerna tillsammans med var du kan ange dessa parametervärden genom att referera till mall- och arbetsflödesdefinitionsparametrarna:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": "[parameters('<template-parameter-name>')]"
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parametrar för säker arbetsflödesdefinition

För en arbetsflödesdefinitionsparameter som hanterar känslig information, lösenord, åtkomstnycklar eller hemligheter vid `securestring` `secureobject` körning deklarerar eller redigerar du parametern för att använda parametertypen eller. Du kan referera till den här parametern i hela och inom arbetsflödesdefinitionen. På mallens översta nivå deklarerar du en parameter som har samma typ för att hantera den här informationen vid distributionen.

Om du vill ange värdet för `parameters` arbetsflödesdefinitionsparametern använder du objektet som ligger *utanför* arbetsflödesdefinitionen men som fortfarande *finns i* logikappresursdefinitionen för att referera till mallparametern. Slutligen, för att skicka värdet till din mallparameter vid distributionen, lagra det värdet i [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) och referera till nyckelvalvet i [parameterfilen](#template-parameter-files) som används av mallen vid distributionen.

Den här exempelmallen visar hur du kan slutföra dessa uppgifter genom att definiera säkra parametrar när det behövs så att du kan lagra deras värden i Azure Key Vault:

* Deklarera skyddade parametrar för de värden som används för att autentisera åtkomsten.
* Använd dessa värden på både mall- och arbetsflödesdefinitionsnivåer.
* Ange dessa värden med hjälp av en parameterfil.

**Mall**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Parameterfil**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Metodtips - parametrar för arbetsflödesdefinition

Så här kontrollerar du att Logic App Designer korrekt kan visa parametrar för arbetsflödesdefinition:

* Inkludera `defaultValue` attributet, som kan ange tomma värden, för alla parametrar utom värden som är känsliga eller måste skyddas.

* Använd alltid säkra parametrar för användarnamn, lösenord och hemligheter. Om du vill dölja eller skydda känsliga parametervärden följer du anvisningarna i följande avsnitt:

  * [Säkerhetsrekommendationer för åtgärdsparametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Säkerhetsrekommendationer för parametrar i arbetsflödesdefinitioner](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Skicka säkra parametervärden med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Mer information om parametrar för arbetsflödesdefinition finns i [Parametrar - Arbetsflödesdefinitionsspråk](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definitioner för anslutningsresurser

När logikappen skapar och använder anslutningar till andra tjänster och system med `resources` hjälp av hanterade [kopplingar](../connectors/apis-list.md)innehåller mallens objekt resursdefinitionerna för dessa anslutningar.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Definitioner av anslutningsresurser refererar till mallens parametrar på den högsta nivån för deras värden, vilket innebär att du kan ange dessa värden vid distributionen med hjälp av en parameterfil. Se till att anslutningar använder samma Azure-resursgrupp och plats som logikappen.

Här är ett exempel på resursdefinition för en Office 365 Outlook-anslutning och motsvarande mallparametrar:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

Logikappens resursdefinition fungerar också med definitioner av anslutningsresurser på följande sätt:

* I arbetsflödesdefinitionen `parameters` deklarerar `$connections` objektet en parameter för de anslutningsvärden som ska användas vid logikappkörning. Den utlösare eller åtgärd som skapar en anslutning använder `$connections` också motsvarande värden som går igenom den här parametern.

* *Utanför* arbetsflödesdefinitionen men fortfarande *i* logikappens resursdefinition anger ett annat `parameters` `$connections` objekt de värden som ska användas vid körning för parametern genom att referera till motsvarande mallparametrar. Dessa värden använder malluttryck för att referera till resurser som på ett säkert sätt lagrar metadata för anslutningarna i logikappen.

  Metadata kan till exempel innehålla anslutningssträngar och åtkomsttoken, som du kan lagra i [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Om du vill skicka dessa värden till mallparametrarna refererar du till nyckelvalvet i [parameterfilen](#template-parameter-files) som används av mallen vid distributionen. Mer information om skillnader i referensparametrar finns i [Referenser till parametrar](#parameter-references) senare i det här avsnittet.

  När du öppnar logikappens arbetsflödesdefinition i kodvyn via `$connections` Azure-portalen eller Visual Studio visas objektet utanför arbetsflödesdefinitionen men på samma nivå. Den här ordningen i kodvyn gör dessa parametrar enklare att referera till när du uppdaterar arbetsflödesdefinitionen manuellt:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Logikappens resursdefinition har `dependsOn` ett objekt som anger beroendena på de anslutningar som används av logikappen.

Varje anslutning som du skapar har ett unikt namn i Azure. När du skapar flera anslutningar till samma tjänst eller system läggs varje anslutningsnamn till med ett tal `office365`som `office365-1`ökar med varje ny anslutning som skapas, till exempel , och så vidare.

I det här exemplet visas interaktionerna mellan logikappens resursdefinition och en resursdefinition för Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Parametrar för säker anslutning

För en anslutningsparameter som hanterar känslig information, lösenord, åtkomstnycklar eller hemligheter `parameterValues` innehåller anslutningens resursdefinition ett objekt som anger dessa värden i namnvärdesparformat. Om du vill dölja den här informationen kan du deklarera `securestring` `secureobject` eller redigera mallparametrarna för dessa värden med hjälp av parametertyperna eller. Du kan sedan lagra informationen i [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Om du vill skicka dessa värden till mallparametrarna refererar du till nyckelvalvet i [parameterfilen](#template-parameter-files) som används av mallen vid distributionen.

Här är ett exempel som ger kontonamn och åtkomstnyckel för en Azure Blob Storage-anslutning:

**Parameterfil**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Mall**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Autentisera anslutningar

Efter distributionen fungerar logikappen från på slutet med giltiga parametrar. Du måste dock fortfarande auktorisera oauth-anslutningar för att generera giltiga åtkomsttoken för [att autentisera dina autentiseringsuppgifter](../active-directory/develop/authentication-scenarios.md). Mer information finns i [Auktorisera OAuth-anslutningar](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Vissa anslutningar stöder användning av ett Azure Active Directory-tjänsthuvudnamn (Azure AD) för att auktorisera anslutningar för en logikapp som är [registrerad i Azure AD](../active-directory/develop/quickstart-register-app.md). [service principal](../active-directory/develop/app-objects-and-service-principals.md) Den här Azure Data Lake-anslutningsresursdefinitionen visar till exempel hur du refererar till mallparametrar som hanterar tjänstens huvudnamnsinformation och hur mallen deklarerar dessa parametrar:

**Definition av anslutningsresurs**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Attribut | Beskrivning |
|-----------|-------------|
| `token:clientId` | Programmet eller klient-ID:t som är kopplat till tjänstens huvudnamn |
| `token:clientSecret` | Nyckelvärdet som är associerat med tjänstens huvudnamn |
| `token:TenantId` | Katalog-ID:et för din Azure AD-klient |
| `token:grantType` | Den begärda bidragstypen, som måste vara `client_credentials`. Mer information finns i [Microsofts identitetsplattform och OAuth 2.0-klientautentiseringsflödet](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definitioner av mallparameter**

Mallens objekt på `parameters` den översta nivån deklarerar dessa parametrar för exempelanslutningen:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Mer information om hur du arbetar med tjänsthuvudnamn finns i följande avsnitt:

* [Skapa ett huvudnamn för tjänsten med hjälp av Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md)
* [Skapa ett huvudnamn för Azure-tjänsten med hjälp av Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Skapa ett tjänsthuvudnamn med ett certifikat med hjälp av Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Referenser till parametrar

Om du vill referera till mallparametrar kan du använda malluttryck med [mallfunktioner](../azure-resource-manager/templates/template-functions.md), som utvärderas vid distributionen. Malluttryck använder hakparenteser (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Om du vill referera till arbetsflödesdefinitionsparametrar använder du uttryck och funktioner för [arbetsflödesdefinitionsspråk](../logic-apps/workflow-definition-language-functions-reference.md), som utvärderas vid körning. Du kanske märker att vissa mallfunktioner och arbetsflödesdefinitionsfunktioner har samma namn. Arbetsflödesdefinitionsuttryck börjar med "at"-symbolen (**@**):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Du kan skicka mallparametervärden till arbetsflödesdefinitionen för logikappen som ska användas under körning. Undvik dock att använda mallparametrar, uttryck och syntax i arbetsflödesdefinitionen eftersom Logic App Designer inte stöder mallelement. Mallsyntax och uttryck kan också komplicera koden på grund av skillnader i när uttryck utvärderas.

Följ i stället dessa allmänna steg för att deklarera och referera till parametrar för arbetsflödesdefinition som ska användas vid körning, deklarera och referera till mallparametrarna som ska användas vid distributionen och ange vilka värden som ska överföras vid distributionen med hjälp av en parameterfil. Fullständig information finns i avsnittet [Arbetsflödesdefinition och parametrar](#workflow-definition-parameters) tidigare i det här avsnittet.

1. Skapa mallen och deklarera mallparametrarna för de värden som ska accepteras och användas vid distributionen.

1. I arbetsflödesdefinitionen deklarerar du parametrarna för att värdena ska acceptera och använda vid körning. Du kan sedan referera till dessa värden i hela och inom arbetsflödesdefinitionen.

1. I `parameters` objektet som ligger *utanför* arbetsflödesdefinitionen men fortfarande *finns i* logikappens resursdefinition anger du värdena för parametrarna för arbetsflödesdefinitionen genom att referera till motsvarande mallparametrar. På så sätt kan du skicka mallparametervärden till dina arbetsflödesdefinitionsparametrar.

1. I parameterfilen anger du värdena för mallen som ska användas vid distributionen.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Fullständig exempelmall

Här är den parameteriserade exempelmallen som används av det här avsnittets exempel:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa mallar för logikappar](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
