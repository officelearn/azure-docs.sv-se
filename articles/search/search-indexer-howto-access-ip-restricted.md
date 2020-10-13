---
title: Tillåt åtkomst till indexerings-IP-intervall
titleSuffix: Azure Cognitive Search
description: Anvisningar som beskriver hur du konfigurerar IP-brandväggens regler så att indexerarna kan ha åtkomst.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: f485569caef285601d1dce7acd116f13675da83a
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950201"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Konfigurera regler för IP-brandvägg för att aktivera indexerings åtkomst

IP-brandväggens regler för Azure-resurser, till exempel lagrings konton, Cosmos DB-konton och Azure SQL-servrar tillåter endast trafik från olika IP-adressintervall för åtkomst till data.

Den här artikeln beskriver hur du konfigurerar IP-regler, via Azure Portal, för ett lagrings konto så att Azure Kognitiv sökning indexerare kan komma åt data på ett säkert sätt. I den här hand boken kan den här guiden översättas direkt till andra Azure-resurser som även ger IP-brandvägg regler för att skydda åtkomsten till data.

> [!NOTE]
> Regler för IP-brandvägg för lagrings kontot är bara effektiva om lagrings kontot och Sök tjänsten finns i olika regioner. Om din installation inte tillåter detta rekommenderar vi att du använder alternativet för [betrott tjänst undantag](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Hämta IP-adressen för Sök tjänsten

Hämta det fullständigt kvalificerade domän namnet (FQDN) för Sök tjänsten. Detta kommer att se ut `<search-service-name>.search.windows.net` . Du kan ta reda på det fullständiga domän namnet genom att leta upp Sök tjänsten på Azure Portal.

   ![Hämta FQDN för tjänst](media\search-indexer-howto-secure-access\search-service-portal.png "Hämta FQDN för tjänst")

IP-adressen för Sök tjänsten kan erhållas genom att utföra en `nslookup` (eller a `ping` ) av FQDN. Detta är en av de IP-adresser som ska läggas till i brand Väggs reglerna.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Hämta IP-adressintervall för tjänst tag gen "AzureCognitiveSearch"

IP-adressintervall för `AzureCognitiveSearch` service tag gen kan antingen erhållas via [identifierings-API: t (för hands versionen)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) eller den [nedladdnings bara JSON-filen](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Under den här genom gången ska vi anta att Sök tjänsten är det offentliga Azure-molnet. den [offentliga Azure-JSON-filen](https://www.microsoft.com/download/details.aspx?id=56519) bör laddas ned.

   ![Ladda ned JSON-fil](media\search-indexer-howto-secure-access\service-tag.png "Ladda ned JSON-fil")

Från JSON-filen, förutsatt att Sök tjänsten är i västra centrala USA, visas listan över IP-adresser för körnings miljön för flera klient organisationer i listan nedan.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

För/32 IP-adresser tar du bort "/32" (52.253.133.74/32-> 52.253.133.74), andra kan användas orda Grant.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Lägg till IP-adressintervall i brand Väggs regler

Det enklaste sättet att lägga till IP-adressintervall till ett lagrings kontos brand Väggs regel är via Azure Portal. Leta upp lagrings kontot på portalen och gå till fliken "**brand väggar och virtuella nätverk**".

   ![Brand vägg och virtuella nätverk](media\search-indexer-howto-secure-access\storage-firewall.png "Brand vägg och virtuella nätverk")

Lägg till de tre IP-adresserna som hämtades tidigare (1 för Sök tjänstens IP-adress, 2 för `AzureCognitiveSearch` tjänst tag gen) i adress intervallet och klicka på**Spara**

   ![IP-regler för brand vägg](media\search-indexer-howto-secure-access\storage-firewall-ip.png "IP-regler för brand vägg")

Brand Väggs reglerna tar 5-10 minuter att bli uppdaterade efter vilka indexerare kommer att kunna komma åt data i lagrings kontot.

## <a name="next-steps"></a>Efterföljande moment

Nu när du vet hur du kan hämta de två uppsättningarna med IP-adresser för att tillåta åtkomst till index, använder du följande länkar för att uppdatera IP-brandväggens regler för några vanliga data källor.

- [Konfigurera Azure Storage brand väggar](../storage/common/storage-network-security.md)
- [Konfigurera IP-brandvägg för CosmosDB](../cosmos-db/firewall-support.md)
- [Konfigurera IP-brandvägg för Azure SQL Server](../azure-sql/database/firewall-configure.md)