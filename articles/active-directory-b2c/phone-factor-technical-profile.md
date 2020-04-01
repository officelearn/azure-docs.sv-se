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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437451"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för telefonfaktor i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för registrering och verifiering av telefonnummer. Denna tekniska profil:

- Tillhandahåller ett användargränssnitt för att interagera med användaren för att verifiera eller registrera ett telefonnummer.
- Stöder telefonsamtal och textmeddelanden för att validera telefonnumret.
- Stöder flera telefonnummer. Användaren kan välja ett av de telefonnummer som ska verifieras.  
- Returnerar ett anspråk som anger om användaren har angett ett nytt telefonnummer. Du kan använda det här anspråket för att avgöra om telefonnumret ska sparas i Azure AD B2C-användarprofilen.  
- Använder en [innehållsdefinition](contentdefinitions.md) för att styra utseendet och känslan.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `Proprietary`anges till . **Hantattributet** måste innehålla det fullständigt kvalificerade namnet på protokollhanterarsammansättningen som används av Azure AD B2C för telefonfaktor:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas en teknisk profil för telefonfaktor för registrering och validering:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Omvandling av indataanspråk

Elementet InputClaimsTransformations kan innehålla en samling indataanspråksomvandlingar som används för att ändra indataanspråken eller generera nya. Följande omvandling av indataanspråk genererar ett `UserId` anspråk som används senare i insamlingen av indataanspråk.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Ingående anspråk

Elementet InputClaims måste innehålla följande anspråk. Du kan också mappa namnet på ditt anspråk till namnet som definieras i den tekniska profilen för telefonfaktorn. 

|  Datatyp| Krävs | Beskrivning |
| --------- | -------- | ----------- | 
| sträng| Ja | En unik identifierare för användaren. Anspråksnamnet eller PartnerClaimType måste `UserId`anges till . Detta påstående bör inte innehålla personlig identifierbar information.|
| sträng| Ja | Lista över anspråkstyper. Varje anspråk innehåller ett telefonnummer. Om något av indataanspråken inte innehåller något telefonnummer uppmanas användaren att registrera sig och verifiera ett nytt telefonnummer. Det validerade telefonnumret returneras som ett utdataanspråk. Om ett av indataanspråken innehåller ett telefonnummer uppmanas användaren att verifiera det. Om flera inmatningsanspråk innehåller ett telefonnummer uppmanas användaren att välja och verifiera ett av telefonnumren. |

I följande exempel visas användning av flera telefonnummer. Mer information finns i [exempelprincip](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Utgående fordringar

OutputClaims-elementet innehåller en lista över anspråk som returneras av den tekniska profilen för telefonfaktorn.

|  Datatyp| Krävs | Beskrivning |
|  -------- | ----------- |----------- |
| boolean | Ja | Anger om det nya telefonnumret har angetts av användaren. Anspråksnamnet eller PartnerClaimType måste ställas in på`newPhoneNumberEntered`|
| sträng| Ja | Det verifierade telefonnumret. Anspråksnamnet eller PartnerClaimType måste `Verified.OfficePhone`anges till .|

Elementet OutputClaimsTransformations kan innehålla en samling OutputClaimsTransformation-element som används för att ändra utdataanspråken eller generera nya.

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet **CryptographicKeys** används inte.


## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| InnehållDefinitionReferenceId | Ja | Identifieraren för [innehållsdefinitionen som](contentdefinitions.md) är associerad med den här tekniska profilen. |
| ManuellPhoneNumberEntryTilllämt| Inga | Ange om en användare får ange ett telefonnummer manuellt eller inte. Möjliga värden: `true` `false` , eller (standard).|
| setting.authenticationMode | Inga | Metoden för att validera telefonnumret. Möjliga `sms`värden: `phone`, `mixed` , eller (standard).|
| setting.autodial| Inga| Ange om den tekniska profilen ska ringa upp automatiskt eller skicka ett SMS automatiskt. Möjliga värden: `true` `false` , eller (standard). Automatisk uppringning kräver `setting.authenticationMode` `sms`att `phone`metadata ställs in på , eller . Insamlingen av indataanspråk måste ha ett enda telefonnummer. |

### <a name="ui-elements"></a>Element för användargränssnitt

Användargränssnittselement för användargränssnittet för telefonfaktorautentisering kan [lokaliseras](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>Nästa steg

- Kontrollera [sociala och lokala konton med MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) starter pack.
