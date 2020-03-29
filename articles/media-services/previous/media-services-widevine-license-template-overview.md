---
title: Översikt över Widevine-licensmall | Microsoft-dokument
description: Det här avsnittet innehåller en översikt över en Widevine-licensmall som används för att konfigurera Widevine-licenser.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c7511279e66ab598e4ae3c26f053915b7393b39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978398"
---
# <a name="widevine-license-template-overview"></a>Översikt över Widevine-licensmallen 
Du kan använda Azure Media Services för att konfigurera och begära Google Widevine-licenser. När spelaren försöker spela upp ditt Widevine-skyddade innehåll skickas en begäran till licensleveranstjänsten för att få en licens. Om licenstjänsten godkänner begäran utfärdar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

En Widevine-licensbegäran formateras som ett JSON-meddelande.  

>[!NOTE]
> Du kan skapa ett tomt meddelande{}utan värden, bara " ." Sedan skapas en licensmall med standardvärden. Standardfunktionen fungerar i de flesta fall. Microsoft-baserade scenarier för licensleverans bör alltid använda standardinställningarna. Om du behöver ange värdena "provider" och "content_id" måste en provider matcha Widevine-autentiseringsuppgifter.

    {  
       "payload": "<license challenge>",
       "content_id": "<content id>" 
       "provider": "<provider>"
       "allowed_track_types": "<types>",
       "content_key_specs": [  
          {  
             "track_type": "<track type 1>"
          },
          {  
             "track_type": "<track type 2>"
          },
          …
       ],
       "policy_overrides": {  
          "can_play": <can play>,
          "can persist": <can persist>,
          "can_renew": <can renew>,
          "rental_duration_seconds": <rental duration>,
          "playback_duration_seconds": <playback duration>,
          "license_duration_seconds": <license duration>,
          "renewal_recovery_duration_seconds": <renewal recovery duration>,
          "renewal_server_url": "<renewal server url>",
          "renewal_delay_seconds": <renewal delay>,
          "renewal_retry_interval_seconds": <renewal retry interval>,
          "renew_with_usage": <renew with usage>
       }
    }

## <a name="json-message"></a>JSON-meddelande
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| payload |Base64-kodad sträng |Licensbegäran som skickas av en klient. |
| content_id |Base64-kodad sträng |Identifieraren som används för att härleda nyckel-ID- och innehållsnyckeln för varje content_key_specs.track_type. |
| Leverantör |sträng |Används för att slå upp innehållsnycklar och principer. Om Microsoft-nyckelleverans används för Widevine-licensleverans ignoreras den här parametern. |
| policy_name |sträng |Namn på en tidigare registrerad princip. Valfri. |
| allowed_track_types |Enum |SD_ONLY eller SD_HD. Styr vilka innehållsnycklar som ingår i en licens. |
| content_key_specs |Matris med JSON-strukturer, se avsnittet "Innehållsnyckelspecifikationer".  |En finarekornig kontroll över vilka innehållsnycklar som ska returneras. Mer information finns i avsnittet "Specifikationer för innehållsnyckel". Det går bara att ange ett av allowed_track_types- och content_key_specs-värdena. |
| use_policy_overrides_exclusively |Boolesk, sann eller falsk |Använd principattribut som anges av policy_overrides och utelämna alla tidigare lagrade principen. |
| policy_overrides |JSON-struktur, se avsnittet "Princip åsidosättningar". |Principinställningar för den här licensen.  I händelse av att den här tillgången har en fördefinierad princip används dessa angivna värden. |
| session_init |JSON-struktur, se avsnittet "Sessioninitualisering". |Valfria data skickas till licensen. |
| parse_only |Boolesk, sann eller falsk |Licensbegäran tolkas, men ingen licens utfärdas. Värden från licensbegäran returneras dock i svaret. |

## <a name="content-key-specs"></a>Viktiga specifikationer för innehåll
Om det finns en befintlig princip behöver du inte ange något av värdena i innehållsnyckelspecifikationen. Den befintliga principen som är associerad med det här innehållet används för att bestämma utdataskyddet, till exempel HDCP (High-bandwidth Digital Content Protection) och CGMS (Copy General Management System). Om en befintlig princip inte är registrerad hos Widevine-licensservern kan innehållsleverantören injicera värdena i licensbegäran.   

Varje content_key_specs värde måste anges för alla spår, oavsett use_policy_overrides_exclusively alternativet. 

