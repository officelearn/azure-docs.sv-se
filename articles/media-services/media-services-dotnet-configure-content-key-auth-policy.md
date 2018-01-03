---
title: "Konfigurera en princip för auktorisering av innehållsnyckel med hjälp av Media Services .NET SDK | Microsoft Docs"
description: "Lär dig hur du konfigurerar en auktoriseringsprincip för en innehållsnyckel med Media Services .NET SDK."
services: media-services
documentationcenter: 
author: mingfeiy
manager: cfowler
editor: 
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 39782bd687c9c1b50699c05e61e57d9c767a8d32
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Dynamisk kryptering: Konfigurera en princip för auktorisering av innehållsnyckel
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Översikt
 Du kan använda Azure Media Services för att leverera MPEG DASH, Smooth Streaming och HTTP Live Streaming (HLS) dataströmmar som skyddas med den Standard AES (Advanced Encryption) med hjälp av krypteringsnycklar för 128-bitars eller [PlayReady digital rights management (DRM) ](https://www.microsoft.com/playready/overview/). Med Media Services kan leverera du också DASH krypterat med Widevine DRM-dataströmmar. Både PlayReady och Widevine krypteras enligt specifikationen vanliga kryptering (ISO/IEC 23001 7 CENC).

Media Services tillhandahåller också ett nyckel/licensleveranstjänst som klienter kan hämta AES-nycklar eller PlayReady/Widevine-licenser för att spela upp det krypterade innehållet.

Om du vill Media Services för att kryptera en tillgång, måste du koppla en krypteringsnyckel (CommonEncryption eller EnvelopeEncryption) med tillgången. Mer information finns i [skapa ContentKeys med .NET](media-services-dotnet-create-contentkey.md). Du måste också konfigurera auktoriseringsprinciper för nyckeln (som beskrivs i den här artikeln).

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll med hjälp av AES eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. För att avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Principen för auktorisering av innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar. Alternativen är öppen eller token begränsning. Begränsad token principen måste åtföljas av en token som utfärdas av en säkerhetstokentjänst (STS). Media Services stöder token i simple web token ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) format och JSON Web Token ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) format.

Media Services ger inte STS. Du kan skapa en anpassad STS eller använda Azure Access Control Service problemet tokens. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfiguration (som beskrivs i den här artikeln). Om token är giltig och anspråk i token som matchar de som konfigurerats för att innehållsnyckeln, returnerar Media Services viktiga tjänsten krypteringsnyckeln till klienten.

Mer information finns i följande artiklar:

- [JWT-token autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrera Azure Media Services OWIN MVC-baserade app med Azure Active Directory och begränsa viktiga innehållsleverans baserat på JWT anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Vissa förutsättningar gäller
* När ditt Media Services-konto har skapats läggs standard strömmande slutpunkten till ditt konto i tillståndet ”stoppad”. Om du vill starta strömning ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste strömmande slutpunkten vara i tillståndet ”körs”. 
* Din tillgång måste innehålla en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer. Mer information finns i [koda en tillgång](media-services-encode-asset.md).
* Ladda upp och koda dina tillgångar med hjälp av alternativet AssetCreationOptions.StorageEncrypted.
* Om du planerar att ha flera nycklar för innehåll som kräver i samma konfiguration, rekommenderar vi att du skapar en enda auktoriseringsprincip och återanvända med nycklar för multiinnehåll.
* Tjänsten key cachelagrar ContentKeyAuthorizationPolicy och dess relaterade objekt (alternativ och begränsningar) i 15 minuter. Du kan skapa ContentKeyAuthorizationPolicy och ange om du vill använda en token begränsning, testa den och uppdatera principen till den öppna begränsningen. Den här processen tar ungefär 15 minuter innan principen växlar till den öppna versionen av principen.
* Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort en befintlig lokaliserare och skapa en ny.
* Det går för närvarande kryptera progressiv hämtning.
* Ett Media Services strömningsslutpunkt anger värdet för huvudet CORS 'Access Control-Tillåt-ursprung' preflight-svar som jokertecknet '\*'. Det här värdet fungerar bra med de flesta spelare, inklusive Azure Media Player Roku och JWPlayer och andra. Dock vissa spelare som använder dashjs fungerar inte eftersom autentiseringsuppgifterna-läget är inställt på ”innehåller” XMLHttpRequest i sina dashjs inte tillåter jokertecknet ”\*” som värde för 'Access Control-Tillåt-ursprung'. Som en tillfällig lösning till den här begränsningen i dashjs om du är värd för din klient från en enda domän, ange Media Services domänen i svarshuvudet Preflight. Öppna ett supportärende via Azure portal om du behöver hjälp.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dynamisk kryptering
### <a name="open-restriction"></a>Öppna begränsning
Öppna begränsning innebär systemet levererar nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning.

I följande exempel skapas en öppen auktoriseringsprincip och lägger till den innehållsnyckeln:

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }


