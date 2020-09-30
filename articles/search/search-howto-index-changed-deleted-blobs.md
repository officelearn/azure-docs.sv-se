---
title: Ändrade och borttagna blobbar
titleSuffix: Azure Cognitive Search
description: När ett inledande sökindex bygger som importerar från Azure Blob Storage kan efterföljande indexering bara hämta de blobbar som har ändrats eller tagits bort. I den här artikeln förklaras detaljerna.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2e73039418233c97fc20242ed7af7df14c5b47ee
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534785"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Så här konfigurerar du ändrings-och borttagnings identifiering för blobbar i Azure Kognitiv sökning indexering

När ett inledande sökindex har skapats kanske du vill konfigurera efterföljande index jobb för att bara hämta de dokument som har skapats eller tagits bort sedan den första körningen. För Sök innehåll som kommer från Azure Blob Storage sker ändrings identifiering automatiskt när du använder ett schema för att utlösa indexering. Som standard indexerar tjänsten om bara de ändrade blobbar som fastställs av blobens `LastModified` tidsstämpel. Till skillnad från andra data källor som stöds av Sök indexerare har blobbar alltid en tidsstämpel, vilket eliminerar behovet av att konfigurera en princip för ändrings identifiering manuellt.

Även om ändrings identifieringen är en specifik, är inte borttagnings identifieringen. Om du vill identifiera borttagna dokument, se till att använda en "mjuk borttagning"-metod. Om du tar bort Blobbarna till höger tas inte motsvarande dokument bort från Sök indexet.

Det finns två sätt att implementera metoden för mjuk borttagning. Båda beskrivs nedan.

## <a name="native-blob-soft-delete-preview"></a>Intern BLOB-mjuk borttagning (förhands granskning)

> [!IMPORTANT]
> Stöd för intern BLOB-mjuk borttagning är i för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2020-06-30 – för hands version](./search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för Portal eller .NET SDK.

> [!NOTE]
> När du använder den inbyggda principen för mjuk borttagning av BLOB måste dokument nycklarna för dokumenten i ditt index antingen vara en BLOB-egenskap eller BLOB-metadata.

I den här metoden ska du använda den inbyggda funktionen för [mjuk borttagning av BLOB](../storage/blobs/soft-delete-blob-overview.md) som erbjuds av Azure Blob Storage. Om den inbyggda BLOB-borttagningen är aktive rad på ditt lagrings konto har data källan en inbyggd princip uppsättning för mjuk borttagning och indexeraren hittar en blob som har överförts till ett mjukt borttaget läge, tar indexeraren bort dokumentet från indexet. Den inbyggda principen för mjuk borttagning av BLOB stöds inte när du indexerar blobbar från Azure Data Lake Storage Gen2.

Gör så här:

1. Aktivera [inbyggd mjuk borttagning för Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md). Vi rekommenderar att du anger bevarande principen till ett värde som är mycket högre än schemats intervall schema. På det här sättet är det mycket tid för indexeraren att bearbeta de mjuka borttagna blobarna om det uppstår ett problem med att köra indexeraren eller om du har ett stort antal dokument att indexera. Azure Kognitiv sökning-indexerare tar bara bort ett dokument från indexet om det bearbetar blobben när det är i ett mjukt borttaget tillstånd.

1. Konfigurera en intern identifierings princip för mjuk borttagning av BLOB på data källan. Ett exempel på detta visas nedan. Eftersom den här funktionen är i för hands version måste du använda REST API för förhands granskning.

1. Kör indexeraren eller Ställ in indexeraren så att den körs enligt ett schema. När indexeraren kör och bearbetar blobben tas dokumentet bort från indexet.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>Indexera om borttagna blobar (med interna principer för mjuk borttagning)

Om du tar bort en BLOB från Azure Blob Storage med inbyggd mjuk borttagning aktive rad på ditt lagrings konto, övergår bloben till ett mjukt borttaget läge, vilket innebär att du kan ta bort bloben inom kvarhållningsperioden. Om du ångrar en borttagning när indexeraren har bearbetat den, kommer indexeraren inte alltid att indexera den återställda blobben. Detta beror på att indexeraren bestämmer vilka blobbar som ska indexeras baserat på blobens `LastModified` tidsstämpel. När en mjuk borttagen BLOB tas bort, `LastModified` uppdateras inte tidsstämpeln, så om indexeraren redan har bearbetat blobbar med nyare `LastModified` tidsstämplar, kommer den inte att indexera om den borttagna blobben. 

För att se till att en icke-borttagen BLOB indexeras om måste du uppdatera blobens `LastModified` tidstämpel. Ett sätt att göra detta är genom att spara om metadata för denna blob. Du behöver inte ändra metadata, men om du sparar om metadata uppdateras blobens `LastModified` tidstämpel så att indexeraren vet att den måste indexera om denna blob.

## <a name="soft-delete-using-custom-metadata"></a>Mjuk borttagning med anpassade metadata

I den här metoden ska du använda en blobs metadata för att ange när ett dokument ska tas bort från Sök indexet. Den här metoden kräver två separata åtgärder, tar bort Sök dokumentet från indexet följt av BLOB-borttagning i Azure Storage.

Gör så här:

1. Lägg till ett anpassat nyckel/värde-par för metadata i blobben för att indikera Azure-Kognitiv sökning att den tas bort logiskt.

1. Konfigurera en princip för identifiering av mjuk borttagnings kolumner på data källan. Ett exempel på detta visas nedan.

1. När indexeraren har bearbetat blobben och tagit bort dokumentet från indexet kan du ta bort blobben i Azure Blob Storage.

Följande princip anser till exempel att en BLOB tas bort om den har en metadata-egenskap `IsDeleted` med värdet `true` :

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>Indexera om borttagna blobbar (med anpassade metadata)

När en indexerare bearbetar en borttagen blob och tar bort motsvarande sökdokument från indexet, återkommer inte denna BLOB om du återställer den senare om blobens `LastModified` tidstämpel är äldre än den sista indexeraren som körs.

Om du vill indexera om dokumentet ändrar du `"softDeleteMarkerValue" : "false"` för bloben och kör om indexeraren.

## <a name="help-us-make-azure-cognitive-search-better"></a>Hjälp oss att göra Azure Kognitiv sökning bättre

Om du har funktions förfrågningar eller idéer om förbättringar kan du ange dina ininformation på [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Om du behöver hjälp med den befintliga funktionen kan du publicera din fråga på [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Nästa steg

* [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
* [Så här konfigurerar du en BLOB-indexerare](search-howto-indexing-azure-blob-storage.md)
* [Översikt över BLOB-indexering](search-blob-storage-integration.md)