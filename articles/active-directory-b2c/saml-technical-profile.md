---
title: Definiera en tekniska SAML-profilen i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en tekniska SAML-profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c99099c9b0fdf485bcf1db1d00b23e1e119ec557
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614165"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en tekniska SAML-profilen i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C har stöd för SAML 2.0-identitetsprovider. Den här artikeln beskriver specifika för den tekniska profilen för att interagera med en anspråksprovider som stöder detta standardiserade protokoll. Med tekniska SAML-profilen du federera med en SAML-baserad identitetsprovider som AD FS och Salesforce, så att dina användare kan logga in med sina befintliga sociala eller företagsidentiteter.

## <a name="metadata-exchange"></a>Metadata för exchange

Metadata är information som används i SAML-protokoll för att exponera konfigurationen av en SAML-part, till exempel en tjänstleverantör eller identitetsprovider. Metadata definierar platsen för tjänster, till exempel inloggning och utloggning, certifikat och inloggningsmetod. Identitetsprovidern använder metadata kunna kommunicera med Azure AD B2C. Metadata har konfigurerats i XML-format och kan signeras digitalt så att den andra parten kan validera integriteten för metadata. När Azure AD B2C federerar med en SAML-identitetsprovider, fungerar den som en tjänsteleverantör initiera en SAML-begäran och väntar på en SAML-svar. Och i vissa fall kan excepts oombedda SAML-autentisering, som även kallas identitetsprovider initieras. 

Metadata kan konfigureras i båda parter som ”statiska Metadata” eller ”dynamisk Metadata”. I det statiska läget kopiera hela metadata från en part och ange den i den andra parten. I dynamiskt läge anger du URL: en till metadata medan den andra parten läser konfigurationen dynamiskt. Principerna är desamma kan du ställa in metadata för den tekniska profilen för Azure AD B2C i din identitetsprovider och Ställ in metadata för identitetsprovidern i Azure AD B2C.

Varje SAML-identitetsprovider har olika steg för att exponera och ange tjänstleverantör i det här fallet Azure AD B2C och konfigurera Azure AD B2C-metadata i identitetsprovidern. Titta på din identitetsprovider dokumentationen för vägledning i hur du gör.

I följande exempel visas en URL-adress till SAML-metadata för en Azure AD B2C-tekniska profilen:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersätt följande värden:

- **din klient** med klientnamnet på din, till exempel din tenant.onmicrosoft.com
- **din princip** med namnet på din. Använd principen konfigurerar du den tekniska profilen för SAML-provider eller en policy som ärver från principen.
- **din tekniska profil** med namn på tekniska profilen din SAML identitetsprovider

## <a name="digital-signing-certificates-exchange"></a>Digital signering certifikat exchange

Om du vill skapa ett förtroende mellan Azure AD B2C och SAML-identitetsprovider, måste du ange en giltig X509 certifikat med privat nyckel. Du kan ladda upp certifikatet med den privata nyckeln (PFX-fil) till Azure AD B2C-principarkivet nyckel. Azure AD B2C signerar SAML-inloggningen-begäran med det certifikat som du anger. 

Certifikatet används på följande sätt:

- Azure AD B2C genererar och loggar en SAML-begäran med Azure AD B2C privata nyckeln för certifikatet. SAML-begäran skickas till identitetsprovidern, och som validerar begäran med hjälp av Azure AD B2C offentliga nyckeln för certifikatet. Det offentliga certifikatet i Azure AD B2C är tillgängligt via tekniska profilens metadata. Du kan också manuellt överföra .cer-filen till SAML-identitetsprovider.
- Identitetsprovidern loggar data som skickas till Azure AD B2C med identitetsleverantörens privata nyckeln för certifikatet. Azure AD B2C verifierar data med identitetsleverantörens offentligt certifikat. Varje identitetsprovidern har olika steg för installation, titta på dina Identitetsproviders dokumentationen för vägledning i hur du gör. Din princip behöver åtkomst till den offentliga nyckeln för certifikatet med hjälp av identitetsleverantörens metadata i Azure AD B2C.

