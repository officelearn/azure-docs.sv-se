---
title: "Konfigurera innehållsnyckelns auktoriseringsprincip med Media Services .NET SDK | Microsoft Docs"
description: "Lär dig hur du konfigurerar en auktoriseringsprincip för en innehållsnyckel med Media Services .NET SDK."
services: media-services
documentationcenter: 
author: Mingfeiy
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
ms.openlocfilehash: 75dd9107dca215a0b31db3d44bada69210fe9ac6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>Dynamisk kryptering: Konfigurera innehållsnyckelns auktoriseringsprincip
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Översikt
Microsoft Azure Media Services kan du leverera MPEG DASH, Smooth Streaming och HTTP Live Streaming (HLS) dataströmmar som skyddas med Standard AES (Advanced Encryption) (med 128-bitars krypteringsnycklar) eller [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS kan du leverera DASH krypterat med Widevine DRM-dataströmmar. Både PlayReady och Widevine krypteras enligt den gemensamma  krypteringsspecifikationen (ISO/IEC 23001 7 CENC).

Media Services tillhandahåller också en **nyckel-/ Licensleveranstjänst** från vilka klienterna kan hämta AES-nycklar eller PlayReady/Widevine-licenser för att spela upp det krypterade innehållet.

Om du vill använda för Media Services att kryptera en tillgång, måste du associera en krypteringsnyckel (**CommonEncryption** eller **EnvelopeEncryption**) med tillgången (enligt [här](media-services-dotnet-create-contentkey.md)) och även konfigurera auktoriseringsprinciper för nyckeln (som beskrivs i den här artikeln).

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll med AES eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. Om du vill avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Principen för auktorisering av innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar: **öppna** eller **token** begränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i den **Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) format och **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) format.

Media Services tillhandahåller inte Secure Token tjänster. Du kan skapa en anpassad STS eller använda Microsoft Azure ACS problemet tokens. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfiguration (som beskrivs i den här artikeln). Media Services viktiga tjänsten returneras krypteringsnyckeln till klienten om token är giltig och anspråk i token som matchar de som konfigurerats för innehållsnyckeln.

Mer information finns i

[JWT-token autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrera Azure Media Services OWIN MVC baserat app med Azure Active Directory och begränsa viktiga innehållsleverans baserat på JWT anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Använd Azure ACS problemet tokens](http://mingfeiy.com/acs-with-key-services).

### <a name="some-considerations-apply"></a>Vissa förutsättningar gäller:
* När AMS-kontot skapas en **standard** strömningsslutpunkt har lagts till i ditt konto i den **stoppad** tillstånd. Om du vill starta strömning ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering strömmande slutpunkten måste vara i den **kör** tillstånd. 
* Din tillgång måste innehålla en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer. Mer information finns i [koda en tillgång](media-services-encode-asset.md).
* Ladda upp och koda dina tillgångar med **AssetCreationOptions.StorageEncrypted** alternativet.
* Om du planerar att ha flera nycklar för innehåll som kräver i samma konfiguration, rekommenderas att skapa en enda auktoriseringsprincip och återanvända med nycklar för multiinnehåll.
* Tjänsten nyckeln cachelagrar ContentKeyAuthorizationPolicy och dess relaterade objekt (alternativ och begränsningar) i 15 minuter.  Om du skapar en ContentKeyAuthorizationPolicy och ange om du vill använda en ”Token” begränsning, testa, och uppdatera principen till ”öppen” begränsningen, det tar ungefär 15 minuter innan principen växlar till ”öppen” versionen av principen.
* Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort en befintlig lokaliserare (om sådan finns) och skapa en ny.
* Det går för närvarande kryptera progressiv hämtning.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dynamisk kryptering
### <a name="open-restriction"></a>Öppna begränsning
Öppna begränsning innebär systemet ger nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning.

I följande exempel skapar en öppen auktoriseringsprincip och lägger till den innehållsnyckeln.

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
Det här avsnittet beskrivs hur du skapar en princip för auktorisering av innehållsnyckel och associera det med innehållsnyckeln. Auktoriseringsprincipen beskriver vilka auktorisering krav måste uppfyllas för att avgöra om användaren har behörighet att ta emot nyckeln (till exempel innehåller listan ”Verifieringsnyckeln” innehålla den nyckel som token som signerats med).

Om du vill konfigurera alternativet tokenbegränsningar som du behöver använda en XML för att beskriva behörighetskraven för token. Tokenbegränsningar konfigurations-XML måste uppfylla följande XML-schema.

#### <a id="schema"></a>Tokenbegränsningar schema
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

När du konfigurerar den **token** begränsad princip, måste du ange den primära ** verifiering nyckeln **, **utfärdaren** och **målgruppen** parametrar. Den ** primära Verifieringsnyckeln ** innehåller den nyckel som token som signerats med, **utfärdaren** är den säkra tokentjänst som utfärdar token. Den **målgruppen** (kallas ibland **omfång**) beskriver syftet med token eller token auktoriserar åtkomst till resursen. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen. 

När du använder **Media Services SDK för .NET**, du kan använda den **TokenRestrictionTemplate** klassen för att skapa begränsning-token.
I följande exempel skapas en auktoriseringsprincip med en token begränsning. I det här exemplet klienten skulle ha presentera en token som innehåller: signering nyckel (VerificationKey), en token utfärdare och nödvändiga anspråk.

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

#### <a id="test"></a>Test-token
Gör följande för att få en test-token baserat på de tokenbegränsningar som användes för nyckelauktoriseringsprincipen.

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


## <a name="playready-dynamic-encryption"></a>PlayReady-dynamisk kryptering
Media Services kan du konfigurera behörigheter och begränsningar som du vill använda för PlayReady DRM-körningsmiljön att tvinga när en användare försöker att spela upp skyddat innehåll. 

När du skyddar ditt innehåll med PlayReady, en av de saker som du måste ange i principen för auktorisering är en XML-sträng som definierar den [PlayReady-licensmall](media-services-playready-license-template-overview.md). I Media Services SDK för .NET, den **PlayReadyLicenseResponseTemplate** och **PlayReadyLicenseTemplate** klasser hjälper dig att definiera PlayReady License-mall.

[Det här avsnittet](media-services-protect-with-drm.md) visar hur du krypterar ditt innehåll med **PlayReady** och **Widevine**.

### <a name="open-restriction"></a>Öppna begränsning
Öppna begränsning innebär systemet ger nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning.

I följande exempel skapar en öppen auktoriseringsprincip och lägger till den innehållsnyckeln.

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
Om du vill konfigurera alternativet tokenbegränsningar som du behöver använda en XML för att beskriva behörighetskraven för token. Konfigurationen av tokenbegränsningar XML måste motsvara det XML-schemat visas i [detta](#schema) avsnitt.

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
        // It grants the user the ability to playback the content subject to the zero or more restrictions 
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


Att hämta en token för test baserat på de tokenbegränsningar som användes för auktorisering av innehållsnyckel princip Se [detta](#test) avsnitt. 

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

## <a name="next-step"></a>Nästa steg
Nu när du har konfigurerat innehållsnyckelns auktoriseringsprincip, gå till den [konfigurera tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md) avsnittet.

