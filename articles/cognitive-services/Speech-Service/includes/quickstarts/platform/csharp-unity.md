---
title: 'Snabb start: tal SDK C# för Unity Platform setup – Speech service'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform C# för Unity med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 064916c0dad52749f724be8a1912f247128114b6
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816243"
---
Den här guiden visar hur du installerar [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för [Unity](https://unity3d.com/).

> [!NOTE]
> Talet SDK för Unit stöder Windows Desktop (x86 och x64) eller Universell Windows-plattform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) och iOS (x64 simulator, ARM32 och ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

- [2018,3 eller senare](https://store.unity.com/) med [Unity 2019,1 lägga till stöd för UWP-arm64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Version 15,9 eller senare av Visual Studio 2017 är också acceptabelt.
- För Windows ARM64-stöd installerar du de [valfria build-verktygen för arm64 och Windows 10 SDK för arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Följ dessa steg om du vill installera tal-SDK för Unity:

1. Hämta och öppna [tal-SDK för](https://aka.ms/csspeech/unitypackage)enhets enhet, som paketeras som ett Unity Asset-paket (. unitypackage) och bör redan vara kopplat till Unity. När du öppnar till gångs paketet visas dialog rutan **Importera Unity-paket** . Du kan behöva skapa och öppna ett tomt projekt för att det här steget ska fungera.

   [dialog rutan ![importera unions paket i Unity-redigeraren](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Se till att alla filer är markerade och välj **Importera**. Efter en liten stund importeras Unity Asset-paketet till projektet.

Mer information om hur du importerar till gångs paket i enighet finns i [Unity-dokumentationen](https://docs.unity3d.com/Manual/AssetPackages.html).

Nu kan du gå vidare till [Nästa steg](#next-steps) nedan.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
