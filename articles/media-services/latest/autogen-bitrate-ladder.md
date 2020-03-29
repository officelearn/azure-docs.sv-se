---
title: Koda videor med Standardkodare i Media Services - Azure | Microsoft-dokument
description: Det här avsnittet visar hur du använder standardkodaren i Media Services för att koda en indatavideo med en automatiskt genererad bithastighetsstege, baserat på indataupplösningen och bithastigheten.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733325"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Koda med en automatiskt genererad bithastighetsstege

## <a name="overview"></a>Översikt

I den här artikeln beskrivs hur du använder standardkodaren i Media Services för att koda en indatavideo till en automatiskt genererad bithastighetsstege (bitrate-resolution-par) baserat på indataupplösningen och bithastigheten. Den här inbyggda kodarinställningen, eller förinställningen, överskrider aldrig indataupplösningen och bithastigheten. Om indata till exempel är 720p vid 3 Mbit/s är utdata i bästa fall 720p och börjar med lägre hastigheter än 3 Mbit/s.

### <a name="encoding-for-streaming"></a>Kodning för direktuppspelning

När du använder förinställningen **AdaptiveStreaming** i **Transform**får du en utdata som är lämplig för leverans via direktuppspelningsprotokoll som HLS och DASH. När du använder den här förinställningen bestämmer tjänsten på ett intelligent sätt hur många videolager som ska genereras och vilken bithastighet och upplösning. Utdatainnehållet innehåller MP4-filer där AAC-kodat ljud och H.264-kodad video inte är interfolierad.

Ett exempel på hur den här förinställningen används finns i [Strömma en fil](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Resultat

I det här avsnittet visas tre exempel på utdatavideolager som produceras av Media Services-kodaren som ett resultat av kodning med **förinställningen AdaptiveStreaming.** I samtliga fall innehåller utdata en MP4-fil med endast ljudkoda på 128 kbit/s.

### <a name="example-1"></a>Exempel 1
Källa med höjd "1080" och framerate "29.970" producerar 6 videolager:

|Lager|Höjd|Bredd|Bithastighet (kbit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exempel 2
Källa med höjd "720" och framerate "23.970" producerar 5 videolager:

|Lager|Höjd|Bredd|Bithastighet (kbit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exempel 3
Källa med höjd "360" och framerate "29.970" producerar 3 videolager:

|Lager|Höjd|Bredd|Bithastighet (kbit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Strömma en fil](stream-files-dotnet-quickstart.md)
