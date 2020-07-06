---
title: Koda videor med standard-kodare i Media Services-Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder standard-kodaren i Media Services för att koda en indataport med en automatiskt genererad bit hastighets steg, baserat på inlösningen för inläsning och bit hastighet.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "60733325"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Koda med en automatiskt genererad bit hastighets steg

## <a name="overview"></a>Översikt

I den här artikeln förklaras hur du använder standard-kodaren i Media Services för att koda en inmatad video till en automatiskt genererad bit hastighets steg (bit upplösnings par) utifrån inlösningen och bit hastigheten. Den här inbyggda kodarens inställning, eller för inställning, kommer aldrig att överskrida den angivna upplösningen och bit hastigheten. Om indata t. ex. är 720p vid 3 Mbit/s, är utmatningen översatt till bästa och kommer att starta med lägre taxa än 3 Mbit/s.

### <a name="encoding-for-streaming"></a>Kodning för strömning

När du använder **AdaptiveStreaming** -förinställningen i **Transform**får du en utmatning som passar för leverans via strömmande protokoll som HLS och tank streck. När du använder den här för inställningen bestämmer tjänsten intelligent hur många video lager som ska genereras och med vilken bit hastighet och upplösning. Utdata-innehållet innehåller MP4-filer där AAC-kodad ljud-och H. 264-kodad video inte är överlagrad.

Om du vill se ett exempel på hur denna för inställning används, se [strömma en fil](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Utdata

Det här avsnittet innehåller tre exempel på de video lager för utdata som produceras av Media Services Encoder som ett resultat av kodning med **AdaptiveStreaming** -förinställningen. I samtliga fall innehåller utdata en MP4-fil med stereo ljud som är kodad med 128 kbps.

### <a name="example-1"></a>Exempel 1
Källa med höjden "1080" och RAM hastighet "29,970" producerar 6 video lager:

|Lager|Höjd|Bredd|Bit hastighet (kbit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exempel 2
Källa med höjden "720" och RAM hastighet "23,970" producerar 5 video lager:

|Lager|Höjd|Bredd|Bit hastighet (kbit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exempel 3
Källa med höjden "360" och RAM hastighet "29,970" producerar 3 video lager:

|Lager|Höjd|Bredd|Bit hastighet (kbit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Strömma en fil](stream-files-dotnet-quickstart.md)
