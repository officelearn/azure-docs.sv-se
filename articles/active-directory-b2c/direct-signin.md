---
title: Ställ in direkt inloggning med Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om att fylla inloggningsnamnet eller omdirigera direkt till en social identitetsprovider.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c66fc9ecb608d211c839bab720e8f3f37b96fd5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60389856"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Ställ in direkt inloggning med Azure Active Directory B2C

När du konfigurerar inloggning för ditt program med Azure Active Directory (AD) B2C, kan du förkonfigurera inloggningsnamn eller direkt inloggning till en viss social identitetsprovider, till exempel Facebook, LinkedIn eller ett Microsoft-konto. 

## <a name="prepopulate-the-sign-in-name"></a>Fylla inloggningsnamnet

Under en inloggning användarresa, kan en förlitande partsprogram rikta en specifik användare eller domän namn. När du riktar in sig på en användare, ett program kan ange i auktoriseringsbegäran, den `login_hint` frågeparameter med inloggningsnamnet för användaren. Azure AD B2C fyller automatiskt inloggningsnamnet, medan användaren bara behöver ange lösenordet.

![med hjälp av inloggningen tips](./media/direct-signin/login-hint.png) 

Användaren kan ändra värdet i textrutan inloggning.

Om du använder en anpassad princip kan åsidosätta den `SelfAsserted-LocalAccountSignin-Email` tekniska profilen. I den `<InputClaims>` anger DefaultValue signInName anspråket till `{OIDC:LoginHint}`. Den `{OIDC:LoginHint}` variabeln innehåller värdet för den `login_hint` parametern. Azure AD B2C läser signInName anspråkets värde och förväg fylls signInName textrutan.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Omdirigera loggar in till en social provider

Om du har konfigurerat resa logga in för ditt program att inkludera konton i sociala medier, till exempel Facebook, LinkedIn eller Google, kan du ange den `domain_hint` parametern. Den här Frågeparametern ger en ledtråd till Azure AD B2C om social identitetsprovider som ska användas för att logga in. Exempel: om programmet anger `domain_hint=facebook.com`, går du direkt till sidan för Facebook-inloggning.

![med hjälp av tips för domänen](./media/direct-signin/domain-hint.png) 

Om du använder en anpassad princip, kan du konfigurera en domän namn med hjälp av den `<Domain>domain name</Domain>` XML-element för någon `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


