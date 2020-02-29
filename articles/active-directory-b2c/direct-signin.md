---
title: Konfigurera direkt inloggning med Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du fyller i inloggnings namnet eller omdirigerar direkt till en social identitetsprovider.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188774"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Konfigurera direkt inloggning med hjälp av Azure Active Directory B2C

När du konfigurerar inloggning för ditt program med hjälp av Azure Active Directory (AD) B2C, kan du fylla i inloggnings namnet eller dirigera inloggningen till en specifik social identitetsprovider, till exempel Facebook, LinkedIn eller en Microsoft-konto.

## <a name="prepopulate-the-sign-in-name"></a>Förkonfigurera inloggnings namnet

Under en inloggnings användare kan ett förlitande parts program riktas mot ett särskilt användar-eller domän namn. När du riktar en användare till en användare kan du ange den `login_hint` Frågeparametern med användarens inloggnings namn i auktoriseringsbegäran. Azure AD B2C fyller automatiskt i inloggnings namnet, medan användaren bara behöver ange lösen ordet.

![Registrera inloggnings sidan med login_hint frågeparametrar markerad i URL](./media/direct-signin/login-hint.png)

Användaren kan ändra värdet i text rutan för inloggning.

Om du använder en anpassad princip ska du åsidosätta `SelfAsserted-LocalAccountSignin-Email` tekniska profilen. I avsnittet `<InputClaims>` anger du DefaultValue för det signInName-anspråk som ska `{OIDC:LoginHint}`. Variabeln `{OIDC:LoginHint}` innehåller värdet för parametern `login_hint`. Azure AD B2C läser värdet för signInName-anspråket och fyller i text rutan signInName.

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

Om du har konfigurerat inloggnings resan för ditt program att inkludera sociala konton, till exempel Facebook, LinkedIn eller Google, kan du ange `domain_hint` parameter. Den här Frågeparametern ger en ledtråd för Azure AD B2C om den sociala identitets leverantör som ska användas för inloggning. Om programmet till exempel anger `domain_hint=facebook.com`skickas inloggningen direkt till Facebook-inloggnings sidan.

![Registrera inloggnings sidan med domain_hint frågeparametrar markerad i URL](./media/direct-signin/domain-hint.png)

Om du använder en anpassad princip kan du konfigurera domän namnet med hjälp av `<Domain>domain name</Domain>` XML-element för alla `<ClaimsProvider>`.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


