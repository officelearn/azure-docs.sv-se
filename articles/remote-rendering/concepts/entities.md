---
title: Entiteter
description: Definition av entiteter i omfånget för Azure Remote rendering API
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c49c7bfaa7714dda902d05537fbe3d8a55d5abe
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613924"
---
# <a name="entities"></a>Entiteter

En *entitet* representerar ett rörligt objekt i utrymme och är det grundläggande Bygg stenarna för fjärrrenderat innehåll.

## <a name="entity-properties"></a>Egenskaper för entitet

Entiteter har en transformering som definieras av en position, rotation och skala. Själva entiteten har inte någon funktion som kan ha någon funktion. I stället läggs beteendet till i-komponenter som är kopplade till entiteter. Om du till exempel bifogar ett [CutPlaneComponent](../overview/features/cut-planes.md)  skapas ett klipp plan i entitetens position.

Den viktigaste aspekten av själva entiteten är hierarkin och den resulterande hierarkiska omvandlingen. Till exempel när flera entiteter är kopplade som underordnade till en delad överordnad entitet, kan alla dessa entiteter flyttas, roteras och skalas i dem samtidigt genom att ändra den överordnade entitetens omvandling. Entitetens `enabled` tillstånd kan också användas för att stänga av synlighet och svar på Ray-sändningar för ett fullständigt under diagram i hierarkin.

En entitet ägs unikt av dess överordnade, vilket innebär att när det överordnade objektet förstörs, `Entity.Destroy()` så är dess underordnade och alla anslutna [komponenter](components.md). Därför utförs borttagning av en modell från scenen genom att anropar `Destroy` rotnoden i en modell, som returneras av `AzureSession.Actions.LoadModelAsync()` eller dess SAS-variant `AzureSession.Actions.LoadModelFromSASAsync()` .

Entiteter skapas när servern läser in innehåll eller när användaren vill lägga till ett objekt i scenen. Om en användare till exempel vill lägga till ett klipp plan för att visualisera insidan av ett nät, kan användaren skapa en entitet där planet ska finnas och sedan lägga till komponenten klipp ut plan till den.

## <a name="create-an-entity"></a>Skapa en entitet

Om du vill lägga till en ny entitet i scenen, till exempel för att skicka den som ett rot objekt för inläsning av modeller eller koppla komponenter till den, använder du följande kod:

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Fråge funktioner

Det finns två typer av fråge funktioner på entiteter: synkrona och asynkrona anrop. Synkrona frågor kan bara användas för data som finns på klienten och inte omfattar mycket beräkning. Exempel frågar efter komponenter, relativa objekt transformationer eller överordnade/underordnade relationer. Asynkrona frågor används för data som bara finns på servern eller som inbegriper extra beräkning som skulle vara dyra att köra på klienten. Exempel är spatiala gränserfrågor eller frågor om meta-data.

### <a name="querying-components"></a>Fråga komponenter

Om du vill hitta en komponent av en speciell typ använder du `FindComponentOfType` :

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Fråga transformeringar

Transformations frågor är synkrona anrop i objektet. Det är viktigt att Observera att transformeringar som frågas genom API: et är lokala lagrings utrymmen, i förhållande till objektets överordnade objekt. Undantag är rot objekt, där det lokala utrymmet och det internationella utrymmet är identiska.

> [!NOTE]
> Det finns inget dedikerat API för att fråga hela objektets transformering av utrymmet.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>Frågar efter rums gränser

Bindnings frågor är asynkrona anrop som används i en fullständig objektorienterad hierarki, med en entitet som en rot. Se det dedikerade kapitlet om [objekt gränser](object-bounds.md).

### <a name="querying-metadata"></a>Frågar metadata

Metadata är ytterligare data lagrade på objekt som ignoreras av servern. Metadata för objekt är i grunden en uppsättning (namn, värde) par, där _värdet_ kan vara av typen numerisk, boolesk eller sträng. Metadata kan exporteras med modellen.

Metadata-frågor är asynkrona anrop i en speciell entitet. Frågan returnerar bara metadata för en enskild entitet, inte den sammanslagna informationen för ett under diagram.

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

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->GetIsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = query->GetResult();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->GetAsInt64();

            // ...
        }
    });
```

Frågan kommer att lyckas även om objektet inte innehåller några metadata.

## <a name="api-documentation"></a>API-dokumentation

* [C#-enhets klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RemoteManager. CreateEntity ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.createentity)
* [C++-klass för entitet](https://docs.microsoft.com/cpp/api/remote-rendering/entity)
* [C++ RemoteManager:: CreateEntity ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#createentity)

## <a name="next-steps"></a>Nästa steg

* [Komponenter](components.md)
* [Objektgränser](object-bounds.md)
