---
title: Använda Standard-kodare i Azure Media Services för att koda videor med en automatiskt genererad bithastighet stege | Microsoft Docs
description: Det här avsnittet visar hur du använder Standard-kodare i Media Services för att koda indata video med en automatiskt genererad bithastighet stege, baserat på inkommande upplösning och bithastighet. Inkommande upplösning och bithastighet kommer aldrig att överskridas. Till exempel om indata är 720p på 3 Mbit/s, utdata kommer förblir 720p i bästa och startar priser som är lägre än 3 Mbit/s.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 5d13c711d7d71df7469e6408ce78cf0df611632b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Koda med en automatiskt genererad bithastighet stege

## <a name="overview"></a>Översikt

Den här artikeln förklarar hur du använder Standard-kodare i Media Services för att koda ett indatavideo till en automatiskt genererad bithastighet stege (bithastighet upplösning par) baserat på inkommande upplösning och bithastighet. Den här inbyggda kodare inställning eller förinställning, kan inte överstiga inkommande upplösning och bithastighet. Till exempel om indata är 720p med 3 Mbit/s, utdata förblir 720p i bästa och startar priser som är lägre än 3 Mbit/s.

### <a name="encoding-for-streaming"></a>Kodning för strömning

Som namnet antyder, om du använder den **AdaptiveStreaming** förinställningen när du skapar en kodning transformering visas utdata som passar för leverans via strömningsprotokoll som HLS, DASH, CMAF osv. När du använder detta **AdaptiveStreaming** förinställts kodaren Intelligent bestämmer hur många video lager för att generera och på vilka bithastighet och upplösning. Utdata innehåller tillgångsinformation där AAC-kodat ljud och video H.264-kodade inte är överlagrad MP4-filer.

Om du vill se ett exempel på hur denna inställning används, se [överföra en fil](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Resultat

Det här avsnittet innehåller tre exempel utdata video lager produceras av på grund av encoding med Media Services kodaren den **AdaptiveStreaming** förinställda. I samtliga fall innehåller utdata ljuddata MP4-fil med stereoljud kodade 128 kbit/s.

### <a name="example-1"></a>Exempel 1
Källa med höjd ”1080” och ramhastighet ”29.970” ger 6 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exempel 2
Källa med höjd ”720” och ramhastighet ”23.970” ger 5 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exempel 3
Källa med höjd ”360” och ramhastighet ”29.970” ger 3 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Överföra en fil](stream-files-dotnet-quickstart.md)
