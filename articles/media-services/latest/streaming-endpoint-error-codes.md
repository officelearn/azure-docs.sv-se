---
title: Azure Media Services-paketering och ursprungsfel | Microsoft-dokument
description: I det här avsnittet beskrivs fel som kan visas från Azure Media Services Streaming Endpoint (Orgin) tjänst.
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
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7d3a85e6fcc5b9d1c5ca1511cd7edd05ff5d9ae4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582689"
---
# <a name="streaming-endpoint-origin-errors"></a>Slutpunktsfel (Strömmande slutpunkt) 

I det här avsnittet beskrivs fel som kan visas från azure media services [streaming endpoint-tjänsten](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 dålig begäran

Begäran innehåller ogiltig information och avvisas med dessa felkoder och på grund av något av följande skäl:

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|En URL-syntax eller ett formatfel. Exempel på detta är begäranden om en ogiltig typ, ett ogiltigt fragment eller ett ogiltigt spår. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Begäran har ingen krypteringstagg i URL:en. CMAF-begäranden kräver en krypteringstagg i URL:en. Andra protokoll som är konfigurerade med mer än en krypteringstyp kräver också krypteringstaggen för disambiguation. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Begäran till lagring för att uppfylla begäran misslyckades med ett fel i den felaktiga begäran. |

## <a name="403-forbidden"></a>403 Förbjudet

Begäran är inte tillåten på grund av något av följande skäl:

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Begäran till lagring för att uppfylla begäran misslyckades med ett autentiseringsfel. Detta kan inträffa om lagringsnycklarna roterades och tjänsten inte kunde synkronisera lagringsnycklarna. <br/><br/>Kontakta Azure-supporten genom att gå till [Hjälp + support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Lagringsåtgärdsfel misslyckades åtkomsten på grund av otillräckliga kontobehörigheter. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Begäran till lagring för att uppfylla begäran misslyckades eftersom lagringskontot är inaktiverat. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Fel vid lagringsåtgärd misslyckades åtkomsten på grund av allmänna fel. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Utdataformatet blockeras på grund av konfigurationen i StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Kryptering krävs för innehållet, leveransprincip krävs för utdataformatet. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Kryptering anges inte i leveransprincipinställningar. |

## <a name="404-not-found"></a>404 – Hittades inte

Åtgärden försöker agera på en resurs som inte längre finns. Resursen kan till exempel redan ha tagits bort.

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Det begärda spåret hittades inte. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Det gick inte att hitta den begärda resursen. |
|MPE_UNAUTHORIZED |0x80890244 |Åtkomsten är obehörig. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Det gick inte att hitta den begärda tidsstämpeln. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Det begärda dynamiska manifestfiltret hittades inte. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Det begärda fragmentindexet ligger utanför det giltiga intervallet. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Det går inte att hitta livemedieposter för att få moov-buffert. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Det gick inte att hitta fragmentet vid den begärda tidpunkten för ett visst spår.<br/><br/>Det kan vara så att fragmentet inte finns i lager. Prova ett annat lager i presentationen som kan ha ett fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Det gick inte att hitta medieposten för den begärda bithastigheten i manifestet. <br/><br/>Kan vara att spelaren bad om en video spår av en viss bitrate som inte var i manifestet.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Det gick inte att hitta vissa metadata i manifestet eller inte hitta rebase från lagring. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Fel vid lagringsåtgärd hittades inte resursen. |

## <a name="409-conflict"></a>409 Konflikt

ID:t som tillhandahålls `PUT` för `POST` en resurs på en eller åtgärd har tagits av en befintlig resurs. Använd ett annat ID för resursen för att lösa problemet.

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Fel vid lagringsåtgärd, konfliktfel.  |

## <a name="410"></a>410

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|När filtret som har forceEndTimestamp inställt på true för direktuppspelning ligger start- eller sluttidsstämpeln utanför det aktuella DVR-fönstret.|

## <a name="412-precondition-failure"></a>412 Förkonditionsfel

Åtgärden angav en eTag som skiljer sig från den version som är tillgänglig på servern, det vill säga ett optimistiskt samtidighetsfel. Försök igen begäran efter att ha läst den senaste versionen av resursen och uppdatera eTag på begäran.

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Det begärda fragmentet är inte klart.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Fel vid lagringsåtgärd, ett förutsättningsfel.|

## <a name="415-unsupported-media-type"></a>Medietyp som inte stöds

Nyttolastformatet som skickas av klienten är i ett format som inte stöds.

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Bör inte tillämpa kryptering på redan krypterat innehåll.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Krypteringen är ogiltig för indataformatet.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Leveransprinciptypen är ogiltig.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |De ursprungliga inställningarna kan delas med flera utdataformat.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Medieformatet eller medietypen stöds inte. Media Services stöder till exempel inte antalet kvalitetsnivåer som är över 64 år. I FLV-videotagg stöder Media Services inte en videobildruta med flera SPS och flera PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Indataformatet för den begärda tillgången stöds inte. Media Services stöder smooth(live), MP4 (VoD) och Progressiva nedladdningsformat.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Det begärda utdataformatet stöds inte. Media Services stöder formaten Smooth, DASH(CSF, CMAF), HLS (v3, v4, CMAF) och Progressiva nedladdningsformat.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Krypteringstypen som inte stöds.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Den begärda medietypen stöds inte av utdataformatet. De typer som stöds är video, ljud eller "SUBT" undertext.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Källtillgångsmediet kodades med ett medieformat som inte är kompatibelt med utdataformatet.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Källtillgången kodades med ett videoformat som inte är kompatibelt med utdataformatet. H.264, AVC, H.265 (HEVC, hev1 eller hvc1) stöds.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Källtillgången kodades med ett ljudformat som inte är kompatibelt med utdataformatet. Ljudformat som stöds är AAC, E-AC3 (DD+), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Det går inte att konvertera den källskyddade tillgången till utdataformatet.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Skyddsformatet stöds inte av utdataformatet.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Skyddsformatet stöds inte av indataformatet.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Ogiltig video NAL-enhet, till exempel, endast den första NAL i exemplet kan vara en AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Ogiltig NAL-enhetsstorlek.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Ogiltigt längdvärde för NAL-enheten.|
|MPE_FILTER_INVALID|0x80890236|Ogiltiga dynamiska manifestfilter.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Ogiltiga eller filterversioner som inte stöds.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Ogiltig filtertyp.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Ogiltigt intervall anges av filtret.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Ogiltigt spårattribut anges av filtret.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Ogiltig presentationsfönsterlängd anges av filtret.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Ogiltig direktutbacka anges av filtret.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Endast ett absTimeInHNS-element stöds i äldre filter.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Det finns inga fler strömmar alls efter att ha tillämpat filtren.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Den levande back off är bortom DVR fönstret.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Den direktsända baksidan är större än presentationsfönstret.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Överskred högst tio (10) högsta tillåtna standardfilter.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Det är inte tillåtet att använda flera första videokvalitetsoperatorer i kombinerade begärandefilter.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Antalet första kvalitetsbitratattribut måste vara ett (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS-segmentets varaktighet måste vara mindre än en tredjedel av DVR-fönstret och HLS backa.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Fragmentets varaktighet måste vara mindre än eller lika med cirka 20 sekunder, annars är indatakvalitetsnivåerna inte tidsjusterade.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS-specifika fel, kan inte hitta ReservedBox när den ska finnas i DTSSpecficBox under DTS rutan tolkning.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-specifika fel, inga kanaler som finns i DTSSpecficBox under DTS rutan tolkning.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-specifika fel, exempeltyp obalans i DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS-specifika fel, multi-asset är inställd men DTSH exempeltyp matchar inte.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS-specifika fel, kärnströmstorlek är ogiltig.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS-specifika fel, exempelmatchning är ogiltig.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS-specifika fel, sub-stream tilläggsindex är ogiltigt.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS-specifika fel, underströmsblocknummer är ogiltigt.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS-specifika fel, samplingsfrekvensen är ogiltig.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-specifika fel är referensklockkoden i underströmstillägget ogiltig.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-specifika fel, antalet högtalare ommappningsuppsättning är ogiltigt.|

För krypteringsartiklar och exempel finns i:

- [Koncept: innehållsskydd](content-protection-overview.md)
- [Koncept: Viktiga innehållspolicyer](content-key-policy-concept.md)
- [Koncept: Streaming policyer](streaming-policy-concept.md)
- [Exempel: skydda med AES-kryptering](protect-with-aes128.md)
- [Prov: skydda med DRM](protect-with-drm.md)

Filtervägledning finns i:

- [Koncept: dynamiska manifest](filters-dynamic-manifest-overview.md)
- [Koncept: filter](filters-concept.md)
- [Exempel: skapa filter med REST-API:er](filters-dynamic-manifest-rest-howto.md)
- [Exempel: skapa filter med .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exempel: skapa filter med CLI](filters-dynamic-manifest-cli-howto.md)

För levande artiklar och prover, se:

- [Koncept: live streaming översikt](live-streaming-overview.md)
- [Koncept: Live Events och Live Outputs](live-events-outputs-concept.md)
- [Exempel: självstudiekurs för livestreaming](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Räckvidd inte mättat

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Lagringsåtgärdsfel, returnerat http 416-fel, ogiltigt intervall.|

## <a name="500-internal-server-error"></a>500 Internt serverfel

Under bearbetningen av begäran stöter Media Services på ett visst fel som förhindrar att bearbetningen fortsätter.  

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Mottagen och översatt från Winhttp felkod för ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Mottagen och översatt från Winhttp felkod för ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Mottagen och översatt från Winhttp felkod för ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Fel vid lagringsåtgärd, general InternalError för ett av HTTP 500-fel.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Fel vid lagringsåtgärd, allmän OperationTimedOut för ett av HTTP 500-fel.|
|MPE_STORAGE_FAILURE|0x808900F2|Lagringsåtgärdsfel, andra HTTP 500-fel än InternalError eller OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Tjänsten är inte tillgänglig

Servern kan för närvarande inte ta emot begäranden. Det här felet kan orsakas av för stora begäranden till tjänsten. Begränsningsmekanismen för Media Services begränsar resursanvändningen för program som gör en överdriven begäran till tjänsten.

> [!NOTE]
> Kontrollera felmeddelandet och felkodsträngen för att få mer detaljerad information om orsaken till att du fick 503-felet. Det här felet innebär inte alltid begränsning.
> 

|Felkod|Hexadecimalt värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Lagringsåtgärdsfel, mottaget HTTP-server upptagen fel 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="see-also"></a>Se även

- [Felkoder vid kodning](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services-koncept](concepts-overview.md)
- [Kvoter och begränsningar](limits-quotas-constraints.md)

## <a name="next-steps"></a>Nästa steg

[Exempel: åtkomst felkod och meddelande från ApiException med .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