Ett självsignerat certifikat är godkänd för de flesta scenarier. För produktionsmiljöer rekommenderar vi att du använder en X509 certifikat som utfärdas av en certifikatutfärdare. Dessutom som beskrivs senare i det här dokumentet, kan en icke-produktionsmiljö du inaktivera den SAML-signeringscertifikat på båda sidorna.

Följande diagram visar exchange metadata och certifikat:

![exchange för metadata och certifikat](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)


## <a name="digital-encryption"></a>Digitala kryptering

För att kryptera SAML-svar-kontroll, använder identitetsprovidern alltid en offentliga nyckeln för ett krypteringscertifikat i en Azure AD B2C-tekniska profilen. När Azure AD B2C måste att dekryptera data, används den privata delen av krypteringscertifikatet.

Att kryptera SAML-svar-kontroll:

1. Ladda upp en giltig X509 certifikat med privat nyckel (.pfx-fil) till Azure AD B2C-principarkivet nyckel.
2. Lägg till en **CryptographicKey** element med en identifierare för `SamlAssertionDecryption` till den tekniska profilen **CryptographicKeys** samling. Ange den **StorageReferenceId** till namnet på principnyckeln som du skapade i steg 1.
3. Ange tekniska profilens metadata **WantsEncryptedAssertions** till `true`.
4. Uppdatera identitetsprovidern med den nya Azure AD B2C tekniska profilens metadata. Du bör se den **KeyDescriptor** med den **använder** egenskapen `encryption` som innehåller den offentliga nyckeln för certifikatet.

I följande exempel visas avsnittet Azure AD B2C tekniska profilen kryptering i metadata:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="identity-provider-initiated-flow"></a>Identitet providern initierade flöde

I en enda inloggning för session (oombedda) initieras av IDP: N, skickas ett oombedda SAML-svar till tjänstleverantören, i det här fallet en Azure AD B2C-tekniska profilen. I det här flödet användaren går inte via det webbaserade programmet först, men dirigeras till identitetsleverantören. När begäran skickas har en autentiseringssida angetts för användaren av identitetsleverantören. Användaren Slutför inloggningen och sedan begäran omdirigeras till Azure AD B2C med ett SAML-svar som innehåller intyg. Azure AD B2C läser intyg och utfärdar en ny SAML-token och sedan omdirigeras användaren till förlitande part-programmet. Om omdirigeringarna utförs av den **AssertionConsumerService** elementets **plats** egenskapen.


![SAML IDP-initierad](media/saml-technical-profile/technical-profile-idp-saml-idp-initiated.png) 

Beakta följande principkrav när du skapar en identitetsprovider initierade flödet:

- Det första steget i orchestration måste vara ett enda anspråk exchange som pekar på en tekniska SAML-profilen.
- Den tekniska profilen måste ha en metadata för objektet med namnet **IdpInitiatedProfileEnabled** inställd `true`.
- Principen för förlitande part måste vara en SAML-förlitande part.
- Principen för förlitande part måste ha en metadata för objektet med namnet **IdpInitiatedProfileEnabled** inställd `true`.
- Oombedda svaret måste skickas till den `/your-tenant/your-policy/samlp/sso/assertionconsumer` slutpunkt. Alla vidarebefordransstatus som ingår i svaret vidarebefordras till den förlitande parten. Ersätt följande värden: **din klient** med klientnamnet på din. **din princip** med namnet på din förlitande part.
    
## <a name="protocol"></a>Protokoll

