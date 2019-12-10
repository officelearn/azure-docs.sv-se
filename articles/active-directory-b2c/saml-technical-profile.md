---
title: Definiera en teknisk profil för SAML i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för SAML i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74666b1dc2ba4fac25aff0a56a52d048d746d465
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950943"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för SAML i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) har stöd för identitets leverantören för SAML 2,0. I den här artikeln beskrivs de olika kraven för en teknisk profil för att interagera med en anspråks leverantör som stöder detta standardiserade protokoll. Med en teknisk SAML-profil kan du federera med en SAML-baserad identitets leverantör, till exempel [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) och [Salesforce](active-directory-b2c-setup-sf-app-custom.md). Med den här federationen kan användarna logga in med sina befintliga sociala identiteter eller företags identiteter.

## <a name="metadata-exchange"></a>Metadatautväxling

Metadata är information som används i SAML-protokollet för att exponera konfigurationen av en SAML-part, till exempel en tjänst leverantör eller en identitets leverantör. Metadata definierar platsen för tjänsterna, till exempel inloggning och utloggning, certifikat, inloggnings metod med mera. Identitets leverantören använder metadata för att veta hur du kommunicerar med Azure AD B2C. Metadata har kon figurer ATS i XML-format och kan vara signerade med en digital signatur så att den andra parten kan verifiera metadatans integritet. När Azure AD B2C federerar med en SAML-identitetsprovider fungerar det som en tjänst leverantör som initierar en SAML-begäran och väntar på ett SAML-svar. Och i vissa fall godkänner ofiltrerad SAML-autentisering, som även kallas identitets leverantör initierad.

Metadata kan konfigureras i båda parter som "statiska metadata" eller "dynamiska metadata". I statiskt läge kopierar du hela metadata från en part och anger den i den andra parten. I dynamiskt läge ställer du in URL: en till metadata medan den andra parten läser konfigurationen dynamiskt. Principerna är desamma, du ställer in metadata för den Azure AD B2C tekniska profilen i identitets leverantören och anger metadata för identitets leverantören i Azure AD B2C.

Varje SAML-identitetsprovider har olika steg för att exponera och ange tjänst leverantören, i det här fallet Azure AD B2C och ange Azure AD B2C metadata i identitets leverantören. Mer information om hur du gör detta finns i identitets leverantörens dokumentation.

I följande exempel visas en URL-adress till SAML-metadata för en Azure AD B2C teknisk profil:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersätt följande värden:

- **ditt klient namn** med ditt klient namn, till exempel fabrikam.b2clogin.com.
- **din princip** med ditt princip namn. Använd principen där du konfigurerar den tekniska profilen för SAML-providern eller en princip som ärver från principen.
- **din tekniska profil** med din SAML Identity providers tekniska profil namn.

## <a name="digital-signing-certificates-exchange"></a>Utbyte av digitala signerings certifikat

Om du vill skapa ett förtroende mellan Azure AD B2C och SAML Identity Provider måste du ange ett giltigt X509-certifikat med den privata nyckeln. Du laddar upp certifikatet med den privata nyckeln (. pfx-filen) till lagrings platsen för Azure AD B2Cs princip. Azure AD B2C signera SAML-inloggningsbegäran digitalt med det certifikat som du anger.

Certifikatet används på följande sätt:

- Azure AD B2C skapar och signerar en SAML-begäran med hjälp av den Azure AD B2C privata nyckeln för certifikatet. SAML-begäran skickas till identitets leverantören som verifierar begäran med hjälp av Azure AD B2C offentliga nyckeln för certifikatet. Det Azure AD B2C offentliga certifikatet är tillgängligt via metadata för teknisk profil. Alternativt kan du ladda upp CER-filen manuellt till SAML-identitetsprovider.
- Identitets leverantören signerar de data som skickas till Azure AD B2C med hjälp av identitets leverantörens privata nyckel för certifikatet. Azure AD B2C validerar data med hjälp av identitets leverantörens offentliga certifikat. Varje identitets leverantör har olika steg för installations programmet, titta på identitets leverantörens dokumentation för att få hjälp med att göra det. I Azure AD B2C behöver principen åtkomst till certifikatets offentliga nyckel med hjälp av identitets leverantörens metadata.

Ett självsignerat certifikat är acceptabelt i de flesta fall. För produktions miljöer rekommenderar vi att du använder ett X509-certifikat som utfärdats av en certifikat utfärdare. Som beskrivs senare i det här dokumentet för en miljö som inte är en produktions miljö kan du inaktivera SAML-signering på båda sidorna.

Följande diagram visar metadata och certifikat utbyte:

