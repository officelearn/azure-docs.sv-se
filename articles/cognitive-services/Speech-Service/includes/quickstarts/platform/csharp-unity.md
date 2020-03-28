---
title: 'Snabbstart: Plattformsinställning för Tal SDK för C# Unity - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för C# Unity med taltjänsten SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 8d9e05b5d8105cce8355d217cc6922c25bbe1730
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468607"
---
Den här guiden visar hur du installerar [Tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för [enhet](https://unity3d.com/).

> [!NOTE]
> Speech SDK för unity stöder Windows Desktop (x86 och x64) eller Universal Windows Platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) och iOS (x64 simulator, ARM32 och ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

- [Unity 2018.3 eller senare](https://store.unity.com/) med [Unity 2019.1 lägga till stöd för UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Version 15.9 eller högre av Visual Studio 2017 är också acceptabelt.
- För Windows ARM64-stöd installerar du de [valfria byggverktygen för ARM64 och Windows 10 SDK för ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Så här installerar du Tal-SDK för enhet:

1. Ladda ner och öppna [Tal SDK för enhet](https://aka.ms/csspeech/unitypackage), som är förpackat som ett Unity-tillgångspaket (.unitypackage) och bör redan associeras med Unity. När tillgångspaketet öppnas visas dialogrutan **Importera enhetspaket.** Du kan behöva skapa och öppna ett tomt projekt för att det här steget ska fungera.

   [![Dialogrutan Importera Enhetspaket i Unity Editor](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Kontrollera att alla filer är markerade och välj **Importera**. Efter en stund importeras Unity-tillgångspaketet till projektet.

Mer information om hur du importerar tillgångspaket till Unity finns i [Unity-dokumentationen](https://docs.unity3d.com/Manual/AssetPackages.html).

Du kan nu gå vidare till [Nästa steg](#next-steps) nedan.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
