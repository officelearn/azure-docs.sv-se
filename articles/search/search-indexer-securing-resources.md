---
title: Åtkomst till indexerings resurser på ett säkert sätt
titleSuffix: Azure Cognitive Search
description: Konceptuell översikt över säkerhets alternativen på nätverks nivå för Azure Data Access av indexerare i Azure Kognitiv sökning.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 5075c4858f9584cb19442e19d9009d46d0e00ff8
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463718"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>Indexerare får åtkomst till data källor med hjälp av funktioner i Azure Network Security

Azure Kognitiv sökning indexerare kan göra utgående anrop till olika Azure-resurser under körningen. Den här artikeln förklarar koncepten bakom indexerare åtkomst till resurser när dessa resurser skyddas av IP-brandväggar, privata slut punkter och andra säkerhetsmekanismer på nätverks nivå. Möjliga resurs typer som en indexerare kan komma åt i en typisk körning visas i tabellen nedan.

| Resurs | Syfte inom körning av indexerare |
| --- | --- |
| Azure Storage (blobbar, tabeller, ADLS gen 2) | Datakälla |
| Azure Storage (blobbar, tabeller) | Färdighetsuppsättningar (cachelagring av berikade dokument och lagring av kunskaps lager-projektioner) |
| Azure Cosmos DB (olika API: er) | Datakälla |
| Azure SQL Database | Datakälla |
| SQL Server på virtuella Azure IaaS-datorer | Datakälla |
| SQL-hanterade instanser | Datakälla |
| Azure Functions | Värd för anpassade webb-API-kunskaper |
| Cognitive Services | Ansluten till färdigheter som används för fakturerings anrikning utöver gränsen på 20 fria dokument |

> [!NOTE]
> Kognitiva tjänst resursen som är kopplad till en färdigheter används för fakturering, baserat på de anrikninger som utförs och skrivs till Sök indexet. Den används inte för att komma åt API:er för Cognitive Services. Åtkomst från en indexerare pipeline till API:er för Cognitive Services sker via en säker kommunikations kanal, där data är starkt krypterade vid överföring och aldrig lagras i vila.

Kunder kan skydda dessa resurser via flera nätverks isolerings mekanismer som erbjuds av Azure. Med undantag av kognitiva tjänst resurser har indexerare begränsad möjlighet att komma åt alla andra resurser, även om de är isolerade på ett nätverk som är isolerade i tabellen nedan.

| Resurs | IP-begränsning | Privat slutpunkt |
| --- | --- | ---- |
| Azure Storage (blobbar, tabeller, ADLS gen 2) | Stöds endast om lagrings kontot och Sök tjänsten finns i olika regioner | Stöds |
| Azure Cosmos DB-SQL API | Stöds | Stöds |
| Azure Cosmos DB-Cassandra, Mongo och Gremlin API | Stöds | Stöd saknas |
| Azure SQL Database | Stöds | Stöds |
| SQL Server på virtuella Azure IaaS-datorer | Stöds | E.t. |
| SQL-hanterade instanser | Stöds | E.t. |
| Azure Functions | Stöds | Stöds endast för vissa SKU: er för Azure Functions |

> [!NOTE]
> Utöver de alternativ som anges ovan kan kunder för skyddade Azure Storage-konton i nätverket utnyttja det faktum att Azure Kognitiv sökning är en [betrodd Microsoft-tjänst](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services). Det innebär att en bestämd Sök tjänst kan kringgå virtuella nätverk eller IP-begränsningar på lagrings kontot och kan komma åt data i lagrings kontot, om lämplig rollbaserad åtkomst kontroll har Aktiver ATS på lagrings kontot. Information finns i [guiden How to](search-indexer-howto-access-trusted-service-exception.md). Det här alternativet kan användas i stället för IP-begränsning, om lagrings kontot eller Sök tjänsten inte kan flyttas till en annan region.

När du väljer vilken säker åtkomst mekanism som en indexerare ska använda, bör du tänka på följande begränsningar:

