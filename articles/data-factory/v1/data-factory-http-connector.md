---
title: Flytta data från en HTTP-källan – Azure | Microsoft Docs
description: Lär dig mer om att flytta data från en lokal eller ett molnkluster HTTP-källan med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 06eb11132d7e3968850aadb4bfdaa53261f14ada
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167485"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Flytta data från en HTTP-källa med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-http-connector.md)
> * [Version 2 (aktuell version)](../connector-http.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Azure Data Factory-tjänsten finns i [HTTP-anslutningsappen i V2](../connector-http.md).


Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från en lokal eller HTTP-slutpunkt till ett datalager för mottagare som stöds i molnet. Den här artikeln bygger vidare på [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md), som anger en allmän översikt över dataförflyttning med hjälp av Kopieringsaktivitet. Artikeln visas även de datalager som Kopieringsaktivitet stöds som källor och mottagare.

Data Factory stöder för närvarande endast flyttar data från en HTTP-källa till datalager. Det stöder inte flyttar data från andra datalager till en HTTP-mål.

## <a name="supported-scenarios-and-authentication-types"></a>Scenarier som stöds och typer av autentisering

Du kan använda den här HTTP-anslutningen för att hämta data från *både ett moln och en slutpunkt för HTTP/S lokala* med hjälp av HTTP **hämta** eller **POST** metoder. Följande autentiseringstyper som stöds: **anonym**, **grundläggande**, **sammanfattad**, **Windows**, och  **ClientCertificate**. Observera skillnaden mellan den här anslutningen och [tabell Webbanslutning](data-factory-web-table-connector.md). Tabellen Webbanslutning extraherar innehåll från en HTML-webbsidan.

När du kopierar data från en lokal HTTP-slutpunkt, måste du installera Data Management Gateway i lokala miljö eller i en Azure-dator. Läs om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar gatewayen i [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Kom igång

Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från en HTTP-källa med olika verktyg eller API: er:

- Det enklaste sättet att skapa en pipeline är att använda guiden kopieringsdata. En snabb genomgång av hur du skapar en pipeline med hjälp av guiden kopieringsdata finns i [självstudie: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

- Du kan också använda följande verktyg för att skapa en pipeline: den **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, en **Azure Resource Manager mallen**, **.NET API**, eller **REST API**. Stegvisa instruktioner om hur du skapar en pipeline med en Kopieringsaktivitet finns i den [Kopieringsaktiviteten självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). JSON-exempel att kopieringsdata från en HTTP-källa till Azure Blob storage, finns i [JSON-exempel](#json-examples).

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

I följande tabell beskrivs JSON-element som är specifika för HTTP-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ | Den **typ** egenskapen måste anges till **Http**. | Ja |
| url | Den grundläggande Webbadressen till webbservern. | Ja |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är **anonym**, **grundläggande**, **sammanfattad**, **Windows**, och **ClientCertificate**. <br><br> Referera till senare avsnitt i den här artikeln för fler egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |
| enableServerCertificateValidation | Anger om du vill aktivera server SSL-certifikatsverifiering om källan är en HTTPS-webbserver. När HTTPS-server använder ett självsignerat certifikat, Ställ in på **FALSKT**. | Nej<br /> (standardvärdet är **SANT**) |
| gatewayName | Namnet på Data Management Gateway-instans för att ansluta till en lokal HTTP-källa. | Ja, om du kopierar data från en lokal HTTP-källa |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till HTTP-slutpunkt. Värdet är automatiskt skapade när du konfigurerar autentiseringsinformation i guiden Kopiera eller genom att använda den **ClickOnce** dialogrutan. | Nej<br /> (gäller endast när du kopierar data från en lokal HTTP-server) |

Mer information om hur du anger autentiseringsuppgifter för en datakälla för den lokala HTTP-anslutningen finns i [flytta data mellan lokala källor och molnet med hjälp av Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Med hjälp av grundläggande, sammanfattad eller Windows-autentisering

Ange **authenticationType** till **grundläggande**, **sammanfattad**, eller **Windows**. Förutom de allmänna HTTP Kopplingsegenskaperna som beskrivs i föregående avsnitt, ange följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användarnamnet du använder för att få åtkomst till HTTP-slutpunkt. | Ja |
| lösenord | Lösenordet för användaren (**användarnamn**). | Ja |

**Exempel: Med grundläggande, sammanfattad eller Windows-autentisering**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Med hjälp av ClientCertificate autentisering

Om du vill använda grundläggande autentisering, ange **authenticationType** till **ClientCertificate**. Förutom de allmänna HTTP Kopplingsegenskaperna som beskrivs i föregående avsnitt, ange följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| embeddedCertData | Base64-kodade innehåll binära data i PFX-filen. | Ange antingen **embeddedCertData** eller **certThumbprint** |
| certThumbprint | Tumavtryck för certifikatet som installerades på gateway-datorns certifikatarkiv. Gäller endast när du kopierar data från en lokal HTTP-källa. | Ange antingen **embeddedCertData** eller **certThumbprint** |
| lösenord | Lösenordet som är associerat med certifikatet. | Nej |

Om du använder **certThumbprint** för autentisering och certifikatet är installerat i det personliga arkivet i den lokala datorn kan bevilja läsbehörighet till gateway-tjänsten:

1. Öppna Microsoft Management Console (MMC). Lägg till den **certifikat** snapin-modul som riktar sig mot **lokala**.
2. Expandera **certifikat** > **personliga**, och välj sedan **certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj sedan **alla uppgifter** >**hantera privata nycklar**.
3. På den **Security** fliken, lägga till användarkontot som Data Management Gateway-värdtjänsten körs under, med läsbehörighet till certifikatet.  

**Exempel: Använder ett klientcertifikat**

Den här länkade tjänsten länkar din data factory på en lokal webbserver för HTTP. Den använder ett klientcertifikat som är installerad på den dator som har Data Management Gateway installerad.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**Exempel: Använder ett klientcertifikat i en fil**

Den här länkade tjänsten länkar din data factory på en lokal webbserver för HTTP. Den använder en klientcertifikatfilen på den dator som har Data Management Gateway installerad.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Vissa delar av en datauppsättning JSON-fil som struktur, tillgänglighet och princip, är liknande för alla datauppsättningstyper av (Azure SQL Database, Azure Blob storage, Azure Table storage).

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [skapar datauppsättningar](data-factory-create-datasets.md).

Den **typeProperties** är olika för varje typ av datauppsättning. Den **typeProperties** avsnittet innehåller information om var data i datalagret. Den **typeProperties** för en datauppsättning för den **Http** typ har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** av datauppsättningen måste anges till **Http**. | Ja |
| relativeUrl | En relativ URL till den resurs som innehåller data. Om sökvägen inte anges används den URL som anges i länkade tjänstedefinition. <br><br> Du kan använda för att skapa en dynamisk URL [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md). Exempel: **relativeUrl**: **$$Text.Format (”/ min/rapporten? månad = {0: yyyy}-{0:MM} & fmt = csv”, SliceStart)**. | Nej |
| requestMethod | HTTP-metoden. Tillåtna värden är **hämta** och **POST**. | Nej <br />(standardvärdet är **hämta**) |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | Nej |
| RequestBody | Brödtexten för HTTP-begäran. | Nej |
| Format | Om du vill *hämta data från en HTTP-slutpunkt som – är* utan parsning den hoppar du över den **format** inställningen. <br><br> Om du vill parsa HTTP-svarsinnehåll vid kopiering av följande formattyper av som stöds: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, och **ParquetFormat**. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-format](data-factory-supported-file-and-compression-formats.md#parquet-format). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

**Exempel: Med hjälp av metoden GET (standard)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Exempel: Med POST-metoden**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Egenskaper som namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapa pipelines](data-factory-create-pipelines.md). 

Egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens varierar med varje aktivitetstyp av. För en Kopieringsaktivitet varierar egenskaper beroende på vilka typer av källor och mottagare.

För närvarande när källan i Kopieringsaktiviteten är av den **HttpSource** skriver du följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| httpRequestTimeout | Timeout (i **TimeSpan** värde) för HTTP-begäran att få svar. Tidsgränsen är det för att få svar timeout inte att läsa svarsdata. | Nej<br />(standardvärde: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat de format som stöds

Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) mer.

## <a name="json-examples"></a>JSON-exempel

I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplen visar hur du kopierar data från en HTTP-källa till Azure Blob storage. Men du kan kopiera data *direkt* från källor till någon av mottagare [som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopieringsaktivitet i Azure Data Factory.

**Exempel: Kopieringsdata från en HTTP-källa till Azure Blob storage**

Data Factory-lösning för det här exemplet innehåller följande Data Factory-entiteter:

*   En länkad tjänst av typen [HTTP](#linked-service-properties).
*   En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Indata [datauppsättning](data-factory-create-datasets.md) av typen [Http](#dataset-properties).
*   Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   En [pipeline](data-factory-create-pipelines.md) som har en Kopieringsaktivitet som använder [HttpSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en HTTP-källa till en Azure-blob varje timme. JSON-egenskaper som används i exemplen beskrivs i avsnitten som följer exemplen.

### <a name="http-linked-service"></a>HTTP-länkade tjänsten

Det här exemplet använder HTTP-länkade tjänsten med anonym autentisering. Se [HTTP-länkade tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Länkad Azure storage-tjänst

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP-datauppsättningen för indata

Ange **externa** till **SANT** informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i data factory.

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blob

Data skrivs till en ny blob varje timme (**frekvens**: **timme**, **intervall**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Pipeline som använder en Kopieringsaktivitet

Det innehåller en Kopieringsaktivitet som är konfigurerad för att använda uppsättningar för indata och utdata för pipelinen. Kopieringsaktivitet är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **HttpSource** och **mottagare** är **BlobSink**.

Lista över egenskaper som **HttpSource** stöder finns i [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Om du vill mappa kolumner från en källdatauppsättning till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering

Mer information om viktiga faktorer som påverkar prestandan för dataflytt (Kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den, finns det [Kopieringsaktiviteten prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md).