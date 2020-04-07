---
title: Återgivningslägen
description: Beskriver de olika återgivningslägena på serversidan
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681706"
---
# <a name="rendering-modes"></a>Återgivningslägen

Fjärrrendering erbjuder två huvudsakliga driftslägen, **TileBasedComposition-läge** och **DepthBasedComposition-läge.** Dessa lägen avgör hur arbetsbelastningen fördelas över flera GPU:er på servern. Läget måste anges vid anslutningstillfället och kan inte ändras under körning.

Båda lägena kommer med fördelar men också med inneboende funktionsbegränsningar, så att välja det lämpligaste läget är användningsfallspecifikt.

## <a name="modes"></a>Lägen

De två lägena diskuteras nu mer i detalj.

### <a name="tilebasedcomposition-mode"></a>Läget TileBasedComposition

I **TileBasedComposition-läge** återger varje involverad GPU specifika undersektorer (paneler) på skärmen. Huvud-GPU komponerar den slutliga bilden från panelerna innan den skickas som en videobildruta till klienten. Därför kräver alla GPU:er att ha samma uppsättning resurser för rendering, så de inlästa tillgångarna måste passa in i en enda GPU minne.

Renderingskvaliteten i det här läget är något bättre än i **DepthBasedComposition-läge,** eftersom MSAA kan arbeta med hela uppsättningen geometri för varje GPU. Följande skärmdump visar att kantutjämning fungerar korrekt för båda kanterna likaså:

![MSAA i TileBasedComposition](./media/service-render-mode-quality.png)

I det här läget kan dessutom varje del växlas till ett transparent material eller växlas till **genomskinligt** läge via [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>"DepthBasedComposition"-läge

I **DepthBasedComposition-läge** återges varje involverad GPU med full skärmupplösning men bara en delmängd av maskor. Den slutliga bildkompositionen på huvud-GPU:n ser till att delarna slås ihop korrekt enligt deras djupinformation. Naturligtvis är minne nyttolast fördelas över GPU: er, vilket möjliggör rendering modeller som inte skulle passa in i en enda GPU minne.

Varje enskild GPU använder MSAA för att kantutjämning lokalt innehåll. Det kan dock finnas inneboende aliasing mellan kanter från olika GPU: er. Den här effekten mildras genom att den slutliga bilden efterbehandlings, men MSAA-kvaliteten är fortfarande sämre än i **tilebasedcomposition-läge.**

MSAA-artefakter illustreras i följande ![bild: MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

Kantutjämning fungerar korrekt mellan skulpturen och gardinen, eftersom båda delarna återges på samma GPU. Å andra sidan visar kanten mellan gardin och vägg vissa aliasing eftersom dessa två delar består av olika GPU: er.

Den största begränsningen i det här läget är att geometridelar inte kan växlas till genomskinliga material dynamiskt och inte heller fungerar **genomskinligt** läge för [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Andra tillstånd åsidosättning funktioner (kontur, färg nyans, ...) fungerar, dock. Även material som har markerats som genomskinliga vid konverteringstiden fungerar korrekt i det här läget.

### <a name="performance"></a>Prestanda

Prestandaegenskaperna för båda lägena varierar beroende på användningsfallet, och det är svårt att resonera eller ge allmänna rekommendationer. Om du inte begränsas av de begränsningar som nämns ovan (minne eller genomskinlighet/genomskinlighet) rekommenderas att du provar båda lägena och övervakar prestanda med hjälp av olika kamerapositioner.

## <a name="setting-the-render-mode"></a>Ställa in återgivningsläge

Renderingsläget som används på en virtuell `AzureSession.ConnectToRuntime` fjärrrendering anges under via `ConnectToRuntimeParams`.

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>Nästa steg

* [Sessioner](../concepts/sessions.md)
* [Åsidosättkomponent för hierarkiskt tillstånd](../overview/features/override-hierarchical-state.md)
