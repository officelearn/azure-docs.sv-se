---
title: Definiera en teknisk profil för telefonfaktor i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för telefonfaktor i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332657"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för telefonfaktor i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för registrering och verifiering av telefonnummer. Denna tekniska profil:

- Tillhandahåller ett användargränssnitt för att interagera med användaren.
- Använder innehållsdefinition för att styra utseendet och känslan.
- Stöder både telefonsamtal och textmeddelanden för att validera telefonnumret.
- Stöder flera telefonnummer. Användaren kan välja ett av de telefonnummer som ska verifieras.  
- Om ett telefonnummer anges ber användargränssnittet för telefonfaktorn användaren att verifiera telefonnumret. Om det inte anges uppmanas användaren att registrera ett nytt telefonnummer.
- Returnerar ett anspråk som anger om användaren har angett ett nytt telefonnummer. Du kan använda det här anspråket för att avgöra om telefonnumret ska sparas i Azure AD-användarprofilen.  

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `Proprietary`anges till . **Hantattributet** måste innehålla det fullständigt kvalificerade namnet på protokollhanterarsammansättningen som används av Azure AD B2C för telefonfaktor:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas en teknisk profil för telefonfaktor för registrering och validering:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Ingående anspråk

Elementet InputClaims måste innehålla följande anspråk. Du kan också mappa namnet på ditt anspråk till namnet som definieras i den tekniska profilen för telefonfaktorn. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

I följande exempel visas användning av flera telefonnummer. Mer information finns i [exempelprincip](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

Elementet InputClaimsTransformations kan innehålla en samling InputClaimsTransformation-element som används för att ändra indataanspråken eller generera nya innan de visas på sidan telefonfaktor.

## <a name="output-claims"></a>Utgående fordringar

OutputClaims-elementet innehåller en lista över anspråk som returneras av den tekniska profilen för telefonfaktorn.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

Elementet OutputClaimsTransformations kan innehålla en samling OutputClaimsTransformation-element som används för att ändra utdataanspråken eller generera nya.

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet **CryptographicKeys** används inte.


## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| InnehållDefinitionReferenceId | Ja | Identifieraren för [innehållsdefinitionen som](contentdefinitions.md) är associerad med den här tekniska profilen. |
| ManuellPhoneNumberEntryTilllämt| Inga | Ange om en användare får ange ett telefonnummer manuellt eller inte. Möjliga `true` värden: `false` eller (standard).|

### <a name="ui-elements"></a>Element för användargränssnitt

Användargränssnittselement för användargränssnittet för telefonfaktorautentisering kan [lokaliseras](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>Nästa steg

- Kontrollera [sociala och lokala konton med MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) starter pack.

