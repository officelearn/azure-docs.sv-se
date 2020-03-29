---
title: Definiera en teknisk profil för en SAML-utfärdare i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för en SAML-utfärdare (Security Assertion Markup Language) i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967276"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för en SAML-tokenutfärdare i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) avger flera typer av säkerhetstoken när den bearbetar varje autentiseringsflöde. En teknisk profil för en SAML-tokenutfärdare avger en SAML-token som returneras tillbaka till det förlitande part-programmet (tjänsteleverantör). Vanligtvis är denna tekniska profil det sista orchestration-steget i användarresan.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `None`anges till . Ange elementet **OutputTokenFormat** till `SAML2`.

I följande exempel visas `Saml2AssertionIssuer`en teknisk profil för:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Indata, utdata och kvarstående anspråk

Elementen **InputClaims**, **OutputClaims**och **PersistClaims** är tomma eller frånvarande. **InutputClaimsTransformations** och **OutputClaimsTransformations** element är också frånvarande.

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| EmittentUri | Inga | Utfärdarnamnet som visas i SAML-svaret. Värdet ska vara samma namn som konfigurerats i det förlitande partprogrammet. |

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet CryptographicKeys innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| MetadataSignera | Ja | X509-certifikatet (RSA-nyckeluppsättning) som ska användas för att signera SAML-metadata. Azure AD B2C använder den här nyckeln för att signera metadata. |
| SamlMessageSignera| Ja| Ange det X509-certifikat (RSA-nyckeluppsättning) som ska användas för att signera SAML-meddelanden. Azure AD B2C använder den `<samlp:Response>` här nyckeln för att signera svaret skicka till den förlitande parten.|

## <a name="session-management"></a>Sessionshantering

Om du vill konfigurera Azure AD B2C SAML-sessioner `UseTechnicalProfileForSessionManagement` mellan ett förlitande part-program, attributet för elementet, referens till [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO-session.

## <a name="next-steps"></a>Nästa steg

Se följande artikel om du till exempel använder en teknisk profil för SAML-utfärdare:

- [Registrera ett SAML-program i Azure AD B2C](connect-with-saml-service-providers.md)












