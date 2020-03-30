---
title: Konfigurera direkt inloggning med Azure Active Directory B2C | Microsoft-dokument
description: Läs om hur du fyller i inloggningsnamnet eller omdirigerar direkt till en leverantör av social identitet.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a02ad3ea43ae9d91489417bc314e3c23d54a958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188774"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Konfigurera direkt inloggning med Azure Active Directory B2C

När du konfigurerar inloggning för ditt program med Azure Active Directory (AD) B2C kan du fylla i inloggningsnamnet eller direktinstallera till en viss leverantör av social identitet, till exempel Facebook, LinkedIn eller ett Microsoft-konto.

## <a name="prepopulate-the-sign-in-name"></a>Fylla i inloggningsnamnet

Under en inloggningsanvändare kan ett program för förlitande part rikta in sig på ett visst användarnamn eller domännamn. När ett program riktar sig till en användare `login_hint` kan det i auktoriseringsbegäran ange frågeparametern med användarens inloggningsnamn. Azure AD B2C fyller automatiskt i inloggningsnamnet, medan användaren bara behöver ange lösenordet.

![Registrera dig på sidan med login_hint frågeparam markerad i URL](./media/direct-signin/login-hint.png)

Användaren kan ändra värdet i inloggningstextrutan.

Om du använder en anpassad `SelfAsserted-LocalAccountSignin-Email` princip åsidosätter du den tekniska profilen. I `<InputClaims>` avsnittet anger du defaultvalue för signInName-anspråket på `{OIDC:LoginHint}`. Variabeln `{OIDC:LoginHint}` innehåller `login_hint` parameterns värde. Azure AD B2C läser värdet för signInName-anspråket och fyller i textrutan signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Omdirigera inloggning till en social leverantör

Om du har konfigurerat inloggningsresan för ditt program så att den innehåller sociala konton, till exempel Facebook, LinkedIn eller Google, kan du ange parametern. `domain_hint` Den här frågeparametern ger en ledtråd till Azure AD B2C om den sociala identitetsprovidern som ska användas för inloggning. Om programmet till exempel `domain_hint=facebook.com`anger går inloggning direkt till Facebooks inloggningssida.

![Registrera dig på sidan med domain_hint frågeparam markerad i URL](./media/direct-signin/domain-hint.png)

Om du använder en anpassad princip kan du `<Domain>domain name</Domain>` konfigurera domännamnet `<ClaimsProvider>`med hjälp av XML-elementet i alla .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


