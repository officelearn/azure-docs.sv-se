---
title: Entiteter
description: Definition av entiteter i azure remote rendering API
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681953"
---
# <a name="entities"></a>Entiteter

En *entitet* representerar ett flyttbart objekt i rymden och är den grundläggande byggstenen i fjärrrenderade innehåll.

## <a name="entity-properties"></a>Egenskaper för entitet

Entiteter har en transformering definierad av en position, rotation och skala. I sig har entiteterna inga observerbara funktioner. I stället läggs beteende till genom komponenter som är kopplade till entiteter. Om du till exempel bifogar en [CutPlaneComponent](../overview/features/cut-planes.md) skapas ett snittplan vid entitetens position.

Den viktigaste aspekten av själva entiteten är hierarkin och den resulterande hierarkiska transformeringen. När till exempel flera entiteter är kopplade som underordnade till en delad överordnad entitet kan alla dessa entiteter flyttas, roteras och skalas unisont genom att ändra transformeringen av den överordnade entiteten.

En entitet ägs unikt av dess överordnade, vilket `Entity.Destroy()`innebär att när den överordnade förstörs med , så är dess underordnade och alla anslutna [komponenter](components.md). Således, ta bort en modell från `Destroy` scenen åstadkoms genom att anropa `AzureSession.Actions.LoadModelAsync()` rotnoden `AzureSession.Actions.LoadModelFromSASAsync()`för en modell, returneras av eller dess SAS variant .

Entiteter skapas när servern läser in innehåll eller när användaren vill lägga till ett objekt i scenen. Om en användare till exempel vill lägga till ett snittplan för att visualisera insidan av ett nät, kan användaren skapa en entitet där planet ska finnas och sedan lägga till den klippta plankomponenten i det.

## <a name="query-functions"></a>Frågefunktioner

Det finns två typer av frågefunktioner på entiteter: synkrona och asynkrona anrop. Synkrona frågor kan endast användas för data som finns på klienten och inte innebär mycket beräkning. Exempel är frågor om komponenter, relativa objekttransformeringar eller överordnade/underordnade relationer. Asynkrona frågor används för data som bara finns på servern eller innebär extra beräkning som skulle vara för dyrt att köra på klienten. Exempel är rumsliga gränser frågor eller metadatafrågor.

### <a name="querying-components"></a>Fråga komponenter

Om du vill hitta en `FindComponentOfType`komponent av en viss typ använder du:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Fråga transformeringar

Transformeringsfrågor är synkrona anrop på objektet. Det är viktigt att notera att transformeringar som efterfrågas via API:et är lokala utrymmestransformeringar, i förhållande till objektets överordnade. Undantag är rotobjekt, för vilka lokalt utrymme och världsutrymme är identiska.

> [!NOTE]
> Det finns inget dedikerat API för att fråga om världsrymdstransformeringen av godtyckliga objekt.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Fråga rumsliga gränser

Bounds-frågor är asynkrona anrop som fungerar på en fullständig objekthierarki, med en entitet som en rot. Se det särskilda kapitlet om [objekt bounds](object-bounds.md).

### <a name="querying-metadata"></a>Fråga metadata

Metadata är ytterligare data som lagras på objekt, som ignoreras av servern. Objektmetadata är i huvudsak en uppsättning (namn, värde) par, där _värdet_ kan vara av numerisk, boolesk eller strängtyp. Metadata kan exporteras med modellen.

Metadatafrågor är asynkrona anrop på en viss entitet. Frågan returnerar bara metadata för en enskild entitet, inte den sammanslagna informationen i ett underdiagram.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

Frågan lyckas även om objektet inte innehåller några metadata.

## <a name="next-steps"></a>Nästa steg

* [Komponenter](components.md)
* [Objekt bounds](object-bounds.md)
