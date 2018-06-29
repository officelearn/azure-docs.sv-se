---
title: Ställ in direkt loggar du in med Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om att fylla i inloggningsnamn eller omdirigera direkt till en sociala identitetsleverantör.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/18/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc3baa8fe4139acec94a722bf8c2bccb708a9470
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102696"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Ställ in direkt loggar du in med Azure Active Directory B2C

När du ställer in logga in för ditt program med hjälp av Azure Active Directory (AD) B2C, kan du ange inloggningsnamn eller direkt logga in på en specifik sociala identitetsleverantör, till exempel Facebook, LinkedIn eller ett Microsoft-konto. 

## <a name="prepopulate-the-sign-in-name"></a>Fylla i inloggningsnamn

När en användare loggar in resa kan en förlitande partsprogram mål ett specifikt namn för användare eller domän. När måldatorn en användare, ett program kan ange i auktoriseringsbegäran i `login_hint` Frågeparametern med inloggning användarnamn. Azure AD B2C fyller automatiskt inloggningsnamnet, medan användaren bara behöver ange lösenordet.

![med hjälp av inloggningen tips](./media/direct-signin/login-hint.png) 

Användaren kan ändra värdet i textrutan inloggning.

Om du använder en anpassad princip kan åsidosätta den `SelfAsserted-LocalAccountSignin-Email` tekniska profil. I den `<InputClaims>` avsnittet genom att ange DefaultValue för signInName anspråket till `{OIDC:LoginHint}`. Den `{OIDC:LoginHint}` variabeln innehåller värdet för den `login_hint` parameter. Azure AD B2C läser signInName anspråkets värde och före fylls textrutan signInName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Omdirigera inloggning till en sociala provider

Om du har konfigurerat resan för ditt program att inkludera sociala konton, till exempel Facebook, LinkedIn eller Google, kan du ange den `domain_hint` parameter. Den här Frågeparametern ger en ledtråd till Azure AD B2C om sociala identitetsleverantören som ska användas för inloggning. Om programmet anger till exempel `domain_hint=facebook.com`, går direkt till sidan för Facebook-inloggning.

![med hjälp av domän tips](./media/direct-signin/domain-hint.png) 

Om du använder en anpassad princip, kan du konfigurera det namn med den `<Domain>domain name</Domain>` XML-elementet för någon `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


