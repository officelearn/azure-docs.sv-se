---
title: Steg utrymme
description: Beskriver steg utrymmes inställningar och användnings fall
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: b67294c503e513290b474e0059771a73ad526a6a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189453"
---
# <a name="stage-space"></a>Steg utrymme

När du kör ARR på en enhet som tillhandahåller huvud spårnings data, t. ex. HoloLens 2, skickas Head-attityden till både användar programmet och servern. Utrymmet där Head-transformeringen definieras i kallas för *scen utrymmet*.

Om du vill justera lokalt och fjärrinnehållet förutsätts det att det mellanliggande utrymmet och hela utrymmet är identiska på både klienten och servern. Om användaren bestämmer sig för att lägga till en ytterligare omvandling ovanpå kameran måste den skickas till-servern och kunna justera lokalt och fjärrinnehållet på rätt sätt.

Vanliga orsaker till att flytta mellanlagrings utrymmet är [världs lås verktyg](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) eller andra verkliga funktioner för världs omslutning samt att flytta ett virtuellt tecken i VR.

## <a name="stage-space-settings"></a>Steg utrymmes inställningar

> [!CAUTION]
> EXPERIMENT: den här funktionen är experimentell och kommer att ändras med tiden. Därför kan det krävas ytterligare arbete för att uppgradera koden om du uppdaterar till nyare klient-SDK-versioner. Den aktuella implementeringen avbryter en kort stund för lokal/fjärrstyrd innehålls justering när du ändrar scen utrymmets ursprung.
Därför är den för närvarande endast avsedd att användas i världs låsnings syfte som ankare som bara visar mycket små förändringar över tid.

För att informera servern om att en ytterligare transformering används för scen utrymmet måste dess ursprung som definieras av en position och en rotation i mitt utrymme skickas. Den här inställningen kan nås via *inställningen för scen utrymme*.

> [!IMPORTANT]
> I [simuleringen av Skriv bordet](../../concepts/graphics-bindings.md) tillhandahålls enhetens lagrings plats i användar programmet. I det här fallet måste du hoppa över det mellanliggande lagrings utrymmet innan det redan har multiplicerats med kamera omvandlingen.

```cs
void ChangeStageSpace(AzureSession session)
{
    StageSpaceSettings settings = session.Actions.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<StageSpaceSettings> settings = *session->Actions()->StageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->Position({0, 0, 0});
    settings->Rotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Skript för Unity Stage Space

Union-integreringen innehåller ett skript med namnet **StageSpace** som kan läggas till i kamerans överordnade GameObject. När det här skriptet är tillgängligt tar det hand om att ange det mellanliggande utrymmet.

## <a name="next-steps"></a>Nästa steg

* [Grafik bindning](../../concepts/graphics-bindings.md)
* [Omprojektion av sena steg](late-stage-reprojection.md)
