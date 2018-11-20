---
title: Lär dig hur du hanterar databaskonton i Azure Cosmos DB
description: Lär dig hur du hanterar databaskonton i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 97fb5c2558d55b3f80f2e771971faa109a930c5f
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626484"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Hantera indexering i Azure Cosmos DB

I Azure Cosmos DB kan välja du om du vill att en container automatiskt ska indexera alla objekt eller inte. Alla objekt i en Azure Cosmos-container indexeras automatiskt som standard, men du kan inaktivera automatisk indexering. När indexering stängs av kan objekt nås via sina egna länkar eller via frågorna med hjälp av ID för objekt, till exempel dokument-id. Du kan uttryckligen begära att leverera resultat utan att använda index genom att ange rubriken **x-ms-documentdb-enable-scan** i REST API eller begäransalternativet **EnableScanInQuery** med .NET SDK.

När automatisk indexering är avstängd kan du fortfarande selektivt lägga till specifika objekt till indexet. Däremot kan du lämna automatisk indexering på och selektivt välja att undanta specifika objekt. Konfigurationer för indexering på/av är användbara när du har en delmängd av objekt som ska efterfrågas.  

Skrivningar för dataflöde och begäransenheter är proportionerliga i förhållande till antalet värden som måste indexeras, som anges med den inkluderade uppsättningen i indexeringsprincipen. Om du har en god förståelse för frågemönster kan du uttryckligen välja att inkludera/exkludera delmängd med sökvägar för att förbättra skrivdataflödet.

## <a name="manage-indexing-using-azure-portal"></a>Hantera indexering med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Skapa ett nytt Azure Cosmos DB-konto eller välj ett befintligt konto.

3. Öppna fönstret **Datautforskaren**.

4. Välj en befintlig container, expandera den och ändra följande värden:

   * Öppna fönstret **Scale & Settings** (Skalning och inställningar).
   * Ändra **indexingMode** från *consistent* (konsekvent) till *none* (ingen) eller inkludera/exkludera vissa sökvägar från indexering.
   * Spara ändringarna genom att klicka på **OK**.

   ![Hantera indexering med hjälp av Azure-portalen](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Hantera indexering med hjälp av Azure-SDK:er

### <a id="dotnet"></a>.NET SDK

I följande exempel visas hur du lägger till ett objekt uttryckligen med hjälp av [SQL API .NET SDK](sql-api-sdk-dotnet.md) och egenskapen [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective).

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

* [Indexeringsöversikt](index-overview.md)
* [Indexeringspolicy](index-policy.md)
* [Indextyper](index-types.md)
* [Indexsökvägar](index-paths.md)
