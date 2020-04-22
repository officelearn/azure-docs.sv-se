---
title: Skapa parameterfil
description: Skapa parameterfil för att skicka in värden under distributionen av en Azure Resource Manager-mall
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a1a1f703594f8eaa572ea38ecef88b4cd6ba5a4b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682899"
---
# <a name="create-resource-manager-parameter-file"></a>Parameterfilen Skapa Resource Manager

I stället för att skicka parametrar som infogade värden i skriptet kan det vara enklare att använda en JSON-fil som innehåller parametervärdena. Den här artikeln visar hur du skapar parameterfilen.

## <a name="parameter-file"></a>Parameterfil

Parameterfilen har följande format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Observera att parametervärdena lagras som oformaterad text i parameterfilen. Den här metoden fungerar för värden som inte är känsliga, till exempel att ange SKU för en resurs. Det fungerar inte för känsliga värden, till exempel lösenord. Om du behöver skicka ett känsligt värde som en parameter lagrar du värdet i ett nyckelvalv och refererar till nyckelvalvet i parameterfilen. Det känsliga värdet hämtas säkert under distributionen.

Följande parameterfil innehåller ett oformaterat textvärde och ett värde som lagras i ett nyckelvalv.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Mer information om hur du använder värden från ett nyckelvalv finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definiera parametervärden

Om du vill ta reda på hur du definierar parametervärdena öppnar du mallen som du distribuerar. Titta på parameteravsnittet i mallen. I följande exempel visas parametrarna från en mall.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

Den första detaljen som ska märkas är namnet på varje parameter. Värdena i parameterfilen måste matcha namnen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Lägg märke till typen av parameter. Värdena i parameterfilen måste ha samma typer. För den här mallen kan du ange båda parametrarna som strängar.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Leta sedan efter ett standardvärde. Om en parameter har ett standardvärde kan du ange ett värde, men du behöver inte.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Slutligen, titta på tillåtna värden och eventuella begränsningar som max längd. De anger det värdeintervall som du kan ange för parametern.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

## <a name="parameter-type-formats"></a>Format för parametertyp

I följande exempel visas formaten för olika parametertyper.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="file-name"></a>Filnamn

Den allmänna konventionen för namngivning av parameterfilen är att lägga till **.parameters** i mallnamnet. Om mallen till exempel heter **azuredeploy.json**kallas parameterfilen **azuredeploy.parameters.json**. Den här namngivningskonventionen hjälper dig att se sambandet mellan mallen och parametrarna.

Om du vill distribuera till olika miljöer skapar du mer än en parameterfil. När du namnger parameterfilen lägger du till ett sätt att identifiera dess användning. Använd till exempel **azuredeploy.parameters-dev.json** och **azuredeploy.parameters-prod.json**


## <a name="parameter-precedence"></a>Parameterprioritet

Du kan använda infogade parametrar och en lokal parameterfil i samma distributionsåtgärd. Du kan till exempel ange vissa värden i den lokala parameterfilen och lägga till andra värden infogade under distributionen. Om du anger värden för en parameter i både den lokala parameterfilen och infogade, har det infogade värdet företräde.

När du använder en extern parameterfil kan du dock inte skicka andra värden antingen infogade eller från en lokal fil. Alla infogade parametrar ignoreras. Ange alla parametervärden i den externa filen.

## <a name="parameter-name-conflicts"></a>Konflikter i parameternamn

Om mallen innehåller en parameter med samma namn som en av parametrarna i PowerShell-kommandot, visar PowerShell parametern från mallen med postfixet **FromTemplate**. En parameter med namnet **ResourceGroupName** i mallen står till exempel i konflikt med parametern **ResourceGroupName** i cmdleten [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Du uppmanas att ange ett värde för **ResourceGroupNameFromTemplate**. Du kan undvika den här förvirringen genom att använda parameternamn som inte används för distributionskommandon.

## <a name="next-steps"></a>Nästa steg

- Information om hur du definierar parametrar i mallen finns [i Parametrar i Azure Resource Manager-mallar](template-parameters.md).
- Mer information om hur du använder värden från ett nyckelvalv finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen](key-vault-parameter.md).
- Mer information om parametrar finns [i Parametrar i Azure Resource Manager-mallar](template-parameters.md).
