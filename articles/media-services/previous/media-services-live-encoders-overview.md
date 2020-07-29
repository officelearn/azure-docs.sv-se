---
title: Konfigurera lokala kodare när du använder Azure Media Services för att skapa data strömmar med flera bit hastigheter | Microsoft Docs
description: Det här avsnittet innehåller en lista över lokala Live-kodare som du kan använda för att avbilda dina Live-händelser och skicka en enda bit Ströms direkt ström till AMS-kanaler (som är aktive rad i real tid) för vidare bearbetning. Avsnittet innehåller länkar till självstudier som visar hur du konfigurerar förtecknade kodare.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5a17f98d69cb2196f92e19b66fc23dcd89bce188
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687149"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Så här konfigurerar du lokala kodare när du använder Azure Media Services för att skapa data strömmar med flera bit hastigheter
Det här avsnittet innehåller en lista över lokala Live-kodare som du kan använda för att avbilda dina Live-händelser och skicka en enda bit Ströms direkt ström till AMS-kanaler (som är aktive rad i real tid) för vidare bearbetning. Avsnittet innehåller också länkar till självstudier som visar hur du konfigurerar kodare i listan.

> [!NOTE]
> Vid direktuppspelning via RTMP ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 1935 och 1936 är öppna.

## <a name="haivision-kb-encoder"></a>Haivision KB-kodare
Information om hur du konfigurerar [HAIVISION KB Encoder](https://www.haivision.com/products/kb-series/) Encoder för att skicka en enda bit Ströms bit ström till en AMS-kanal finns i [Konfigurera Haivision KB-kodare](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Wirecast för strömning
Information om hur du konfigurerar [NetStream Wirecast](https://www.telestream.net/wirecast/overview.htm) -kodaren för att skicka en enda bit Ströms bit ström till en AMS-kanal finns i [Konfigurera Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Mer information finns i [grundämne Live](https://www.elemental.com/products/aws-elemental-appliances-software/#elemental-live).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg

[Direkt uppspelning med Azure Media Services för att skapa data strömmar med flera bit](media-services-manage-live-encoder-enabled-channels.md)hastigheter.

