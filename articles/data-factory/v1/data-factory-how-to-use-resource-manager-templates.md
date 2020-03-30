---
title: Använda Resource Manager-mallar i Data Factory
description: Lär dig hur du skapar och använder Azure Resource Manager-mallar för att skapa datafabrikentiteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 930a3f0cf629c99fc03a84c701bcf7b2807c77c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972865"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Använda mallar för att skapa Azure Data Factory-entiteter
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

## <a name="overview"></a>Översikt
När du använder Azure Data Factory för dina dataintegrationsbehov kan du hitta dig själv att återanvända samma mönster i olika miljöer eller implementera samma uppgift upprepade gånger i samma lösning. Mallar hjälper dig att implementera och hantera dessa scenarier på ett enkelt sätt. Mallar i Azure Data Factory är idealiska för scenarier som innebär återanvändning och upprepning.

Tänk på situationen där en organisation har 10 fabriker över hela världen. Loggarna från varje anläggning lagras i en separat lokal SQL Server-databas. Företaget vill bygga ett enda datalager i molnet för ad hoc-analys. Den vill också ha samma logik men olika konfigurationer för utvecklings-, test- och produktionsmiljöer.

I det här fallet måste en uppgift upprepas i samma miljö, men med olika värden i de 10 datafabrikerna för varje tillverkningsanläggning. I själva verket är **upprepning** närvarande. Templating tillåter uttag av detta generiska flöde (det vill säga rörledningar som har samma aktiviteter i varje datafabrik), men använder en separat parameterfil för varje tillverkningsanläggning.

Eftersom organisationen vill distribuera dessa 10 datafabriker flera gånger i olika miljöer kan mallar dessutom använda den här **återanvändningsbarheten** genom att använda separata parameterfiler för utvecklings-, test- och produktionsmiljöer.

## <a name="templating-with-azure-resource-manager"></a>Vikaler med Azure Resource Manager
[Azure Resource Manager-mallar](../../azure-resource-manager/templates/overview.md) är ett bra sätt att uppnå templating i Azure Data Factory. Resource Manager-mallar definierar infrastrukturen och konfigurationen av din Azure-lösning via en JSON-fil. Eftersom Azure Resource Manager-mallar fungerar med alla/de flesta Azure-tjänster kan de användas i stor utsträckning för att enkelt hantera alla resurser i dina Azure-resurser. Se [Skapa Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md) om du vill veta mer om Resource Manager-mallarna i allmänhet.

## <a name="tutorials"></a>självstudiekurserna
Se följande självstudier för steg-för-steg-instruktioner för att skapa datafabrikentiteter med hjälp av Resource Manager-mallar:

* [Självstudiekurs: Skapa en pipeline för att kopiera data med hjälp av Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Självstudiekurs: Skapa en pipeline för att bearbeta data med hjälp av Azure Resource Manager-mall](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory-mallar på GitHub
Kolla in följande snabbstartsmallar för Azure på GitHub:

* [Skapa en datafabrik för att kopiera data från Azure Blob Storage till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Skapa en datafabrik med Hive-aktivitet i Azure HDInsight-klustret](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Skapa en datafabrik för att kopiera data från Salesforce till Azure Blobbar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Skapa en datafabrik som kedjar aktiviteter: kopierar data från en FTP-server till Azure Blobbar, anropar ett datafiler på ett HDInsight-kluster på begäran för att omvandla data och kopior resulterar i Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Dela gärna dina Azure Data Factory-mallar på [Azure Quick start](https://azure.microsoft.com/documentation/templates/). Se [bidragsguiden](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) när du utvecklar mallar som kan delas via den här databasen.

I följande avsnitt finns information om hur du definierar datafabriksresurser i en Resource Manager-mall.

## <a name="defining-data-factory-resources-in-templates"></a>Definiera datafabriksresurser i mallar
Mallen på den översta nivån för att definiera en datafabrik är:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
DataFactoryName definieras i "variabler" som:

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

Mer information om JSON-egenskaperna för den specifika länkade tjänst som du vill distribuera finns i [Lagringslänkade tjänster](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [Compute Linked Services.](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Parametern "dependsOn" anger namnet på motsvarande datafabrik. Ett exempel på att definiera en länkad tjänst för Azure Storage visas i följande JSON-definition:

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
Mer information om JSON-egenskaperna för den specifika datauppsättning som du vill distribuera finns [i Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Observera parametern "dependsOn" anger namnet på motsvarande datafabrik och lagringslänkade tjänst. Ett exempel på att definiera datauppsättningstypen för Azure blob storage visas i följande JSON-definition:

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

Se [definiera pipelines](data-factory-create-pipelines.md#pipeline-json) för information om JSON-egenskaperna för att definiera den specifika pipelinen och aktiviteter som du vill distribuera. Observera parametern "dependsOn" anger namnet på datafabriken och eventuella motsvarande länkade tjänster eller datauppsättningar. Ett exempel på en pipeline som kopierar data från Azure Blob Storage till Azure SQL Database visas i följande JSON-kodavsnitt:

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
## <a name="parameterizing-data-factory-template"></a>Parameterisering av datafabriksmall
Metodtips för parameterisering finns i [Metodtips för att skapa Azure Resource Manager-mallar](../../azure-resource-manager/resource-manager-template-best-practices.md). I allmänhet bör parameteranvändningen minimeras, särskilt om variabler kan användas i stället. Ange endast parametrar i följande scenarier:

* Inställningarna varierar beroende på miljö (t.ex. utveckling, test och produktion)
* Hemligheter (t.ex. lösenord)

Om du behöver hämta hemligheter från [Azure Key Vault](../../key-vault/key-vault-overview.md) när du distribuerar Azure Data Factory-entiteter med hjälp av mallar anger du **nyckelvalvet** och **det hemliga namnet** som visas i följande exempel:

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
> När du exporterar mallar för befintliga datafabriker stöds för närvarande inte, är det på gång.
>
>
