---
title: Licensiering&reg; av Microsoft Smooth Streaming Client Porting Kit
description: Läs mer om hur&reg; du licensierar Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: bd77c53a195a9549f6aaad9ee2928206f5324b0c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686899"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licensiering&reg; av Microsoft Smooth Streaming Client Porting Kit 
## <a name="overview"></a>Översikt
Microsoft Smooth Streaming Client Porting Kit **(SSPK** för kort) är en Smooth Streaming klientimplementering som är optimerad för att hjälpa inbäddade enhetstillverkare, kabel-och mobiloperatörer, innehållsleverantörer, mobiltelefontillverkare, oberoende programvaruleverantörer (ISV) och lösningsleverantörer för att skapa produkter och tjänster för direktuppspelning adaptivt innehåll i Smooth Streaming-format. SSPK är en enhet och plattformsoberoende implementering av Smooth Streaming-klient som kan portas av licenstagaren till vilken enhet och plattform som helst. 

Nedan ingår en arkitektur på hög nivå och rutan IIS Smooth Streaming Porting Kit är implementeringen av Smooth Streaming Client som tillhandahålls av Microsoft och innehåller all kärnlogik för uppspelning av Smooth Streaming-innehåll. Det här innehållet portas sedan av partner för en viss enhet eller plattform genom att implementera lämpliga gränssnitt. 

![SSPK (SSPK)](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Beskrivning
SSPK är licensierat på villkor som erbjuder utmärkt affärsvärde. SSPK-licensen förser branschen med:

* Källa för smooth streaming porting kit i C++ 
  * implementerar smooth streaming-klientfunktioner
  * lägger till formattolkning, heuristik, buffringslogik, etc.
* API:er för spelarprogram 
  * programmeringsgränssnitt för interaktion med ett mediespelarprogram
* PAL-gränssnitt (Platform Abstraction Layer) 
  * programmeringsgränssnitt för interaktion med operativsystemet (trådar, uttag)
* HAL-gränssnitt (Hardware Abstraction Layer) 
  * programmeringsgränssnitt för interaktion med maskinvaru-A/V-avkodare (avkodning, rendering)
* DRM-gränssnitt (Digital Rights Management) 
  * programmeringsgränssnitt för hantering av DRM genom DRM Abstraction Layer (DAL)
  * Microsoft PlayReady Porting Kit levereras separat men integreras via det här gränssnittet. Om du vill ha mer information om licensiering av Microsoft PlayReady Device klickar du [här](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Exempel på implementering 
  * exempel PAL-implementering för Linux
  * exempel HAL-implementering för GStreamer

## <a name="licensing-options"></a>Licensieringsalternativ
Microsoft Smooth Streaming Client Porting Kit görs tillgängligt för licenstagare enligt två olika licensavtal: ett för att utveckla Smooth Streaming Client Interim Products och en annan för att distribuera Smooth Streaming Client Final Products till slutanvändare.

* För chipet tillverkare, systemintegratörer eller oberoende programvaruleverantörer (ISV) som kräver en källkod portning kit för att utveckla Interim Products, en Microsoft Smooth Streaming Client Porting Kit **Interim Product License** bör utföras.
* För enhetstillverkare eller ISV:er som kräver distributionsrättigheter för smooth streaming-klientfinalprodukter till slutanvändare bör Microsoft Smooth Streaming Client Porting Kit **Final Product License** utföras.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Client Porting Kit Interim Product License
Under den här licensen erbjuder Microsoft ett Smooth Streaming Client Porting Kit och nödvändiga immateriella rättigheter för att utveckla och distribuera Smooth Streaming Client Interim Products till andra Smooth Streaming Client Porting Kit-enhetslicenstagare som distribuerar Smooth Streaming Client Final Products.

#### <a name="fee-structure"></a>Avgiftsstruktur
En engångslicensavgift på 50 000 USD ger tillgång till Smooth Streaming Client Porting Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Slutproduktlicens för Microsoft Smooth Streaming Client Porting Kit
Under den här licensen erbjuder Microsoft alla nödvändiga immateriella rättigheter för att ta emot Smooth Streaming Client Interim Products från andra Smooth Streaming Client Porting Kit-licenstagare och för att distribuera företagsmärkta Smooth Streaming Client Final Products till slutanvändare.

#### <a name="fee-structure"></a>Avgiftsstruktur
Smooth Streaming Client Final Product erbjuds under en royaltymodell enligt:

* $0.10 per enhetsimplementering som levereras
* Royaltyn är begränsad till $ 50.000 varje år
* Ingen royalty för de första 10 000 enhetsimplementeringarna varje år 

## <a name="licensing-procedure-and-sspk-access"></a>Licensieringsförfarande och SSPK-åtkomst
E-post [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) för alla licensfrågor.

SSPK:s distributionsportal är tillgänglig för registrerade interimslicenstagare.

Interims- och Final SSPK-licenstagare kan ställa tekniska frågor till [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming Client Interim Product Agreement Licensees

* Adroit Affärslösningar, Inc
* Avancerad sas för digital sändning
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digitala tjänster, Inc.
* Arion Teknologi, Inc.
* AVC Multimedia Software Co, Ltd
* Cavium, Inc.
* EchoStar inköpsbolag
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Elektronik., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Liberty Globala tjänster BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffran Digital Limited
* Sichuan Changhong Elektriska Co, Ltd
* MjukHem
* Sony Företag
* Tatung Teknik Inc.
* Top Victory Investeringar, Ltd
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE-bolag

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming Client Final Product Agreement Licenstagare
* Avancerad sas för digital sändning
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digitala tjänster, Inc.
* AmtRAN Technology Co, Ltd
* Arcadyan Teknologi Corporation
* Arion Teknologi, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş (på andra sätt)
* Brittiska Sky Broadcasting Limited
* CastPal Teknologi Inc., Shenzhen
* Compal Elektronik, Inc.
* Dongguan Digital AV Technology Corp, Ltd
* EchoStar inköpsbolag
* Enseo, Inc.
* FilmFlex Filmer Limited
* Fluendo S.A.
* FUNAI ELEKTRISK CO., LTD
* Gibson Innovationer Limited
* Haier Information Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co, Ltd 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co, Ltd
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI-bolag
* Nintendo Co., Ltd.
* Orange SA
* Saffran Digital Limited
* Sagemcom Bredband SAS
* Shenzhen Chuangwei-RGB Electronics Co, Ltd
* Shenzhen Coship Elektronik CO., LTD
* Shenzhen Jiuzhou Electric Co, Ltd
* Shenzhen Skyworth digital teknik Co, Ltd
* Sichuan Changhong Electric Co, Ltd
* Skardin Industri Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* MjukHem
* Sony Företag
* Technicolor Leveransteknik, SAS
* Tongfang Global Ltd.
* Top Victory Investeringar, Ltd
* Toshiba Lifestyle Produkter & Services Corporation
* Universal Media Corporation /Slovakien/ s.r.o.
* VIZIO, Inc.
* Wistron Företag
* ZTE-bolag

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

