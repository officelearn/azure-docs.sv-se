---
title: Webbaktivitet i Azure Data Factory
description: Lär dig hur du kan använda webbaktivitet, en av de kontrollflödesaktiviteter som stöds av Data Factory, för att anropa en REST-slutpunkt från en pipeline.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: a5cdb24a80dcbd95e4ccc59dd55f4acb9ae18060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417903"
---
# <a name="web-activity-in-azure-data-factory"></a>Webbaktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Webbaktiviteten kan används till att anropa en anpassad REST-slutpunkt från en Data Factory-pipeline. Du kan överföra datauppsättningar och länkade tjänster så att de förbrukas och används av aktiviteten.

> [!NOTE]
> Webbaktivitet kan bara anropa offentligt exponerade webbadresser. Det stöds inte för webbadresser som finns i ett privat virtuellt nätverk.

## <a name="syntax"></a>Syntax

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namn på webbaktiviteten | Sträng | Ja
typ | Måste vara inställt på **WebActivity**. | Sträng | Ja
metod | Vila API-metod för målslutpunkten. | Sträng. <br/><br/>Typer som stöds: "GET", "POST", "PUT" | Ja
url | Målslutpunkt och sökväg | Sträng (eller uttryck med resultType av sträng). Aktiviteten kommer att timeout på 1 minut med ett fel om den inte får ett svar från slutpunkten. | Ja
Headers | Rubriker som skickas till begäran. Om du till exempel vill ange språk `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`och skriva på en begäran: . | Sträng (eller uttryck med resultType av sträng) | Ja, rubrik av innehållstyp krävs. `"headers":{ "Content-Type":"application/json"}`
body | Representerar nyttolasten som skickas till slutpunkten.  | Sträng (eller uttryck med resultType av sträng). <br/><br/>Se schemat för nyttolasten för begäran i [schemaavsnittet Begär nyttolastschema.](#request-payload-schema) | Krävs för POST/PUT-metoder.
autentisering | Autentiseringsmetod som används för att anropa slutpunkten. Typer som stöds är "Grundläggande eller ClientCertificate". Mer information finns i avsnittet [Autentisering.](#authentication) Om autentisering inte krävs utesluter du den här egenskapen. | Sträng (eller uttryck med resultType av sträng) | Inga
datauppsättningar | Lista över datauppsättningar som skickas till slutpunkten. | Matris med datauppsättningsreferenser. Kan vara en tom matris. | Ja
länkadeTjänster | Lista över länkade tjänster som skickas till slutpunkten. | Matris med länkade tjänstreferenser. Kan vara en tom matris. | Ja

> [!NOTE]
> REST-slutpunkter som webbaktiviteten anropar måste returnera ett svar av typen JSON. Aktiviteten kommer att timeout på 1 minut med ett fel om den inte får ett svar från slutpunkten.

I följande tabell visas kraven för JSON-innehåll:

| Värdetyp | Begärandetext | Själva svaret |
|---|---|---|
|JSON-objekt | Stöds | Stöds |
|JSON-matris | Stöds <br/>(För närvarande fungerar inte JSON-matriser som ett resultat av ett fel. En korrigering pågår.) | Stöd saknas |
| JSON-värde | Stöds | Stöd saknas |
| Icke-JSON-typ | Stöd saknas | Stöd saknas |
||||

## <a name="authentication"></a>Autentisering

Nedan finns de autentiseringstyper som stöds i webbaktiviteten.

### <a name="none"></a>Ingen

Om autentisering inte krävs ska du inte inkludera egenskapen "autentisering".

### <a name="basic"></a>Basic

Ange användarnamn och lösenord som ska användas med den grundläggande autentiseringen.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klientcertifikat

Ange base64-kodat innehåll i en PFX-fil och lösenordet.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Hanterad identitet

Ange den resursuri för vilken åtkomsttoken ska begäras med hjälp av den hanterade identiteten för datafabriken. Använd för att anropa Azure `https://management.azure.com/`Resource Management API. Mer information om hur hanterade identiteter fungerar finns på [översiktssidan för hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Om din datafabrik är konfigurerad med en git-databas måste du lagra dina autentiseringsuppgifter i Azure Key Vault för att kunna använda grundläggande autentisering eller klientcertifikatautentisering. Azure Data Factory lagrar inte lösenord i git.

## <a name="request-payload-schema"></a>Schema för begär nyttolast
När du använder metoden POST/PUT representerar brödtextegenskapen nyttolasten som skickas till slutpunkten. Du kan skicka länkade tjänster och datauppsättningar som en del av nyttolasten. Här är schemat för nyttolasten:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Exempel
I det här exemplet anropar webbaktiviteten i pipelinen en REST-slutpunkt. Den skickar en Azure SQL-länkad tjänst och en Azure SQL-datauppsättning till slutpunkten. REST-slutpunkten använder Azure SQL-anslutningssträngen för att ansluta till Azure SQL-servern och returnerar namnet på instansen av SQL-servern.

### <a name="pipeline-definition"></a>Definition av pipeline

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Parametervärden för pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Slutpunktskod för webbtjänsten

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory:

- [Köra pipelineaktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
