---
title: VM-storlekar
description: Beskriver de distinkta VM-storlekar som kan tilldelas
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: e8e439a055b71ed291573965c561ee31610e3ed4
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121620"
---
# <a name="vm-sizes"></a>VM-storlekar

Åter givnings tjänsten kan köras på två olika typer av datorer i Azure, som kallas `Standard` och `Premium` .

## <a name="polygon-limits"></a>Polygon-gränser

Det finns en hård begränsning på **20 000 000 polygoner** för `Standard` VM-storleken. Det finns ingen sådan begränsning för `Premium` storleken.

När åter givningen på en standard-VM träffar den här begränsningen växlar den åter givning till en schack brädets bakgrund:

![Rutig](media/checkerboard.png)

## <a name="allocate-the-vm"></a>Allokera den virtuella datorn

Önskad typ av virtuell dator måste anges vid initierings tiden för rendering session. Det går inte att ändra i en pågående session. I följande kod exempel visas den plats där VM-storleken måste anges:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

För [PowerShell-skripten](../samples/powershell-example-scripts.md)måste den virtuella datorns storlek anges i `arrconfig.json` filen:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Hur renderaren utvärderar antalet polygoner

Antalet polygoner som beaktas för begränsnings testet är antalet polygoner som faktiskt skickas till åter givningen. Den här geometrin är vanligt vis summan av alla instansierade modeller, men det finns också undantag. Följande geometri **ingår inte**:
* Inlästa modell instanser som är helt utanför vyn Frustum.
* Modeller eller modell delar som har växlats till osynlig, med hjälp av komponenten för att [åsidosätta hierarkiska tillstånd](../overview/features/override-hierarchical-state.md).

Därför är det möjligt att skriva ett program som är riktat mot den `standard` storlek som läser in flera modeller med ett antal polygoner nära gränsen för varje enskild modell. När programmet bara visar en enskild modell i taget utlöses inte schack bräden.

### <a name="how-to-determine-the-number-of-polygons"></a>Så här fastställer du antalet polygoner

Det finns två sätt att fastställa antalet polygoner i en modell eller scen som bidrar till den `standard` virtuella datorns budget gräns:
* På sidan modell konvertering hämtar du JSON- [filen för konvertering av utdata](../how-tos/conversion/get-information.md)och kontrollerar `numFaces` posten i [avsnittet *inputStatistics* ](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Om ditt program hanterar dynamiskt innehåll kan antalet åter givnings bara polygoner efter frågas dynamiskt under körning. Använd en [utvärderings fråga för prestanda](../overview/features/performance-queries.md#performance-assessment-queries) och kontrol lera `polygonsRendered` medlemmen i `FrameStatistics` struct. `polygonsRendered`Fältet ställs in på `bad` när åter givningen träffar i polygonens begränsning. Schack brädets bakgrund är alltid blek i en viss fördröjning för att se till att användar åtgärder kan vidtas efter den asynkrona frågan. Användar åtgärd kan till exempel dölja eller ta bort modell instanser.

## <a name="pricing"></a>Prissättning

En detaljerad beskrivning av prissättningen för varje typ av virtuell dator finns på [prissättnings sidan för Fjärråter givning](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Nästa steg
* [PowerShell-exempelskript](../samples/powershell-example-scripts.md)
* [Modell konvertering](../how-tos/conversion/model-conversion.md)

