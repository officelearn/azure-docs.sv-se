---
title: Använda Resource Manager-mallar i Data Factory | Microsoft Docs
description: Lär dig hur du skapar och använder Azure Resource Manager-mallar för att skapa Data Factory-entiteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 9fb63ae141665dbeb64ee7046427098d4482aa55
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111330"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Använd mallar för att skapa Azure Data Factory-entiteter
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

## <a name="overview"></a>Översikt
När du använder Azure Data Factory för dina databehov integration, kan bli återanvända samma mönster i olika miljöer eller utför samma uppgift upprepade gånger inom samma lösning. Mallar kan du implementera och hantera dessa scenarier på ett enkelt sätt. Mallar i Azure Data Factory är perfekt för scenarier som innefattar återanvändning och upprepas.

Överväg att situation där en organisation har 10 fabriker över hela världen. Loggar från varje anläggning lagras i en separat lokal SQL Server-databas. Företaget vill skapa ett enda datalager i molnet för adhoc-analys. Det vill också har den samma logik, men olika konfigurationer för utveckling, testning och produktionsmiljöer.

I det här fallet måste en uppgift du upprepa i samma miljö, men med olika värden över 10 datafabriker för varje anläggning. I praktiken **upprepning** finns. Mall kan abstraktion av den här Allmänt flöde (det vill säga pipelines som har samma aktiviteter i varje data factory), men använder en separat parameterfil för varje anläggning.

Dessutom som organisationen vill distribuera dessa 10 datafabriker flera gånger i olika miljöer, mallar kan använda den här **återanvändningsmöjligheter** genom att använda separata parameterfiler för utveckling, test, och produktionsmiljöer.

## <a name="templating-with-azure-resource-manager"></a>Mall med Azure Resource Manager
[Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md#template-deployment) är ett bra sätt att uppnå mall i Azure Data Factory. Resource Manager-mallar definierar infrastrukturen och konfigurationen av din Azure-lösning via en JSON-fil. Eftersom Azure Resource Manager-Mallar fungerar med alla/de flesta Azure-tjänster kan användas det brett att enkelt hantera alla resurser för dina Azure-tillgångar. Se [redigera Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md) vill veta mer om Resource Manager-mallar i allmänhet.

## <a name="tutorials"></a>Självstudier
Se följande självstudiekurser för stegvisa instruktioner för att skapa Data Factory-entiteter med hjälp av Resource Manager-mallar:

* [Självstudier: Skapa en pipeline för att kopiera data med hjälp av Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Självstudier: Skapa en pipeline för att bearbeta data med hjälp av Azure Resource Manager-mall](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory-mallar på GitHub
Kolla in följande Snabbstart för Azure-mallar på GitHub:

* [Skapa en Data factory för att kopiera data från Azure Blob Storage till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Skapa en datafabrik med Hive-aktivitet i Azure HDInsight-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Skapa en Data factory för att kopiera data från Salesforce till Azure-Blobar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Skapa en datafabrik som länkar aktiviteter: kopierar data från en FTP-server till Azure Blobs, anropar en hive-skript i ett kluster för HDInsight på begäran för att omvandla data och kopierar resultat till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Passa på att dela din Azure Data Factory-mallar i samband med [Azure snabbstartsguiden](https://azure.microsoft.com/documentation/templates/). Referera till den [bidragsguiden](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) när du utvecklar mallar som kan delas via den här databasen.

Följande avsnitt innehåller information om hur du definierar Data Factory-resurser i en Resource Manager-mall.

## <a name="defining-data-factory-resources-in-templates"></a>Definiera Data Factory-resurser i mallar
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
DataFactoryName definieras i ”variabler” som:

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

Se [länkad lagringstjänst](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [beräkna länkade tjänster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) mer information om JSON-egenskaper för den länkade tjänst du vill distribuera. Parametern ”dependsOn” anger namnet på motsvarande data factory. Ett exempel på att definiera en länkad tjänst för Azure Storage visas i följande JSON-definition:

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
Referera till [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mer information om JSON-egenskaper för den specifika datauppsättningstypen som du vill distribuera. Obs! ”dependsOn”-parametern anger namnet på den motsvarande data factory och lagring länkad tjänst. Ett exempel på Definitionstyp datauppsättning för Azure blob-lagring visas i följande JSON-definition:

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

Referera till [definiera pipelines](data-factory-create-pipelines.md#pipeline-json) mer information om JSON-egenskaper för att definiera den specifika pipeline och aktiviteter som du vill distribuera. Observera ”dependsOn”-parametern anger namnet på datafabriken och eventuella motsvarande länkade tjänster eller datauppsättningar. Ett exempel på en pipeline som kopierar data från Azure Blob Storage till Azure SQL Database visas i följande JSON-kodfragmentet:

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
Bästa metoder för Parameterisera Se [bästa praxis för att skapa Azure Resource Manager-mallar](../../azure-resource-manager/resource-manager-template-best-practices.md). I allmänhet parameteranvändningen värden ska minimeras, särskilt om variabler kan användas i stället. Endast ange parametrar i följande scenarier:

* Inställningarna varierar beroende på miljö (exempel: utveckling, testning och produktion)
* Hemligheter (till exempel lösenord)

Om du behöver att hämta hemligheter från [Azure Key Vault](../../key-vault/key-vault-overview.md) när du distribuerar Azure Data Factory-entiteter med hjälp av mallar, ange den **nyckelvalv** och **hemligt namn** enligt den följande exempel:

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
> Exportera mallar för befintliga datafabriker för närvarande inte stöds ännu, är det i fungerar.
>
>
