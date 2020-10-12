---
title: Så här konfigurerar du en mikrofons mat ris-tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar en mikrofon mat ris så att tal enheternas SDK kan använda den.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: a2652bed6c8e7dec0a6fe8f9471793c3873646bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "82781761"
---
# <a name="how-to-configure-a-microphone-array"></a>Konfigurera en mikrofonmatris

I den här artikeln får du lära dig hur du konfigurerar en [mikrofon mat ris](https://aka.ms/sdsdk-microphone). Det innefattar att ange arbets vinkeln och hur du väljer vilken mikrofon som ska användas för tal enheter SDK.

Tal enheter SDK fungerar bäst med en mikrofon som har utformats enligt [våra rikt linjer](https://aka.ms/sdsdk-microphone). Mikrofonens mat ris konfiguration kan tillhandahållas av operativ systemet eller anges via någon av följande metoder.

Tal enheter SDK ursprungligen stödde Microphone-matriser genom att välja från en fast uppsättning konfigurationer.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

I Windows anges mikrofonens mat ris konfiguration av ljud driv rutinen.

Från v-1.11.0 stöder tal enheter SDK också konfiguration från en [JSON-fil](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
I Windows hämtas geometri information från mikrofonen automatiskt från ljud driv rutinen. Så, egenskaperna `DeviceGeometry` ,  `SelectedGeometry` och `MicArrayGeometryConfigFile` är valfria. Vi använder [JSON-filen](https://aka.ms/sdsdk-micarray-json) som används `MicArrayGeometryConfigFile` för att bara hämta beamforming-intervallet.

Om en mikrofon mat ris anges med använder `AudioConfig::FromMicrophoneInput` vi den angivna mikrofonen. Om ingen mikrofon anges eller `AudioConfig::FromDefaultMicrophoneInput` anropas använder vi standard mikrofonen, som anges i ljud inställningar i Windows.
Microsoft Audio-stacken i tal enhets-SDK: n stöder bara samplings frekvenser som är integrala med 16 KHz.

## <a name="linux"></a>Linux
På Linux måste du tillhandahålla mikrofonens geometri information. Användningen av `DeviceGeometry` och `SelectedGeometry` stöds fortfarande. Den kan också tillhandahållas via JSON-filen med hjälp av `MicArrayGeometryConfigFile` egenskapen. I likhet med Windows kan beamforming-intervallet tillhandahållas av JSON-filen.

Om en mikrofon mat ris anges med använder `AudioConfig::FromMicrophoneInput` vi den angivna mikrofonen. Om en mikrofon inte anges eller `AudioConfig::FromDefaultMicrophoneInput` anropas, registrerar vi från alsa-enheten med namnet *default*. Som standard pekar *standardvärdet* på kort 0 enhet 0, men användare kan ändra den i `asound.conf` filen. 

Microsoft Audio-stacken i tal enheter SDK stöder bara nedsampling för exempel frekvenser som är integrala med 16 KHz. Dessutom stöds följande format: 32-bitars IEEE little endian Float, 32-bit little endian signerad int, 24-bitars little endian signerat int, 16-bitars little endian signerat heltal och 8-bitars signerat int.

## <a name="android"></a>Android
För närvarande stöds endast [Roobo v1](speech-devices-sdk-android-quickstart.md) av tal enheter SDK. Beteendet är samma som tidigare versioner, förutom `MicArrayGeometryConfigFile` att egenskapen nu kan användas för att ange en JSON-fil som innehåller beamforming-intervallet.

## <a name="microphone-array-configuration-json"></a>JSON för konfiguration av mikrofon mat ris

JSON-filen för mikrofon konfigurationen av Microphone-matrisen kommer att följa [JSON-schemat](https://aka.ms/sdsdk-micarray-json). Nedan följer några exempel som följer schemat.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Eller


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Eller

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj din tal enhet](get-speech-devices-sdk.md)
