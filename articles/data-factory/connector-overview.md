---
title: Översikt över Azure Data Factory anslutning
description: Lär dig om de anslutningar som stöds i Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9b346c24b6a363e66e56f6c16b70058df3864c88
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945844"
---
# <a name="azure-data-factory-connector-overview"></a>Översikt över Azure Data Factory anslutning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory stöder följande data lager och format via kopiera, data flöde, slå upp, hämta metadata och ta bort aktiviteter. Klicka på varje data lager för att lära dig vilka funktioner som stöds och motsvarande konfigurationer i detalj.

## <a name="supported-data-stores"></a>Datalager som stöds

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integrera med fler data lager

Azure Data Factory kan uppnå en bredare uppsättning data lager än den lista som anges ovan. Om du behöver flytta data till/från ett data lager som inte finns i listan Azure Data Factory inbyggd koppling, finns här några utöknings bara alternativ:
- För databasen och informations lagret kan du vanligt vis hitta en motsvarande ODBC-drivrutin, med vilken du kan använda [allmän ODBC-anslutning](connector-odbc.md).
- För SaaS-program:
    - Om den tillhandahåller RESTful-API: er kan du använda [Generic rest Connector](connector-rest.md).
    - Om den innehåller OData-feed kan du använda [allmän OData-anslutning](connector-odata.md).
    - Om det innehåller SOAP-API: er kan du använda [allmän HTTP-anslutning](connector-http.md).
    - Om den har ODBC-drivrutin kan du använda [allmän ODBC-anslutning](connector-odbc.md).
- För andra kan du kontrol lera om du kan läsa in data eller visa data som alla data lager som stöds av ADF, t. ex. Azure Blob/File/FTP/SFTP/etc, och sedan låta ADF Hämta därifrån. Du kan anropa anpassad data inläsnings funktion via [Azure Function](control-flow-azure-function-activity.md), [anpassad aktivitet](transform-data-using-dotnet-custom-activity.md), [Databricks](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md), [webb aktivitet](control-flow-web-activity.md)osv.

## <a name="supported-file-formats"></a>Filformat som stöds

Azure Data Factory stöder följande fil format. Se varje artikel för formatbaserade inställningar.

- [Avro-format](format-avro.md)
- [Binärt format](format-binary.md)
- [Common Data Model-formatet](format-common-data-model.md)
- [Avgränsat textformat](format-delimited-text.md)
- [Deltaformat](format-delta.md)
- [Excel-format](format-excel.md)
- [JSON-format](format-json.md)
- [ORC-format](format-orc.md)
- [Parquet-format](format-parquet.md)
- [XML-format](format-xml.md)

## <a name="next-steps"></a>Nästa steg

- [Kopierings aktivitet](copy-activity-overview.md)
- [Mappa dataflöden](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)
