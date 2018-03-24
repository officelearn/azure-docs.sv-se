---
title: Använd Resource Manager-mallar i Data Factory | Microsoft Docs
description: Lär dig hur du skapar och använda Azure Resource Manager-mallar för att skapa Data Factory entiteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 71aa5b6bdf23e4e072247432184683f9985586b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Använda mallar för att skapa Azure Data Factory entiteter
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). 

## <a name="overview"></a>Översikt
När du använder Azure Data Factory för dina behov för integrering av data, kan du själv återanvända samma mönster i olika miljöer eller genomföra samma uppgift upprepade gånger inom samma lösning. Mallar kan du implementera och hantera dessa scenarier på ett enkelt sätt. Mallar i Azure Data Factory är idealisk för scenarier som omfattar återanvändning och upprepas.

Överväg att situation där en organisation har 10 produktionsanläggningarna över hela världen. Loggar från varje lagras i en separat lokal SQL Server-databas. Företaget vill skapa ett enskilt datalager i molnet för ad hoc-analys. Det vill även ha samma logik men olika konfigurationer för utveckling, test- och miljöer.

I det här fallet behöver en aktivitet upprepas i samma miljö, men med olika värden över 10 datafabriker för varje anläggning. I praktiken **upprepning** finns. Templating gör för detta allmänna flöde (det vill säga pipelines som har samma aktiviteter i varje data factory), men använder en separat parameter-fil för varje anläggning.

Som organisationen vill distribuera dessa 10 datafabriker flera gånger mellan olika miljöer mallar kan dessutom använda detta **återanvändning** genom att använda en separat parameter filer för utveckling, test- och miljöer.

## <a name="templating-with-azure-resource-manager"></a>Templating med Azure Resource Manager
[Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md#template-deployment) är ett bra sätt att uppnå templating i Azure Data Factory. Resource Manager-mallar definierar infrastrukturen och konfigurationen av din Azure-lösning via en JSON-fil. Eftersom Azure Resource Manager-Mallar fungerar med alla/de flesta Azure-tjänster, kan det ofta användas för att enkelt hantera alla resurser för dina Azure tillgångar. Se [redigera Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md) lära dig mer om Resource Manager-mallar i allmänhet.

## <a name="tutorials"></a>Självstudier
Se följande kurser för stegvisa instruktioner för att skapa Data Factory-enheter med hjälp av Resource Manager-mallar:

* [Självstudier: Skapa en pipeline för att kopiera data med hjälp av Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Självstudier: Skapa en pipeline för bearbetning av data med hjälp av Azure Resource Manager-mall](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory-mallar på GitHub
Checka ut följande Azure Snabbstart mallar på GitHub:

* [Skapa en datafabrik för att kopiera data från Azure Blobblagring till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Skapa en datafabrik med Hive aktivitet på Azure HDInsight-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Skapa en datafabrik för att kopiera data från Salesforce till Azure-BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Skapa en datafabrik som länkar aktiviteter: kopierar data från en FTP-server till Azure BLOB, anropar en hive-skript på ett HDInsight-kluster för på begäran att transformera data och kopierar resultatet till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Passa på att dela din Azure Data Factory-mallar på [Azure Snabbstart](https://azure.microsoft.com/documentation/templates/). Referera till den [bidrag guiden](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) när du arbetar med mallar som kan delas via den här databasen.

Följande avsnitt innehåller information om hur du definierar Data Factory resurser i en Resource Manager-mall.

## <a name="defining-data-factory-resources-in-templates"></a>Definiera Data Factory resurser i mallar
Översta mallen för att definiera en datafabrik är:

```JSON
"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definiera en datafabrik
Du definierar en datafabrik i Resource Manager-mallen enligt följande exempel:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
DataFactoryName har definierats i ”variabler” som:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definiera länkade tjänster

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Se [länkade lagringstjänsten](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [Compute länkade tjänster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) mer information om JSON-egenskaperna för den specifika länkade tjänst som du vill distribuera. Parametern ”dependsOn” anger namnet på motsvarande datafabriken. Ett exempel för att definiera en länkad tjänst för Azure Storage visas i följande JSON-definitionen:

### <a name="define-datasets"></a>Definiera datauppsättningar

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Referera till [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mer information om JSON-egenskaperna för specifika dataset-typen som du vill distribuera. Obs! ”dependsOn”-parametern anger namnet på motsvarande data factory och lagring länkade tjänsten. Ett exempel på definierar typen av datauppsättning för Azure-blobblagring visas i följande JSON-definitionen:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definiera pipelines

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Referera till [definierar pipelines](data-factory-create-pipelines.md#pipeline-json) mer information om JSON-egenskaperna för att definiera specifika pipeline och aktiviteter som du vill distribuera. Observera ”dependsOn”-parametern anger namnet på data factory och eventuella motsvarande länkade tjänster eller datauppsättningar. Ett exempel på en pipeline som kopierar data från Azure Blobblagring till Azure SQL Database visas i följande fragment JSON:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Parameterisera Data Factory-mall
Metodtips för Parameterisera finns [bästa praxis för att skapa mallar för Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md). I allmänhet ska parametrar minimeras, särskilt om variabler kan användas i stället. Ange bara parametrar i följande scenarier:

* Inställningarna varierar beroende på miljö (exempel: utveckling, test och produktion)
* Hemligheter (till exempel lösenord)

Om du behöver ta emot hemligheter från [Azure Key Vault](../../key-vault/key-vault-get-started.md) när du distribuerar Azure Data Factory-enheter med hjälp av mallar kan du ange den **nyckelvalv** och **hemliga namnet** som visas i följande exempel:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Exportera mallar för befintliga datafabriker inte stöds för närvarande ännu, är det i fungerar.
>
>
