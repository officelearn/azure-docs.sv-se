---
title: Licensiering Microsoft® jämn direktuppspelning Klientportningskit
description: Mer information om hur till licensiering Microsoft® Smooth Streaming-klienten porta Kit.
services: media-services
documentationcenter: ''
author: xpouyat
manager: cfowler
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: xpouyat
ms.openlocfilehash: 71cb4ff7c5948067dfb039caf69241cfa9e22408
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243904"
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
* Adroit företag lösningar, Inc
* Avancerade Digital sändning SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret verksamma ämnet
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon digitala tjänster, Inc.
* Arion teknik, Inc.
* AVC Multimedia programvara Co., Ltd.
* Cavium, Inc.
* EchoStar köpa Corporation
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Liberty globala tjänster BV
* MediaTek Inc.
* MStar Co, Ltd
* NINTENDO Co., Ltd.
* OpenTV, Inc.
* T.ex Digital begränsad
* Av Changhong Electric Co., Ltd.
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* TCL teknik (Huizhou) för Electronics Co., Ltd.
* Översta Victory investeringar, Ltd.
* Vestel Elektronik Sanayi stött Ticaret verksamma ämnet
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming-klient slutliga produkt avtal licenstagare
* Avancerade Digital sändning SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret verksamma ämnet
* Albis Technologies Ltd.
* Amazon digitala tjänster, Inc.
* AmTRAN teknik Co., Ltd.
* Arcadyan Technology Corporation
* Arion teknik, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* Brittiska Sky Broadcasting begränsad
* CastPal teknik Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV teknik Corp., Ltd.
* EchoStar köpa Corporation
* Enseo, Inc.
* Filmflex filmer begränsad
* Fluendo S.A.
* Gibson innovationer begränsad
* Haier Information Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd.
* Hon Hai Precision bransch Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* NINTENDO Co., Ltd.
* Orange SA
* T.ex Digital begränsad
* Sagemcom bredband SAS
* Shenzhen Coship Electronics CO., Ltd.
* Shenzhen Jiuzhou Electric Co., Ltd.
* Shenzhen Skyworth Digital teknik Co., Ltd.
* Av Changhong Electric Co., Ltd.
* Skardin industriella Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Begränsad TCL utländskt nummer marknadsföring (Offshore kommersiell Macao)
* SAS-Technicolor leverans teknik
* Tongfang globala Ltd.
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

