---
title: Licensiering Microsoft® jämn direktuppspelning Klientportningskit
description: Mer information om hur till licensiering Microsoft® Smooth Streaming-klienten porta Kit.
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
ms.date: 02/08/2019
ms.author: xpouyat
ms.openlocfilehash: 6aefa6b8745d14f59981520660b1f4c1323f8617
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456167"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licensiering Microsoft® jämn direktuppspelning Klientportningskit 
## <a name="overview"></a>Översikt
Microsoft Smooth Streaming-klienten porta Kit (**SSPK** för kort) är en Smooth Streaming-klientimplementeringen som är optimerad för att hjälpa embedded enhetstillverkare, kabel och mobila operatörer, innehåll tjänstleverantörer, luren tillverkare, oberoende programvaruleverantörer (ISV), och lösningsleverantörer skapa produkter och tjänster för direktuppspelning med anpassningsbar innehåll i Smooth Streaming-format. SSPK är en enhet och plattformsoberoende implementering av Smooth Streaming-klient som kan överföras av licenstagaren till alla enheter och plattformar. 

Finns med nedan är den övergripande arkitekturen och IIS Smooth Streaming porta Kit är Smooth Streaming-klient-implementering som tillhandahålls av Microsoft och innehåller alla kärnlogik för uppspelning av Smooth Streaming-innehåll. Det här innehållet är sedan överförde av partner för en viss enhet eller plattform genom att implementera lämpliga gränssnitt. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Beskrivning
SSPK licensieras på villkor som erbjuder utmärkt affärsvärde. SSPK licens får branschen:

* Smooth Streaming porta Kit källa i C++ 
  * implementerar Smooth Streaming klientfunktioner
  * lägger till formatet parsning heuristik, buffring logik och så vidare.
* Spelarappen API: er 
  * programmeringsgränssnitt för interaktion med ett mediespelarprogram
* Plattformen Abstraction Layer (PAL)-gränssnitt 
  * programmeringsgränssnitt för interaktion med operativsystemet (trådar, sockets)
* Maskinvarugränssnitt Abstraction Layer (HAL) 
  * programmeringsgränssnitt för interaktion med maskinvara A / V-avkodare (avkodning, rendering)
* Digital Rights Management (DRM)-gränssnitt 
  * programmeringsgränssnitt för hantering av DRM via DRM Abstraction Layer (DAL)
  * Microsoft PlayReady porta Kit levereras separat men integrerar via det här gränssnittet. Mer information om licensiering för Microsoft PlayReady Device klickar du på [här](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Implementering-exempel 
  * exempel på PAL implementering för Linux
  * exempel på HAL implementering för GStreamer

## <a name="licensing-options"></a>Licensieringsalternativ
Microsoft Smooth Streaming-klienten porta Kit görs tillgänglig för licenstagare under två distinkta licensavtal: en för att utveckla Smooth Streaming-tiden klientprodukter och en annan för att distribuera Smooth Streaming-klienten slutprodukterna för slutanvändare.

* För kretsuppsättning tillverkare, systemintegrerare eller oberoende leverantörer (ISV) som kräver en källa code porteringsguiden kit för att utveckla tiden produkter, Microsoft Smooth Streaming-klienten porta Kit **tiden produktlicens** ska köras.
* För enhetstillverkare eller ISV: er som behöver distributionsrättigheter för Smooth Streaming-klienten slutprodukter till slutanvändare, Microsoft Smooth Streaming-klienten porta Kit **sista produktlicens** ska köras.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming-klient porta Kit tiden produktlicens
Under denna licens, erbjuder Microsoft en Smooth Streaming-klienten porta Kit och nödvändiga immateriella rättigheter att utveckla och distribuera Smooth Streaming-tiden klientprodukter till andra Smooth Streaming-klienten porta Kit enheten licenstagare som distribuera Smooth Streaming-klienten slutprodukter.

#### <a name="fee-structure"></a>Avgift struktur
En enstaka licensavgiften för USA $ 50 000 ger åtkomst till den Smooth Streaming-klienten porta Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming-klient porta Kit sista produktlicens
Under denna licens erbjuder Microsoft alla nödvändiga immateriella rättigheter att ta emot Smooth Streaming-tiden klientprodukter från andra Smooth Streaming-klienten porta Kit licenstagare samt för att distribuera företagsanpassad Smooth Streaming-klienten sista Produkter för slutanvändare.

#### <a name="fee-structure"></a>Avgift struktur
Smooth Streaming-klienten slutgiltiga produkten finns under en royalty-modell som under:

* $0.10 per enhetsimplementering levereras
* Royalty är högst 50 000 per år
* Ingen royalty för första 10 000 enheter implementeringar varje år 

## <a name="licensing-procedure-and-sspk-access"></a>Licensiering proceduren och SSPK åtkomst
E-post [ sspkinfo@microsoft.com ](mailto:sspkinfo@microsoft.com) licensiering för alla frågor.

Den [SSPK Distribution portal](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) är tillgänglig för registrerade tiden licenstagare.

Tiden och slutlig SSPK licenstagare kan skicka in tekniska frågor till [ smoothpk@microsoft.com ](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming-klient tillfälliga produkten avtal licenstagare
* Adroit Business Solutions, Inc
* Avancerade Digital sändning SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret verksamma ämnet
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar köpa Corporation
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* T.ex Digital begränsad
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* TCL Technology Electronics (Huizhou) Co., Ltd.
* Översta Victory investeringar, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming-klient slutliga produkt avtal licenstagare
* Avancerade Digital sändning SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret verksamma ämnet
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* Brittiska Sky Broadcasting begränsad
* CastPal Technology Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar köpa Corporation
* Enseo, Inc.
* FilmFlex Movies Limited
* Fluendo S.A.
* Gibson Innovations Limited
* Haier Information Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Orange SA
* T.ex Digital begränsad
* Sagemcom Broadband SAS
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Begränsad TCL utländskt nummer marknadsföring (Offshore kommersiell Macao)
* Technicolor Delivery Technologies, SAS
* Tongfang Global Ltd.
* Översta Victory investeringar, Ltd.
* Toshiba livsstil produkter och tjänster Corporation
* Universal Media Corporation /Slovakia/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

