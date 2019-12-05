---
title: Hämta Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Tal tjänsten fungerar med en mängd olika enheter och ljud källor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. I den här artikeln får du lära dig hur du får åtkomst till tal enheter SDK och börjar utveckla.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cdf447239da2f99c208ebd555f3d68e0302699b8
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806243"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Hämta Cognitive Services tal enheter SDK

Tal enheter SDK är ett förjusterat bibliotek som utformats för att fungera med syftes färdiga utvecklings paket och varierande konfigurationer för mikrofon mat ris.

## <a name="choose-a-development-kit"></a>Välj ett utvecklings paket

|Enheter|Specifikation|Beskrivning|Scenarier|
|--|--|--|--|
|[Roobo Smart Audio dev-paket](https://ddk.roobo.com)</br>[Installations](speech-devices-sdk-roobo-v1.md) / [snabb start](speech-devices-sdk-android-quickstart.md)![Roobo Smart Audio dev kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 MIC-matris, ARM-SOC, WIFI, ljud ut, i/o. </br>[Android](speech-devices-sdk-android-quickstart.md)|De första tal enheternas SDK för att anpassa Microsoft MIC-matrisen och front bearbetnings-SDK: n för att utveckla högkvalitativa avskrifter och tal scenarier|Konversations avskrift, smart högtalare, röst agent, Wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)</br>[Installations](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [snabb start](speech-devices-sdk-windows-quickstart.md)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 MIC mat ris RGB-och djup kameror. </br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Ett Developer Kit med AI-sensorer (Advanced artificiell Intelligence) för att skapa sofistikerade modeller för dator vision och tal. Den kombinerar en förstklassig mat ris-och djup kamera med hög klass med en video kamera och orienterings sensor – allt i en liten enhet med flera lägen, alternativ och SDK: er för att hantera en mängd olika beräknings typer.|Konversations avskrift, Robotics, smart uppbyggnad|
|Roobo Smart Audio dev kit 2![Roobo Smart Audio dev kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 MIC-matris, ARM-SOC, WIFI, Bluetooth, i/o. </br>Linux|De andra generationens tal enheter SDK som tillhandahåller alternativa OS och fler funktioner i en kostnads effektiv referens design.|Konversations avskrift, smart högtalare, röst agent, Wearable|
|URbetter T11-utvecklings tavlan![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 MIC-matris, ARM-SOC, WIFI, Ethernet, HDMI, USB-kamera. </br>Linux|En nivå för tal enheter på bransch nivå som anpassar Microsoft MIC-matrisen och stöder utökade I/O, till exempel HDMI/Ethernet och mer USB-kringutrustning|Konversations avskrift, utbildning, sjukhus, robots, OTT Box, Voice agent, enhet till|

## <a name="download-the-speech-devices-sdk"></a>Ladda ned SDK för tal-enheter

Hämta [tal enheter SDK](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med SDK för tal-enheter](https://aka.ms/sdsdk-quickstart)
