---
title: "Licensiering Microsoft® Smooth Streaming klienten portera Kit"
description: "Mer information om hur för Microsoft® Smooth Streaming klienten portera Kit."
services: media-services
documentationcenter: 
author: xpouyat
manager: cfowler
editor: 
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: xpouyat
ms.openlocfilehash: b5a36ac6771bef220afe29446cd56c1b65a498d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licensiering Microsoft® Smooth Streaming klienten portera Kit
## <a name="overview"></a>Översikt
Microsoft Smooth Streaming klienten portera Kit (**SSPK** för kort) är en Smooth Streaming-klient-implementering som är optimerad för att hjälpa inbäddade enhetstillverkare, kabel och mobila operatorer, innehåll leverantörer, luren tillverkare oberoende programvaruleverantörer (ISV) och lösningsleverantörer att skapa produkter och tjänster för direktuppspelning av anpassningsbar strömning innehåll i Smooth Streaming-format. SSPK är en enhet och plattform oberoende implementering av Smooth Streaming-klient som kan överföras av licenstagaren till enheten och plattform. 

Med nedan är en hög nivå arkitektur och IIS Smooth Streaming portera Kit är klienten för Smooth Streaming-implementering som tillhandahålls av Microsoft och innehåller alla kärnlogik för uppspelning av Smooth Streaming-innehåll. Detta är portar av partner för en viss enhet eller plattformen genom att implementera rätt gränssnitt. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Beskrivning
SSPK licensieras på villkor som ger utmärkt affärsvärde. SSPK licens får branschen:

* Smooth Streaming portera Kit källa i C++ 
  * implementerar Smooth Streaming klientfunktioner
  * lägger till formatet parsning heuristik, buffring logik osv.
* Programmet Player API: er 
  * programmeringsgränssnitt för interaktion med ett media player-program
* Plattform Abstraction Layer (PAL) gränssnitt 
  * programmeringsgränssnitt för interaktion med operativsystemet (trådar, sockets)
* Hardware Abstraction Layer (HAL) gränssnitt 
  * programmeringsgränssnitt för interaktion med A / V-avkodare (avkoda, återgivning)
* Digital Rights Management (DRM) gränssnitt 
  * programmeringsgränssnitt för hantering av DRM via DRM Abstraction Layer (DAL)
  * Microsoft PlayReady portera Kit levereras separat, men integrerar via det här gränssnittet. Mer information om Microsoft PlayReady Device licensiering, klickar du på [här](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Exempel på implementering 
  * exempel på PAL implementering för Linux
  * exempel på HAL implementering för GStreamer

## <a name="licensing-options"></a>Licensieringsalternativ
Microsoft Smooth Streaming klienten portera Kit görs tillgängligt för licenstagare enligt två distinkta licensavtal: en för att utveckla Smooth Streaming klienten Interim produkter och en annan för att distribuera Smooth Streaming klienten slutliga produkter till slutanvändare.

* För kretsuppsättning tillverkare, systemintegrerare eller oberoende leverantörer (ISV) som kräver en källa code portera kit för att utveckla Interim produkter, Microsoft Smooth Streaming klienten portera Kit **Interim produktlicensen** ska köras.
* För enhetstillverkare eller ISV: er som behöver distributionsrättigheter för Smooth Streaming klienten slutliga produkter till slutanvändare, Microsoft Smooth Streaming klienten portera Kit **slutliga produktlicensen** ska köras.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming klienten portera Kit Interim produktlicens
Under denna licens, erbjuder Microsoft en Smooth Streaming klienten portera Kit och nödvändiga immateriella rättigheter att utveckla och distribuera Smooth Streaming klienten Interim produkter till andra Smooth Streaming klienten portera Kit enheten licenstagare som distribuera Smooth Streaming klienten slutliga produkter.

#### <a name="fee-structure"></a>Avgift struktur
USA 50 000 enstaka licens avgift ger åtkomst till den Smooth Streaming klienten portera Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming klienten portera Kit Final produktlicens
Under denna licens erbjuder Microsoft alla nödvändiga immateriella rättigheter får Smooth Streaming klienten Interim produkter från andra Smooth Streaming klienten portera Kit licenstagare och distribuera företagsanpassad Smooth Streaming klienten slutliga Produkter för slutanvändare.

#### <a name="fee-structure"></a>Avgift struktur
Smooth Streaming klienten slutliga produkten erbjuds under en royalty modell som under:

* $0.10 per enhet implementering levererade
* Royalty är begränsad till 50 000 varje år
* Ingen royalty för första 10 000 enheter implementeringar varje år 

## <a name="licensing-procedure-and-sspk-access"></a>Licensiering proceduren och SSPK åtkomst
Kontakta e- [ sspkinfo@microsoft.com ](mailto:sspkinfo@microsoft.com) licensiering för alla frågor.

Den [SSPK Distribution portal](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) är tillgänglig för den registrerade Interim licenstagare.

Interimistisk och slutlig SSPK licenstagare kan skicka tekniska frågor till [ smoothpk@microsoft.com ](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming klienten tillfälliga produkten avtal licenstagare
* Adroit Business-lösningar, Inc
* Avancerade Digital Broadcast SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret a. s.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital tjänster, Inc.
* Arion Technology, Inc.
* AVC Multimedia programvara Companys
* Cavium, Inc.
* EchoStar köp Corporation
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Companys
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Liberty globala tjänster BV
* MediaTek Inc.
* MStar Co, Ltd
* NINTENDO Companys
* OpenTV, Inc.
* T.ex Digital begränsad
* Av Changhong elektriska Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* TCL Technoly Electronics (Huizhou) Companys
* TOP Victory investeringar, Ltd.
* Vestel Elektronik Sanayi para Ticaret a. s.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming klienten färdiga produkten avtal licenstagare
* Avancerade Digital Broadcast SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret a. s.
* Albis Technologies Ltd.
* Amazon Digital tjänster, Inc.
* AmTRAN teknik Companys
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. PARA TİC. A.Ş
* Brittiska Sky Broadcasting begränsad
* CastPal Technology Inc., Shenzhen
* Compal Electronic, Inc.
* Dongguan Digital AV teknik Corp., Ltd.
* EchoStar köp Corporation
* Enseo, Inc.
* Filmflex filmer begränsad
* Fluendo S.A.
* Gibson innovationer begränsad
* Haier Information Applicantion S.R.L
* HANDAN BroadInfoCom Companys
* Hisense internationella Companys 
* Homecast Co., Ltd
* Hon Hai Precision branschen Companys
* Infomir GMBH
* Kaonmedia Companys
* KDDI Corporation
* NINTENDO Companys
* Orange SA
* T.ex Digital begränsad
* Sagemcom bredband SAS
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou elektriska Co., Ltd
* Shenzhen Skyworth Digital teknik Co., Ltd
* Av Changhong elektriska Companys
* Skardin industriell Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Begränsad TCL utomeuropeiska marknadsföring (Offshore Macao kommersiell)
* SAS-Technicolor leverans tekniker
* Tongfang globala Ltd.
* TOP Victory investeringar, Ltd.
* Toshiba Lifestyle produkter och tjänster Corporation
* Universal Media Corporation /Slovakia/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

