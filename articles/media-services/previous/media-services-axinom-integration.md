---
title: Med hjälp av Axinom för att leverera Widevine-licenser till Azure Media Services | Microsoft Docs
description: Den här artikeln beskriver hur du kan använda Azure Media Services (AMS) för att leverera en dataström som krypteras dynamiskt med AMS med både PlayReady och Widevine DRMs. PlayReady-licens kommer från licensserver för Media Services PlayReady och Widevine-licens levereras med Axinom licensservern.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 81247863eb86752113989f6e48e79f5c8bc75505
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061162"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd Axinom för att leverera Widevine-licenser till Azure Media Services
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Översikt
Azure Media Services (AMS) har lagt till Google Widevines dynamiska skydd (se [Mingfei's blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) information). Dessutom Azure Media Player (APM) också har lagt till stöd för Widevine (se [AMP dokumentet](http://amp.azure.net/libs/amp/latest/docs/) information). Det här är en större fullgöra med strömmande DASH-innehåll som skyddas av CENC med flera-native-DRM (PlayReady och Widevine) på moderna webbläsare utrustad med mus och EME.

Från och med Media Services .NET SDK version 3.5.2 kan Media Services du konfigurera Widevine-licensmall och hämta Widevine-licenser. Du kan också använda följande AMS-partner för hjälp med att leverera Widevine-licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) och [castLabs](http://castlabs.com/company/partners/azure/).

Den här artikeln beskriver hur du integrerar och testa Widevine licensservern som hanteras av Axinom. Exempel:  

* Konfigurera dynamic Common Encryption med multi-DRM (PlayReady och Widevine) med motsvarande licens förvärv webbadresserna.
* Generera en JWT-token för att uppfylla server licensavtalet;
* Utveckla Azure Media Player-app, som hanterar licenser med JWT-token autentisering.

Hela systemet och flödet av innehållsnyckeln, nyckel-ID, nyckel seed, JTW token och dess anspråk kan beskrivas bäst med följande diagram:

![DASH och CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Konfigurera dynamisk skydd och viktiga leveransprincipen finns Mingfei's blog: [konfigurera Widevine-paketering med Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Du kan konfigurera skydd för dynamiska CENC med multi-DRM för DASH strömning med båda av följande:

1. PlayReady-skydd för MS Edge och IE11 som kan ha en token auktorisering begränsning. Den tokenbegränsade principen måste åtföljas av en token som utfärdas av en säker säkerhetstokentjänst (STS), till exempel Azure Active Directory.
2. Widevine skydd för Chrome, kan det kräva token autentisering med token som utfärdas av en annan STS. 

Se [JWT-Token generering](media-services-axinom-integration.md#jwt-token-generation) avsnittet varför Azure Active Directory kan inte användas som en STS för Axinom's Widevine licensservern.

### <a name="considerations"></a>Överväganden
1. Du måste använda Axinom angetts viktiga startvärde (8888000000000000000000000000000000000000) och din genererade eller valda nyckel-ID för att generera innehållsnyckeln för att konfigurera viktiga delivery service. Axinom licensserver alla licenser som innehåller innehåll nycklar baserat på samma nyckel startvärdet som inte är giltig för testning och produktion.
2. Widevine-licens förvärv URL: en för att testa: [ https://drm-widevine-licensing.axtest.net/AcquireLicense ](https://drm-widevine-licensing.axtest.net/AcquireLicense). Både HTTP och HTTS tillåts.

## <a name="azure-media-player-preparation"></a>Förberedelser för Azure Media Player
AMP v1.4.0 stöder uppspelning av AMS-innehåll som är packade dynamiskt med PlayReady och Widevine DRM.
Om Widevine licensservern inte kräver tokenautentisering, är det ingenting ytterligare måste du göra om du vill testa en DASH-innehåll som skyddas av Widevine. Ett exempel AMP teamet tillhandahåller en enkel [exempel](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), där du kan se den arbetar i kant och IE11 med PlayReady och Chrome med Widevine.
Widevine-licensservern som tillhandahålls av Axinom kräver JWT-token autentisering. JWT-token måste skickas med licensbegäran via en HTTP-huvudet ”X-AxDRM-meddelandet”. Du måste lägga till följande javascript på webbsidan som är värd för AMP innan du anger källan för detta ändamål:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Resten av AMP koden är standard AMP API som AMP dokumentet [här](http://amp.azure.net/libs/amp/latest/docs/).

Ovanstående javascript för inställningen anpassade authorization-huvud är fortfarande ett kortsiktigt tillvägagångssätt innan den officiella långsiktig strategi i AMP släpps.

## <a name="jwt-token-generation"></a>JWT-Token generering
Axinom Widevine krävs licens för att testa autentisering för JWT-token. Dessutom kan är en av anspråk i JWT-token av en sammansatt objekttyp i stället för primitiv datatyp.

Azure AD kan tyvärr bara utfärda JWT-token med primitiva typer. På liknande sätt, .NET Framework-API (System.IdentityModel.Tokens.SecurityTokenHandler och JwtPayload) kan du bara ange komplexa objekttyp som anspråk. Anspråk är fortfarande serialiserad som sträng. Därför kan vi använda någon av två för att generera JWT-token för begäran för Widevine-licens.

John Sheehan [JWT NuGet-paketet](https://www.nuget.org/packages/JWT) uppfyller dina behov så att vi ska använda den här NuGet-paketet.

Nedan visas koden för genererar JWT-token med nödvändiga anspråk som krävs av Axinom Widevine licensserver för testning:

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

Axinom Widevine licensserver

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Överväganden
1. Även om AMS Playreadys licensleveranstjänst kräver ”ägar =” före en autentiseringstoken Axinom Widevine licensservern inte använder den.
2. Nyckeln Axinom kommunikation används som nyckel för signeringscertifikatet. Nyckeln är en hexadecimal sträng, men den måste behandlas som en serie byte inte en sträng när kodning. Detta uppnås med metoden ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Hämta nyckel-ID
Kanske såg du att i koden för att generera en JWT-token, nyckel-ID krävs. Eftersom JWT-token måste vara klar innan du läser in AMP spelare, nyckel måste-ID hämtas för att generera en JWT-token.

Naturligtvis finns flera sätt att få bevarande av nyckel-ID. Till exempel en kan lagra nyckeln ID tillsammans med innehållet metadata i en databas. Eller du kan hämta nyckeln ID från DASH MPD (Media presentationsbeskrivning)-fil. Koden nedan gäller den senare.

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
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
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
Med Widevine-stöd i Azure Media Services-innehållsskydd och Azure Media Player senaste dessutom kommer du att implementera strömning av DASH + flera-native-DRM (PlayReady och Widevine) med både PlayReady Licenstjänsten i AMS och Widevine-licens servern från Axinom för följande moderna webbläsare:

* Chrome
* Microsoft Edge på Windows 10
* Internet Explorer 11 på Windows 8.1 och Windows 10
* Både Firefox (stationär dator) och Safari på Mac (inte iOS) stöds också via Silverlight och samma URL med Azure Media Player

Följande parametrar som krävs i licensservern Mini lösning användning Axinom Widevine. Förutom nyckel-ID, resten av parametrarna som tillhandahålls av Axinom baserat på deras Widevine-serverinstallation.

| Parameter | Hur den används |
| --- | --- |
| Kommunikation nyckeln ID |Måste tas med som värde för anspråk ”com_key_id” i JWT-token (se [detta](media-services-axinom-integration.md#jwt-token-generation) avsnitt). |
| Kommunikation-nyckel |Måste användas som signeringsnyckeln för JWT-token (se [detta](media-services-axinom-integration.md#jwt-token-generation) avsnitt). |
| Nyckel-dirigering |Måste användas för att generera nyckeln med angivna innehåll nyckeln ID (se [detta](media-services-axinom-integration.md#content-protection) avsnitt). |
| URL för anskaffning av Widevine-licens |Måste användas i Konfigurera tillgångsleveransprincip för direktuppspelning av STRECK (se [detta](media-services-axinom-integration.md#content-protection) avsnitt). |
| Innehåll nyckel-ID |Måste vara en del av värdet för rätt meddelande anspråk av JWT-token (se [detta](media-services-axinom-integration.md#jwt-token-generation) avsnitt). |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Bekräftelser
Vi vill bekräfta följande personer som har bidragit till att skapa det här dokumentet: Kristjan Jõgi av Axinom och Mingfei Yan Amit Rajput.