- [Tjänst slut punkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) stöds inte för någon Azure-resurs.
- Det går inte att tillhandahålla en Sök tjänst till ett enskilt virtuellt nätverk – den här funktionen erbjuds inte av Azure Kognitiv sökning.
- Om indexerarna använder (utgående) privata slut punkter för att få åtkomst till resurser kan ytterligare [avgifter för privata länkar](https://azure.microsoft.com/pricing/details/search/) tillkomma.

## <a name="indexer-execution-environment"></a>Miljö för körning av indexerare

Azure Kognitiv sökning-indexerare kan effektivt extrahera innehåll från data källor, lägga till berikade innehåll och eventuellt generera prognoser innan resultatet skrivs till Sök indexet. Beroende på hur många ansvars områden som har tilldelats till en indexerare, kan det köras i en av två miljöer:

- En miljö som är privat för en speciell Sök tjänst. Indexerare som körs i sådana miljöer delar resurser med andra arbets belastningar (till exempel andra kunder som initierar indexering eller frågor om arbets belastningen). Vanligt vis är det bara indexerare som inte kräver mycket resurser (till exempel inte använder en färdigheter) i den här miljön.
- En miljö med flera klienter som är värd för indexerare som är resurs krävande, till exempel med en färdigheter. Resurs krävande resurser körs i den här miljön för att erbjuda optimala prestanda samtidigt som det säkerställs att Sök tjänst resurserna är tillgängliga för andra arbets belastningar. Den här miljön för flera innehavare hanteras och skyddas av Azure Kognitiv sökning utan extra kostnad för kunden.

För att det ska gå att köra en indexerare, fastställer Azure Kognitiv sökning den bästa miljön där du vill köra indexeraren.

## <a name="granting-access-to-indexer-ip-ranges"></a>Bevilja åtkomst till indexerare IP-intervall

Om resursen som indexeraren försöker komma åt är begränsad till endast en viss uppsättning IP-intervall, måste du expandera uppsättningen för att inkludera möjliga IP-intervall som en indexerare-begäran kan ha sitt ursprung i. Som anges ovan finns det två möjliga miljöer där indexerare körs och från vilka åtkomst begär Anden kan komma från. Du måste lägga till IP-adresserna för __båda__ miljöerna för att indexerings åtkomsten ska fungera.

- För att hämta IP-adressen för Sök tjänstens särskilda privata miljö, `nslookup` (eller `ping` ) det fullständigt kvalificerade domän namnet (FQDN) för Sök tjänsten. FQDN för en Sök tjänst i det offentliga molnet är till exempel `<service-name>.search.windows.net` . Den här informationen finns på Azure Portal.
- IP-adresserna för flera klient miljöer är tillgängliga via `AzureCognitiveSearch` tjänst tag gen. [Azure Service-Taggar](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) har ett publicerat intervall med IP-adresser för varje tjänst – detta är tillgängligt via ett [identifierings-API (för hands version)](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) eller en [nedladdnings bar JSON-fil](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). I båda fallen uppdelas IP-intervall efter region – du kan bara välja de IP-adressintervall som tilldelats den region där Sök tjänsten är etablerad.

För vissa data källor kan själva tjänst tag gen användas direkt i stället för att räkna upp listan över IP-intervall (IP-adressen för Sök tjänsten måste fortfarande användas explicit). Dessa data källor begränsar åtkomsten genom att ställa in en [regel för nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview), som inbyggt stöder tillägg av en tjänst tagg, till skillnad från IP-regler som de som erbjuds av Azure Storage, CosmosDB, Azure SQL osv., data källorna som stöder möjligheten att använda `AzureCognitiveSearch` tjänst tag gen direkt utöver Sök tjänstens IP-adress:

- [SQL Server på virtuella IaaS-datorer](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers#restrict-access-to-the-azure-cognitive-search)

- [SQL-hanterade instanser](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers#verify-nsg-rules)

Information beskrivs i [guiden How to guide](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Bevilja åtkomst via privata slut punkter

Indexerare kan använda [privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för att få åtkomst till resurser, åtkomst till vilka är låsta för att välja virtuella nätverk eller inte ha någon offentlig åtkomst aktive rad.
Den här funktionen är endast tillgänglig för betalda tjänster, med gränser för antalet privata slut punkter som skapas. Information om gränserna dokumenteras på [sidan Azure Search gränser](search-limits-quotas-capacity.md).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Steg 1: skapa en privat slut punkt för den säkra resursen

Kunderna bör anropa Sök hanterings åtgärden, [skapa eller uppdatera *Shared Private Link* -API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) för att skapa en privat slut punkts anslutning till en säker resurs (till exempel ett lagrings konto). Trafik som går över den här (utgående) privata slut punkts anslutningen kommer endast från det virtuella nätverk som är i Sök tjänstens aktuella "privata" index körnings miljö.

Azure Kognitiv sökning verifierar att anroparna i detta API har behörighet att godkänna privata slut punkts anslutnings begär anden till den säkra resursen. Om du till exempel begär en privat slut punkts anslutning till ett lagrings konto som du inte har åtkomst till, kommer anropet att avvisas.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Steg 2: Godkänn anslutningen till den privata slut punkten

När den (asynkrona) åtgärd som skapar en delad privat länk-resurs slutförs, skapas en privat slut punkts anslutning i ett "väntande" läge. Ingen trafik flödar över anslutningen än.
Kunden förväntas sedan hitta denna begäran på sin säkra resurs och godkänna. Detta kan vanligt vis göras via portalen eller via [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Steg 3: tvinga indexerare att köras i den privata miljön

En godkänd privat slut punkt tillåter utgående anrop från Sök tjänsten till en resurs som har någon form av åtkomst begränsningar på nätverks nivå (till exempel en data källa för lagrings kontot som har kon figurer ATS för att endast nås från vissa virtuella nätverk) att lyckas.
Det innebär att alla indexerare som kan kontakta en sådan data källa över den privata slut punkten kommer att lyckas.
Om den privata slut punkten inte är godkänd, eller om indexeraren inte använder den privata slut punkts anslutningen, kommer körningen av indexet att avslutas `transientFailure` .

Om du vill aktivera indexerare för att få åtkomst till resurser via anslutningar för privata slut punkter, måste du ange att `executionEnvironment` indexeraren ska köras så att `"Private"` alla indexerare körs kan använda den privata slut punkten. Detta beror på att privata slut punkter är etablerade i den privata Sök tjänst miljön.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

De här stegen beskrivs mer detaljerat i [guiden för att hjälpa](search-indexer-howto-access-private.md)dig.
När du har en godkänd privat slut punkt till en resurs får indexerare som är inställt för *privata* försök att få åtkomst via anslutningen till den privata slut punkten.

### <a name="limits"></a>Begränsningar

För att säkerställa optimala prestanda och stabilitet för Sök tjänsten införs begränsningar (av Sök tjänstens SKU) på följande dimensioner:

- Typerna av indexerare som kan ställas in för *privat*.
- Antalet delade privata länk resurser som kan skapas.
- Antalet distinkta resurs typer som delade privata länk resurser kan skapas för.

Dessa gränser dokumenteras i [tjänst begränsningar](search-limits-quotas-capacity.md).
