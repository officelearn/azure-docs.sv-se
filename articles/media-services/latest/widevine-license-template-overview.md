---
title: Azure Media Services med Widevine-licens mall översikt | Microsoft Docs
description: Det här avsnittet ger en översikt över en Widevine-licensmall som används för att konfigurera Widevine-licenser.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: juliako
ms.openlocfilehash: e3af5efd253458401c13f6174d9567f932482eb0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132989"
---
# <a name="widevine-license-template-overview"></a>Widevine-licens mall översikt

Du kan använda Azure Media Services för att konfigurera och begära Google Widevine-licenser. När Windows Media player försöker spela upp ditt Widevine-skyddat innehåll, skickas en begäran till licensleveranstjänst du erhåller en licens. Om Licenstjänsten godkänner begäran, skickar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

En Widevine-licensbegäran formateras som ett JSON-meddelande.  

>[!NOTE]
> Du kan skapa ett tomt meddelande utan värden bara ”{}”. Sedan skapas en licens med standardinställningar. Standardinställningen fungerar i de flesta fall. Microsoft-baserad licens-delivery scenarier bör alltid använda standardinställningarna. Om du behöver ange ”-providern” och ”content_id” värden måste en provider matcha Widevine autentiseringsuppgifter.

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
          "renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>JSON-meddelande

| Namn | Värde | Beskrivning |
| --- | --- | --- |
| nyttolast |Base64-kodad sträng |Licensbegäran som skickades av en klient. |
| content_id |Base64-kodad sträng |Identifierare som används för att härleda nyckel-ID och innehåll nyckel för varje content_key_specs.track_type. |
| Providern |sträng |Används för att söka efter innehåll nycklar och principer. Om Microsoft key leverans används för leverans av Widevine-licens, ignoreras den här parametern. |
| principens_namn |sträng |Namnet på en tidigare registrerad princip. Valfri. |
| allowed_track_types |Enum |SD_ONLY eller SD_HD. Kontroller som innehåll nycklar ingår i en licens. |
| content_key_specs |Matris av JSON strukturer, finns i avsnittet ”innehåll viktiga specifikationerna”.  |En mer detaljerad kontroll för vilka innehåll för att returnera. Mer information finns i avsnittet ”innehåll viktiga specifikationerna”. Endast ett av värdena allowed_track_types och content_key_specs kan anges. |
| use_policy_overrides_exclusively |Boolean, true eller false |Använda attribut anges av policy_overrides och utelämna alla tidigare lagrad princip. |
| policy_overrides |JSON struktur, finns i avsnittet ”principen åsidosättningar”. |Principinställningar för denna licens.  Om tillgången har en fördefinierad princip, används de angivna värdena. |
| session_init |JSON struktur, finns i avsnittet ”Session-initialisering”. |Valfria data skickas till licensen. |
| parse_only |Boolean, true eller false |Licensbegäran parsas, men ingen licens har utfärdats. Dock returneras värden från licensbegäran i svaret. |

## <a name="content-key-specs"></a>Viktiga specifikationer för innehåll
Om det finns en befintlig princip, är det behöver inte ange något av värdena i innehåll nyckelspecifikation. Den befintliga principen som är associerade med det här innehållet används för att fastställa utdata-skydd, till exempel hög bandbredd innehåll skydd HDCP (Digital) och kopiera Allmänt Management System (CGMS). Om en befintlig princip inte är registrerad med Widevine licensservern innehållsleverantören mata in värdena i licensbegäran.   

Varje content_key_specs värde måste anges för alla spår oavsett use_policy_overrides_exclusively alternativ. 

| Namn | Värde | Beskrivning |
| --- | --- | --- |
| content_key_specs. track_type |sträng |Ett namn för spåra. Om content_key_specs anges i dess licens, se till att ange alla spåra typer explicit. Det gick inte att göra det resulterar i det gick inte att spela upp efter 10 sekunder. |
| content_key_specs  <br/> security_level |UInt32 |Definierar stabilitet klientkrav för uppspelning. <br/> -Programvarubaserad vitt om kryptering krävs. <br/> -Kryptografi programvara och en dolda avkodare krävs. <br/> -Nyckel material och kryptografiska åtgärder måste utföras inom en maskinvarustöd betrodda körningsmiljö. <br/> -Kryptering och avkodning av innehåll måste utföras inom en maskinvarustöd betrodda körningsmiljö.  <br/> -Kryptering, avkodning och alla hanteringen av media (komprimerade och okomprimerade) måste hanteras i en körningsmiljö med maskinvarustöd betrodda. |
| content_key_specs <br/> required_output_protection.hDC |sträng, en HDCP_NONE HDCP_V1, HDCP_V2 |Anger om HDCP krävs. |
| content_key_specs <br/>key |Base64-<br/>kodad sträng |Innehållsnyckeln ska användas för den här spåra. Om anges krävs track_type eller key_id. Innehållsleverantören kan använda det här alternativet för att mata in innehållsnyckeln för spåret i stället för att låta licensservern Widevine Generera eller söka efter en nyckel. |
| content_key_specs.key_id |Binary base64-kodad sträng 16 byte |Unik identifierare för nyckeln. |

