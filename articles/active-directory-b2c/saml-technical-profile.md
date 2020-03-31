---
title: Definiera en SAML-teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en SAML-teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8c81d2bc499c3d9cae262ef62be2dac2d7280be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183847"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en SAML-teknisk profil i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för SAML 2.0-identitetsprovidern. I den här artikeln beskrivs detaljerna för en teknisk profil för att interagera med en anspråksprovider som stöder det här standardiserade protokollet. Med en SAML-teknisk profil kan du federera med en SAML-baserad identitetsleverantör, till exempel [ADFS](identity-provider-adfs2016-custom.md) och [Salesforce](identity-provider-salesforce-custom.md). Med den här federationen kan användarna logga in med sina befintliga sociala identiteter eller företagsidentiteter.

## <a name="metadata-exchange"></a>Utbyte av metadata

Metadata är information som används i SAML-protokollet för att exponera konfigurationen av en SAML-part, till exempel en tjänsteleverantör eller identitetsprovider. Metadata definierar platsen för tjänsterna, till exempel inloggning och ut signering, certifikat, inloggningsmetod med mera. Identitetsprovidern använder metadata för att veta hur man kommunicerar med Azure AD B2C. Metadata konfigureras i XML-format och kan signeras med en digital signatur så att den andra parten kan verifiera metadatas integritet. När Azure AD B2C matas med en SAML-identitetsprovider fungerar den som en tjänsteleverantör som initierar en SAML-begäran och väntar på ett SAML-svar. Och i vissa fall accepterar oönskad SAML-autentisering, som också kallas identitetsprovider initierad.

Metadata kan konfigureras på båda parter som "Statiska metadata" eller "Dynamiska metadata". I statiskt läge kopierar du hela metadata från en part och ställer in dem i den andra parten. I dynamiskt läge ställer du in URL:en till metadata medan den andra parten läser konfigurationen dynamiskt. Principerna är desamma, du anger metadata för den tekniska profilen för Azure AD B2C i din identitetsprovider och anger metadata för identitetsprovidern i Azure AD B2C.

Varje SAML-identitetsprovider har olika steg för att exponera och ange tjänsteprovidern, i det här fallet Azure AD B2C, och ange Azure AD B2C-metadata i identitetsprovidern. Titta på din identitetsleverantörs dokumentation för vägledning om hur du gör det.

I följande exempel visas en URL-adress till SAML-metadata för en teknisk profil i Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersätt följande värden:

- **ditt klientnamn** med ditt klientnamn, till exempel fabrikam.b2clogin.com.
- **din policy** med ditt policynamn. Använd principen där du konfigurerar den tekniska PROFILEN för SAML-providern eller en princip som ärver från den principen.
- **din-tekniska profil** med din SAML-identitetsleverantörs tekniska profilnamn.

## <a name="digital-signing-certificates-exchange"></a>Utbyte av digitala signeringscertifikat

Om du vill skapa ett förtroende mellan Azure AD B2C och din SAML-identitetsprovider måste du ange ett giltigt X509-certifikat med den privata nyckeln. Du överför certifikatet med den privata nyckeln (.pfx-filen) till Azure AD B2C-principnyckelarkivet. Azure AD B2C signerar konsekvent SAML-inloggningsbegäran med hjälp av certifikatet som du anger.

Certifikatet används på följande sätt:

- Azure AD B2C genererar och signerar en SAML-begäran med hjälp av den privata azure AD B2C-nyckeln för certifikatet. SAML-begäran skickas till identitetsprovidern, som validerar begäran med azure AD B2C-den offentliga nyckeln för certifikatet. Det offentliga Azure AD B2C-certifikatet är tillgängligt via metadata för teknisk profil. Du kan också ladda upp .cer-filen manuellt till din SAML-identitetsleverantör.
- Identitetsprovidern signerar data som skickas till Azure AD B2C med identitetsproviderns privata nyckel till certifikatet. Azure AD B2C validerar data med identitetsproviderns offentliga certifikat. Varje identitetsprovider har olika steg för installation, titta på din identitetsproviders dokumentation för vägledning om hur du gör det. I Azure AD B2C behöver din princip åtkomst till certifikatets offentliga nyckel med identitetsleverantörens metadata.

Ett självsignerat certifikat är acceptabelt för de flesta scenarier. För produktionsmiljöer rekommenderas att du använder ett X509-certifikat som utfärdas av en certifikatutfärdare. Som beskrivs senare i det här dokumentet kan du inaktivera SAML-signeringen på båda sidor för en icke-produktionsmiljö.

Följande diagram visar metadata och certifikatutbyte:

