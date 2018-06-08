---
title: Skapa Azure Machine Learning-experiment med en Azure Resource Manager-mall | Microsoft Docs
description: Den här artikeln innehåller ett exempel för att skapa ett konto för Azure Machine Learning-experiment med en Azure Resource Manager-mall.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 65efee0eea5e2595ef8833788281c5ba60ca3a43
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830790"
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Konfigurera tjänsten Azure Machine Learning experiment

## <a name="overview"></a>Översikt
Azure Machine Learning experiment tjänstkonto, arbetsytan och projektet är Azure-resurser. De kan därför distribueras med hjälp av mallar Resources Manager. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. En beskrivning av de begrepp som används i samband med distribution och hantering av Azure-lösningar finns i [Översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Distribuera en mall
Distribution av en mall kräver endast några steg i Azure-kommandoradsgränssnittet eller i Azure-portalen.

### <a name="deploy-a-template-from-the-command-line"></a>Distribuera en mall från kommandoraden
Med hjälp av kommandoradsgränssnittet, distribuera ett enda kommando en mall till en befintlig resursgrupp.
Se följande information om hur du skapar en mall.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Distribuera en mall från Azure-portalen
Om du vill kan använda du också Azure-portalen för att skapa och distribuera en mall. Gör på följande sätt:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** och Sök efter ”mallar”.
3. Välj **mallar**.
4. Klicka på **+ Lägg till** och kopiera mallinformationen om. 
5. Välj den mall du skapade i steg #4 och klicka på **distribuera**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Skapa en mall från en befintlig Azure-resurs i Azure-portalen
Om du redan har en Azure-datorn experiment som kontot finns i [Azure-portalen](https://portal.azure.com), du kan skapa en mall från den här resursen. 

1. Navigera till ett Azure-konto för experiment i [Azure-portalen](https://portal.azure.com).
2. Under **inställningar**, klicka på **automatiseringsskriptet**.
3. Hämta en mall. 

Du kan också redigera mallfilerna manuellt. Se följande exempel på en mall och parametrar filer. 

### <a name="template-file-example"></a>Exempel på en mall
Skapa en fil med namnet ”mall file.json” och nedanför innehållet. 

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
* AccountName: Namnet på kontot experiment.
* Plats: En av de Azure regionerna som stöds.
* Storage-konto SKU: Azure ML stöder endast standardlagring inte premium. Mer information om lagring finns [lagring introduktion](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

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
* [Skapa och installera Azure Machine Learning](../service/quickstart-installation.md)
