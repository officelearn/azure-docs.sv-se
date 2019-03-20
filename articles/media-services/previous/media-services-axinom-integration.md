---
title: Använd Axinom för att leverera Widevine-licenser till Azure Media Services | Microsoft Docs
description: 'Den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en dataström som krypteras dynamiskt med AMS med både PlayReady och Widevine DRM: er. PlayReady-licensen kommer från licensserver för Media Services PlayReady och Widevine-licens levereras av Axinom licensservern.'
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 6714beae690e23c686fc08b88e93044ae3901c89
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839499"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd Axinom för att leverera Widevine-licenser till Azure Media Services 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Översikt
Azure Media Services (AMS) har lagt till skydd för Google Widevine dynamisk (se [Mingfei's blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) information). Dessutom Azure Media Player (AMP) har också lagt till stöd för Widevine (se [AMP dokumentet](https://amp.azure.net/libs/amp/latest/docs/) information). Det här är en större fullgöra med strömmande DASH-innehåll som skyddas av CENC med flera-native-DRM (PlayReady och Widevine) på moderna webbläsare som är utrustade med MSE och EME.

Från och med Media Services .NET SDK version 3.5.2, kan Media Services du konfigurera Widevine-licensmall och få Widevine-licenser. Du kan också använda följande AMS-partner för att leverera Widevine-licenser: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Den här artikeln beskriver hur du integrerar och testa Widevine-licensserver som hanteras av Axinom. Till exempel:  

* Konfigurera dynamic Common Encryption med multi-DRM (PlayReady och Widevine) med motsvarande licens förvärv webbadresserna.
* Generera en JWT-token för att uppfylla server licensavtalet;
* Utveckla Azure Media Player-appen, som hanterar licenser med JWT-token autentisering.

Hela systemet och flödet av innehållsnyckeln, nyckel-ID, viktiga seed, JTW token och dess anspråk kan beskrivas bäst med följande diagram:

![DASH och CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Konfigurera dynamisk skydd och viktiga leveransprincip finns Mingfei's blog: [Konfigurera Widevine-paketering med Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Du kan konfigurera dynamisk CENC skydd med multi-DRM för DASH-strömnings med båda av följande:

1. PlayReady-skydd för Microsoft Edge och IE11 som kan ha en token auktorisering-begränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker säkerhetstokentjänst (STS), till exempel Azure Active Directory.
2. Widevine skydd för Chrome, det kan kräva autentisering med enhetstoken till token som utfärdas av en annan Säkerhetstokentjänst. 

Se [JWT-Tokengenerering](media-services-axinom-integration.md#jwt-token-generation) för varför Azure Active Directory kan inte användas som en STS för Axinom's Widevine-licensserver.

### <a name="considerations"></a>Överväganden
1. Du måste använda Axinom angetts viktiga startvärde (8888000000000000000000000000000000000000) och din genererade eller valda nyckel-ID för att generera innehållsnyckeln för att konfigurera nyckelleveranstjänst. Axinom licensservern utfärdar alla licenser som innehåller nycklar för multiinnehåll baserat på samma nyckel startvärdet, vilken är giltig för testning och produktion.
2. Widevine-licens förvärv URL: en för testning: [ https://drm-widevine-licensing.axtest.net/AcquireLicense ](https://drm-widevine-licensing.axtest.net/AcquireLicense). Både HTTP och HTTS tillåts.

## <a name="azure-media-player-preparation"></a>Azure Media Player Preparation
AMP v1.4.0 har stöd för uppspelning av AMS-innehåll som levereras dynamiskt med både PlayReady och Widevine DRM.
Om Widevine licensservern inte kräver autentisering med enhetstoken du det finns inget ytterligare du behöver göra för att testa en DASH-innehåll som skyddas av Widevine. Exempelvis kan AMP-teamet erbjuder en enkel [exempel](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), där du kan se hur det fungerar i Microsoft Edge och IE11 med PlayReady och Chrome med Widevine.
Widevine-licensservern som tillhandahålls av Axinom kräver autentisering för JWT-token. Detta JWT-token måste skickas med licensbegäran via en HTTP-huvud ”X-AxDRM-Message”. Du måste lägga till följande javascript på webbsidan som är värd för och innan du anger källan för detta ändamål:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Resten av AMP kod är standard och API som i dokumentet och [här](https://amp.azure.net/libs/amp/latest/docs/).

Ovanstående javascript för inställningen anpassade auktoriseringsrubriken är fortfarande en kortsiktig metod innan den officiella långsiktiga metoden i AMP släpps.

## <a name="jwt-token-generation"></a>JWT-Token generering
Axinom Widevine licensserver för att testa kräver autentisering för JWT-token. En av anspråk i JWT-token är dessutom typu komplexa objektet i stället för primitiva datatyp.

Azure AD kan tyvärr bara utfärda JWT-token med primitiva typer. På samma sätt, .NET Framework-API (System.IdentityModel.Tokens.SecurityTokenHandler och JwtPayload) kan du bara ange komplexa objekttyp som anspråk. Anspråk serialiseras dock fortfarande som sträng. Därför kan vi använda någon av två för att generera JWT-token för begäran för Widevine-licens.

John Sheehan [JWT-NuGet-paketet](https://www.nuget.org/packages/JWT) passar så att vi kan använda den här NuGet-paketet.

Nedan visas koden för generering JWT-token med de nödvändiga anspråk som krävs av Axinom Widevine licensserver för testning:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Axinom Widevine-licensserver

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Överväganden
1. Även om AMS Playreadys licensleveranstjänst kräver ”ägar =” före en autentiseringstoken Axinom Widevine-licensserver inte använder den.
2. Nyckeln Axinom kommunikation används som nyckel för signeringscertifikatet. Nyckeln är en hexadecimal sträng, men den måste behandlas som en serie byte inte en sträng när kodning. Detta uppnås med metoden ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Hämta nyckel-ID
Du kanske har märkt att i koden för att generera en JWT-token, nyckel-ID krävs. Eftersom de JWT-token måste vara redo innan du läser in och spelare, nyckel måste-ID hämtas för att generera JWT-token.

Naturligtvis finns flera sätt att hämta bevarande av nyckel-ID. Till exempel en kan lagra nyckel ID tillsammans med innehållsmetadata i en databas. Alternativt kan du hämta nyckel-ID från DASH MPD (Media presentationsbeskrivning)-fil. Koden nedan är för det senare.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Sammanfattning
Senaste tillägget Widevine support i både Content Protection för Azure Media Services och Azure Media Player är vi implementera strömning av DASH + Multi-Factor-native-DRM (PlayReady och Widevine) med både PlayReady-Licenstjänsten i AMS och Widevine-licens servern från Axinom för följande moderna webbläsare:

* Chrome
* Microsoft Edge i Windows 10
* Internet Explorer 11 på Windows 8.1 och Windows 10
* Både Firefox (skrivbord) och Safari på Mac (inte iOS) också stöds via Silverlight och samma URL med Azure Media Player

Följande parametrar krävs i licensservern Mini lösning att använda Axinom Widevine. Förutom nyckel-ID, resten av parametrarna tillhandahålls av Axinom baserat på deras Widevine-serverinstallation.

| Parameter | Hur den används |
| --- | --- |
| Kommunikation nyckel-ID |Måste tas med som värdet för anspråket ”com_key_id” i JWT-token (se [detta](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |
| Nyckel för kommunikation |Måste användas som signeringsnyckeln för JWT-token (se [detta](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |
| Nyckel-dirigering |Måste användas för att generera nyckeln med angivna innehåll ID-nyckeln (se [detta](media-services-axinom-integration.md#content-protection) avsnittet). |
| URL för anskaffning av Widevine-licens |Måste användas i Konfigurera tillgångsleveransprincip för DASH-strömning (se [detta](media-services-axinom-integration.md#content-protection) avsnittet). |
| ID för innehåll |Måste ingå som en del av värdet för rätt meddelande anspråk i JWT-token (se [detta](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Bekräftelser
Vi hoppas att du är medveten om följande personer som har bidragit till att skapa det här dokumentet: Kristjan Jõgi av Axinom Mingfei daN och Amit Rajput.

