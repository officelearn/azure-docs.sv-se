---
title: Skapa Azure Machine Learning-experimentering med en Azure Resource Manager-mall | Microsoft Docs
description: Den här artikeln innehåller ett exempel för att skapa en Azure Machine Learning-experimentering-konto med en Azure Resource Manager-mall.
services: machine-learning
author: hning86
ms.author: haining
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 24ed164f4a1dfdb9a3913efa78fe58fab2b53696
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991971"
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Konfigurera tjänsten Azure Machine Learning-experimentering

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

## <a name="overview"></a>Översikt
Azure Machine Learning Experimentation Service-konto, arbetsytan och projekt är Azure-resurser. De kan därför distribueras med hjälp av Resources Manager-mallar. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. En beskrivning av de begrepp som används i samband med distribution och hantering av Azure-lösningar finns i [Översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Distribuera en mall
Distribuerar en mall kräver bara ett par steg i Azure-kommandoradsgränssnitt eller i Azure-portalen.

### <a name="deploy-a-template-from-the-command-line"></a>Distribuera en mall från kommandoraden
Med hjälp av kommandoradsgränssnittet, kan ett enda kommando distribuera en mall till en befintlig resursgrupp.
Se följande information om hur du skapar en mall.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Distribuera en mall från Azure portal
Om du vill kan använda du också Azure-portalen för att skapa och distribuera en mall. Gör på följande sätt:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** och Sök efter ”mallar”.
3. Välj **mallar**.
4. Klicka på **+ Lägg till** och kopiera mallinformationen. 
5. Välj den mall som skapades i steg #4 och klicka på **distribuera**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Skapa en mall från en befintlig Azure-resurs i Azure portal
Om du redan har en Azure Machine-experimentering konto tillgängliga i [Azure-portalen](https://portal.azure.com), du kan skapa en mall från den här resursen. 

1. Navigera till ett Azure-konto för experimentering i [Azure-portalen](https://portal.azure.com).
2. Under **inställningar**, klicka på **automationsskript**.
3. Ladda ned mallen. 

Du kan också redigera mallfilerna manuellt. Se ett exempel på en mall och parametrar följande filer. 

### <a name="template-file-example"></a>Exempel på en mall
Skapa en fil med namnet ”mall-file.json” nedan innehåll. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>Parametrar 
Skapa en fil med nedan innehåll och spara den som < parameters.json >. 

Det finns tre värden som du kan ändra. 
* AccountName: Namnet på experimenteringskontot.
* Plats: En stöds Azure-regioner.
* Lagringskontots SKU: Azure ML stöder endast standardlagring, inte premium. Mer information om storage finns i [storage introduktion](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Nästa steg
* [Skapa och installera Azure Machine Learning](quickstart-installation.md)
