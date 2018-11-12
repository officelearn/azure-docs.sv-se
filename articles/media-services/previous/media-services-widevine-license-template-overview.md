---
title: Översikt över Widevine-licensmallen | Microsoft Docs
description: Det här avsnittet ger en översikt över Widevine-licensmall som används för att konfigurera Widevine-licenser.
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
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 065845bb2abd02f02fe2050780bc0613cbd2f12c
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036010"
---
# <a name="widevine-license-template-overview"></a>Översikt över Widevine-licensmallen
Du kan använda Azure Media Services för att konfigurera och begära Google Widevine-licenser. När spelaren försöker att spela upp Widevine-skyddat innehåll, skickas en begäran till licensleveranstjänst du erhåller en licens. Om Licenstjänsten godkänner begäran, skickar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

En Widevine-licensbegäran formateras som ett JSON-meddelande.  

>[!NOTE]
> Du kan skapa ett tomt meddelande utan värden bara ”{}”. Sedan skapas en licensmall med standardinställningar. Standardinställningen fungerar för de flesta fall. Microsoft-baserade-licensleverans scenarier bör alltid använda standardinställningarna. Om du vill ställa in ”provider” och ”content_id” värden måste en provider matcha Widevine autentiseringsuppgifter.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>JSON-meddelande
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| nyttolast |Base64-kodad sträng |Licensbegäran som skickats av en klient. |
| content_id |Base64-kodad sträng |Identifieraren som används för att härleda nyckel-ID och innehåll nyckel för varje content_key_specs.track_type. |
| Providern |sträng |Används för att söka efter innehåll nycklar och principer. Om du använder Microsoft key leverans för Widevine-licensleverans, ignoreras den här parametern. |
| principens_namn |sträng |Namnet på en tidigare registrerad princip. Valfri. |
| allowed_track_types |Enum |SD_ONLY eller SD_HD. Styr vilka nycklar av innehåll som ingår i en licens. |
| content_key_specs |Matris med JSON strukturer som stöds, se avsnittet ”Content viktiga specifikationer”.  |En mer detaljerad kontroll på vilka nycklar ska returneras. Mer information finns i avsnittet ”innehåll viktiga specifikationer”. Endast en av de allowed_track_types och content_key_specs värdena kan anges. |
| use_policy_overrides_exclusively |Booleskt värde, true eller false |Använd principen attribut anges av policy_overrides och utelämna alla tidigare lagrade principen. |
| policy_overrides |JSON struktur, finns i avsnittet ”principen åsidosättningar”. |Principinställningar för denna licens.  I den händelse att den här tillgången har en fördefinierad princip, används dessa angivna värden. |
| session_init |JSON struktur, finns i avsnittet ”sessionen initieras”. |Valfria data skickas till licensen. |
| parse_only |Booleskt värde, true eller false |Licensbegäran tolkas, men ingen licens har utfärdats. Dock returneras värden från licensbegäran i svaret. |

## <a name="content-key-specs"></a>Viktiga specifikationer för innehåll
Om det finns en befintlig princip, är det du behöver inte ange något av värdena i innehåll nyckelspecifikation. Den befintliga principen som är associerade med det här innehållet används för att fastställa utdata-skydd, till exempel hög bandbredd digitalt innehåll Protection (HDCP) och kopiera Allmänt Management System (CGMS). Om en befintlig princip inte är registrerad med Widevine licensservern innehållsleverantören mata in värdena i licensbegäran.   

Varje content_key_specs värde måste anges för alla spår, oavsett use_policy_overrides_exclusively alternativ. 

