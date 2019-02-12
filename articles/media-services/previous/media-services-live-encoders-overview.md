---
title: Konfigurera lokala kodare med Azure Media Services för att skapa dataströmmar med flera bithastigheter | Microsoft Docs
description: Det här avsnittet innehåller en lokal livekodare som du kan använda för att spara din live-händelser och skicka en direktsänd dataström med enkel bithastighet till AMS-kanaler (som är direktsänd kodning aktiverat) för vidare bearbetning. Innehåller avsnittslänkar till självstudier om hur du konfigurerar listade kodare.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 50e1d3c894455f31513f7bcd1bff42a9329b329a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992882"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Så här konfigurerar du lokala kodare med Azure Media Services för att skapa dataströmmar med flera bithastigheter
Det här avsnittet innehåller en lokal livekodare som du kan använda för att spara din live-händelser och skicka en direktsänd dataström med enkel bithastighet till AMS-kanaler (som är direktsänd kodning aktiverat) för vidare bearbetning. Avsnittet innehåller också länkar till självstudier om hur du konfigurerar listade kodare.

> [!NOTE]
> När strömmande via RTMP, kontrollerar du inställningarna för brandväggen och/eller proxyn för att bekräfta att utgående TCP-portarna 1935 och 1936 är öppna.

## <a name="flash-media-live-encoder"></a>Flash Media Live Encoder
Information om hur du konfigurerar den [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) att skicka en direktsänd dataström med enkel bithastighet till en kanal för AMS finns i [konfigurera FMLE](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Haivision KB-kodare
Information om hur du konfigurerar den [Haivision KB-kodare](https://www.haivision.com/products/kb-series/) encoder att skicka en direktsänd dataström med enkel bithastighet till en kanal för AMS finns i [konfigurera Haivision KB-kodare](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Information om hur du konfigurerar den [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) encoder att skicka en direktsänd dataström med enkel bithastighet till en kanal för AMS finns i [konfigurera Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Information om hur du konfigurerar den [Tricaster](http://newtek.com/products/tricaster-40.html) encoder att skicka en direktsänd dataström med enkel bithastighet till en kanal för AMS finns i [konfigurera Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Mer information finns i [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg

[Liveuppspelning med Azure Media Services för att skapa dataströmmar med flera bithastigheter](media-services-manage-live-encoder-enabled-channels.md).

