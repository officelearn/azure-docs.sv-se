---
title: Hybridutformning av DRM-subsystem(s) med Azure Media Services | Microsoft Docs
description: Det här avsnittet beskrivs hybridutformning av DRM-subsystem(s) med Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 84e2eb6a16ce9ee4889eacbf394754c5c2544f7a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245315"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybridutformning av DRM-subsystem(s)

Det här avsnittet beskrivs hybridutformning av DRM-subsystem(s) med Azure Media Services.

## <a name="overview"></a>Översikt

Azure Media Services har stöd för följande tre DRM-systemet:

* PlayReady
* Widevine (Modular)
* FairPlay

DRM-stöd omfattar DRM-kryptering (dynamisk kryptering) och -licensleverans, med Azure Media Player stöder alla 3 DRM: er som en webbläsare player SDK.

En detaljerad behandling av DRM/CENC undersystem design och implementering finns i dokumentet som heter [CENC med Multi-DRM och Access Control](media-services-cenc-with-multidrm-access-control.md).

Även om vi har fullt stöd för tre DRM-system, måste ibland kunder använda olika delar av sina egna infrastruktur/undersystem förutom Azure Media Services för att skapa en hybrid DRM-undersystemet.

Nedan finns några vanliga frågor och svar av kunder:

* ”Kan jag använda min egen DRM-servrar för fjärrskrivbordslicenser”? (I det här fallet kunder har investerat i servergruppen för DRM-licens med inbäddade affärslogik).
* ”Kan jag använda bara din leveranser av DRM-licenser i Azure Media Services utan som är värd för innehåll i AMS”?

## <a name="modularity-of-the-ams-drm-platform"></a>Modularitet av AMS DRM-plattform

Som en del av en omfattande plattform för video har Azure Media Services DRM en design med flexibiliteten och modulariteten i åtanke. Du kan använda Azure Media Services med någon av följande olika kombinationer som beskrivs i tabellen nedan (en förklaring av beteckningen används i tabellen nedan). 

|**Innehåll som är värd för & ursprung**|**Innehåll kryptering**|**DRM-licensleverans**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Från tredje part|
|AMS|Från tredje part|AMS|
|AMS|Från tredje part|Från tredje part|
|Från tredje part|Från tredje part|AMS|

### <a name="content-hosting--origin"></a>Innehåll som är värd för & ursprung

* AMS: videotillgång finns i AMS och strömning sker via AMS slutpunkter för direktuppspelning (men inte nödvändigtvis dynamisk paketering).
* Från tredje part: video är värd för och levereras på en tredje parts strömningsplattform utanför AMS.

### <a name="content-encryption"></a>Innehåll kryptering

* AMS: kryptering av innehåll är genomförd dynamiskt på begäran med AMS dynamisk kryptering.
* Från tredje part: content kryptering utförs utanför AMS med hjälp av en förväg bearbetnings arbetsflöde.

### <a name="drm-license-delivery"></a>DRM-licensleverans

* AMS: DRM-licens levereras av AMS-Licenstjänsten leverans.
* Från tredje part: DRM-licens levereras av en tredje parts DRM-licensserver utanför AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurera baserat på ditt scenario med hybridanvändning

### <a name="content-key"></a>Innehållsnyckel

Du kan styra följande attribut för både AMS dynamisk kryptering och AMS licensleveranstjänst genom konfigurationen av en innehållsnyckel:

* Innehållsnyckeln används för dynamisk DRM-kryptering.
* DRM-licens innehåll som ska levereras av licensleveranstjänster: rättigheter, innehållsnyckeln och begränsningar.
* Typ av **innehåll auktoriseringsprincipen principbegränsning**: öppen, IP- eller tokenbegränsningar.
* Om **token** typ av **innehållsnyckelauktorisering principbegränsning används**, **innehåll auktoriseringsprincipen principbegränsning** måste vara uppfyllda innan en licens har utfärdats.

### <a name="asset-delivery-policy"></a>Principen för tillgångsleverans

Du kan styra följande attribut används av AMS dynamisk Paketeraren och dynamisk kryptering av ett AMS-slutpunkten för direktuppspelning genom konfigurationen av en tillgångsleveransprincip:

* Streaming DRM kryptering kombination och protokoll, till exempel DASH under CENC (PlayReady och Widevine), smooth streaming-under PlayReady, HLS under Widevine eller PlayReady.
* Standard/inbäddat licensierar leverans URL: er för var och en av de involverade DRM: er.
* Licensiera om förvärv URL: er (LA_URLs) i MPD DASH eller HLS spelningslistan innehåller frågesträngen för nyckel-ID (barn) för Widevine och FairPlay, respektive.

## <a name="scenarios-and-samples"></a>Scenarier och exempel

Baserat på förklaringar i föregående avsnitt, följande fem hybridscenarier använder respektive **innehållsnyckeln**-**tillgångsleveransprincip** kompatibla kombinationer (exemplen nämns i den sista kolumn Följ tabellen):

|**Innehåll som är värd för & ursprung**|**DRM-kryptering**|**DRM-licensleverans**|**Konfigurera innehållsnyckel**|**Konfigurera tillgångsleveransprincip**|**Exemplet**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ja|Ja|Exempel 1|
|AMS|AMS|Från tredje part|Ja|Ja|Exempel 2|
|AMS|Från tredje part|AMS|Ja|Nej|Exempel 3|
|AMS|Från tredje part|Utanför|Nej|Nej|Exempel 4|
|Från tredje part|Från tredje part|AMS|Ja|Nej|    

I exemplen fungerar PlayReady-skydd för både DASH och smooth streaming. Video adresserna nedan är smooth streaming URL: er. För att få de motsvarande DASH-URL: er kan bara lägga till ”(format = mpd-time-csf)”. Du kan använda den [azure media testa player](https://aka.ms/amtest) att testa i en webbläsare. Det kan du konfigurera vilka strömningsprotokoll som ska användas under vilken teknisk. Stöd för PlayReady via EME IE11 och MS Edge på Windows 10. Mer information finns i [information om testverktyget](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Exempel 1

* Källadress (bas): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH och smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (bindestreck): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Exempel 2

* Källadress (bas): http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH och smooth): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Exempel 3

* Käll-URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH och smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Exempel 4

* Käll-URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH och smooth): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Sammanfattning

Sammanfattningsvis, Azure Media Services DRM-komponenterna är flexibla, du kan använda dem i ett hybridscenario genom att konfigurera innehållsnyckeln och tillgångsleveransprincip, enligt beskrivningen i det här avsnittet.

## <a name="next-steps"></a>Nästa steg
Visa Media Services utbildningsvägar.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