| Namn | Värde | Beskrivning |
| --- | --- | --- |
| content_key_specs. track_type |sträng |Ett namn på spårtyp. Om content_key_specs anges i licensbegäran måste du uttryckligen ange alla spårtyper. Underlåtenhet att göra detta resulterar i underlåtenhet att spela upp senaste 10 sekunder. |
| content_key_specs  <br/> security_level |uint32 (på andra) |Definierar klients robusthetskrav för uppspelning. <br/> - Programvarubaserad white-box-kryptografi krävs. <br/> - Programvarukryptografi och en fördunklad dekoder krävs. <br/> - Nyckelmaterial- och kryptografiåtgärderna måste utföras i en maskinvarustödd betrodd körningsmiljö. <br/> - Kryptografi och avkodning av innehåll måste utföras i en maskinvarustödd betrodd körningsmiljö.  <br/> - Kryptografi, avkodning och all hantering av mediet (komprimerad och okomprimerad) måste hanteras i en maskinvarustödd betrodd körningsmiljö. |
| content_key_specs <br/> required_output_protection.hdc |sträng, en av HDCP_NONE, HDCP_V1, HDCP_V2 |Anger om HDCP krävs. |
| content_key_specs <br/>key |Base64-<br/>kodad sträng |Innehållsnyckel som ska användas för det här spåret. Om det anges krävs track_type eller key_id. Innehållsleverantören kan använda det här alternativet för att injicera innehållsnyckeln för det här spåret i stället för att låta Widevine-licensservern generera eller slå upp en nyckel. |
| content_key_specs.key_id |Base64-kodad sträng binär, 16 byte |Unik identifierare för nyckeln. |

## <a name="policy-overrides"></a>Princip åsidosättningar
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| policy_overrides. can_play |Boolesk, sann eller falsk |Anger att uppspelning av innehållet är tillåtet. Standardvärdet är false. |
| policy_overrides. can_persist |Boolesk, sann eller falsk |Anger att licensen kan sparas i icke-flyktig lagring för offlineanvändning. Standardvärdet är false. |
| policy_overrides. can_renew |Boolesk, sann eller falsk |Anger att förnyelse av den här licensen är tillåten. Om värdet är true kan licensens varaktighet förlängas med pulsslag. Standardvärdet är false. |
| policy_overrides. license_duration_seconds |int64 (int64) |Anger tidsfönstret för den här specifika licensen. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standard är 0. |
| policy_overrides. rental_duration_seconds |int64 (int64) |Anger tidsfönstret medan uppspelning är tillåten. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standard är 0. |
| policy_overrides. playback_duration_seconds |int64 (int64) |Visningsfönstret efter uppspelningen startar inom licensens varaktighet. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standard är 0. |
| policy_overrides. renewal_server_url |sträng |Alla pulsslagsbegäranden (förnyelse) för den här licensen dirigeras till den angivna webbadressen. Det här fältet används bara om can_renew är sant. |
| policy_overrides. renewal_delay_seconds |int64 (int64) |Hur många sekunder efter license_start_time innan förnyelse görs första gången. Det här fältet används bara om can_renew är sant. Standard är 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 (int64) |Anger fördröjningen i sekunder mellan efterföljande begäranden om förnyelse av licenser, i händelse av fel. Det här fältet används bara om can_renew är sant. |
| policy_overrides. renewal_recovery_duration_seconds |int64 (int64) |Det tidsfönster då uppspelningen kan fortsätta medan förnyelse görs, men misslyckas på grund av backend-problem med licensservern. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Det här fältet används bara om can_renew är sant. |
| policy_overrides. renew_with_usage |Boolesk, sann eller falsk |Anger att licensen skickas för förnyelse när användningen startar. Det här fältet används bara om can_renew är sant. |

## <a name="session-initialization"></a>Initiering av session
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| provider_session_token |Base64-kodad sträng |Den här sessionstoken skickas tillbaka i licensen och finns i efterföljande förnyelser. Sessionstoken finns inte kvar än sessioner. |
| provider_client_token |Base64-kodad sträng |Klienttoken för att skicka tillbaka licenssvaret. Om licensbegäran innehåller en klienttoken ignoreras det här värdet. Klienttoken kvarstår utöver licenssessioner. |
| override_provider_client_token |Boolesk, sann eller falsk |Om false och licensbegäran innehåller en klienttoken använder du token från begäran även om en klienttoken angavs i den här strukturen. Om värdet är true använder du alltid den token som anges i den här strukturen. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Konfigurera dina Widevine-licenser med hjälp av .NET-typer
Media Services tillhandahåller .NET API:er som du kan använda för att konfigurera dina Widevine-licenser. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klasser enligt definitionen i Media Services .NET SDK
Följande klasser är definitionerna av dessa typer:

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Exempel
I följande exempel visas hur du använder .NET API:er för att konfigurera en enkel Widevine-licens:

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Använda PlayReady och/eller Widevine Dynamic Common Encryption](media-services-protect-with-playready-widevine.md)

