---
title: Hämta Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Taltjänsten fungerar med en mängd olika enheter och ljudkällor. Nu kan du ta dina talprogram till nästa nivå med matchad maskinvara och programvara. I den här artikeln får du lära dig hur du får tillgång till Talenheter SDK och börja utveckla.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f8c1500dbbd9135a850e145199de8fea68cc4630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220538"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Hämta SDK för Cognitive Services-talenheter

Speech Devices SDK är ett förinstämt bibliotek som utformats för att fungera med specialbyggda utvecklingspaket och olika konfigurationer av mikrofonmatris.

## <a name="choose-a-development-kit"></a>Välj ett utvecklingspaket

|Enheter|Specifikation|Beskrivning|Scenarier|
|--|--|--|--|
|[Roobo Smart Audio Dev Kit](https://ddk.roobo.com)<br>[Installation](speech-devices-sdk-roobo-v1.md) / [Quickstart](speech-devices-sdk-android-quickstart.md)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mic Array, ARM SOC, WIFI, Audio Out, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|Den första Speech Devices SDK att anpassa Microsoft Mic Array och frontbearbetning SDK, för att utveckla högkvalitativa transkription och talscenarier|Konversationsavskrift, smart högtalare, röstagent, bärbar|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Snabbstart azure](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / Kinect DK för[installation](speech-devices-sdk-windows-quickstart.md)![](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Mic Array RGB och Djup kameror. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Ett utvecklarpaket med avancerade artificiell intelligenssensorer (AI) för att bygga sofistikerade datorseende- och talmodeller. Den kombinerar en förstklassig rumslig mikrofonmatris och djupkamera med en videokamera och orienteringssensor – allt i en liten enhet med flera lägen, alternativ och SDK:er för att rymma en rad beräkningstyper.|Konversation transkription, Robotics, Smart Building|
|Roobo Smart Audio Dev Kit 2<br>[Installation](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic Array, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|Den andra generationens Talenheter SDK som ger alternativa OPERATIVSYSTEM och fler funktioner i en kostnadseffektiv referensdesign.|Konversationsavskrift, smart högtalare, röstagent, bärbar|
|URbetter T11 Utvecklingsnämnd![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMI, USB-kamera. <br>Linux|En talenhet på branschnivå SDK som anpassar Microsoft Mic-matrisen och stöder utökad I/O som HDMI/Ethernet och mer USB-kringutrustning|Konversation transkription, Utbildning, Sjukhus, Robotar, OTT Box, Röstagent, Drive Thru|

## <a name="download-the-speech-devices-sdk"></a>Ladda ner SDK för talenheter

Ladda ner [SDK för talenheter](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Komma igång med SDK för talenheter](https://aka.ms/sdsdk-quickstart)
