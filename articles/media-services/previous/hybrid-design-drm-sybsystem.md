---
title: Hybrid design av DRM-undersystem med Azure Media Services | Microsoft Docs
description: I det här avsnittet beskrivs hybrid design av DRM-undersystem med Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: d15bfcfbae3b24e1a9b29dc74f9b41a979e63ae9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "69014700"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybrid design av DRM-undersystem 

I det här avsnittet beskrivs hybrid design av DRM-undersystem med Azure Media Services.

## <a name="overview"></a>Översikt

Azure Media Services tillhandahåller stöd för följande tre DRM-system:

* PlayReady
* Widevine (modulär)
* FairPlay

DRM-stödet innehåller DRM-kryptering (dynamisk kryptering) och licens leverans, med Azure Media Player stöd för alla tre DRM: er som webb läsar-SDK: er.

En detaljerad behandling av design och implementering av DRM/CENC-undersystem finns i dokumentet [med rubriken Cenc med multi-DRM och Access Control](media-services-cenc-with-multidrm-access-control.md).

Även om vi erbjuder fullständigt stöd för tre DRM-system, kan kunder behöva använda olika delar av sin egen infrastruktur/under system, förutom Azure Media Services för att bygga ett hybrid DRM-undersystem.

Nedan visas några vanliga frågor som ställs av kunder:

* "Kan jag använda mina egna DRM-licensserver?" (I det här fallet har kunder investerat i en Server grupp för DRM-licenser med inbyggd affärs logik).
* "Kan jag bara använda din leverans av DRM-licenser i Azure Media Services utan att vara värd för innehållet i AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modulärhet för AMS DRM-plattformen

Som en del av en omfattande moln video plattform har Azure Media Services DRM en design med flexibilitet och modulärhet i åtanke. Du kan använda Azure Media Services med någon av följande kombinationer som beskrivs i tabellen nedan (en förklaring av notationen som används i tabellen nedan). 

|**Innehåll som är värd för & ursprung**|**Innehålls kryptering**|**DRM-licensleverans**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Tredje part|
|AMS|Tredje part|AMS|
|AMS|Tredje part|Tredje part|
|Tredje part|Tredje part|AMS|

### <a name="content-hosting--origin"></a>Innehåll som är värd för & ursprung

* AMS: video till gång är värd för AMS och strömning är via AMS streaming-slutpunkter (men inte nödvändigt vis dynamisk paketering).
* Tredje part: video tillhandahålls och levereras på en strömmande plattform från tredje part utanför AMS.

### <a name="content-encryption"></a>Innehålls kryptering

* AMS: innehålls kryptering utförs dynamiskt/på begäran av AMS dynamisk kryptering.
* Tredje part: innehålls kryptering utförs utanför AMS med ett för bearbetnings arbets flöde.

### <a name="drm-license-delivery"></a>DRM-licensleverans

* AMS DRM-Licens levereras av AMS licens Delivery Service.
* Tredje part: En DRM-Licens levereras av en tredjeparts-licens server utanför AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurera baserat på ditt hybrid scenario

### <a name="content-key"></a>Innehållsnyckel

Genom att konfigurera en innehålls nyckel kan du kontrol lera följande attribut för både AMS Dynamic Encryption och AMS License Delivery Service:

* Innehålls nyckeln som används för dynamisk DRM-kryptering.
* Innehåll för DRM-licens som ska levereras av licens leverans tjänster: rättigheter, innehålls nyckel och begränsningar.
* Typ av **princip begränsning för Auktoriseringsprinciper för innehålls nyckel**: öppna, IP eller token-begränsning.
* Om **token** -typen för **begränsning av innehålls nycklar används**måste begränsningen för **innehålls nyckelns auktoriseringsprincip** uppfyllas innan en licens utfärdas.

### <a name="asset-delivery-policy"></a>Till gångs leverans princip

Genom att konfigurera en till gångs leverans princip kan du kontrol lera följande attribut som används av AMS Dynamic Packer och dynamisk kryptering av en AMS streaming-slutpunkt:

* Kombination av strömnings protokoll och DRM-kryptering, till exempel bindestreck under CENC (PlayReady och Widevine), smidig strömning under PlayReady, HLS under Widevine eller PlayReady.
* URL: en för standard-/inbäddade licens leverans för var och en av de berörda DRM: er.
* Om URL: er för licens hämtning (LA_URLs) i streck-eller HLS-spelnings listan innehåller frågesträng för nyckel-ID (barn) för Widevine respektive FairPlay.

## <a name="scenarios-and-samples"></a>Scenarier och exempel

Baserat på förklaringarna i föregående avsnitt, använder följande fem hybrid scenarier respektive konfigurations kombinationer för **innehålls nyckel**-**till gångs** -och konfigurations principer (de exempel som anges i den sista kolumnen följer tabellen):

|**Innehåll som är värd för & ursprung**|**DRM-kryptering**|**DRM-licensleverans**|**Konfigurera innehålls nyckel**|**Konfigurera till gångs leverans princip**|**Exempel**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ja|Ja|Exempel 1|
|AMS|AMS|Tredje part|Ja|Ja|Exempel 2|
|AMS|Tredje part|AMS|Ja|Nej|Exempel 3|
|AMS|Tredje part|Utanpå|Nej|Nej|Exempel 4|
|Tredje part|Tredje part|AMS|Ja|Nej|    

I exemplen fungerar PlayReady-skyddet både för streck och smidig strömning. Video-URL: erna nedan är smidiga strömmande URL: er. För att hämta motsvarande streck-URL: er lägger du bara till "(format = mpd-Time-CSF)". Du kan använda [Azure Media test Player](https://aka.ms/amtest) för att testa i en webbläsare. Det gör att du kan konfigurera vilket strömnings protokoll som ska användas, under vilka Tech. IE11 och Microsoft Edge på Windows 10 stöder PlayReady genom EME. Mer information finns i [information om test verktyget](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Exempel 1

* Källa (bas) URL: https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (streck & utjämna): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (bindestreck): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Exempel 2

* Källa (bas) URL: https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (streck & utjämna): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Exempel 3

* Käll-URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (streck & utjämna): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Exempel 4

* Käll-URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (streck & utjämna): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Sammanfattning

Azure Media Services DRM-komponenter är flexibla i sammanfattning, du kan använda dem i ett hybrid scenario genom att konfigurera innehålls nyckeln och till gångs leverans principen på det sätt som beskrivs i det här avsnittet.

## <a name="next-steps"></a>Nästa steg
Visa Media Services utbildnings vägar.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