![metadata och certifikatutbyte](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digital kryptering

För att kryptera SAML-svarspåfråhållet använder identitetsprovidern alltid en offentlig nyckel för ett krypteringscertifikat i en teknisk profil i Azure AD B2C. När Azure AD B2C behöver dekryptera data används den privata delen av krypteringscertifikatet.

Så här krypterar du SAML-svarspåståendet:

1. Ladda upp ett giltigt X509-certifikat med den privata nyckeln (.pfx-filen) till Azure AD B2C-principnyckelarkivet.
2. Lägg till ett **CryptographicKey-element** med en identifierare `SamlAssertionDecryption` för den tekniska profilen **CryptographicKeys-samlingen.** Ange **StorageReferenceId** till namnet på den principnyckel som du skapade i steg 1.
3. Ange de tekniska profilmetadata **som villkrypteradeAssertions** till `true`.
4. Uppdatera identitetsprovidern med de nya tekniska profilmetadata för Azure AD B2C. Du bör se **KeyDescriptor** med **egenskapen use** set till innehåller den offentliga nyckeln för `encryption` ditt certifikat.

I följande exempel visas avsnittet Azure AD B2C-kryptering av teknisk profil i metadata:

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

**Attributet Name** för protokollelementet måste `SAML2`anges till .

## <a name="output-claims"></a>Utgående fordringar

**Elementet OutputClaims** innehåller en lista över anspråk som returneras `AttributeStatement` av SAML-identitetsprovidern under avsnittet . Du kan behöva mappa namnet på anspråket som definieras i din policy till det namn som definierats i identitetsleverantören. Du kan också inkludera anspråk som inte returneras av identitetsprovidern så länge du anger `DefaultValue` attributet.

Om du vill läsa SAML-påståendet **NamedId** in **Subject** som ett `assertionSubjectName`normaliserat anspråk anger du anspråket **PartnerClaimType** på . Kontrollera att **NameId** är det första värdet i kontroll-XML. När du definierar mer än ett påstående väljer Azure AD B2C ämnesvärdet från det senaste påståendet.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

I följande exempel visas de anspråk som returneras av Facebook-identitetsleverantören:

- **UtfärdarenUserId-anspråk** mappas till **påståendetSubjectName-anspråk.**
- Anspråket **first_name** mappas till **påståendet givenName.**
- Det **last_name** påståendet mappas till **efternamnet.**
- **DisplayName-anspråket** utan namnmappning.
- **E-postanspråket** utan namnmappning.

Den tekniska profilen returnerar också anspråk som inte returneras av identitetsleverantören:

- **Identitetprovider-anspråk** som innehåller namnet på identitetsleverantören.
- **AutentiseringKällanspråk** med standardvärdet **socialIdpAuthentication**.

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
| PartnerEntity | Ja | URL för metadata för SAML-identitetsprovidern. Kopiera identitetsproviderns metadata och lägg till dem i CDATA-elementet`<![CDATA[Your IDP metadata]]>` |
| VillSignedRequests | Inga | Anger om den tekniska profilen kräver att alla utgående autentiseringsbegäranden ska signeras. Möjliga `true` värden: `false`eller . Standardvärdet är `true`. När värdet är `true`inställt på måste **den SamlMessageSigning** kryptografiska nyckeln anges och alla utgående autentiseringsbegäranden signeras. Om värdet är `false`inställt på utelämnas **parametrarna SigAlg** och **Signature** (frågesträng eller postparameter) från begäran. Dessa metadata styr också **attributet AuthnRequestsSigned,** som är utdata i metadata för den tekniska profilen för Azure AD B2C som delas med identitetsprovidern. Azure AD B2C signerar inte begäran om värdet **för WantsSignedRequests** `false` i metadata för den tekniska profilen är inställt på `false` och identitetsproviderns metadata **WantAuthnRequestsSigned** är inställt på eller inte angivet. |
| XmlSignatureAlgorithm | Inga | Den metod som Azure AD B2C använder för att signera SAML-begäran. Dessa metadata styr värdet för **SigAlg-parametern** (frågesträng eller postparameter) i SAML-begäran. Möjliga `Sha256`värden: `Sha384` `Sha512`, `Sha1`, eller . Se till att du konfigurerar signaturalgoritmen på båda sidor med samma värde. Använd bara den algoritm som certifikatet stöder. |
| VillSignedAssertions | Inga | Anger om den tekniska profilen kräver att alla inkommande påståenden signeras. Möjliga `true` värden: `false`eller . Standardvärdet är `true`. Om värdet är `true`inställt på måste `saml:Assertion` alla kontrollavsnitt som skickas av identitetsprovidern till Azure AD B2C signeras. Om värdet är `false`inställt på bör identitetsprovidern inte signera påståendena, men även om det gör det validerar inte Azure AD B2C signaturen. Den här metadatabesumren styr också metadataflaggan **WantsAssertionsSigned**, som är utdata i metadata för den tekniska profilen för Azure AD B2C som delas med identitetsprovidern. Om du inaktiverar valideringen av påståenden kanske du också vill inaktivera valideringen av svarssignaturen (mer information finns i **ResponsesSigned**). |
| SvarSignerade | Inga | Möjliga `true` värden: `false`eller . Standardvärdet är `true`. Om värdet är `false`inställt på bör identitetsprovidern inte signera SAML-svaret, men även om det gör det validerar inte Azure AD B2C signaturen. Om värdet är `true`inställt på är SAML-svaret som skickas av identitetsprovidern till Azure AD B2C signerat och måste valideras. Om du inaktiverar VALIDERINGen av SAML-svar kanske du också vill inaktivera valideringen av kontrollsignaturen (mer information finns i **WantsSignedAssertions**). |
| VillEncryptedAssertions | Inga | Anger om den tekniska profilen kräver att alla inkommande påståenden krypteras. Möjliga `true` värden: `false`eller . Standardvärdet är `false`. Om värdet är `true`inställt på måste identifieringar som skickas av identitetsprovidern till Azure AD B2C signeras och den kryptografiska nyckeln **SamlAssertionDecryption** måste anges. Om värdet är `true`inställt på innehåller metadata för den tekniska profilen för Azure AD B2C krypteringsavsnittet. **encryption** Identitetsprovidern läser metadata och krypterar SAML-svarspåståendet med den offentliga nyckeln som finns i metadata för den tekniska profilen för Azure AD B2C. Om du aktiverar krypteringen av påståenden kan du också behöva inaktivera valideringen av svarssignaturen (mer information finns i **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Inga | Anger om en enskild inloggningssessionsprofil är aktiverad som initierades av en PROFIL FÖR SAML-identitetsprovider. Möjliga `true` värden: `false`eller . Standardvärdet är `false`. I flödet som initieras av identitetsprovidern autentiseras användaren externt och ett oönskat svar skickas till Azure AD B2C, som sedan använder token, kör orkestreringssteg och sedan skickar ett svar till det förlitande part-programmet. |
| NameIdPolicyFormat | Inga | Anger begränsningar för namnidentifieraren som ska användas för att representera det begärda ämnet. Om det utelämnas kan alla typer av identifierare som stöds av identitetsprovidern för det begärda ämnet användas. Till exempel `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** kan användas med **NameIdPolicyAllowCreate**. Titta i identitetsleverantörens dokumentation för vägledning om vilka namn-ID-principer som stöds. |
| NameIdPolicyÅterföra | Inga | När du använder **NameIdPolicyFormat**kan `AllowCreate` du också ange egenskapen **för NameIDPolicy**. Värdet för dessa `true` metadata `false` är eller för att ange om identitetsprovidern tillåts skapa ett nytt konto under inloggningsflödet. Titta på din identitetsleverantörs dokumentation för vägledning om hur du gör det. |
| AutentiseringFörfråganUtgåningar | Inga | Valfria protokollmeddelandetilläggselement som avtalas mellan Azure AD BC och identitetsprovidern. Tillägget visas i XML-format. Du lägger till XML-data `<![CDATA[Your IDP metadata]]>`i CDATA-elementet . Kontrollera identitetsleverantörens dokumentation för att se om tilläggselementet stöds. |
| IncludeAuthnContextClassReferences | Inga | Anger en eller flera URI-referenser som identifierar autentiseringskontextklasser. Om du till exempel vill att en användare ska kunna `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`logga in med endast användarnamn och lösenord anger du värdet till . Om du vill tillåta inloggning via användarnamn och lösenord via en `PasswordProtectedTransport`skyddad session (SSL/TLS) anger du . Titta i identitetsleverantörens dokumentation för vägledning om **AuthnContextClassRef-URI:er** som stöds. Ange flera IKT:er som en kommaavgränsad lista. |
| IncludeKeyInfo | Inga | Anger om SAML-autentiseringsbegäran innehåller certifikatets offentliga nyckel när `HTTP-POST`bindningen är inställd på . Möjliga `true` värden: `false`eller . |
| InkluderaClaimResolvingInClaimsHandling  | Inga | För indata- och utdataanspråk anger om [anspråksresolution](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` `false`  , eller (standard). Om du vill använda en anspråksmatchningsmatchare `true`i den tekniska profilen ställer du in den på . |

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet **CryptographicKeys** innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- | ----------- |
| SamlMessageSignera |Ja | X509-certifikatet (RSA-nyckeluppsättning) som ska användas för att signera SAML-meddelanden. Azure AD B2C använder den här nyckeln för att signera begäranden och skicka dem till identitetsprovidern. |
| SamlAssertionDecryption |Ja | X509-certifikatet (RSA-nyckeluppsättning) som ska användas för att dekryptera SAML-meddelanden. Detta certifikat bör tillhandahållas av identitetsleverantören. Azure AD B2C använder det här certifikatet för att dekryptera data som skickas av identitetsprovidern. |
| MetadataSignera |Inga | X509-certifikatet (RSA-nyckeluppsättning) som ska användas för att signera SAML-metadata. Azure AD B2C använder den här nyckeln för att signera metadata.  |

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar om hur du arbetar med SAML-identitetsleverantörer i Azure AD B2C:

- [Lägga till ADFS som SAML-identitetsprovider med hjälp av anpassade principer](identity-provider-adfs2016-custom.md)
- [Logga in med Salesforce-konton via SAML](identity-provider-salesforce-custom.md)
