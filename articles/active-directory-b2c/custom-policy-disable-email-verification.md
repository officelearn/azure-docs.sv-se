---
title: Inaktivera e-postverifiering under kund registrering med en anpassad princip
titleSuffix: Azure AD B2C
description: Lär dig hur du inaktiverar e-postverifiering under kund registrering i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136150"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Inaktivera e-postverifiering under kund registrering med en anpassad princip i Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med sociala och lokala konton.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Lägg till metadata till den självkontrollerade tekniska profilen

**LocalAccountSignUpWithLogonEmail** Technical Profile är en [självkontrollerad](self-asserted-technical-profile.md), som anropas under registrerings flödet. Om du vill inaktivera e-postverifieringen ställer du in `EnforceEmailVerification` metadata på falskt. Åsidosätt de tekniska profilerna för LocalAccountSignUpWithLogonEmail i tilläggs filen. 

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>  .
1. Hitta `ClaimsProviders`-elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråks leverantör i `ClaimsProviders`-elementet:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
4. Välj **ramverk för identitets upplevelse**.
5. Välj **överför anpassad princip**och ladda upp de två principfiler som du har ändrat.
2. Välj den registrerings-eller inloggnings princip som du laddade upp och klicka på knappen **Kör nu** .
3. Du bör kunna registrera dig med en e-postadress utan verifieringen.


## <a name="next-steps"></a>Nästa steg

- Läs mer om den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md) i IEF-referensen.
