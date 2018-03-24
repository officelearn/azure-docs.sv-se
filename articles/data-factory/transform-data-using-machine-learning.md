---
title: Skapa förutsägande data pipelines med Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en förutsägbar pipeline med hjälp av Azure Machine Learning - Batchkörningsaktivitet i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: bd72fd957948e77df6ffb0b310c590132c663235
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Skapa förutsägande pipelines med hjälp av Azure Machine Learning och Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Version 2 – förhandsversion](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) gör det möjligt att bygga, testa och distribuera prediktiva Analyslösningar. Från en övergripande synsätt görs i tre steg:

1. **Skapa en träningsexperiment**. Du kan göra det här steget med hjälp av Azure ML Studio. ML studio är en gemensam visual utvecklingsmiljö som används för att träna och testa en förutsägelseanalysmodell med hjälp av utbildningsdata.
2. **Konvertera den till en prediktivt experiment**. När din modell har tränats med befintliga data och du är redo att använda den för att samla in nya data, förbereda och förenkla experimentet för resultatfunktioner.
3. **Distribuera den som en webbtjänst**. Du kan publicera experimentet bedömningsprofil som Azure-webbtjänst. Du kan skicka data till din modell via den här slutet för webbtjänst och ta emot resultatet förutsägelser från modellen.  

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Machine Learning-Batchkörningsaktivitet i V1](v1/data-factory-azure-ml-batch-execution-activity.md).


### <a name="data-factory-and-machine-learning-together"></a>Data Factory och Machine Learning tillsammans
Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad [Azure Machine Learning] [azure-machine-learning] webbtjänst för förutsägelseanalys. Med hjälp av den **Batchkörningsaktivitet** i Azure Data Factory-pipelinen, du kan anropa en Azure ML-webbtjänst för att göra förutsägelser på data i batch. 

Förutsägelsemodeller i Azure ML bedömningen experiment måste vara retrained med nya indatauppsättningar med tiden. Du kan träna om Azure ML-modell från Data Factory-pipelinen genom att göra följande:

1. Publicera träningsexperiment (inte prediktivt experiment) som en webbtjänst. Du kan göra det här steget i Azure ML Studio som du gjorde för att exponera prediktivt experiment som en webbtjänst i scenariot ovan.
2. Använda Azure ML-Batchkörningsaktivitet för att anropa webbtjänsten för utbildning experimentet. I princip kan du använda Azure ML-batchkörning aktiviteten anropa både utbildning webbtjänsten och webbtjänsten för bedömningsprofil.

När du är klar med omtränings uppdatera bedömningsprofil webbtjänsten (prediktivt experiment visas som en webbtjänst) med den nyligen tränade modellen med hjälp av den **Azure ML uppdatera resurs aktiviteten**. Se [uppdatering modeller med Uppdateringsresursaktivitet](update-machine-learning-models.md) artikeln för information.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst

Du skapar en **Azure Machine Learning** länkade tjänsten för att länka en Azure Machine Learning-webbtjänst till ett Azure data factory. Den länkade tjänst som används av Azure Machine Learning-Batchkörningsaktivitet och [Uppdateringsresursaktivitet](update-machine-learning-models.md). 


```JSON
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Se [Compute länkade tjänster](compute-linked-services.md) artikel beskrivningar om egenskaper i JSON-definitionen. 

Azure Machine Learning stöder både klassiska webbtjänster och nya webbtjänster för experimentet förutsägbara. Du kan välja rätt som ska användas från Data Factory. För att få information som krävs för att skapa den länkade tjänsten för Azure Machine Learning, gå till https://services.azureml.net, där alla (nya) webbtjänster och klassiska webbtjänster visas. Klicka på den webbtjänst som du vill komma åt och klickar på **förbruka** sidan. Kopiera **primärnyckel** för **apiKey** -egenskapen och **gruppbegäranden** för **mlEndpoint** egenskapen. 

![Azure Machine Learning-webbtjänster](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning Batch Execution-aktivitet

Följande JSON-fragment definierar en Azure Machine Learning Batch Execution-aktivitet. Aktivitetsdefinitionen har en referens till tjänsten Azure Machine Learning länkade som du skapade tidigare. 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"path1"
            }, 
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"path2"
            }        
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path3"
            }, 
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path4"
            }         
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```



| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| namn              | Namnet på aktivitet i pipelinen     | Ja      |
| description       | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ              | För Data Lake Analytics U-SQL-aktivitet, aktivitetstypen är **AzureMLBatchExecution**. | Ja      |
| linkedServiceName | Azure Machine Learning-länkade tjänster länkade tjänsten. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel. | Ja      |
| webServiceInputs  | Nyckeln, värde-par, mappning namnen på Azure Machine Learning Web Service indata. Nyckeln måste matcha indataparametrar som definierats i den publicerade Azure Machine Learning-webbtjänst. Värdet är en länkad Azure Storage-tjänster och FilePath egenskaper-par anger inkommande Blob-platser. | Nej       |
| webServiceOutputs | Nyckeln, värde-par, mappning namnen på Azure Machine Learning Web Service utdata. Nyckeln måste matcha utdataparametrarna som definierats i den publicerade Azure Machine Learning-webbtjänst. Värdet är en länkad Azure Storage-tjänster och FilePath egenskaper par anger utdata Blob-platser. | Nej       |
| globalParameters  | Nyckeln, värde-par som ska skickas till Azure ML Batch Execution Service-slutpunkten. Nycklar måste matcha namnen på de webbtjänstparametrar som definierats i publicerade Azure ML-webbtjänsten. Värden angavs i egenskapen GlobalParameters i Azure ML batch Körningsbegäran | Nej       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: Försök med hjälp av Web service indata/utdata som refererar till data i Azure Blob Storage

I det här scenariot Azure Machine Learning-webbtjänsten gör förutsägelser med hjälp av data från en fil i ett Azure blob storage och lagrar förutsägelse resultaten i blob storage. Följande JSON definierar Data Factory-pipelinen med en AzureMLBatchExecution aktivitet. Inkommande och utgående data i Azure-bloggen Storage refereras med ett par LinkedName och filsökväg. I exemplet länkade tjänsten av in- och utdataenheter skiljer sig kan du använda olika länkade tjänster för var och en av dina indata/utdata för Data Factory för att kunna hämta rätt filer och skicka till Azure ML Web Service. 

> [!IMPORTANT]
> I din Azure ML experiment, web service inkommande och utgående har portar och globala parametrar standardnamn (”input1”, ”input2”) som du kan anpassa. De namn som du använder för webServiceInputs, webServiceOutputs och globalParameters inställningar måste exakt matcha namnen i experiment. Du kan visa nyttolasten i begäran av exemplet på Batch Execution sidan för din Azure ML-slutpunkt att verifiera förväntade mappningen.
>
> 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"amltest/input/in1.csv"
            }, 
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"amltest/input/in2.csv"
            }        
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out1.csv"
            }, 
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out2.csv"
            }         
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario 2: Försök med Reader/Writer-moduler för att referera till data i olika lagringsobjekt
Ett annat vanligt scenario när du skapar Azure ML experiment är att använda modulerna importera Data och utdata. Importera Data-modulen används för att läsa in data i ett experiment och modulen utdata är att spara data från dina experiment. Mer information om importera Data och utdata finns [importera Data](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [utdata](https://msdn.microsoft.com/library/azure/dn905984.aspx) avsnitt i MSDN Library.     

När du använder importera Data och utdata-moduler är bra att använda en Web service-parameter för varje egenskap för dessa moduler. Dessa webb-parametrar kan du konfigurera värden under körningen. Du kan till exempel skapa ett experiment med en importera Data-modul som använder en Azure SQL Database: XXX.database.windows.net. När webbtjänsten har distribuerats, du vill aktivera användare för webbtjänsten att ange en annan Azure SQL-Server som kallas `YYY.database.windows.net`. Du kan använda en Web service-parametern så att det här värdet som ska konfigureras.

> [!NOTE]
> Webbtjänst och utdata skiljer sig från webbtjänstparametrar. I det första scenariot har du sett hur indata och utdata kan anges för en Azure ML-webbtjänst. I det här scenariot skicka parametrar för en webbtjänst som är kopplade till egenskaperna för Import av Data/utdata-moduler.
>
> 

Nu ska vi titta på ett scenario för att använda webbtjänstparametrar. Du har en distribuerad Azure Machine Learning-webbtjänst som använder en modul för dataläsare för att läsa data från en av de datakällor som stöds av Azure Machine Learning (till exempel: Azure SQL Database). När batch-körningen har utförts skrivs resultaten med skrivarmodul (Azure SQL Database).  Ingen service indata och utdata har definierats i experiment. I detta fall rekommenderar vi att du konfigurerar relevanta webbtjänstparametrar för läsare och skrivare moduler. Den här konfigurationen kan du reader/writer konfigureras när du använder aktiviteten AzureMLBatchExecution-moduler. Du anger webbtjänstparametrar i den **globalParameters** avsnittet i JSON-aktivitet på följande sätt.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```


> [!NOTE]
> Web service-parametrar är skiftlägeskänsliga, så se till att de namn som du anger i aktiviteten JSON matchar de som visas av webbtjänsten.
>

När du är klar med omtränings uppdatera bedömningsprofil webbtjänsten (prediktivt experiment visas som en webbtjänst) med den nyligen tränade modellen med hjälp av den **Azure ML uppdatera resurs aktiviteten**. Se [uppdatering modeller med Uppdateringsresursaktivitet](update-machine-learning-models.md) artikeln för information.



## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Pig-aktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
