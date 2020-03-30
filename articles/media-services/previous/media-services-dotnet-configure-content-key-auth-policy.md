---
title: Konfigurera en behörighetsprincip för innehållsnyckel med hjälp av Media Services .NET SDK | Microsoft-dokument
description: Lär dig hur du konfigurerar en auktoriseringsprincip för en innehållsnyckel med hjälp av Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 58d52cd194ca4391c61f2477189984273df1198a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251211"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurera en behörighetsprincip för innehållsnyckel

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Översikt
 Du kan använda Azure Media Services för att leverera strömmar av MPEG-DASH, Smooth Streaming och HTTP Live Streaming (HLS) som skyddas med Advanced Encryption Standard (AES) med hjälp av 128-bitars krypteringsnycklar eller [Drm (PlayReady digital rights management).](https://www.microsoft.com/playready/overview/) Med Media Services kan du även leverera DASH-strömmar krypterade med Widevine DRM. Både PlayReady och Widevine krypteras enligt den gemensamma krypteringsspecifikationen (ISO/IEC 23001 7 CENC).

Media Services tillhandahåller också en nyckel-/licensleveranstjänst från vilken klienter kan få AES-nycklar eller PlayReady/Widevine-licenser för att spela upp det krypterade innehållet.

Om du vill att Media Services ska kryptera en tillgång måste du associera en krypteringsnyckel (CommonEncryption eller EnvelopeEncryption) med tillgången. Mer information finns i [Skapa ContentKeys med .NET](media-services-dotnet-create-contentkey.md). Du måste också konfigurera auktoriseringsprinciper för nyckeln (enligt beskrivningen i den här artikeln).

När en stream begärs av en spelare använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med hjälp av AES- eller DRM-kryptering. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de auktoriseringsprinciper som du har angett för nyckeln.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Behörighetsprincipen för innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar. Alternativen är öppna eller tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formatet enkel webbtoken[(SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)och JSON Web Token[(JWT).](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)

Media Services tillhandahåller inte STS. Du kan skapa en anpassad STS eller använda Azure Access Control Service för att utfärda token. STS måste konfigureras för att skapa en token som signerats med den angivna nyckeln och utfärda anspråk som du angav i tokenbegränsningskonfigurationen (enligt beskrivningen i den här artikeln). Om token är giltig och anspråken i token matchar de som konfigurerats för innehållsnyckeln returnerar Media Services nyckelleveranstjänst krypteringsnyckeln till klienten.

Mer information finns i följande artiklar:

- [JWT-tokenautentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrera Azure Media Services OWIN MVC-baserad app med Azure Active Directory och begränsa leverans av innehållsnyckel baserat på JWT-anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Vissa överväganden gäller
* När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill börja strömma ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning vara i tillståndet "Köra". 
* Din tillgång måste innehålla en uppsättning adaptiva bitrate MP4s eller adaptiv bitrate Smooth Streaming-filer. Mer information finns i [Koda en tillgång](media-services-encode-asset.md).
* Ladda upp och koda dina tillgångar med alternativet AssetCreationOptions.StorageEncrypted.
* Om du planerar att ha flera innehållsnycklar som kräver samma principkonfiguration rekommenderar vi att du skapar en enda auktoriseringsprincip och återanvänder den med flera innehållsnycklar.
* Nyckelleveranstjänsten cachelagrar ContentKeyAuthorizationPolicy och dess relaterade objekt (principalternativ och begränsningar) i 15 minuter. Du kan skapa ContentKeyAuthorizationPolicy och ange att använda en tokenbegränsning, testa den och sedan uppdatera principen till den öppna begränsningen. Den här processen tar ungefär 15 minuter innan principen växlar till den öppna versionen av principen.
* Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort eventuella befintliga lokaliserare och skapa en ny.
* För närvarande kan du inte kryptera progressiva nedladdningar.
* En slutpunkt för direktuppspelning av Media Services anger värdet för CORS-huvudet "Access-Control-Allow-Origin" i preflight-svar som jokertecknet '\*'. Det här värdet fungerar bra med de flesta spelare, inklusive Azure Media Player, Roku och JWPlayer och andra. Vissa spelare som använder dashjs fungerar dock inte eftersom XMLHttpRequest i sina dashjs inte tillåter jokertecknet "\*" som värdet för "Access-Control-Allow-Origin". Som en lösning på den här begränsningen i dashjs kan Media Services ange domänen i svarshuvudet för preflight. Om du behöver hjälp kan du öppna en supportbiljett via Azure-portalen.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dynamisk kryptering
### <a name="open-restriction"></a>Öppen begränsning
Öppen begränsning innebär att systemet levererar nyckeln till alla som gör en nyckelbegäran. Den här begränsningen kan vara användbar för testning.

I följande exempel skapas en öppen auktoriseringsprincip och den läggs till i innehållsnyckeln:
```csharp
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

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Token begränsning
I det här avsnittet beskrivs hur du skapar en behörighetsprincip för innehållsnyckel och associerar den med innehållsnyckeln. Auktoriseringsprincipen beskriver vilka auktoriseringskrav som måste uppfyllas för att avgöra om användaren har behörighet att ta emot nyckeln. Innehåller till exempel verifieringsnyckellistan nyckeln som token signerades med?

Om du vill konfigurera alternativet tokenbegränsning måste du använda en XML för att beskriva tokens auktoriseringskrav. XML-koden för tokenbegränsningsbegränsningen måste överensstämma med följande XML-schema:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
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
```
När du konfigurerar principen tokenbegränsad måste du ange den primära verifieringsnyckeln, utfärdaren och målgruppsparametrarna. Den primära verifieringsnyckeln innehåller nyckeln som token signerades med. Utfärdaren är STS som utfärdar token. Målgruppen (kallas ibland scope) beskriver avsikten med token eller den resurs som token auktoriserar åtkomst till. Medietjänstnyckelleveranstjänsten verifierar att dessa värden i token matchar värdena i mallen.

När du använder Media Services SDK för .NET kan du använda klassen TokenRestrictionTemplate för att generera begränsningstoken.
I följande exempel skapas en auktoriseringsprincip med en tokenbegränsning. I det här exemplet måste klienten presentera en token som innehåller en signeringsnyckel (VerificationKey), en tokenutfärdare och obligatoriska anspråk.
```csharp
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

        // Add ContentKeyAuthorizationPolicy to ContentKey
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
```
#### <a name="test-token"></a>Testtoken
Så här hämtar du en testtoken baserat på tokenbegränsningen som användes för nyckelauktoriseringsprincipen:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>PlayReady dynamisk kryptering
Du kan använda Media Services för att konfigurera de rättigheter och begränsningar som du vill att PlayReady DRM-körningen ska tillämpas när en användare försöker spela upp skyddat innehåll. 

När du skyddar ditt innehåll med PlayReady är en av de saker du behöver ange i auktoriseringsprincipen en XML-sträng som definierar [licensmallen PlayReady](media-services-playready-license-template-overview.md). I klasserna Media Services SDK för .NET hjälper klasserna PlayReadyLicenseResponseTemplate och PlayReadyLicenseTemplate dig att definiera licensmallen PlayReady.

Mer information om hur du krypterar ditt innehåll med PlayReady och Widevine finns i [Använda PlayReady och/eller Widevine dynamisk gemensam kryptering](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Öppen begränsning
Öppen begränsning innebär att systemet levererar nyckeln till alla som gör en nyckelbegäran. Den här begränsningen kan vara användbar för testning.

I följande exempel skapas en öppen auktoriseringsprincip och den läggs till i innehållsnyckeln:

```csharp
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
```

### <a name="token-restriction"></a>Token begränsning
Om du vill konfigurera alternativet tokenbegränsning måste du använda en XML för att beskriva tokens auktoriseringskrav. XML-koden för tokenbegränsningskonfigurationen måste överensstämma med XML-schemat som visas i avsnittet "Tokenbegränsningsschema".

```csharp
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

        // Add ContentKeyAuthorizationPolicy to ContentKey
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
```

Om du vill hämta en testtoken baserat på tokenbegränsningen som användes för nyckelauktoriseringsprincipen läser du avsnittet "[Test token](#test-token)" . 

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>Typer som används när du definierar ContentKeyAuthorizationPolicy
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a name="tokentype"></a><a id="TokenType"></a>TokenType (TokenType)

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat innehållsnyckelns auktoriseringsprincip läser [du Konfigurera en princip för tillgångsleverans](media-services-dotnet-configure-asset-delivery-policy.md).

