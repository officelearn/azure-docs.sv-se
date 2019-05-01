---
title: Azure Media Services-paketering och det ursprungliga fel | Microsoft Docs
description: Det här avsnittet beskriver fel som kan visas från tjänsten Azure Media Services paketering.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2019
ms.author: juliako
ms.openlocfilehash: c350a989f23eb667923a53f6eb06dd55905b0fab
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927319"
---
# <a name="media-services-packaging-errors"></a>Media Services paketering fel 

Det här avsnittet beskriver fel som kan visas från Azure Media Services [paketering service](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Felaktig förfrågan

Begäran innehåller ogiltig information och avvisas med dessa felkoder och på grund av något av följande orsaker:

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|En URL-syntax eller format fel. Exempel inkluderar begäranden för en ogiltig typ, ett ogiltigt avsnitt eller ett ogiltigt spår. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Begäran har ingen kryptering-tagg i URL: en. CMAF begäranden kräver en kryptering-tagg i URL: en. Andra protokoll som är konfigurerade med mer än en krypteringstyp kräver också kryptering taggen för tvetydigheter. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Begäran till storage att uppfylla begäran misslyckades med felet felaktig begäran. |

## <a name="403-forbidden"></a>403 Åtkomst nekas

Förfrågan tillåts inte på grund av något av följande orsaker:

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Begäran till lagring för att slutföra begäran misslyckades med ett autentiseringsfel. Detta kan inträffa om storage-nycklar har roterats och tjänsten kunde inte synkronisera storage-nycklar. <br/><br/>Kontakta Azure stöder genom att gå till [hjälp + support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Storage-åtgärdsfel åtkomst misslyckades på grund av otillräckliga behörigheter för kontot. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Begäran att lagring för att slutföra begäran misslyckades eftersom lagringskontot är inaktiverad. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Storage-åtgärdsfel åtkomst p.g.a. ett allmänt fel. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Utdataformat blockeras på grund av konfigurationen i StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Kryptering krävs för innehållet, leveransprincip krävs för utdataformat. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Kryptering har inte angetts i inställningar för leverans. |

## <a name="404-not-found"></a>404 Hittades inte

Åtgärden försöker att agera på en resurs som inte längre finns. Till exempel kan resursen har redan tagits bort.

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Det begärda spåret hittades inte. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Den begärda resursen hittades inte. |
|MPE_UNAUTHORIZED |0x80890244 |Åtkomsten kan inte användas. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Den begärda tidsstämpeln hittades inte. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Den begärda dynamiskt manifestfilter hittades inte. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Det begärda fragment indexet ligger utanför det giltiga intervallet. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Live-media som inte går att hitta poster för att hämta moov buffert. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Det gick inte att hitta fragmentet på den begärda tiden för en viss spår.<br/><br/>Kan vara att fragmentet inte är i lagring. Prova ett annat lager av presentationen som kan ha ett fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Det går inte att hitta posten media för den begärda bithastigheten i manifestet. <br/><br/>Kan vara att spelaren efterfrågat video reda på en bithastighet som inte är i manifestet.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Det går inte att hitta vissa metadata i manifestet eller gick inte att hitta rebase från storage. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Storage-åtgärdsfel, det gick inte att hitta resursen. |

## <a name="409-conflict"></a>409 konflikt

ID som angetts för en resurs på en `PUT` eller `POST` åtgärden har tagits av en befintlig resurs. Använd ett annat ID för resursen för att lösa problemet.

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Storage-åtgärdsfel, konflikt fel.  |

## <a name="410"></a>410

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|När filtret som har för direktsänd strömning är inställd på true, starten eller slutet tidsstämpel forceEndTimestamp utanför den aktuella DVR-perioden.|

## <a name="412-precondition-failure"></a>412 Förhandsvillkoret misslyckades

Åtgärden angav en eTag som skiljer sig från versionen som är tillgänglig på servern, det vill säga ett optimistisk Samtidighetsfel. Försök begäran när du läser den senaste versionen av resursen och uppdaterar eTag på begäran.

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Det begärda fragmentet är inte klar.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Åtgärdsfel för lagring, ett Förhandsvillkoret-fel.|

## <a name="415-unsupported-media-type"></a>415 mediatypen

Nyttolastformatet skickades av klienten är i ett format som inte stöds.

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Bör inte gälla kryptering på redan krypterat innehåll.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Krypteringen är ogiltig för Indataformatet.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Leverans är principen ogiltig.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |De ursprungliga inställningarna kan delas av flera utdataformat.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Media-format eller typ stöds inte. Media Services stöder till exempel inte kvaliteten på antalet som är över 64. I FLV videotaggen, Media Services stöder inte en bildruta med flera Service Pack och flera PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Indataformatet för tillgång begärt stöds inte. Media Services stöder (live), Smooth MP4 (VoD) och progressiv Hämta format.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Utdataformat begärt stöds inte. Media Services har stöd för Smooth, DASH (CSF, CMAF), HLS (v3, v4, CMAF), och progressiv Hämta format.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Krypteringstyp för påträffades som inte stöds.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Den begärda mediatypen stöds inte av utdataformat. Typerna som stöds är video, ljud eller ”SUBT” underrubrik.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Tillgången källmediet kodats med ett media-format som inte är kompatibel med utdataformat.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Käll-tillgången kodats med ett videoformat som inte är kompatibel med utdataformat. H.264 AVC, H.265 (– HEVC, hev1 eller hvc1) stöds.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Käll-tillgången kodats med ett ljudformat som inte är kompatibel med utdataformat. Stöds ljudformat är AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Den skyddade källa tillgången kan inte konverteras till formatet för utdata.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Formatet skydd stöds inte av utdataformat.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Formatet skydd stöds inte av Indataformatet.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Ogiltig video NAL enhet, till exempel bara den första NAL i det här exemplet kan vara en AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Ogiltig storlek på NAL.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Ogiltigt längdvärde för NAL i enheten.|
|MPE_FILTER_INVALID|0x80890236|Ogiltig dynamiska manifestfilter.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Ogiltig eller stöds inte filter versioner.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Ogiltig filtertyp.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Ogiltigt intervall har angetts av filtret.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Ogiltig spåra attributet anges av filtret.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Ogiltig mappningsmall längd anges av filtret.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Ogiltig live backoff anges av filtret.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Endast en absTimeInHNS element stöds i äldre filter.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Det är inga flera strömmar efter att ha tillämpat filter.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Live backoff ligger utanför DVR-perioden.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Live backoff är större än fönstret.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Överskred maximalt tillåten standardfilter tio (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Flera första videokvalitet-operatorn är inte tillåten i kombinerade begäran filter.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Antalet första kvalitet med flera bithastigheter attribut måste vara ett (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS segment varaktighet måste vara mindre än en tredjedel av DVR-perioden och HLS undantag.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Fragment varaktighet måste vara mindre än eller lika med cirka 20 sekunder eller inkommande kvalitetsnivå är inte justerad tid.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS-specifikt fel kan inte hitta ReservedBox när den ska visa DTSSpecficBox under DTS-parsning.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-specifikt fel, inga kanaler finns i DTSSpecficBox vid parsning av DTS-rutan.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-specifikt fel, exempel typmatchningsfel i DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS-specifikt fel flera tillgången har angetts men DTSH exempel Typmatchningsfel.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS-specifikt fel core strömstorleken är ogiltig.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS-specifikt fel, exempel lösning är ogiltig.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS-specifikt fel underordnade stream tillägg indexet är ogiltigt.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS-specifikt fel underordnade stream blocknummer är ogiltig.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS-specifikt fel samplingsfrekvensen är ogiltig.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-specifikt fel klockan referenskod i underordnade stream-tillägget är ogiltigt.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-specifikt fel antal talare ommappning set är ogiltig.|

Kryptering artiklar och exempel finns:

- [Koncept: innehållsskydd](content-protection-overview.md)
- [Begrepp: Viktiga Innehållsprinciper](content-key-policy-concept.md)
- [Begrepp: Principer för direktuppspelning](streaming-policy-concept.md)
- [Exempel: skydda med AES-kryptering](protect-with-aes128.md)
- [Exempel: skydda med DRM](protect-with-drm.md)

Råd om filter finns i:

- [Koncept: dynamiska manifest](filters-dynamic-manifest-overview.md)
- [Koncept: filter](filters-concept.md)
- [Exempel: skapa filter med REST API: er](filters-dynamic-manifest-rest-howto.md)
- [Exempel: skapa filter med .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exempel: skapa filter med CLI](filters-dynamic-manifest-cli-howto.md)

Live artiklar och exempel finns:

- [Koncept: live direktuppspelning översikt](live-streaming-overview.md)
- [Begrepp: Händelser och Live utdata](live-events-outputs-concept.md)
- [Exempel: live direktuppspelning självstudien](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 intervallet inte Satisfiable

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Åtgärdsfel för lagring, returnerade http 416-fel, ogiltigt intervall|

## <a name="500-internal-server-error"></a>500 Internt serverfel

Under bearbetning av begäran påträffar Media Services ett fel som förhindrar att bearbetningen inte kan fortsätta.  

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Mottagna och översatta från Winhttp felkoden ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Mottagna och översatta från Winhttp felkoden ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Mottagna och översatta från Winhttp felkoden ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Storage-åtgärdsfel, allmän InternalError för en HTTP 500-fel.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Storage-åtgärdsfel, allmän OperationTimedOut för en HTTP 500-fel.|
|MPE_STORAGE_FAILURE|0x808900F2|Storage-åtgärdsfel, andra HTTP 500-fel än InternalError eller OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Tjänsten är inte tillgänglig

Servern är för närvarande inte att ta emot begäranden. Det här felet kan orsakas av hög begäranden till tjänsten. Media Services begränsning mekanism begränsar resursanvändningen för program som gör överdrivna för tjänsten.

> [!NOTE]
> Kontrollera felmeddelandet och felkodsträngen för att få mer detaljerad information om orsaken som du fick 503-felet. Det här felet betyder alltid inte begränsning.
> 

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Storage-åtgärdsfel tog emot HTTP-server upptagen fel 503.|

## <a name="provide-feedback"></a>Ge feedback

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se också

- [Felkoder vid kodning](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services-begrepp](concepts-overview.md)
- [Kvoter och begränsningar](limits-quotas-constraints.md)

## <a name="next-steps"></a>Nästa steg

[Exempel: komma åt felkod och ett meddelande från ApiException med .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
