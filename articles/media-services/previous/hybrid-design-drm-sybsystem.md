---
title: Hybriddesign av DRM-delsystem med Azure Media Services | Microsoft-dokument
description: I det här avsnittet beskrivs hybriddesign av DRM-undersystem med Hjälp av Azure Media Services.
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
ms.openlocfilehash: d2f4ddfbff791fbfeb2eb006a628c0fdeb4fdce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975201"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybriddesign av DRM-delsystem 

I det här avsnittet beskrivs hybriddesign av DRM-undersystem med Hjälp av Azure Media Services.

## <a name="overview"></a>Översikt

Azure Media Services ger stöd för följande tre DRM-system:

* PlayReady
* Widevine (Modulär)
* FairPlay

DRM-stödet omfattar DRM-kryptering (dynamisk kryptering) och licensleverans, med Azure Media Player som stöd för alla 3 DRMs som en webbläsare spelare SDK.

För en detaljerad behandling av DRM/ CENC undersystem design och implementering, se dokumentet med titeln [CENC med Multi-DRM och Access Control](media-services-cenc-with-multidrm-access-control.md).

Även om vi erbjuder fullständig support för tre DRM-system, ibland kunder måste använda olika delar av sin egen infrastruktur / delsystem utöver Azure Media Services för att bygga en hybrid DRM delsystem.

Nedan följer några vanliga frågor som ställs av kunder:

* "Kan jag använda mina egna DRM-licensservrar?" (I det här fallet har kunder investerat i DRM-licensservergrupp med inbäddad affärslogik).
* "Kan jag bara använda din DRM-licensleverans i Azure Media Services utan att vara värd för innehåll i AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularitet av AMS DRM-plattformen

Som en del av en omfattande molnvideoplattform har Azure Media Services DRM en design med flexibilitet och modularitet i åtanke. Du kan använda Azure Media Services med någon av följande olika kombinationer som beskrivs i tabellen nedan (en förklaring av notationen som används i tabellen följer). 

|**Innehåll som är värd för & ursprung**|**Kryptering av innehåll**|**DRM-licensleverans**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Tredje part|
|AMS|Tredje part|AMS|
|AMS|Tredje part|Tredje part|
|Tredje part|Tredje part|AMS|

### <a name="content-hosting--origin"></a>Innehåll som är värd för & ursprung

* AMS: videotillgång finns i AMS och streaming sker via AMS-slutpunkter för direktuppspelning (men inte nödvändigtvis dynamisk förpackning).
* Tredje part: video är värd och levereras på en tredje part streaming plattform utanför AMS.

### <a name="content-encryption"></a>Kryptering av innehåll

* AMS: innehållskryptering utförs dynamiskt/på begäran av AMS dynamisk kryptering.
* Tredjeparts: innehållskryptering utförs utanför AMS med hjälp av ett förbearbetningsarbetsflöde.

### <a name="drm-license-delivery"></a>DRM-licensleverans

* AMS: DRM-licensen levereras av AMS-licensleveranstjänst.
* Tredjeparts: DRM-licensen levereras av en DRM-licensserver från tredje part utanför AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurera baserat på ditt hybridscenario

### <a name="content-key"></a>Innehållsnyckel

Genom konfiguration av en innehållsnyckel kan du styra följande attribut för både AMS-dynamisk kryptering och AMS-licensleveranstjänst:

* Innehållsnyckeln som används för dynamisk DRM-kryptering.
* DRM-licensinnehåll som ska levereras av licensleveranstjänster: rättigheter, innehållsnyckel och begränsningar.
* Typ av begränsning av **innehållsnyckelauktoriseringsprincip:** begränsning av öppna, IP eller token.
* Om begränsning av **tokentyp** av **innehållsnyckelbeuktoriseringsprincip används**måste begränsningen av **behörighetsprincipen för innehållsnyckeln** uppfyllas innan en licens utfärdas.

### <a name="asset-delivery-policy"></a>Policy för leverans av tillgångar

Genom konfiguration av en princip för tillgångsleverans kan du styra följande attribut som används av AMS dynamisk paketerare och dynamisk kryptering av en AMS-slutpunkt för direktuppspelning:

* Streamingprotokoll och DRM-krypteringskombination, till exempel DASH under CENC (PlayReady och Widevine), smidig strömning under PlayReady, HLS under Widevine eller PlayReady.
* Standard-/inbäddade licensleveransadresser för var och en av de berörda DRM-modulerna.
* Oavsett om url:er för licensförvärv (LA_URLs) i DASH MPD- eller HLS-spellista innehåller frågesträngen med nyckel-ID (KID) för Widevine respektive FairPlay.

## <a name="scenarios-and-samples"></a>Scenarier och exempel

Baserat på förklaringarna i föregående avsnitt använder följande fem hybridscenarier respektive konfigurationskombinationer för-**tillgångsleveransprinciper** (de exempel som nämns i den sista kolumnen följer tabellen): **Content key**

|**Innehåll som är värd för & ursprung**|**DRM-kryptering**|**DRM-licensleverans**|**Konfigurera innehållsnyckel**|**Konfigurera princip för tillgångsleverans**|**Prov**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ja|Ja|Exempel 1|
|AMS|AMS|Tredje part|Ja|Ja|Exempel 2|
|AMS|Tredje part|AMS|Ja|Inga|Exempel 3|
|AMS|Tredje part|Utanför|Inga|Inga|Exempel 4|
|Tredje part|Tredje part|AMS|Ja|Inga|    

I exemplen fungerar PlayReady-skyddet för både DASH och smooth streaming. Video-URL:erna nedan är utjämnade strömmande webbadresser. Om du vill hämta motsvarande DASH-url:er lägger du bara till "(format=mpd-time-csf)". Du kan använda [azure media testspelaren](https://aka.ms/amtest) för att testa i en webbläsare. Det låter dig konfigurera vilket streamingprotokoll som ska användas, enligt vilken teknik. IE11 och Microsoft Edge på Windows 10 stöder PlayReady via EME. Mer information finns [i information om testverktyget](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Exempel 1

* Url för källa (bas):https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH & slät):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH):https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS):https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Exempel 2

* Url för källa (bas):https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & slät):http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Exempel 3

* Url till källa:https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & slät):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Exempel 4

* Url till källa:https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & slät):https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="summary"></a>Sammanfattning

Sammanfattningsvis azure media services DRM-komponenter är flexibla, kan du använda dem i ett hybridscenario genom att korrekt konfigurera innehållsnyckel och tillgångsleveransprincip, enligt beskrivningen i det här avsnittet.

## <a name="next-steps"></a>Nästa steg
Visa utbildningsvägar för Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