## <a name="policy-overrides"></a>Princip för åsidosättningar
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| policy_overrides&#46;can_play |Boolean, true eller false |Anger att spela upp innehåll tillåts. Standardvärdet är false. |
| policy_overrides&#46;can_persist |Boolean, true eller false |Anger att licensen kan sparas till nonvolatile lagring för användning offline. Standardvärdet är false. |
| policy_overrides&#46;can_renew |Boolean, true eller false |Anger att förnyelse av denna licens tillåts. Om värdet är true kan licensen varaktighet utökas genom pulsslag. Standardvärdet är false. |
| policy_overrides&#46;license_duration_seconds |Int64 |Anger tidsfönstret för den här specifika licens. Värdet 0 anger att det finns ingen gräns för hur länge. Standardvärdet är 0. |
| policy_overrides&#46;rental_duration_seconds |Int64 |Anger tidsfönstret när uppspelningen är tillåtet. Värdet 0 anger att det finns ingen gräns för hur länge. Standardvärdet är 0. |
| policy_overrides&#46;playback_duration_seconds |Int64 |Visa tidsfönstret när uppspelning startas inom licens varaktighet. Värdet 0 anger att det finns ingen gräns för hur länge. Standardvärdet är 0. |
| policy_overrides&#46;renewal_server_url |sträng |Alla pulsslag (förnyelse)-begäranden för denna licens dirigeras till angiven URL. Det här fältet används endast om can_renew är true. |
| policy_overrides&#46;renewal_delay_seconds |Int64 |Hur många sekunder efter license_start_time innan förnyelse prövas först. Det här fältet används endast om can_renew är true. Standardvärdet är 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |Int64 |Anger fördröjningen i sekunder mellan efterföljande licens förnyelse begäranden om fel uppstår. Det här fältet används endast om can_renew är true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |Int64 |Fönstret i tid i vilka uppspelning kan fortsätta medan förnyelse görs, men misslyckades på grund av backend-problem med licensservern. Värdet 0 anger att det finns ingen gräns för hur länge. Det här fältet används endast om can_renew är true. |
| policy_overrides&#46;renew_with_usage |Boolean, true eller false |Anger att licensen skickas för förnyelse när förbrukning startar. Det här fältet används endast om can_renew är true. |

## <a name="session-initialization"></a>Sessionsinitieringen av
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| provider_session_token |Base64-kodad sträng |Den här sessionstoken som skickas tillbaka licensen och finns i efterföljande uppdateringar. Sessionstoken inte finns kvar efter sessioner. |
| provider_client_token |Base64-kodad sträng |Klienttoken att skicka tillbaka i svaret licens. Värdet ignoreras om licensbegäran innehåller en klienttoken. Klienttoken kvarstår utöver licens sessioner. |
| override_provider_client_token |Boolean, true eller false |Om false och licensbegäran innehåller en klienttoken Använd token från begäran även om en klienttoken har angetts i den här strukturen. Om värdet är true alltid använda den token som angetts i den här strukturen. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurera Widevine-licens med .NET 

Media Services tillhandahåller en klass som låter dig konfigurera en Widevine-licens. Om du vill skapa licensen skicka JSON till [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Så här konfigurerar du mallen kan du:

1.  Direkt-konstruktion/hårdkoda en JSON-sträng (som kan vara problematiskt);

    ```csharp
        ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
    };
    ```

2.  Konstruktion behövs klasser med egenskaper som mappar till de JSON-attribut och skapa en instans av dem före serialisering dem till JSON-sträng. Nedan visas ett exempel på dessa klasser och hur de instansieras och serialiseras.

    ```csharp
    public class policy_overrides
    {
        public bool can_play { get; set; }
        public bool can_persist { get; set; }
        public bool can_renew { get; set; }
        public int rental_duration_seconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
        public int playback_duration_seconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
        public int license_duration_seconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    }

    public class content_key_spec
    {
        public string track_type { get; set; }
        public int security_level { get; set; }
        public output_protection required_output_protection { get; set; }
    }

    public class output_protection
    {
        public string hdcp { get; set; }
    }

    public class widevine_template
    {
        public string allowed_track_types { get; set; }
        public content_key_spec[] content_key_specs { get; set; }
        public policy_overrides policy_overrides { get; set; }
    }
    ```

### <a name="configure-the-license"></a>Konfigurera licensen

Använda klasser som definierats i föregående avsnitt för att skapa JSON som används för att konfigurera [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
void ConfigureLicense()
{
    widevine_template objwidevine_template = new widevine_template()
    {
        allowed_track_types = "SD_HD",
        content_key_specs = new content_key_spec[]
        {
            new content_key_spec()
            {
                track_type = "SD",
                security_level = 1,
                required_output_protection = new output_protection()
                {
                hdcp = "HDCP_V2"
                }
            }
        },
        policy_overrides = new policy_overrides()
        {
            can_play = true,
            can_persist = true,
            can_renew = false,
            license_duration_seconds = 2592000,
            playback_duration_seconds = 10800,
            rental_duration_seconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(objwidevine_template)
    };
}
```

## <a name="next-steps"></a>Nästa steg

[Översikt](content-protection-overview.md)