![metadata och certifikat utbyte](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digital kryptering

För att kryptera SAML-svarets kontroll, använder identitets leverantören alltid en offentlig nyckel för ett krypterings certifikat i en Azure AD B2C teknisk profil. När Azure AD B2C behöver dekryptera data, använder den den privata delen av krypterings certifikatet.

Kryptera SAML-svars kontroll:

1. Ladda upp ett giltigt X509-certifikat med den privata nyckeln (. pfx-fil) till lagrings platsen för Azure AD B2Cs princip.
2. Lägg till ett **CryptographicKey** -element med en identifierare för `SamlAssertionDecryption` till den tekniska profilen **CryptographicKeys** -samlingen. Ange **StorageReferenceId** till namnet på den princip nyckel som du skapade i steg 1.
3. Ange **WantsEncryptedAssertions** för den tekniska profilen till `true`.
4. Uppdatera identitets leverantören med de nya metadata för Azure AD B2C teknisk profil. Du bör se nyckel **beskrivningen** med egenskapen **use** inställd på `encryption` som innehåller certifikatets offentliga nyckel.

I följande exempel visas avsnittet Azure AD B2C teknisk profil kryptering i metadata:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protokoll

Namnattributet **för** protokoll elementet måste anges till `SAML2`.

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returnerats av SAML Identity Provider i avsnittet `AttributeStatement`. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i identitets leverantören. Du kan också ta med anspråk som inte returneras av identitets leverantören så länge som du ställer in `DefaultValue`-attributet.

Om du vill läsa SAML Assertion **NamedId** i **ämnet** som ett normaliserat anspråk ställer du in anspråks **PartnerClaimType** till `assertionSubjectName`. Kontrol lera att **NameId** är det första värdet i Assertion XML. När du definierar fler än en kontroll, Azure AD B2C väljer subject-värdet från den senaste försäkran.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

I följande exempel visas de anspråk som returneras av Facebook Identity Provider:

- **IssuerUserId** -anspråket är mappat till **assertionSubjectName** -anspråket.
- **First_name** -anspråk är mappat till **givenName** -anspråket.
- **Last_Name** -anspråk är mappat till anspråket efter **namn** .
- **DisplayName** -anspråket utan namn mappning.
- **E-** postanspråk utan namn mappning.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

- **IdentityProvider** -anspråket som innehåller namnet på identitets leverantören.
- **AuthenticationSource** -anspråket med standardvärdet **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| PartnerEntity | Ja | URL för metadata för SAML Identity Provider. Kopiera metadata för identitets leverantören och Lägg till dem i CDATA-elementet `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nej | Anger om den tekniska profilen kräver att alla utgående autentiseringsbegäranden signeras. Möjliga värden: `true` eller `false`. Standardvärdet är `true`. När värdet är inställt på `true`måste den kryptografiska nyckeln **SamlMessageSigning** anges och alla utgående autentiseringsbegäranden signeras. Om värdet är inställt på `false`utelämnas parametrarna **SigAlg** och **Signature** (frågesträng eller post parameter) från begäran. Dessa metadata styr också attributet metadata **AuthnRequestsSigned** , som är utdata i metadata för den Azure AD B2C tekniska profil som delas med identitets leverantören. Azure AD B2C signerar inte begäran om värdet för **WantsSignedRequests** i metadata för teknisk profil är inställt på `false` och **WantAuthnRequestsSigned** för identitetsprovider har angetts till `false` eller inte har angetts. |
| XmlSignatureAlgorithm | Nej | Metoden som Azure AD B2C använder för att signera SAML-begäran. Dessa metadata styr värdet för parametern **SigAlg** (frågesträng eller post parameter) i SAML-begäran. Möjliga värden: `Sha256`, `Sha384`, `Sha512`eller `Sha1`. Se till att du konfigurerar signeringsalgoritmen på båda sidor med samma värde. Använd bara den algoritm som ditt certifikat stöder. |
| WantsSignedAssertions | Nej | Anger om den tekniska profilen kräver att all inkommande kontroll ska signeras. Möjliga värden: `true` eller `false`. Standardvärdet är `true`. Om värdet är inställt på `true`måste alla intygs avsnitt `saml:Assertion` skickas av identitets leverantören till Azure AD B2C signeras. Om värdet är inställt på `false`, ska identitets leverantören inte signera intygen, men även om det sker Azure AD B2C verifierar inte signaturen. Dessa metadata styr också metadata-flaggan **WantsAssertionsSigned**, som är utdata i metadata för den Azure AD B2C tekniska profil som delas med identitets leverantören. Om du inaktiverar validerings verifieringen kanske du också vill inaktivera verifiering av svars signatur (mer information finns i **ResponsesSigned**). |
| ResponsesSigned | Nej | Möjliga värden: `true` eller `false`. Standardvärdet är `true`. Om värdet är inställt på `false`, ska identitets leverantören inte signera SAML-svaret, men även om det gör det verifierar Azure AD B2C inte signaturen. Om värdet är inställt på `true`signeras det SAML-svar som skickas av identitets leverantören till Azure AD B2C och måste verifieras. Om du inaktiverar verifieringen av SAML-svar kan du också vilja inaktivera verifiering av kontrollerad signatur (mer information finns i **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nej | Anger om den tekniska profilen kräver att all inkommande kontroll ska krypteras. Möjliga värden: `true` eller `false`. Standardvärdet är `false`. Om värdet är inställt på `true`måste intyg som skickas av identitets leverantören till Azure AD B2C signeras och **SamlAssertionDecryption** kryptografiska nyckel måste anges. Om värdet är inställt på `true`, innehåller metadata för den Azure AD B2C tekniska profilen **krypterings** avsnittet. Identitets leverantören läser metadata och krypterar SAML-svars kontrollen med den offentliga nyckel som anges i metadata för den Azure AD B2C tekniska profilen. Om du aktiverar intygs kryptering kan du behöva inaktivera verifiering av svars signatur (mer information finns i **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Nej | Anger om en enkel inloggnings-sessionsnyckel har Aktiver ATS som initierades av en SAML Identity Provider-profil. Möjliga värden: `true` eller `false`. Standardvärdet är `false`. I flödet som initieras av identitets leverantören, autentiseras användaren externt och ett oönskat svar skickas till Azure AD B2C, som sedan använder token, kör Orchestration-steg och skickar sedan ett svar till förlitande part-programmet. |
| NameIdPolicyFormat | Nej | Anger begränsningar i namn identifieraren som ska användas för att representera det begärda ämnet. Om detta utelämnas kan alla typer av identifierare som stöds av identitets leverantören för det begärda ämnet användas. Till exempel `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** kan användas med **NameIdPolicyAllowCreate**. I dokumentationen för identitets leverantören hittar du information om vilka namn-ID-principer som stöds. |
| NameIdPolicyAllowCreate | Nej | När du använder **NameIdPolicyFormat**kan du också ange egenskapen `AllowCreate` för **NameIDPolicy**. Värdet för dessa metadata är `true` eller `false` för att indikera om identitetsprovider tillåts att skapa ett nytt konto under inloggnings flödet. Mer information om hur du gör detta finns i identitets leverantörens dokumentation. |
| AuthenticationRequestExtensions | Nej | Valfria protokoll meddelande tilläggs element som överenskommits mellan Azure AD BC och identitets leverantören. Tillägget visas i XML-format. Du lägger till XML-data i CDATA-elementet `<![CDATA[Your IDP metadata]]>`. Se efter i identitets leverantörens dokumentation om tillägg elementet stöds. |
| IncludeAuthnContextClassReferences | Nej | Anger en eller flera URI-referenser som identifierar autentiserings kontext klasser. Om du till exempel vill tillåta att en användare loggar in med användar namn och lösen ord, ställer du in värdet på `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Ange `PasswordProtectedTransport`för att tillåta inloggning via användar namn och lösen ord över en skyddad session (SSL/TLS). Läs dokumentationen om identitets leverantören för information om de **AuthnContextClassRef** -URI: er som stöds. Ange flera URI: er som en kommaavgränsad lista. |
| IncludeKeyInfo | Nej | Anger om SAML-autentiseringsbegäran innehåller certifikatets offentliga nyckel när bindningen har angetts till `HTTP-POST`. Möjliga värden: `true` eller `false`. |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Ja | X509-certifikatet (RSA-nyckel uppsättning) som används för att signera SAML-meddelanden. Azure AD B2C använder den här nyckeln för att signera förfrågningarna och skicka dem till identitets leverantören. |
| SamlAssertionDecryption |Ja | X509-certifikatet (RSA-nyckel uppsättning) som används för att dekryptera SAML-meddelanden. Det här certifikatet bör tillhandahållas av identitets leverantören. Azure AD B2C använder det här certifikatet för att dekryptera data som skickas av identitets leverantören. |
| MetadataSigning |Nej | X509-certifikatet (RSA-nyckel uppsättning) som används för att signera SAML-metadata. Azure AD B2C använder den här nyckeln för att signera metadata.  |

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns exempel på hur du arbetar med SAML Identity providers i Azure AD B2C:

- [Lägg till ADFS som en SAML Identity Provider med anpassade principer](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Logga in med Salesforce-konton via SAML](active-directory-b2c-setup-sf-app-custom.md)