| Namn | Värde | Beskrivning |
| --- | --- | --- |
| content_key_specs. track_type |sträng |Ett typnamn spåra. Om content_key_specs anges i licensbegäran, se till att ange alla spåra typer explicit. Det gick inte att göra det resulterar i det gick inte att spela upp de senaste 10 sekunder. |
| content_key_specs  <br/> security_level |UInt32 |Definierar stabilitet klientkrav för uppspelning. <br/> -Programvarubaserad vit-box-kryptering krävs. <br/> -Kryptografi programvara och en dold avkodare måste anges. <br/> – Viktiga material och kryptografi aktiviteter måste utföras inom en maskinvarustöd betrodda körningsmiljö. <br/> -Kryptering och avkodning av innehåll måste utföras inom en maskinvarustöd betrodda körningsmiljö.  <br/> -Kryptering, avkodning och alla hantering av media (komprimerade och okomprimerade) måste hanteras inom en maskinvarustöd betrodda körningsmiljö. |
| content_key_specs <br/> required_output_protection.hDC |sträng, ett av HDCP_NONE, HDCP_V1, HDCP_V2 |Anger om HDCP krävs. |
| content_key_specs <br/>key |Base64-<br/>kodad sträng |Innehållsnyckeln ska användas för den här spåra. Om anges krävs track_type eller key_id. Innehållsleverantören kan använda det här alternativet för att mata in innehållsnyckeln för den här track i stället för att låta licensservern Widevine Generera eller söka efter en nyckel. |
| content_key_specs.key_id |Binary base64-kodad sträng, 16 byte |Unik identifierare för nyckeln. |

## <a name="policy-overrides"></a>Princip för åsidosättningar
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| policy_overrides. can_play |Booleskt värde, true eller false |Anger den uppspelningen av innehåll tillåts. Standardvärdet är false. |
| policy_overrides. can_persist |Booleskt värde, true eller false |Anger att licensen kan sparas till nonvolatile lagring för användning offline. Standardvärdet är false. |
| policy_overrides. can_renew |Booleskt värde, true eller false |Anger att förnyelse av denna licens tillåts. Om värdet är true, kan varaktigheten för licensen utökas med pulsslag. Standardvärdet är false. |
| policy_overrides. license_duration_seconds |Int64 |Anger tidsperioden för denna specifika licens. Värdet 0 anger att det finns ingen gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides. rental_duration_seconds |Int64 |Anger tidsperioden medan uppspelning är tillåten. Värdet 0 anger att det finns ingen gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides. playback_duration_seconds |Int64 |Fönstret Visa tid när uppspelning startas inom hela licens. Värdet 0 anger att det finns ingen gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides. renewal_server_url |sträng |Alla begäranden för pulsslag (förnyelse) för den här licensen dirigeras till den angivna URL: en. Det här fältet används endast om can_renew är sant. |
| policy_overrides. renewal_delay_seconds |Int64 |Hur många sekunder efter license_start_time innan förnyelse görs först. Det här fältet används endast om can_renew är sant. Standardvärdet är 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Anger fördröjningen i sekunder mellan begäranden om efterföljande licens förnyelse, om fel uppstår. Det här fältet används endast om can_renew är sant. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Fönstret för gånger på vilka uppspelning kan fortsätta medan förnyelse görs, men misslyckades på grund av backend-problem med licensservern. Värdet 0 anger att det finns ingen gräns för varaktigheten. Det här fältet används endast om can_renew är sant. |
| policy_overrides. renew_with_usage |Booleskt värde, true eller false |Anger att licensen har skickats kan förnyas när användning startar. Det här fältet används endast om can_renew är sant. |

## <a name="session-initialization"></a>Sessionen initieras
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| provider_session_token |Base64-kodad sträng |Den här sessionstoken som skickas tillbaka licensen och finns i efterföljande uppdateringar. Sessionstoken inte finns kvar efter sessioner. |
| provider_client_token |Base64-kodad sträng |Token för klienten att skicka tillbaka licens-svar. Om licensbegäran om innehåller en token för klienten, ignoreras det här värdet. Klienten token kvarstår efter licens-sessioner. |
| override_provider_client_token |Booleskt värde, true eller false |Om FALSKT och licensbegäran innehåller en token för klienten kan använda token från förfrågan även om en klient-token har angetts i den här strukturen. Om värdet är true, Använd alltid den token som anges i den här strukturen. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Konfigurera Widevine-licenser med hjälp av .NET-typer
Media Services tillhandahåller .NET API: er som du kan använda för att konfigurera Widevine-licenser. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klasser som definieras i Media Services .NET SDK
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
I följande exempel visas hur du använder .NET API: er för att konfigurera en enkel Widevine-licens:

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


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se också
[Använda PlayReady och/eller Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md)

