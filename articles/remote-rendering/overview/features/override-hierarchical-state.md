---
title: Åsidosättning av hierarkiskt tillstånd
description: Förklarar begreppet hierarkiska tillståndssidosättningskomponenter.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680796"
---
# <a name="hierarchical-state-override"></a>Åsidosättning av hierarkiskt tillstånd

I många fall är det nödvändigt att dynamiskt ändra utseendet på delar av en [modell,](../../concepts/models.md)till exempel dölja subgrafer eller byta delar till transparent rendering. Att ändra materialen för varje del som berörs är inte praktiskt eftersom det kräver att iterera över hela scendiagrammet och hantera materialkloning och tilldelning på varje nod.

Använd `HierarchicalStateOverrideComponent`. Den här komponenten implementerar hierarkiska tillståndsuppdateringar på godtyckliga grenar av scendiagrammet. Det innebär att ett tillstånd kan definieras på valfri nivå i scendiagrammet och det sipprar ner i hierarkin tills det antingen åsidosätts av ett nytt tillstånd eller tillämpas på ett lövobjekt.

Som ett exempel, överväga modellen av en bil och du vill byta hela bilen vara transparent, med undantag för den inre motordelen. Det här användningsfallet omfattar endast två instanser av komponenten:

* Den första komponenten tilldelas modellens rotnod och aktiverar genomskinlig rendering för hela bilen.
* Den andra komponenten tilldelas motorns rotnod och åsidosätter tillståndet igen genom att uttryckligen stänga av genomskinligt läge.

## <a name="features"></a>Funktioner

Den fasta uppsättningen tillstånd som kan åsidosättas är:

* **Dold**: Respektive maskor i scendiagrammet döljs eller visas.
* **Färgtonsfärg:** Ett återgesobjekt kan färgtonas med sin individuella nyansfärg och tonade vikt. Bilden nedan visar färgtonning fälgen på ett hjul.
  
  ![Färgfärg](./media/color-tint.png)

* **Genomskinlig :** Geometrin återges halvtransparent, till exempel för att avslöja de inre delarna av ett objekt. Följande bild visar hela bilen som återges i genomskinligt läge, med undantag för det röda bromsoket:

  ![Genomskinligt](./media/see-through.png)

  > [!IMPORTANT]
  > Genomskinlig effekt fungerar bara när [återgivningsläget](../../concepts/rendering-modes.md) *TileBasedComposition* används.

* **Markerad**: Geometrin återges med en [markeringsdisposition](outlines.md).

  ![Markeringsdisposition](./media/selection-outline.png)

* **DisableCollision**: Geometrin är undantagen från [rumsliga frågor](spatial-queries.md). Den **dolda** flaggan stänger inte av kollisioner, så dessa två flaggor ställs ofta ihop.

## <a name="hierarchical-overrides"></a>Hierarkiska åsidosättningar

Kan `HierarchicalStateOverrideComponent` kopplas på flera nivåer i en objekthierarki. Eftersom det bara kan finnas en komponent av `HierarchicalStateOverrideComponent` varje typ på en entitet hanterar var och en lägen för dold, genomskinlig, markerad, färgton och kollision.

Därför kan varje tillstånd ställas in på ett av:

* `ForceOn`- tillståndet är aktiverat för alla maskor på och under denna nod
* `ForceOff`- staten är inaktiverad för alla maskor på och under denna nod
* `InheritFromParent`- tillståndet påverkas inte av den här åsidosättningskomponenten

Du kan ändra lägen `SetState` direkt eller via funktionen:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>Färgton

Färgtonfärgsidogången är något speciell eftersom det finns både ett på/av/inherit-tillstånd och en färgtonsfärgegenskap. Alfadelen av nyansfärgen definierar toningseffektens vikt: Om den är inställd på 0,0 visas ingen nyansfärg och om den är inställd på 1,0 återges objektet med ren nyansfärg. För mellanvärden blandas den slutliga färgen med nyansfärgen. Färgtonsfärgen kan ändras per bildruta för att uppnå en färganimering.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

En instans `HierarchicalStateOverrideComponent` av sig själv lägger inte mycket runtime overhead. Det är dock alltid bra att hålla antalet aktiva komponenter lågt. När du till exempel implementerar ett markeringssystem som markerar det plockade objektet rekommenderas att komponenten tas bort när markeringen tas bort. Att hålla komponenterna runt med neutrala funktioner kan snabbt lägga upp.

Transparent rendering lägger mer arbetsbelastning på serverns GPU:er än standardåtergivning. Om stora delar av scendiagrammet växlas till *genomskinlighet*, med många lager av geometri som syns, kan det bli en flaskhals. Detsamma gäller för objekt med [markeringskonturer](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Nästa steg

* [Konturer](../../overview/features/outlines.md)
* [Återgivningslägen](../../concepts/rendering-modes.md)
* [Rumsliga frågor](../../overview/features/spatial-queries.md)
