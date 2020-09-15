---
title: Kamera
description: Beskriver kamera inställningar och användnings fall
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564916"
---
# <a name="camera"></a>Kamera

För att säkerställa att lokalt och externt innehåll kan kombineras sömlöst, måste olika kamera egenskaper vara synkroniserade mellan server och klient. I synnerhet kamera omvandling och projektion. Till exempel måste det lokalt återgivna innehållet använda samma Camera-transformering och projektion som den senaste fjärran sluten bildades med.

![Lokal och fjärran sluten kamera](./media/camera.png)

I bilden ovan har den lokala kameran flyttats jämfört med den fjärran sluten ram som skickats av servern. Det innebär att lokalt innehåll måste återges från samma perspektiv som den fjärranslutna ramen och slutligen projiceras den resulterande bilden i det lokala kamera utrymmet, vilket förklaras i detalj i den [försenade skedet omprojektionen](late-stage-reprojection.md).

Fördröjningen mellan fjär och lokal åter givning innebär att alla ändringar av inställningarna tillämpas med en fördröjning. Ett nytt värde kan därför inte användas direkt i samma ram.

De nära och långt planet avstånden kan ställas in på kamera inställningarna. På HoloLens 2 definieras de återstående transformerings-och projektions data av maskin varan. När du använder [Skriv bords simuleringen](../../concepts/graphics-bindings.md)anges de via inmatad `Update` funktion.

De ändrade värdena kan användas lokalt så snart den första fjärran sluten ram anländer till dem. På HoloLens 2 tillhandahålls de data som ska användas för åter givning av *HolographicFrame* precis som i andra Holographic-program. Vid en [simulering av Skriv bordet](../../concepts/graphics-bindings.md)hämtas de via resultatet av `Update` funktionen.

## <a name="camera-settings"></a>Kamera inställningar

Följande egenskaper kan ändras i kamera inställningarna:

**Nära och långt plan:**

För att se till att inga ogiltiga intervall kan anges är **NearPlane** -och **FarPlane** -egenskaperna skrivskyddade och en separat funktion **SetNearAndFarPlane** finns för att ändra intervallet. Dessa data kommer att skickas till servern i slutet av ramen.

> [!IMPORTANT]
> I Union hanteras detta automatiskt när du ändrar huvud kamerans nära och ett långt plan.

**EnableDepth**:

Ibland är det bra att inaktivera bufferten för djupet i fjärravbildningen för fel sökning. Genom att inaktivera djupet stoppas även servern från att skicka djupgående data, vilket minskar bandbredden.

> [!TIP]
> I Union anges en fel söknings komponent som heter **EnableDepthComponent** som kan användas för att växla den här funktionen i redigerings gränssnittet.

Du kan ändra kamera inställningarna på följande sätt:

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>API-dokumentation

* [C#-CameraSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++-CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. Update-funktion](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: uppdaterings funktion](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Nästa steg

* [Grafik bindning](../../concepts/graphics-bindings.md)
* [Omprojektion av sena steg](late-stage-reprojection.md)
