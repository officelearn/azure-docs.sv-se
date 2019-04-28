---
title: Azure Media Services med Widevine-licensen mallöversikt | Microsoft Docs
description: Det här avsnittet ger en översikt över Widevine-licensmall som används för att konfigurera Widevine-licenser.
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
ms.openlocfilehash: c6fc363a7ab9de215647e371a9d3c846f8688bd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466331"
---
# <a name="widevine-license-template-overview"></a>Översikt över Widevine-licensmallen 

Azure Media Services kan du kryptera ditt innehåll med **Google Widevine**. Media Services tillhandahåller också en tjänst för att leverera Widevine-licenser. Du kan använda API: er för Azure Media Services för att konfigurera Widevine-licenser. När en spelare försöker att spela upp Widevine-skyddat innehåll, skickas en begäran till licensleveranstjänst att få licensen. Om Licenstjänsten godkänner begäran, skickar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

En Widevine-licensbegäran formateras som ett JSON-meddelande.  

>[!NOTE]
> Du kan skapa ett tomt meddelande utan värden bara ”{}”. Sedan skapas en licensmall med standardinställningar. Standardinställningen fungerar för de flesta fall. Microsoft-baserade-licensleverans scenarier bör alltid använda standardinställningarna. Om du vill ställa in ”provider” och ”content_id” värden måste en provider matcha Widevine autentiseringsuppgifter.

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
| nyttolast |Base64-kodad sträng |Licensbegäran som skickats av en klient. |
| content_id |Base64-kodad sträng |Identifieraren som används för att härleda nyckel-ID och innehåll nyckel för varje content_key_specs.track_type. |
| provider |string |Används för att söka efter innehåll nycklar och principer. Om du använder Microsoft key leverans för Widevine-licensleverans, ignoreras den här parametern. |
| policy_name |string |Namnet på en tidigare registrerad princip. Valfri. |
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
| content_key_specs. track_type |string |Ett typnamn spåra. Om content_key_specs anges i licensbegäran, se till att ange alla spåra typer explicit. Det gick inte att göra det resulterar i det gick inte att spela upp de senaste 10 sekunder. |
| content_key_specs  <br/> security_level |UInt32 |Definierar stabilitet klientkrav för uppspelning. <br/> -Programvarubaserad vit-box-kryptering krävs. <br/> -Kryptografi programvara och en dold avkodare måste anges. <br/> – Viktiga material och kryptografi aktiviteter måste utföras inom en maskinvarustöd betrodda körningsmiljö. <br/> -Kryptering och avkodning av innehåll måste utföras inom en maskinvarustöd betrodda körningsmiljö.  <br/> -Kryptering, avkodning och alla hantering av media (komprimerade och okomprimerade) måste hanteras inom en maskinvarustöd betrodda körningsmiljö. |
| content_key_specs <br/> required_output_protection.hdc |sträng, ett av HDCP_NONE, HDCP_V1, HDCP_V2 |Anger om HDCP krävs. |
| content_key_specs <br/>key |Base64-<br/>kodad sträng |Innehållsnyckeln ska användas för den här spåra. Om anges krävs track_type eller key_id. Innehållsleverantören kan använda det här alternativet för att mata in innehållsnyckeln för den här track i stället för att låta licensservern Widevine Generera eller söka efter en nyckel. |
| content_key_specs.key_id |Binary base64-kodad sträng, 16 byte |Unik identifierare för nyckeln. |

## <a name="policy-overrides"></a>Princip för åsidosättningar
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| policy_overrides&#46;can_play |Booleskt värde, true eller false |Anger den uppspelningen av innehåll tillåts. Standardvärdet är false. |
| policy_overrides&#46;can_persist |Booleskt värde, true eller false |Anger att licensen kan sparas till nonvolatile lagring för användning offline. Standardvärdet är false. |
| policy_overrides&#46;can_renew |Booleskt värde, true eller false |Anger att förnyelse av denna licens tillåts. Om värdet är true, kan varaktigheten för licensen utökas med pulsslag. Standardvärdet är false. |
| policy_overrides&#46;license_duration_seconds |int64 |Anger tidsperioden för denna specifika licens. Värdet 0 anger att det finns ingen gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Anger tidsperioden medan uppspelning är tillåten. Värdet 0 anger att det finns ingen gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Fönstret Visa tid när uppspelning startas inom hela licens. Värdet 0 anger att det finns ingen gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides&#46;renewal_server_url |string |Alla begäranden för pulsslag (förnyelse) för den här licensen dirigeras till den angivna URL: en. Det här fältet används endast om can_renew är sant. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Hur många sekunder efter license_start_time innan förnyelse görs först. Det här fältet används endast om can_renew är sant. Standardvärdet är 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Anger fördröjningen i sekunder mellan begäranden om efterföljande licens förnyelse, om fel uppstår. Det här fältet används endast om can_renew är sant. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Fönstret för gånger på vilka uppspelning kan fortsätta medan förnyelse görs, men misslyckades på grund av backend-problem med licensservern. Värdet 0 anger att det finns ingen gräns för varaktigheten. Det här fältet används endast om can_renew är sant. |
| policy_overrides&#46;renew_with_usage |Booleskt värde, true eller false |Anger att licensen har skickats kan förnyas när användning startar. Det här fältet används endast om can_renew är sant. |

## <a name="session-initialization"></a>Sessionen initieras
| Namn | Värde | Beskrivning |
| --- | --- | --- |
| provider_session_token |Base64-kodad sträng |Den här sessionstoken som skickas tillbaka licensen och finns i efterföljande uppdateringar. Sessionstoken inte finns kvar efter sessioner. |
| provider_client_token |Base64-kodad sträng |Token för klienten att skicka tillbaka licens-svar. Om licensbegäran om innehåller en token för klienten, ignoreras det här värdet. Klienten token kvarstår efter licens-sessioner. |
| override_provider_client_token |Booleskt värde, true eller false |Om FALSKT och licensbegäran innehåller en token för klienten kan använda token från förfrågan även om en klient-token har angetts i den här strukturen. Om värdet är true, Använd alltid den token som anges i den här strukturen. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurera Widevine-licens med .NET 

Media Services tillhandahåller en klass som låter dig konfigurera en Widevine-licens. För att konstruera licensen, skicka JSON till [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

För att konfigurera mallen, kan du:

### <a name="directly-construct-a-json-string"></a>Direkt skapa en JSON-sträng

Den här metoden kan vara felbenägna. Det rekommenderas att använda andra metoden som beskrivs i [definiera behövs klasser och serialisera till JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a> Ange nödvändiga klasserna och serialisera till JSON

#### <a name="define-classes"></a>Definiera klasser

I följande exempel visas ett exempel på definitioner av klasser som mappar till Widevine JSON-schema. Du kan skapa en instans av klasserna före serialisering dem till JSON-sträng.  

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

Använda klasser som definierats i föregående avsnitt för att skapa JSON som används för att konfigurera [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

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

## <a name="next-steps"></a>Nästa steg

Kolla hur man [skydda med DRM](protect-with-drm.md)