Den **namn** attributet för protokoll-elementet måste anges till `SAML2`. 

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| PartnerEntity | Ja | URL för metadata för SAML-identitetsprovider. Kopiera identitet providerrns metadata och lägga till den i CDATA-element `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nej | Anger om den tekniska profilen måste innehålla utgående autentiseringsbegäranden signeras. Möjliga värden: `true` eller `false`. Standardvärdet är `true`. När värdet anges till `true`, **SamlMessageSigning** kryptografisk nyckel måste anges och alla utgående autentiseringsbegäranden signeras. Om värdet anges till `false`, **SigAlg** och **signatur** parametrar (frågesträng eller publicera parametern) har utelämnats från begäran. Dessa metadata styr även metadata **AuthnRequestsSigned** attribut, som är utdata i metadata för den tekniska profilen för Azure AD B2C som delas med identitetsprovidern. |
| XmlSignatureAlgorithm | Nej | Den metod som Azure AD B2C använder för att signera SAML-begäran. Dessa metadata styr värdet för den **SigAlg** parametern (frågesträng eller publicera parameter) i SAML-begäran. Möjliga värden: `Sha256`, `Sha384`, `Sha512`, eller `Sha1`. Kontrollera att du konfigurerar signaturalgoritmen på båda sidorna med samma värde. Använd bara den algoritm som har stöd för ditt certifikat. | 
| WantsSignedAssertions | Nej | Anger om den tekniska profilen kräver alla inkommande intyg signeras. Möjliga värden: `true` eller `false`. Standardvärdet är `true`. Om värdet anges till `true`, alla intyg avsnittet `saml:Assertion` skickas av identiteten provider för att Azure AD B2C måste signeras. Om värdet anges till `false`, identitetsprovidern bör inte registrera intyg, men även om den finns, Azure AD B2C kommer inte att verifiera signaturen. Dessa metadata styr också metadataflaggan **WantsAssertionsSigned**, vilket är utdata i metadata för den tekniska profilen för Azure AD B2C som delas med identitetsprovidern. Om du inaktiverar intyg verifieringen, du kan också inaktivera signaturverifiering svar (Mer information finns i **ResponsesSigned**). |
| ResponsesSigned | Nej | Möjliga värden: `true` eller `false`. Standardvärdet är `true`. Om värdet anges till `false`, identitetsprovidern bör inte signera SAML-svar, men även om den finns, Azure AD B2C kommer inte att verifiera signaturen. Om värdet anges till `true`, SAML-svar som skickas av identitetsleverantören till Azure AD B2C har registrerat och måste verifieras. Om du inaktiverar valideringen av SAML-svar, du kan också inaktivera signaturverifiering försäkran (Mer information finns i **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nej | Anger om den tekniska profilen kräver alla inkommande intyg krypteras. Möjliga värden: `true` eller `false`. Standardvärdet är `false`. Om värdet anges till `true`, intyg som skickas av identitetsleverantören till Azure AD B2C måste signeras och **SamlAssertionDecryption** kryptografisk nyckel måste anges. Om värdet anges till `true`, metadata för den Azure AD B2C tekniska profilen innehåller den **kryptering** avsnittet. Identitetsprovidern läser metadata och krypterar assertion för SAML-svar med den offentliga nyckeln som har angetts i metadata för den tekniska profilen för Azure AD B2C. Om du aktiverar intyg kryptering kan du också behöva inaktivera signaturverifiering svar (Mer information finns i **ResponsesSigned**). | 
| IdpInitiatedProfileEnabled | Nej |Anger om en profil för enkel inloggning för sessionen har aktiverats som initierades av en profil för SAML identity-providern. Möjliga värden: `true` eller `false`. Standardvärdet är `false`. Användaren autentiseras externt i flödet som initieras av identitetsprovidern, och ett oombedda svar skickas till Azure AD B2C, som sedan förbrukar token, kör orchestration-steg och skickar sedan ett svar till den förlitande partsprogram. |

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Den **CryptographicKeys** elementet innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Ja | X509 certifikat (RSA nyckeluppsättning) som ska användas för att signera SAML-meddelanden. Azure AD B2C använder den här nyckeln för att logga begäranden och skicka dem till identitetsleverantören. |
| SamlAssertionDecryption |Ja | X509 certifikat (RSA nyckeluppsättning) som ska användas för att dekryptera SAML-meddelanden. Det här certifikatet ska tillhandahållas av identitetsleverantören. Azure AD B2C använder det här certifikatet för att dekryptera data som skickas av identitetsleverantören. |
| MetadataSigning |Nej | X509 certifikat (RSA nyckeluppsättning) som ska användas för att signera SAML-metadata. Azure AD B2C använder den här nyckeln för att registrera metadata.  |

## <a name="examples"></a>Exempel

- [Lägg till AD FS som en SAML-identitetsprovider anpassade principer](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Logga in med Salesforce-konton via SAML](active-directory-b2c-setup-sf-app-custom.md)













