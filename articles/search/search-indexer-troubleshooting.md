---
title: Felsök vanliga problem med Sök indexering
titleSuffix: Azure Cognitive Search
description: Åtgärda fel och vanliga problem med indexerare i Azure Kognitiv sökning, inklusive anslutning av data källor, brand väggar och saknade dokument.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793628"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Felsöka vanliga indexerings problem i Azure Kognitiv sökning

Indexerare kan köra ett antal problem när de indexerar data i Azure Kognitiv sökning. De huvudsakliga kategorier av felen är:

* [Ansluta till en data Källa](#data-source-connection-errors)
* [Dokument bearbetning](#document-processing-errors)
* [Dokument inmatning till ett index](#index-errors)

## <a name="data-source-connection-errors"></a>Anslutnings fel för data Källa

### <a name="blob-storage"></a>Blob-lagring

#### <a name="storage-account-firewall"></a>Brand vägg för lagrings konto

Azure Storage tillhandahåller en konfigurerbar brand vägg. Som standard inaktive ras brand väggen så att Azure Kognitiv sökning kan ansluta till ditt lagrings konto.

Det finns inget speciellt fel meddelande när brand väggen är aktive rad. Vanligt vis ser brand Väggs fel ut som `The remote server returned an error: (403) Forbidden`.

Du kan kontrol lera att brand väggen är aktive rad i [portalen](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). Den enda lösning som stöds är att inaktivera brand väggen genom att välja att tillåta åtkomst från [alla nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Om indexeraren inte har en ansluten färdigheter _kan_ du försöka [lägga till ett undantag](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) för IP-adresserna för Sök tjänsten. Det här scenariot stöds dock inte och är inte garanterat att det fungerar.

Du kan ta reda på IP-adressen för din Sök tjänst genom att pinga dess FQDN (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Indexering är inte aktiverat

Azure Kognitiv sökning har ett implicit beroende av Cosmos DB indexering. Om du inaktiverar automatisk indexering i Cosmos DB, returnerar Azure Kognitiv sökning ett lyckat tillstånd, men det går inte att indexera container innehåll. Instruktioner för hur du kontrollerar inställningar och aktiverar indexering finns i [Hantera indexering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Fel vid dokument bearbetning

### <a name="unprocessable-or-unsupported-documents"></a>Dokument som inte kan hanteras eller som inte stöds

BLOB-indexeraren [dokument vars dokument format stöds explicit.](search-howto-indexing-azure-blob-storage.md#SupportedFormats).. Ibland innehåller en Blob Storage-behållare dokument som inte stöds. Andra gånger kan det finnas problematiska dokument. Du kan undvika att stoppa din indexerare på dessa dokument genom att [ändra konfigurations alternativ](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Dokument innehåll saknas

BLOB-indexeraren [söker efter och extraherar text från blobbar i en behållare](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Vissa problem med extrahering av text är:

* Dokumentet innehåller bara skannade bilder. PDF-blobar som inte har text innehåll, till exempel scannade bilder (JPGs), genererar inte resultat i en standard-BLOB för BLOB-indexering. Om du har bild innehåll med text element kan du använda [kognitiv sökning](cognitive-search-concept-image-scenarios.md) för att hitta och extrahera texten.
* BLOB-indexeraren har kon figurer ATS för att endast indexera metadata. För att extrahera innehåll måste BLOB-indexeraren konfigureras för att [extrahera både innehåll och metadata](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Index fel

### <a name="missing-documents"></a>Dokument som saknas

Indexerare hittar dokument från en [data källa](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Ibland visas ett dokument från data källan som har indexerats för att saknas i ett index. Det finns några vanliga orsaker till att dessa fel inträffar:

* Dokumentet har inte indexerats. Kontrol lera portalen för att köra en lyckad indexerare.
* Dokumentet uppdaterades när indexeraren kördes. Om indexeraren är enligt ett [schema](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), kommer den att köras igen och hämta dokumentet.
* [Frågan](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) som anges i data källan utesluter dokumentet. Indexerare kan inte indexera dokument som inte är en del av data källan.
* [Fält mappningar](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) eller [AI-berikning](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) har ändrat dokumentet och det ser annorlunda ut än förväntat.
* Använd [Sök-API: et](https://docs.microsoft.com/rest/api/searchservice/lookup-document) för att hitta ditt dokument.
