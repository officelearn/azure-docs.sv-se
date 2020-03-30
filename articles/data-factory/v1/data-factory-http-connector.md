---
title: Flytta data från en HTTP-källa - Azure
description: Lär dig hur du flyttar data från en lokal eller moln-HTTP-källa med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e668f44bbc3d2e381edeb80c568a41355584a4ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260428"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Flytta data från en HTTP-källa med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-http-connector.md)
> * [Version 2 (aktuell version)](../connector-http.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Azure Data Factory-tjänsten läser du [HTTP-anslutningsappen i V2](../connector-http.md).


I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att flytta data från en lokal eller moln-HTTP-slutpunkt till ett diskbänksdatalager som stöds. Den här artikeln bygger på [Flytta data med hjälp av Kopiera aktivitet](data-factory-data-movement-activities.md), som ger en allmän översikt över data förflyttning med hjälp av Kopiera aktivitet. I artikeln visas också de datalager som Kopiera aktivitet stöder som källor och sänkor.

Data Factory stöder för närvarande endast att flytta data från en HTTP-källa till andra datalager. Det stöder inte att flytta data från andra datalager till ett HTTP-mål.

## <a name="supported-scenarios-and-authentication-types"></a>Scenarier och autentiseringstyper som stöds

Du kan använda den här HTTP-anslutningen för att hämta data från *både ett moln och en lokal HTTP/S-slutpunkt* med hjälp av HTTP **GET-** eller POST-metoderna. **POST** Följande autentiseringstyper stöds: **Anonym**, **Basic**, **Digest**, **Windows**och **ClientCertificate**. Observera skillnaden mellan den här kopplingen och [webbtabellkopplingen](data-factory-web-table-connector.md). Webbtabellkopplingen extraherar tabellinnehåll från en HTML-webbsida.

När du kopierar data från en lokal HTTP-slutpunkt måste du installera Data Management Gateway i den lokala miljön eller i en Azure VM.When you copy data from an on-premises HTTP endpoint, you must install Data Management Gateway in the on-premises environment or in an Azure VM. Mer information om Data Management Gateway och steg-för-steg-instruktioner om hur du konfigurerar gatewayen finns i [Flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Komma igång

Du kan skapa en pipeline som har en kopieringsaktivitet för att flytta data från en HTTP-källa med hjälp av olika verktyg eller API:er:

- Det enklaste sättet att skapa en pipeline är att använda guiden Kopiera data. En snabb genomgång av hur du skapar en pipeline med hjälp av guiden Kopiera data finns i [Självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md).

- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, en **Azure Resource Manager-mall**, **.NET API**eller REST **API**. Stegvisa instruktioner om hur du skapar en pipeline som har en kopieringsaktivitet finns i [självstudien Kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). För JSON-exempel som kopierar data från en HTTP-källa till Azure Blob-lagring läser du [JSON-exempel](#json-examples).

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

I följande tabell beskrivs JSON-element som är specifika för den HTTP-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ | **Egenskapen Type** måste anges till **Http**. | Ja |
| url | Bas-URL:en till webbservern. | Ja |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är **Anonym**, **Basic,** **Digest**, **Windows**och **ClientCertificate**. <br><br> Mer information om följande avsnitt finns i senare avsnitt i den här artikeln för fler egenskaper och JSON-exempel för dessa autentiseringstyper. | Ja |
| aktiveraServerCertificateValidation | Anger om server-SSL-certifikatvalidering ska aktiveras om källan är en HTTPS-webbserver. När HTTPS-servern använder ett självsignerat certifikat anger du att det är **falskt**. | Inga<br /> (standardvärdet är **sant)** |
| gatewayName (gatewayName) | Namnet på den Data Management Gateway-instans som ska användas för att ansluta till en lokal HTTP-källa. | Ja, om du kopierar data från en lokal HTTP-källa |
| krypteradKnuren | Den krypterade autentiseringsåtkomsten för åtkomst till HTTP-slutpunkten. Värdet skapas automatiskt när du konfigurerar autentiseringsinformationen i kopieringsguiden eller med hjälp av dialogrutan **ClickOnce.** | Inga<br /> (gäller endast när du kopierar data från en lokal HTTP-server) |

Mer information om hur du anger autentiseringsuppgifter för en lokal HTTP-anslutningsdatakälla finns i [Flytta data mellan lokala källor och molnet med hjälp av Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Använda autentisering av grundläggande, sammanfattning eller Windows

Ange **authenticationType** till **Basic,** **Digest**eller **Windows**. Förutom de allmänna HTTP-kopplingsegenskaperna som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| userName | Det användarnamn som ska användas för att komma åt HTTP-slutpunkten. | Ja |
| password | Lösenordet för användaren (**användarnamn**). | Ja |

**Exempel: Använda autentisering av grundläggande, sammanfattning eller Windows**

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

### <a name="using-clientcertificate-authentication"></a>Använda clientcertificate-autentisering

Om du vill använda grundläggande autentisering anger du **authenticationType** till **ClientCertificate**. Förutom de allmänna HTTP-kopplingsegenskaperna som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| inbäddadeCertData | Det Base64-kodade innehållet i binära data i PFX-filen. | Ange antingen **embeddedCertData** eller **certThumbprint** |
| certThumbprint | Tumavtrycket för certifikatet som installerades på gateway-datorns cert-arkiv. Använd bara när du kopierar data från en lokal HTTP-källa. | Ange antingen **embeddedCertData** eller **certThumbprint** |
| password | Lösenordet som är associerat med certifikatet. | Inga |

Om du använder **certThumbprint** för autentisering och certifikatet är installerat i den lokala datorns personliga arkiv beviljar du läsbehörighet till gateway-tjänsten:

1. Öppna Microsoft Management Console (MMC). Lägg till snapin-modulen **Certifikat** som är inriktad på **lokal dator**.
2. Expandera **Certifikat** > **Personal**och välj sedan **Certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj sedan **Alla uppgifter** >**hantera privata nycklar**.
3. Lägg till användarkontot som värdtjänsten för datahantering gateway körs under på fliken **Säkerhet,** med läsbehörighet till certifikatet.  

**Exempel: Använda ett klientcertifikat**

Den här länkade tjänsten länkar datafabriken till en lokal HTTP-webbserver. Den använder ett klientcertifikat som är installerat på datorn som har Data Management Gateway installerat.

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

**Exempel: Använda ett klientcertifikat i en fil**

Den här länkade tjänsten länkar datafabriken till en lokal HTTP-webbserver. Den använder en klientcertifikatfil på datorn som har Data Management Gateway installerat.

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

Vissa avsnitt i en datauppsättning JSON-fil, till exempel struktur, tillgänglighet och princip, är liknande för alla datauppsättningstyper (Azure SQL Database, Azure Blob storage, Azure Table storage).

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Skapa datauppsättningar](data-factory-create-datasets.md).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning. Avsnittet **typeProperties** innehåller information om platsen för data i datalagret. Avsnittet **typeProperties** för en datauppsättning av **typen Http** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens **typ** måste anges till **Http**. | Ja |
| släktingUrl | En relativ URL till resursen som innehåller data. När sökvägen inte anges används bara webbadressen som anges i den länkade tjänstdefinitionen. <br><br> Om du vill skapa en dynamisk URL kan du använda [datafabriksfunktioner och systemvariabler](data-factory-functions-variables.md). Exempel: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**. | Inga |
| begäranMethod | HTTP-metoden. Tillåtna värden är **GET** och **POST**. | Inga <br />(standard är **GET)** |
| additionalHeaders | Ytterligare HTTP-begäranden. | Inga |
| begäranKropp | Brödtexten för HTTP-begäran. | Inga |
| format | Om du vill *hämta data från en HTTP-slutpunkt som den är* utan att tolka dem hoppar du över **formatinställningen.** <br><br> Om du vill tolka HTTP-svarsinnehållet under kopiering stöds följande formattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Mer information finns i [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parkettformat](data-factory-supported-file-and-compression-formats.md#parquet-format). |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds: **Optimal och** **snabbast**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

**Exempel: Använda metoden GET (standard)**

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

**Exempel: Använda metoden POST**

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

Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Skapa pipelines](data-factory-create-pipelines.md). 

Egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar beroende på varje aktivitetstyp. För en kopieringsaktivitet varierar egenskaperna beroende på vilka typer av källor och sänkor som finns.

När källan i Kopieringsaktivitet är av typen **HttpSource** stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| httpRequestTimeout (På) | Timeout **(TimeSpan-värdet)** för HTTP-begäran för att få ett svar. Det är tidsgränsen för att få ett svar, inte timeout för att läsa svarsdata. | Inga<br />(standardvärde: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds

Mer [information finns i fil- och komprimeringsformat i Azure Data Factory.](data-factory-supported-file-and-compression-formats.md)

## <a name="json-examples"></a>JSON exempel

Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplen visar hur du kopierar data från en HTTP-källa till Azure Blob-lagring. Data kan dock kopieras *direkt* från någon av källorna till någon av de diskhoar [som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopiera aktivitet i Azure Data Factory.

**Exempel: Kopiera data från en HTTP-källa till Azure Blob-lagring**

Data Factory-lösningen för det här exemplet innehåller följande Data Factory-entiteter:

*   En länkad tjänst av typen [HTTP](#linked-service-properties).
*   En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   En [indatauppsättning](data-factory-create-datasets.md) av typen [Http](#dataset-properties).
*   En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   En [pipeline](data-factory-create-pipelines.md) som har en kopieringsaktivitet som använder [HttpSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en HTTP-källa till en Azure-blob varje timme. JSON-egenskaperna som används i dessa exempel beskrivs i avsnitt som följer proverna.

### <a name="http-linked-service"></a>HTTP-länkad tjänst

I det här exemplet används den HTTP-länkade tjänsten med anonym autentisering. Se [HTTP-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

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

### <a name="azure-storage-linked-service"></a>Azure-lagringslänkade tjänst

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

### <a name="http-input-dataset"></a>HTTP-indatauppsättning

Om du ställer **in externt** för **true** informerar datafabriken om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

Data skrivs till en ny blob varje timme **(frekvens:** **timme,** **intervall:** **1**).

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

### <a name="pipeline-that-uses-a-copy-activity"></a>Pipeline som använder en kopieringsaktivitet

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningarna. Kopieringsaktiviteten är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **HttpSource** och **sink-typen** är inställd på **BlobSink**.

Information om listan över egenskaper som **HttpSource** stöder finns i [HttpSource](#copy-activity-properties).

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
> Information om hur du mappar kolumner från en källdatauppsättning till kolumner från en sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering

Mer information om viktiga faktorer som påverkar prestanda för datarörelser (kopiera aktivitet) i Azure Data Factory och olika sätt att optimera den finns i [prestanda- och justeringsguiden för kopieringsaktivitet](data-factory-copy-activity-performance.md).
