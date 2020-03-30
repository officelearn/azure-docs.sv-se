---
title: Felsöka vanliga problem med sökindexerare
titleSuffix: Azure Cognitive Search
description: Åtgärda fel och vanliga problem med indexerare i Azure Cognitive Search, inklusive datakällanslutning, brandvägg och dokument som saknas.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190920"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Felsöka vanliga indexeringsproblem i Azure Cognitive Search

Indexerare kan stöta på ett antal problem när du indexerar data till Azure Cognitive Search. De viktigaste kategorierna av fel är:

* [Ansluta till en datakälla eller andra resurser](#connection-errors)
* [Bearbetning av dokument](#document-processing-errors)
* [Dokumentintag till ett index](#index-errors)

## <a name="connection-errors"></a>Anslutningsfel

> [!NOTE]
> Indexerare har begränsat stöd för åtkomst till datakällor och andra resurser som skyddas av Azure-nätverksäkerhetsmekanismer. För närvarande kan indexerare endast komma åt datakällor via motsvarande IP-adressintervallbegränsningsmekanismer eller NSG-regler när det är tillämpligt. Information om hur du kommer åt varje datakälla som stöds finns nedan.
>
> Du kan ta reda på IP-adressen för din söktjänst genom `<your-search-service-name>.search.windows.net`att pinga dess fullt kvalificerade domännamn (t.ex.. ).
>
> Du kan ta reda på `AzureCognitiveSearch` IP-adressintervallet [för servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) genom att antingen använda [nedladdningsbara JSON-filer](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) eller via [Api för Service Tag Discovery](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). IP-adressintervallet uppdateras varje vecka.

### <a name="configure-firewall-rules"></a>Konfigurera brandväggsregler

Azure Storage, CosmosDB och Azure SQL tillhandahåller en konfigurerbar brandvägg. Det finns inget specifikt felmeddelande när brandväggen är aktiverad. Normalt är brandväggsfel generiska `The remote server returned an error: (403) Forbidden` `Credentials provided in the connection string are invalid or have expired`och ser ut som eller .

Det finns 2 alternativ för att tillåta indexerare att komma åt dessa resurser i en sådan instans:

* Inaktivera brandväggen genom att tillåta åtkomst från **alla nätverk** (om möjligt).
* Du kan också tillåta åtkomst för IP-adressen för söktjänsten `AzureCognitiveSearch` och IP-adressintervallet för [servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) i brandväggsreglerna för din resurs (begränsning av IP-adressintervall).

Information om hur du konfigurerar IP-adressintervallbegränsningar för varje datakälltyp finns i följande länkar:

* [Azure-lagring](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Begränsning**: Som anges i dokumentationen ovan för Azure Storage fungerar IP-adressintervallbegränsningar endast om söktjänsten och ditt lagringskonto finns i olika regioner.

Azure-funktioner (som kan användas som en [anpassad webb-API-färdighet)](cognitive-search-custom-skill-web-api.md)stöder också [IP-adressbegränsningar](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions). Listan över IP-adresser som ska konfigureras är IP-adressen för `AzureCognitiveSearch` söktjänsten och IP-adressintervallet för servicetag.

Information om hur du kommer åt data i SQL-servern på en virtuell Azure-dator beskrivs [här](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurera NSG-regler (Network Security Group)

När du använder data i en SQL-hanterad instans, eller när en Azure VM används som webbtjänst URI för en [anpassad webb-API-färdighet,](cognitive-search-custom-skill-web-api.md)behöver kunderna inte vara berörda av specifika IP-adresser.

I sådana fall kan den virtuella Azure-datorn eller sql-hanterade instansen konfigureras för att finnas i ett virtuellt nätverk. Sedan kan en nätverkssäkerhetsgrupp konfigureras för att filtrera den typ av nätverkstrafik som kan flöda in och ut ur det virtuella nätverkets undernät och nätverksgränssnitt.

Service-taggen `AzureCognitiveSearch` kan användas direkt i de inkommande [NSG-reglerna](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) utan att behöva slå upp sitt IP-adressintervall.

Mer information om hur du kommer åt data i en SQL-hanterad instans beskrivs [här](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indexering" är inte aktiverat

Azure Cognitive Search har ett implicit beroende av Cosmos DB-indexering. Om du inaktiverar automatisk indexering i Cosmos DB returnerar Azure Cognitive Search ett lyckat tillstånd, men misslyckas med att indexera behållarinnehållet. Instruktioner om hur du kontrollerar inställningar och aktiverar indexering finns [i Hantera indexering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Fel i dokumentbearbetning

### <a name="unprocessable-or-unsupported-documents"></a>Dokument som inte kan bearbetas eller inte stöds

Blob-indexerardokumenten [som dokumentformat uttryckligen stöds.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Ibland innehåller en blob-lagringsbehållare dokument som inte stöds. Andra gånger kan det finnas problematiska dokument. Du kan undvika att stoppa indexeraren på dessa dokument genom [att ändra konfigurationsalternativ:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Dokumentinnehåll som saknas saknas

Blob-indexeraren [hittar och extraherar text från blobbar i en behållare](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Några problem med att extrahera text är:

* Dokumentet innehåller bara skannade bilder. PDF-blobbar som har innehåll som inte är text, till exempel skannade bilder (JPGs), ger inte resultat i en standardpipelare för blobindexering. Om du har bildinnehåll med textelement kan du använda [kognitiv sökning](cognitive-search-concept-image-scenarios.md) för att söka efter och extrahera texten.
* Blob-indexeraren är konfigurerad för att bara indexera metadata. Om du vill extrahera innehåll måste blobindexeraren konfigureras för att [extrahera både innehåll och metadata:](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Indexfel

### <a name="missing-documents"></a>Dokument som saknas

Indexerare hittar dokument från en [datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Ibland verkar ett dokument från datakällan som borde ha indexerats saknas i ett index. Det finns ett par vanliga orsaker till att dessa fel kan inträffa:

* Dokumentet har inte indexerats. Kontrollera om det finns en lyckad indexeringskörning i portalen.
* Dokumentet uppdaterades efter indexeringskörningen. Om indexeraren har ett [schema](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)körs den så småningom och hämtar dokumentet.
* Frågan [query](/rest/api/searchservice/create-data-source) som anges i datakällan utesluter dokumentet. Indexerare kan inte indexera dokument som inte ingår i datakällan.
* [Fältmappningar](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) eller [AI-anrikning](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) har ändrat dokumentet och det ser annorlunda ut än förväntat.
* Använd [uppslagsdokument-API:et](https://docs.microsoft.com/rest/api/searchservice/lookup-document) för att hitta dokumentet.
