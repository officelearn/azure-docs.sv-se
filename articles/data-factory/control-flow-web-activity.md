---
title: Web aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda Web-aktivitet, något av kontrollflödesaktiviteter som stöds av Data Factory för att anropa en REST-slutpunkt från en pipeline.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: shlo
ms.openlocfilehash: 71e89828645cadbbbf60527fca9968fd8ed568ff
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059220"
---
# <a name="web-activity-in-azure-data-factory"></a>Webbaktivitet i Azure Data Factory
Webbaktiviteten kan används till att anropa en anpassad REST-slutpunkt från en Data Factory-pipeline. Du kan överföra datauppsättningar och länkade tjänster så att de förbrukas och används av aktiviteten. 

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

## <a name="type-properties"></a>Typegenskaper

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på aktiviteten web | Sträng | Ja
typ | Måste anges till **WebActivity**. | Sträng | Ja
metod | REST API-metoden för mål-slutpunkten. | sträng. <br/><br/>Typer som stöds: ”GET”, ”publicera”, ”PLACERA” | Ja
url | Mål-slutpunkten och sökvägen | Sträng (eller uttryck med resultType av sträng). Aktiviteten kommer timeout på 1 minut med ett fel om det inte får något svar från slutpunkten. | Ja
rubriker | Huvuden som skickas till begäran. Till exempel ange språk och typ för en begäran: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Sträng (eller uttrycket resultType av sträng) | Ja, Content-type-huvudet måste anges. `"headers":{ "Content-Type":"application/json"}`
brödtext | Representerar nyttolasten som skickas till slutpunkten.  | Sträng (eller uttryck med resultType av sträng). <br/><br/>Visa schemat för nyttolasten i begäran i [begäran nyttolast schemat](#request-payload-schema) avsnitt. | Krävs för POST/PUT metoder.
autentisering | Autentiseringsmetod som används för att anropa slutpunkten. Typer som stöds är ”Basic eller ClientCertificate”. Mer information finns i [autentisering](#authentication) avsnitt. Undanta den här egenskapen om verifiering inte krävs. | Sträng (eller uttrycket resultType av sträng) | Nej
Datauppsättningar | Lista över datauppsättningar som skickas till slutpunkten. | Matris med dataset-referenser. Kan vara en tom matris. | Ja
linkedServices | Lista över länkade tjänster skickas till slutpunkten. | Matris med länkade tjänsten refererar till. Kan vara en tom matris. | Ja

> [!NOTE]
> REST-slutpunkter som aktiviteten web anropar måste returnera ett svar av typen JSON. Aktiviteten kommer timeout på 1 minut med ett fel om det inte får något svar från slutpunkten.

I följande tabell visas kraven för innehållet i JSON:

| Värdetyp | Begärandetext | Svarstexten |
|---|---|---|
|JSON-objekt | Stöds | Stöds |
|JSON-matris | Stöds <br/>(För närvarande JSON-matriser fungerar inte på grund av ett programfel. En korrigering pågår.) | Stöds inte |
| JSON-värde | Stöds | Stöds inte |
| Icke-JSON-typ | Stöds inte | Stöds inte |
||||

## <a name="authentication"></a>Autentisering

### <a name="none"></a>Ingen
Om autentisering inte krävs innehåller inte egenskapen ”autentisering”.

### <a name="basic"></a>Basic
Ange användarnamn och lösenord som ska användas med grundläggande autentisering. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klientcertifikat
Ange base64-kodad innehållet i en PFX-filen och lösenordet. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Begäran nyttolast schemat
När du använder POST/PUT-metoden, representerar egenskapen body nyttolasten som skickas till slutpunkten. Du kan överföra länkade tjänster och datauppsättningar som en del av nyttolasten. Här följer schemat för nyttolasten: 

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
I det här exemplet anropar webbaktivitet i pipelinen en REST-slutpunkt. En Azure SQL-länkade tjänst och en Azure SQL-datauppsättning överförs till slutpunkten. REST-slutpunkt använder Azure SQL-anslutningssträng för att ansluta till Azure SQL-servern och returnerar namnet på instansen av SQLServer. 

### <a name="pipeline-definition"></a>Pipeline-definition

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

### <a name="pipeline-parameter-values"></a>Pipeline-parametervärden

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Web service endpoint-kod

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
Se annan kontrollflödesaktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
