---
title: Inaktivera e-postverifiering under kundanmälan med en anpassad princip
titleSuffix: Azure AD B2C
description: Lär dig hur du inaktiverar e-postverifiering under kundanmälning i Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136150"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Inaktivera e-postverifiering under kunduppstÃ¤lp med en anpassad princip i Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Krav

Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med sociala och lokala konton.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Lägga till metadata i den självsäkra tekniska profilen

Den **lokala AnmälSignUpWithLogonEmail-profilen** är en [självpåtrkänt](self-asserted-technical-profile.md), som anropas under registreringsflödet. Om du vill inaktivera `EnforceEmailVerification` e-postverifieringen ställer du in metadata på false. Åsidosätt de tekniska profilerna för LocalAccountSignUpWithLogonEmail i tilläggsfilen. 

1. Öppna filen för tillägg i principen. Till exempel <em> `SocialAndLocalAccounts/` </em>.
1. Leta `ClaimsProviders` reda på elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråksprovider i elementet: `ClaimsProviders`

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

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrollera att du använder katalogen som innehåller din Azure AD-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD-klientorganisation.
3. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
4. Välj **Identity Experience Framework**.
5. Välj **Ladda upp anpassad princip**och ladda sedan upp de två principfiler som du har ändrat.
2. Välj den registrerings- eller inloggningsprincip som du har laddat upp och klicka på knappen **Kör nu.**
3. Du bör kunna registrera dig med en e-postadress utan validering.


## <a name="next-steps"></a>Nästa steg

- Läs mer om den [självsäkra tekniska profilen](self-asserted-technical-profile.md) i IEF-referensen.
