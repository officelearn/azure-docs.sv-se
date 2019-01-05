---
title: Webb-aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda Web-aktivitet, något av kontrollflödesaktiviteter som stöds av Data Factory för att anropa en REST-slutpunkt från en pipeline.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: shlo
ms.openlocfilehash: d42b6b857f04c191ebdfb1687c8ee2adcad95d26
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054299"
---
# <a name="web-activity-in-azure-data-factory"></a>Web-aktivitet i Azure Data Factory
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

## <a name="type-properties"></a>Egenskaperna för anslutningstypen

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på webbaktiviteten | Sträng | Ja
typ | Måste vara inställt på **WebActivity**. | Sträng | Ja
metod | REST API-metoden för mål-slutpunkten. | sträng. <br/><br/>Typer som stöds: ”HÄMTA”, ”POST”, ”PUT” | Ja
url | Mål-slutpunkten och sökvägen | Sträng (eller uttryck med resultType av sträng). Aktiviteten kommer tidsgräns på 1 minut med ett fel om det inte får ett svar från slutpunkten. | Ja
Rubriker | Rubriker som skickas till begäran. Till exempel vill ange språk och typ för en begäran: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Sträng (eller ett uttryck med resultType av sträng) | Ja, krävs Content-type-huvud. `"headers":{ "Content-Type":"application/json"}`
brödtext | Representerar den nyttolast som skickas till slutpunkten.  | Sträng (eller uttryck med resultType av sträng). <br/><br/>Se schemat för nyttolasten i begäran i [begäran nyttolast schemat](#request-payload-schema) avsnittet. | Krävs för efter/PUT-metoder.
autentisering | Autentiseringsmetod som används för att anropa slutpunkten. Typer som stöds är ”Basic eller ClientCertificate”. Mer information finns i [autentisering](#authentication) avsnittet. Undanta den här egenskapen om autentisering inte krävs. | Sträng (eller ett uttryck med resultType av sträng) | Nej
datauppsättningar | Lista över datauppsättningar som har överförts till slutpunkten. | Matris med referenser för datauppsättningen. Kan vara tomma matriser. | Ja
linkedServices | Listan över länkade tjänster som har överförts till slutpunkten. | Matris med länkade tjänsten refererar till. Kan vara tomma matriser. | Ja

> [!NOTE]
> REST-slutpunkter som web-aktivitet anropar måste returnera ett svar av typen JSON. Aktiviteten kommer tidsgräns på 1 minut med ett fel om det inte får ett svar från slutpunkten.

I följande tabell visas kraven för JSON-innehåll:

| Värdetyp | Begärandetext | Svarstext |
|---|---|---|
|JSON-objekt | Stöds | Stöds |
|JSON-matris | Stöds <br/>(För närvarande JSON-matriser fungerar inte på grund av ett programfel. En korrigering pågår.) | Stöds inte |
| JSON-värde | Stöds | Stöds inte |
| Icke-JSON-typ | Stöds inte | Stöds inte |
||||

## <a name="authentication"></a>Autentisering

### <a name="none"></a>Ingen
Om autentisering inte krävs, omfattar inte egenskapen ”autentisering”.

### <a name="basic"></a>Basic
Ange användarnamn och lösenord som ska användas med grundläggande autentisering.

```json
"authentication":{
   "type":"Basic",
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

### <a name="managed-identity"></a>Hanterad identitet

Ange resurs-uri för vilken åtkomst-token kommer att begäras med hjälp av den hanterade identitet för data factory. För att anropa Azure Resource Management-API, använda `https://management.azure.com/`.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>Schema för nyttolasten för begäran
När du använder POST/PUT-metoden, representerar egenskapen brödtext den nyttolast som skickas till slutpunkten. Du kan skicka länkade tjänster och datauppsättningar som en del av nyttolasten. Här är schemat för nyttolasten:

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
I det här exemplet anropar web-aktivitet i pipelinen en REST-slutpunkten. Den skickar en länkad Azure SQL-tjänst och en Azure SQL-datauppsättning till slutpunkten. REST-slutpunkten använder Azure SQL-anslutningssträng för att ansluta till Azure SQL-servern och returnerar namnet på instansen av SQLServer.

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

### <a name="web-service-endpoint-code"></a>Koden för slutpunkt

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

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
