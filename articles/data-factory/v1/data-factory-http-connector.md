---
title: Flytta data från en källa för HTTP - Azure | Microsoft Docs
description: Läs mer om hur du flyttar data från en lokal eller en källa för HTTP-av moln med Azure Data Factory.
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
ms.openlocfilehash: 16b181631d8d91ad8137e57564792789903bccf2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621631"
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Flytta data från en HTTP-datakälla med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-http-connector.md)
> * [Version 2 – förhandsversion](../connector-http.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [HTTP-anslutningen i V2](../connector-http.md).


Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en HTTP-slutpunkt på lokalt/i molnet till datakällan stöds sink. Den här artikeln bygger på den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel som ger en allmän översikt över dataflyttning kopieringsaktiviteten och listan över datakällor som stöds som källor/sänkor.

Data factory stöder för närvarande endast flytta data från en HTTP-källa till andra databaser, men inte flytta data från andra data lagrar till en HTTP-mål.

## <a name="supported-scenarios-and-authentication-types"></a>Scenarier som stöds och typer av autentisering
Du kan använda den här HTTP-anslutningen för att hämta data från **både till molnet och lokala HTTP/s-slutpunkt** med hjälp av HTTP **hämta** eller **POST** metod. Följande autentiseringstyper av som stöds: **anonym**, **grundläggande**, **sammanfattad**, **Windows**, och **ClientCertificate**. Observera skillnaden mellan den här kopplingen och [Web tabell connector](data-factory-web-table-connector.md) är: används att extrahera innehållet från webbsidan HTML.

När du kopierar data från en lokal HTTP-slutpunkt, måste du installera en Data Management Gateway i lokal miljö/Azure VM. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikeln innehåller information om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar en gateway.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en HTTP-datakälla med hjälp av olika verktyg/API: er.

- Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. JSON-exempel att kopiera data från http-källa till Azure Blob Storage finns [JSON-exempel](#json-examples) avsnitt i den här artikeln.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller beskrivning för JSON-element som är specifika för HTTP länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ | Egenskapen type måste anges till: `Http`. | Ja |
| url | Bas-URL till webbservern | Ja |
| AuthenticationType | Anger vilken autentiseringstyp. Tillåtna värden är: **anonym**, **grundläggande**, **sammanfattad**, **Windows**, **ClientCertificate**. <br><br> Avse respektive avsnitt under den här tabellen på fler egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |
| enableServerCertificateValidation | Ange om du vill aktivera server SSL-certifikatsverifiering om datakällan är HTTPS-webbserver. När HTTPS-server använder självsignerade certifikat kan du ange detta till false. | Nej, standard är SANT |
| gatewayName | Namnet på Data Management Gateway för att ansluta till en lokal http-källa. | Ja om du kopierar data från en lokal http-källa. |
| encryptedCredential | Krypterade autentiseringsuppgifter till HTTP-slutpunkten. Genereras automatiskt när du konfigurerar autentiseringsinformation i guiden Kopiera eller ClickOnce popup-dialogruta. | Nej. Gäller bara när du kopierar data från en lokal HTTP-server. |

Se [flytta data mellan lokala källor och molnet med Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) mer information om hur du anger autentiseringsuppgifter för datakälla för lokala HTTP-anslutningen.

### <a name="using-basic-digest-or-windows-authentication"></a>Med hjälp av grundläggande, sammanfattad eller Windows-autentisering

Ange `authenticationType` som `Basic`, `Digest`, eller `Windows`, och ange följande egenskaper utöver HTTP-anslutningen generiska de introduceras ovan:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användarnamn för att få åtkomst till HTTP-slutpunkten. | Ja |
| lösenord | Lösenord för användare (användarnamn). | Ja |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exempel: med grundläggande, sammanfattad eller Windows-autentisering

```JSON
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

Om du vill använda grundläggande autentisering, `authenticationType` som `ClientCertificate`, och ange följande egenskaper utöver HTTP-anslutningen generiska de introduceras ovan:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| embeddedCertData | Base64-kodade innehåll binära data i filen Personal Information Exchange (PFX). | Ange antingen det `embeddedCertData` eller `certThumbprint`. |
| certThumbprint | Tumavtryck för certifikat som har installerats på gateway-datorns certifikatarkiv. Gäller bara när du kopierar data från en lokal http-källa. | Ange antingen det `embeddedCertData` eller `certThumbprint`. |
| lösenord | Lösenordet för certifikatet. | Nej |

Om du använder `certThumbprint` för autentisering och certifikatet har installerats i det personliga arkivet i den lokala datorn, måste du bevilja läsbehörighet till gatewaytjänsten:

1. Starta Microsoft Management Console (MMC). Lägg till den **certifikat** snapin-modulen som riktar sig den **lokal dator**.
2. Expandera **certifikat**, **personliga**, och klicka på **certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj **alla aktiviteter**->**hantera privata nycklar...**
3. På den **säkerhet** Lägg till användarkontot under vilken Data Management Gateway-värdtjänsten körs med läsbehörighet till certifikatet.  

#### <a name="example-using-client-certificate"></a>Exempel: använder klientcertifikat
Det här länkade tjänsten länkar din data factory på en lokal webbserver för HTTP. Den använder ett klientcertifikat som är installerade på datorn med Data Management Gateway är installerad.

```JSON
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

#### <a name="example-using-client-certificate-in-a-file"></a>Exempel: använder klientcertifikat i en fil
Det här länkade tjänsten länkar din data factory på en lokal webbserver för HTTP. Det använder en klient-certifikatfil på datorn med Data Management Gateway är installerad.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. TypeProperties avsnittet för dataset av typen **Http** har följande egenskaper

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Ange vilken typ av datauppsättningen. måste anges till `Http`. | Ja |
| relativeUrl | En relativ URL till den resurs som innehåller data. Om sökvägen inte anges används den URL som angavs i definitionen länkade tjänsten. <br><br> Du kan använda för att skapa dynamiska URL [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md), t.ex. ”relativeUrl” ”: $$Text.Format ('/ min/rapporten? månad = {0:yyyy}-{0:MM} & fmt = csv', SliceStart)”. | Nej |
| requestMethod | HTTP-metod. Tillåtna värden är **hämta** eller **efter**. | Nej. Standardvärdet är `GET`. |
| additionalHeaders | Ytterligare HTTP-begärans sidhuvud. | Nej |
| requestBody | Brödtext för HTTP-begäran. | Nej |
| Format | Om du vill bara **hämta data från HTTP-slutpunkt som-är** hoppa över den här formatinställningar utan parsning den. <br><br> Om du vill att parsa innehållet i HTTP-svar vid kopiering, stöds följande format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. |Nej |
| Komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

### <a name="example-using-the-get-default-method"></a>Exempel: med metoden GET (standard)

```JSON
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

### <a name="example-using-the-post-method"></a>Exempel: med POST-metoden

```JSON
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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten å andra sidan varierar med varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

För närvarande när datakällan i en Kopieringsaktivitet är av typen **HttpSource**, följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| httpRequestTimeout | Tidsgräns (TimeSpan) för HTTP-begäran att få svar. Tidsgränsen är det inget svar timeout inte att läsa svarsdata. | Nej. Standardvärde: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimering format som stöds
Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikeln för information.

## <a name="json-examples"></a>JSON-exempel
I följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från http-källa till Azure Blob Storage. Dock datan kan kopieras **direkt** från alla källor till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Exempel: Kopiera data till Azure Blob Storage från http-källa
Data Factory-lösningen för det här exemplet innehåller följande Data Factory-enheter:

1. En länkad tjänst av typen [HTTP](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [Http](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [HttpSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en HTTP-datakälla till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

### <a name="http-linked-service"></a>HTTP-länkad tjänst
Det här exemplet använder HTTP-länkade tjänsten med anonym autentisering. Se [HTTP länkade tjänsten](#linked-service-properties) avsnittet för olika typer av autentisering som du kan använda.

```JSON
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

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Inkommande HTTP-datamängd
Ange **externa** till **SANT** informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```JSON
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

### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1).

```JSON
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

### <a name="pipeline-with-copy-activity"></a>Pipeline med kopieringsaktiviteten

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **HttpSource** och **sink** är inställd på **BlobSink**.

Se [HttpSource](#copy-activity-properties) lista över egenskaper som stöds av HttpSource.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
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
> Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
