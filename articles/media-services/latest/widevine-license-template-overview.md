---
title: Azure Media Services v3 med Widevine-licensmall översikt
description: Det här avsnittet innehåller en översikt över en Widevine-licensmall som används för att konfigurera Widevine-licenser.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 94ce5e45a9a43e81020096ddc0a67429b286d9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705640"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Media Services v3 med Widevine-licensmall översikt

Med Azure Media Services kan du kryptera ditt innehåll med **Google Widevine**. Media Services tillhandahåller också en tjänst för att leverera Widevine-licenser. Du kan använda Azure Media Services API:er för att konfigurera Widevine-licenser. När en spelare försöker spela upp ditt Widevine-skyddade innehåll skickas en begäran till licensleveranstjänsten för att få licensen. Om licenstjänsten godkänner begäran utfärdar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

En Widevine-licensbegäran formateras som ett JSON-meddelande.  

>[!NOTE]
> Du kan skapa ett tomt meddelande{}utan värden, bara " ." Sedan skapas en licensmall med standardvärden. Standardfunktionen fungerar i de flesta fall. Microsoft-baserade scenarier för licensleverans bör alltid använda standardinställningarna. Om du behöver ange värdena "provider" och "content_id" måste en provider matcha Widevine-autentiseringsuppgifter.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage":<renew with usage>
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
| policy_overrides&#46;can_play |Boolesk, sann eller falsk |Anger att uppspelning av innehållet är tillåtet. Standardvärdet är false. |
| policy_overrides&#46;can_persist |Boolesk, sann eller falsk |Anger att licensen kan sparas i icke-flyktig lagring för offlineanvändning. Standardvärdet är false. |
| policy_overrides&#46;can_renew |Boolesk, sann eller falsk |Anger att förnyelse av den här licensen är tillåten. Om värdet är true kan licensens varaktighet förlängas med pulsslag. Standardvärdet är false. |
| policy_overrides&#46;license_duration_seconds |int64 (int64) |Anger tidsfönstret för den här specifika licensen. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standard är 0. |
| policy_overrides&#46;rental_duration_seconds |int64 (int64) |Anger tidsfönstret medan uppspelning är tillåten. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standard är 0. |
| policy_overrides&#46;playback_duration_seconds |int64 (int64) |Visningsfönstret efter uppspelningen startar inom licensens varaktighet. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standard är 0. |
| policy_overrides&#46;renewal_server_url |sträng |Alla pulsslagsbegäranden (förnyelse) för den här licensen dirigeras till den angivna webbadressen. Det här fältet används bara om can_renew är sant. |
| policy_overrides&#46;renewal_delay_seconds |int64 (int64) |Hur många sekunder efter license_start_time innan förnyelse görs första gången. Det här fältet används bara om can_renew är sant. Standard är 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 (int64) |Anger fördröjningen i sekunder mellan efterföljande begäranden om förnyelse av licenser, i händelse av fel. Det här fältet används bara om can_renew är sant. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 (int64) |Det tidsfönster då uppspelningen kan fortsätta medan förnyelse görs, men misslyckas på grund av backend-problem med licensservern. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Det här fältet används bara om can_renew är sant. |
| policy_overrides&#46;renew_with_usage |Boolesk, sann eller falsk |Anger att licensen skickas för förnyelse när användningen startar. Det här fältet används bara om can_renew är sant. |

## <a name="session-initialization"></a>Initiering av session
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| provider_session_token |Base64-kodad sträng |Den här sessionstoken skickas tillbaka i licensen och finns i efterföljande förnyelser. Sessionstoken finns inte kvar än sessioner. |
| provider_client_token |Base64-kodad sträng |Klienttoken för att skicka tillbaka licenssvaret. Om licensbegäran innehåller en klienttoken ignoreras det här värdet. Klienttoken kvarstår utöver licenssessioner. |
| override_provider_client_token |Boolesk, sann eller falsk |Om false och licensbegäran innehåller en klienttoken använder du token från begäran även om en klienttoken angavs i den här strukturen. Om värdet är true använder du alltid den token som anges i den här strukturen. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurera din Widevine-licens med .NET 

Media Services tillhandahåller en klass där du kan konfigurera en Widevine-licens. För att konstruera licensen, passera JSON till [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Om du vill konfigurera mallen kan du:

### <a name="directly-construct-a-json-string"></a>Konstruera en JSON-sträng direkt

Den här metoden kan vara felbenägen. Vi rekommenderar att du använder annan metod som beskrivs i [Definiera nödvändiga klasser och serialisera till JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a>Definiera nödvändiga klasser och serialisera till JSON

#### <a name="define-classes"></a>Definiera klasser

I följande exempel visas ett exempel på definitioner av klasser som mappas till Widevine JSON-schema. Du kan instansiera klasserna innan serialisera dem till JSON-strängen.  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Konfigurera licensen

Använd klasser som definierats i föregående avsnitt för att skapa JSON som används för att konfigurera [WidevineTemplate:](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

Kolla in hur du [skyddar med DRM](protect-with-drm.md)
