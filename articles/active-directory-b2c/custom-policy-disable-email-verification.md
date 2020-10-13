---
title: Inaktivera e-postverifiering under kund registrering med en anpassad princip
titleSuffix: Azure AD B2C
description: Lär dig hur du inaktiverar e-postverifiering när kunden registrerar sig med en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9372a9caff5aefb53bfa8adf7eb1c68d2a3b7b2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259627"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Inaktivera e-postverifiering under kund registrering med en anpassad princip i Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med sociala och lokala konton.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Lägg till metadata till den självkontrollerade tekniska profilen

**LocalAccountSignUpWithLogonEmail** Technical Profile är en [självkontrollerad](self-asserted-technical-profile.md), som anropas under registrerings flödet. Om du vill inaktivera e-postverifieringen ställer du in `EnforceEmailVerification` metadata på falskt. Åsidosätt de tekniska profilerna för LocalAccountSignUpWithLogonEmail i tilläggs filen. 

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Hitta `ClaimsProviders` elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråks leverantör i- `ClaimsProviders` elementet:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
4. Välj **ramverk för identitets upplevelse**.
5. Välj **överför anpassad princip**och ladda upp de två principfiler som du har ändrat.
2. Välj den registrerings-eller inloggnings princip som du laddade upp och klicka på knappen **Kör nu** .
3. Du bör kunna registrera dig med en e-postadress utan verifieringen.


## <a name="next-steps"></a>Nästa steg

- Läs mer om den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md) i IEF-referensen.