### <a name="token-restriction"></a>Tokenbegränsningar
Det här avsnittet beskrivs hur du skapar en princip för auktorisering av innehållsnyckel och associera det med innehållsnyckeln. Auktoriseringsprincipen beskriver vilka auktorisering krav måste uppfyllas för att avgöra om användaren har behörighet att ta emot nyckeln. Exempelvis innehåller den nyckel som token som signerats med listan med nycklar för verifiering?

Om du vill konfigurera alternativet tokenbegränsningar som du behöver använda en XML för att beskriva behörighetskraven för token. Tokenbegränsningar konfigurations-XML måste uppfylla följande XML-schema:

#### <a name="token-restriction-schema"></a>Tokenbegränsningar schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Du måste ange primär Verifieringsnyckeln, utfärdare och målgrupp parametrar när du konfigurerar begränsad token-principen. Primär Verifieringsnyckeln innehåller den nyckel som token som signerats med. Utfärdaren är STS som utfärdar token. Målgruppen (kallas ibland för scope) beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

När du använder Media Services SDK för .NET, använder du klassen TokenRestrictionTemplate för att generera token för begränsning.
I följande exempel skapas en auktoriseringsprincip med en token begränsning. I det här exemplet presenterar klienten en token som innehåller en signeringsnyckel (VerificationKey), en token utfärdare och nödvändiga anspråk.

    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }

#### <a name="test-token"></a>Test-token
Om du vill hämta en test-token baserat på de tokenbegränsningar som användes för nyckelauktoriseringsprincipen, gör du följande:

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


## <a name="playready-dynamic-encryption"></a>Dynamisk PlayReady-kryptering
Du kan använda Media Services för att konfigurera rättigheter och begränsningar som du vill PlayReady DRM-körningsmiljön att tvinga när en användare försöker att spela upp skyddat innehåll. 

När du skyddar ditt innehåll med PlayReady en av de saker som du måste ange i principen för auktorisering är en XML-sträng som definierar den [PlayReady-licensmall](media-services-playready-license-template-overview.md). I Media Services SDK för .NET hjälpa klasserna PlayReadyLicenseResponseTemplate och PlayReadyLicenseTemplate dig att definiera PlayReady license-mall.

Information om hur du krypterar ditt innehåll med PlayReady och Widevine finns [Använd PlayReady och/eller Widevine dynamic vanliga kryptering](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Öppna begränsning
Öppna begränsning innebär systemet levererar nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning.

I följande exempel skapas en öppen auktoriseringsprincip och lägger till den innehållsnyckeln:

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

### <a name="token-restriction"></a>Tokenbegränsningar
Om du vill konfigurera alternativet tokenbegränsningar som du behöver använda en XML för att beskriva behörighetskraven för token. Konfigurationen av tokenbegränsningar XML måste motsvara det XML-schemat visas i den ”[Token begränsning schemat](#token-restriction-schema)” avsnittet.

    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


Om du vill hämta en token för test baserat på de tokenbegränsningar som användes för nyckelauktoriseringsprincipen finns i ”[Test token](#test-token)” avsnittet. 

## <a id="types"></a>Typer som används när du definierar ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

### <a id="TokenType"></a>TokenType
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat innehållsnyckelns auktoriseringsprincip finns [konfigurera en tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md).

