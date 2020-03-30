---
title: Verifiera anspråk med visningskontroller
titleSuffix: Azure AD B2C
description: Lär dig hur du använder Azure AD B2C-visningskontroller för att verifiera anspråken i användarens färder som tillhandahålls av dina anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188791"
---
# <a name="verification-display-control"></a>Kontroll av verifieringsvisning

Använd en kontroll [för](display-controls.md) verifieringsvisning för att verifiera ett anspråk, till exempel en e-postadress eller ett telefonnummer, med en verifieringskod som skickas till användaren.

## <a name="verificationcontrol-actions"></a>VerifieringSkontrollåtgärder

Kontrollen över verifieringsvisningen består av två steg (åtgärder):

1. Begär ett mål från användaren, till exempel en e-postadress eller ett telefonnummer, till vilket verifieringskoden ska skickas till. När användaren väljer knappen **Skicka kod** körs **SendCode-åtgärden** för verifieringsdisplaykontrollen. **SendCode-åtgärden** genererar en kod, konstruerar innehållet som ska skickas och skickar den till användaren. Adressens värde kan vara ifyllt och fungera som en andra faktorautentisering.

    ![Exempel på sida för skicka kodåtgärd](media/display-control-verification/display-control-verification-email-action-01.png)

1. När koden har skickats läser användaren meddelandet, anger verifieringskoden i kontrollen som tillhandahålls av visningskontrollen och väljer **Verifiera kod**. Genom att välja **Verifiera kod**körs åtgärden **Verifiera kod** för att verifiera koden som är associerad med adressen. Om användaren väljer **Skicka ny kod**körs den första åtgärden igen.

    ![Exempel på sida för kontrollkodåtgärd](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>Verifieringskontroller krävs element

**VerificationControl** måste innehålla följande element:

- Typen av `DisplayControl` är `VerificationControl`.
- `DisplayClaims`
  - **Skicka till** - Ett eller flera anspråk som anger var verifieringskoden ska skickas till. Till exempel *e-post* eller *landskod* och *telefonnummer*.
  - **Verifieringskod** - Verifieringskodsanspråket som användaren tillhandahåller efter att koden har skickats. Detta anspråk måste ställas in `ControlClaimType` efter behov `VerificationCode`och måste ställas in på .
- Utdataanspråk (valfritt) som ska returneras till den självpåsäkrade sidan när användaren har slutfört verifieringsprocessen. Till exempel *e-post* eller *landskod* och *telefonnummer*. Den självpåsterade tekniska profilen använder anspråken för att bevara data eller bubbla upp utdataanspråken till nästa orchestration-steg.
- Två `Action`s med följande namn:
  - **SendCode** - Skickar en kod till användaren. Den här åtgärden innehåller vanligtvis två tekniska profilen för validering, för att generera en kod och för att skicka den.
  - **Verifieracode** - Verifierar koden. Den här åtgärden innehåller vanligtvis en enda teknisk profil för validering.

I exemplet nedan visas en textruta i **e-post** på sidan. När användaren anger sin e-postadress och väljer **SendCode**utlöses **sendcode-åtgärden** i Azure AD B2C-backend.

Sedan anger användaren **verifieringskoden** och väljer **VerifyCode** för att utlösa åtgärden **Verifiera kod** i backend. Om alla valideringar går igenom anses **VerificationControl** vara fullständig och användaren kan fortsätta till nästa steg.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
