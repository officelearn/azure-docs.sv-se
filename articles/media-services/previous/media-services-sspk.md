---
title: Licens för Microsoft &reg; Smooth Streaming client porting kit
description: Läs om hur du licensierar Microsoft &reg; Smooth Streaming client porting kit.
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
ms.openlocfilehash: 423597da44a74afb60e69c4e32cbd6353f81e726
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344041"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licens för Microsoft &reg; Smooth Streaming client porting kit 
## <a name="overview"></a>Översikt
Microsoft Smooth Streaming client Solution Kit (**SSPK** för kort) är en Smooth Streaming klient implementering som är optimerad för att hjälpa inbäddade enhets tillverkare, kablar och mobila operatörer, leverantörer av innehålls tjänster, driv rutiner, oberoende program varu leverantörer (ISV: er) och lösnings leverantörer att skapa produkter och tjänster för att strömma anpassningsbart innehåll i Smooth Streaming-format. SSPK är en enhets-och plattforms oberoende implementering av Smooth Streaming-klienten som kan Portas av licensen till alla enheter och plattformar. 

Nedan följer en övergripande arkitektur och en IIS Smooth Streaming porting Kit-Box är den Smooth Streaming klient implementering som tillhandahålls av Microsoft och innehåller all huvud logik för uppspelning av Smooth Streaming innehåll. Innehållet hamnar sedan av partner för en speciell enhet eller plattform genom att implementera lämpliga gränssnitt. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Description
SSPK är licensierad för villkor som erbjuder utmärkt affärs värde. SSPK-licensen ger branschen:

* Smooth Streaming porting kit-källa i C++ 
  * implementerar Smooth Streaming klient funktioner
  * lägger till format parsning, heuristik, buffring av logik osv.
* API: er för Player-program 
  * programmerings gränssnitt för interaktion med ett Media Player-program
* Plattforms abstraktions lager (PAL)-gränssnitt 
  * programmerings gränssnitt för interaktion med operativ systemet (trådar, Sockets)
* HAL-gränssnitt (Hardware Abstraction Layer) 
  * programmerings gränssnitt för interaktion med maskin vara A/V-avkodare (avkodning, åter givning)
* DRM-gränssnitt (Digital Rights Management) 
  * programmerings gränssnitt för hantering av DRM via DRM abstraktion Layer (DAL)
  * Microsoft PlayReady porting kit levereras separat men integreras via det här gränssnittet. Klicka [här](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl)om du vill ha mer information om Microsoft PlayReady Device License.
* Implementerings exempel 
  * exempel på PAL-implementering för Linux
  * exempel-HAL-implementering för GStreamer

## <a name="licensing-options"></a>Licensierings alternativ
Microsoft Smooth Streaming client porting kit görs tillgängligt för licenser under två olika licens avtal: en för att utveckla Smooth Streaming klientens interimistiska produkter och en annan för att distribuera Smooth Streaming slut produkter till slutanvändare.

* För krets uppsättnings tillverkare, system integrerare eller oberoende program varu leverantörer (ISV) som kräver en kod för käll kods transport för att utveckla tillfälliga produkter, ska en **tillfällig produkt licens** för Microsoft Smooth Streaming client porting kit köras.
* För enhets tillverkare eller ISV: er som kräver distributions rättigheter för Smooth Streaming slut produkter till slutanvändare av Microsoft Smooth Streaming client porting kit **slutlig produkt licens** ska köras.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Interimistisk produkt licens för Microsoft Smooth Streaming client porting kit
Under den här licensen erbjuder Microsoft en Smooth Streaming klient ports paket och de nödvändiga immateriella rättigheter som krävs för att utveckla och distribuera Smooth Streaming klient över gångs produkter till andra Smooth Streaming klient ports paket för enhets licenser som distribuerar Smooth Streaming klientens slut produkter.

#### <a name="fee-structure"></a>Avgifts struktur
En licens avgift i USA $50 000 ger till gång till Smooth Streaming klient ports paket. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Slutlig produkt licens för Microsoft Smooth Streaming client porting kit
Under den här licensen erbjuder Microsoft alla nödvändiga immateriella rättigheter att ta emot Smooth Streaming klientens interimistiska produkter från andra Smooth Streaming klient ports kit-licenser och för att distribuera Smooth Streaming företagets produktbaserade slut produkter till slutanvändare.

#### <a name="fee-structure"></a>Avgifts struktur
Slut produkten för den Smooth Streaming klienten erbjuds enligt en royalty modell enligt följande:

* $0,10 per enhet-implementering har levererats
* Royaltyn är ett tak på $50 000 varje år
* Ingen royalty för de första 10 000 enhets implementeringarna varje år 

## <a name="licensing-procedure-and-sspk-access"></a>Licens procedur och SSPK-åtkomst
E-post [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) för alla licensierings frågor.

SSPK-distributions portalen är tillgänglig för registrerade interimistiska licenser.

Tillfälliga och slutgiltiga SSPK-licenser kan skicka tekniska frågor till [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licenser för interimistisk produkt avtal för Microsoft Smooth Streaming-klient

* Adroit Business Solutions, Inc
* Avancerad digital sändning SA
* Kablosuz Iletism Sanayive dis Ticaret.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC multimedie program co., Ltd.
* Cavium, Inc.
* EchoStar inköps företag
* Enseo, Inc.
* Fluendo-och Sydamerika
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Frihets globala tjänster BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo co., Ltd.
* OpenTV, Inc.
* Saffron Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* De främsta Victory-investeringarna, Ltd.
* Vestel elektronik Sanayi ve Ticaret.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Licens för slutgiltiga produkt avtal för Microsoft Smooth Streaming-klienten
* Avancerad digital sändning SA
* Kablosuz Iletism Sanayive dis Ticaret.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONIK SAN. VE-LUFFARSCHACK. A. Ş
* Brittisk himmelsblå-sändning Limited
* CastPal Technology Inc., Shenzhen
* Comp elektronik, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar inköps företag
* Enseo, Inc.
* FilmFlex filmer begränsade
* Fluendo-och Sydamerika
* FUNAI ELECTRIC CO., LTD
* Gibson innovationer Limited
* Haier information: sökande S. R. L
* HANDAN BroadInfoCom co., Ltd.
* Hisense International co., Ltd. 
* Homecast co., Ltd
* Hon Hai precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia co., Ltd.
* KDDI Corporation
* Nintendo co., Ltd.
* Orange SA
* Saffron Digital Limited
* Sagemcom bred band SAS
* Shenzhen Chuangwei – RGB Electronics Co., Ltd.
* Shenzhene Electronic Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen KTC Technology Co., Ltd. 
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* SKARDIN-industriella Corp.
* Himmelsblå Deutschland Fernsehen GmbH & co. KG
* SmarDTV-och Sydamerika
* SoftAtHome
* Sony Corporation
* Technicolor-leverans tekniker, SAS
* Tongfang global Ltd.
* De främsta Victory-investeringarna, Ltd.
* Toshiba livsstils produkter & Services Corporation
* Universal Media Corporation/Slovakia/-s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

