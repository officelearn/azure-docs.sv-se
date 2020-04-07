---
title: Objektets och resursens livslängd
description: Förklarar livstidshantering för olika typer
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681875"
---
# <a name="object-and-resource-lifetime"></a>Objektets och resursens livslängd

Azure Remote Rendering skiljer mellan två typer: **objekt** och **resurser**.

## <a name="object-lifetime"></a>Objektets livstid

*Objekt* anses vara saker som användaren kan skapa, ändra och förstöra efter eget gottfinnande. Objekt kan dupliceras fritt och varje instans kan mutera med tiden. Följaktligen är [entiteter](entities.md) och [komponenter](components.md) objekt.

Objektens livslängd är helt under användarkontroll. Det är inte relaterat till livstiden för klientsidan representation, dock. Klasser `Entity` som `Component` och `Destroy` har en funktion som måste anropas för att frigöra objektet på fjärråtergivningsvärden. Dessutom `Entity.Destroy()` kommer att förstöra entiteten, dess underordnade och alla komponenter i den hierarkin.

## <a name="resource-lifetime"></a>Resursens livslängd

*Resurser* är saker vars livstid helt hanteras av fjärråtergivningsvärden. Resurser räknas internt. De får deallocated när ingen hänvisar till dem längre.

De flesta resurser kan bara skapas indirekt, vanligtvis genom att läsa in dem från en fil. När samma fil läses in flera gånger returnerar Azure Remote Rendering samma referens och läser inte in data igen.

Många resurser är oföränderliga, till exempel [maskor](meshes.md) och [texturer](textures.md). Vissa resurser är dock föränderliga, till exempel [material](materials.md). Eftersom resurser ofta delas kan det påverka flera objekt om du ändrar en resurs. Om du till exempel ändrar färgen på ett material ändras färgen på alla objekt som använder maskor, vilket i sin tur refererar till det materialet.

### <a name="built-in-resources"></a>Inbyggda resurser

Azure Remote Rendering innehåller några inbyggda resurser, som kan läsas `builtin://` in genom `AzureSession.Actions.LoadXYZAsync()`att förprepending deras respektive identifierare med under anropet till . De inbyggda resurserna visas i dokumentationen för respektive funktion. I [himmelskapitlet](../overview/features/sky.md) visas till exempel de inbyggda himmelstexaderna.

## <a name="general-lifetime"></a>Allmän livslängd

Livslängden för alla objekt och resurser är bunden till anslutningen. Vid frånkoppling kasseras allt. När du ansluter till samma session igen kommer scendiagrammet att vara tomt och alla resurser rensas.

I praktiken är det oftast snabbare att läsa in samma resurs i en session efter en frånkoppling än första gången. Detta beror på att de flesta resurser måste hämtas från Azure Storage första gången, vilket inte är nödvändigt andra gången, vilket sparar avsevärd tid.

## <a name="next-steps"></a>Nästa steg

* [Entiteter](entities.md)
* [Komponenter](components.md)
* [Modeller](models.md)
