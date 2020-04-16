---
title: Skapa prediktiva datapipelpipelar
description: Lär dig hur du skapar en förutsägande pipeline med hjälp av Azure Machine Learning - Batch Execution Activity i Azure Data Factory.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 3587ee711864eb33fea9bc4e61fe226562e8f612
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418872"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Skapa förutsägande pipelines med Azure Machine Learning och Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuell version](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Med Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) kan du skapa, testa och distribuera lösningar för prediktiv analys. Ur hög nivå görs det i tre steg:

1. **Skapa ett träningsexperiment**. Du gör det här steget med hjälp av Azure Machine Learning Studio (klassisk). Azure Machine Learning Studio (klassisk) är en samarbetsmiljö för visuell utveckling som du använder för att träna och testa en modell för prediktiv analys med hjälp av träningsdata.
2. **Konvertera den till ett prediktivt experiment**. När din modell har tränats med befintliga data och du är redo att använda den för att få nya data, förbereder och effektiviserar du experimentet för bedömning.
3. **Distribuera den som en webbtjänst**. Du kan publicera ditt bedömningsexperiment som en Azure-webbtjänst. Du kan skicka data till din modell via den här slutpunkten för webbtjänsten och få resultatprognoser från modellen.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory och Machine Learning tillsammans
Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad [Azure Machine Learning-webbtjänst](https://azure.microsoft.com/documentation/services/machine-learning) för prediktiv analys. Med **batchkörningsaktiviteten** i en Azure Data Factory-pipeline kan du anropa en azure Machine Learning Studio -webbtjänst (klassisk) för att göra förutsägelser om data i batchen.

Med tiden måste de prediktiva modellerna i Azure Machine Learning Studio (klassiska) bedömningsexperiment tränas om med hjälp av nya indatauppsättningar. Du kan träna om en modell från en Data Factory-pipeline genom att göra följande:

1. Publicera utbildningsexperimentet (inte förutsägelseexperimentet) som en webbtjänst. Du gör det här steget i Azure Machine Learning Studio (klassisk) som du gjorde för att exponera förutsägelseexperiment som en webbtjänst i föregående scenario.
2. Använd Azure Machine Learning Studio (klassisk) Batch Execution Activity för att anropa webbtjänsten för utbildningsexperimentet. I grund och botten kan du använda Azure Machine Learning Studio (klassisk) Batch Execution-aktivitet för att anropa både utbildningswebbtjänst och bedömningswebbtjänst.

När du är klar med omskolningen uppdaterar du bedömningswebbtjänsten (förutsägelseexperiment som exponeras som en webbtjänst) med den nyligen utbildade modellen med hjälp av **Azure Machine Learning Studio (klassisk) Update Resource Activity**. Mer information finns i Uppdatera modeller med artikeln [Uppdatera resursaktivitet.](update-machine-learning-models.md)

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst

Du skapar en **Azure Machine Learning-länkad** tjänst för att länka en Azure Machine Learning Web Service till en Azure-datafabrik. Den länkade tjänsten används av Azure Machine Learning Batch Execution Activity och [Update Resource Activity](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Se artikel med länkade tjänster för [beräkningslänkade tjänster](compute-linked-services.md) för beskrivningar av egenskaper i JSON-definitionen.

Azure Machine Learning stöder både Klassiska webbtjänster och nya webbtjänster för ditt förutsägelseexperiment. Du kan välja den rätta att använda från Data Factory. Om du vill ha den information som krävs https://services.azureml.netför att skapa Azure Machine Learning Linked Service går du till , där alla dina (nya) webbtjänster och klassiska webbtjänster visas. Klicka på den webbtjänst som du vill komma åt och klicka på **Använd** sida. Kopiera **primärnyckel** för **apiKey-egenskapen** och **Batch-begäranden** för **egenskapen mlEndpoint.**

![Azure Machine Learning Webbtjänster](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning Batch Execution-aktivitet

Följande JSON-kodavsnitt definierar en Azure Machine Learning Batch Execution-aktivitet. Aktivitetsdefinitionen har en referens till den Azure Machine Learning-länkade tjänst som du skapade tidigare.

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
| namn              | Namnet på den verksamhet som är på gång     | Ja      |
| description       | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ              | För U-SQL-aktivitet för DataSjöanalys är aktivitetstypen **AzureMLBatchExecution**. | Ja      |
| linkedServiceName | Länkade tjänster till Azure Machine Learning Linked Service. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) | Ja      |
| webServiceInputs  | Nyckel, värdepar, mappning av namnen på Azure Machine Learning Web Service-indata. Nyckeln måste matcha de indataparametrar som definierats i den publicerade Azure Machine Learning Web Service. Värde är ett azure storage linked services- och FilePath-egenskapspar som anger indatablobbplatserna. | Inga       |
| webServiceOutputs | Nyckel, värdepar, mappning av namnen på Azure Machine Learning Web Service Outputs. Nyckeln måste matcha de utdataparametrar som definierats i den publicerade Azure Machine Learning Web Service. Värde är ett azure storage linked services- och FilePath-egenskapspar som anger utdatablobbplatserna. | Inga       |
| globalaParametrar  | Nyckel, värdepar som ska skickas till slutpunkten för Azure Machine Learning Studio (klassisk) batchkörningstjänst. Nycklar måste matcha namnen på webbtjänstparametrar som definierats i den publicerade webbtjänsten Azure Machine Learning Studio (klassisk). Värden skickas i egenskapen GlobalParameters för azure Machine Learning Studio (klassisk) batchkörningsbegäran | Inga       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: Experiment med indata/utdata för webbtjänster som refererar till data i Azure Blob Storage

I det här fallet gör Azure Machine Learning Web-tjänsten förutsägelser med hjälp av data från en fil i en Azure blob lagring och lagrar förutsägelse resultat i blob lagring. Följande JSON definierar en Data Factory-pipeline med en AzureMLBatchExecution-aktivitet. In- och utdata i Azure Blog Storage refereras med hjälp av ett LinkedName- och FilePath-par. I exemplet Linked Service of inputs and outputs are different, you can use different Linked Services for each of your inputs/outputs for Data Factory to be able to pick up the right files and send to Azure Machine Learning Studio (classic) Web Service.

> [!IMPORTANT]
> I ditt Experiment i Azure Machine Learning Studio (klassiskt) har in- och utdataportar för webbtjänster och globala parametrar standardnamn ("input1", "input2") som du kan anpassa. Namnen som du använder för inställningar för webServiceInputs, webServiceOutputs och globalParameters måste exakt matcha namnen i experimenten. Du kan visa exempelbegärans nyttolast på hjälpsidan för batchkörning för din Azure Machine Learning Studio -slutpunkt (klassisk) för att verifiera den förväntade mappningen.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario 2: Experiment med läsar-/skrivmoduler för att referera till data i olika lagringar
Ett annat vanligt scenario när du skapar klassiska experiment i Azure Machine Learning Studio är att använda moduler för importdata och utdata. Modulen Importera data används för att läsa in data i ett experiment och modulen Utdata är att spara data från dina experiment. Mer information om moduler för importdata och utdata finns i [Importera data](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [utdataavsnitt](https://msdn.microsoft.com/library/azure/dn905984.aspx) på MSDN-bibliotek.

När du använder modulerna Importera data och utdata är det bra att använda en webbtjänstparameter för varje egenskap för dessa moduler. Med de här webbparametrarna kan du konfigurera värdena under körning. Du kan till exempel skapa ett experiment med en importdatamodul som använder en Azure SQL Database: XXX.database.windows.net. När webbtjänsten har distribuerats vill du aktivera konsumenter av webbtjänsten för `YYY.database.windows.net`att ange en annan Azure SQL Server som kallas . Du kan använda en webbtjänstparameter för att tillåta att det här värdet konfigureras.

> [!NOTE]
> Webbtjänstinmatning och utdata skiljer sig från webbtjänstparametrar. I det första scenariot har du sett hur en indata och utdata kan anges för en Azure Machine Learning Studio (klassisk) webbtjänst. I det här fallet skickar du parametrar för en webbtjänst som motsvarar egenskaper för moduler för importdata/utdata.
>
>

Låt oss titta på ett scenario för att använda webbtjänstparametrar. Du har en distribuerad Azure Machine Learning-webbtjänst som använder en läsarmodul för att läsa data från en av de datakällor som stöds av Azure Machine Learning (till exempel Azure SQL Database). När batchkörningen har utförts skrivs resultaten med en Writer-modul (Azure SQL Database).  Inga webbtjänstindata och utdata definieras i experimenten. I det här fallet rekommenderar vi att du konfigurerar relevanta webbtjänstparametrar för läsar- och skrivmoduler. Med den här konfigurationen kan läsar-/skrivningsmodulerna konfigureras när du använder AzureMLBatchExecution-aktiviteten. Du anger webbtjänstparametrar i avsnittet **globalParameters** i aktiviteten JSON enligt följande.

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
> Webbtjänstparametrarna är skiftlägeskänsliga, så se till att namnen du anger i aktiviteten JSON matchar de som visas av webbtjänsten.
>

När du är klar med omskolningen uppdaterar du bedömningswebbtjänsten (förutsägelseexperiment som exponeras som en webbtjänst) med den nyligen utbildade modellen med hjälp av **Azure Machine Learning Studio (klassisk) Update Resource Activity**. Mer information finns i Uppdatera modeller med artikeln [Uppdatera resursaktivitet.](update-machine-learning-models.md)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
