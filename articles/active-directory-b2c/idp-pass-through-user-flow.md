---
title: Skicka en åtkomst-token via ett användarflöde till ditt program i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du kan passera en åtkomsttoken för OAuth2.0 Identitetsproviders som ett anspråk i ett användarflöde i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fd0f8241c07f603089b896dcfc9ece29f1e33d1c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428212"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomst-token via ett användarflöde till ditt program i Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är för närvarande i offentlig förhandsversion.

En [användarflödet](active-directory-b2c-reference-policies.md) i Azure Active Directory (Azure AD) B2C ger ditt programs användare möjlighet att registrera dig eller logga in med en identitetsprovider. När vägen startar Azure AD B2C får en [åtkomsttoken](active-directory-b2c-reference-tokens.md) från identitetsprovidern. Azure AD B2C använder den token för att hämta information om användaren. Du kan aktivera ett anspråk i ditt användarflöde och skicka token via till de program som du registrerar i Azure AD B2C.

Azure AD-B2C stöder skicka åtkomsttoken [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) och [OpenID Connect](active-directory-b2c-reference-oidc.md) identitetsleverantörer. För alla andra identitetsleverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Förutsättningar

- Programmet måste använda en [v2 användarflödet](user-flow-versions.md).
- Ditt användarflöde är konfigurerad med en identitetsleverantör för OAuth 2.0 eller OpenID Connect.

## <a name="enable-the-claim"></a>Aktivera anspråket

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användarflöden**, och välj sedan ditt användarflöde. Till exempel **B2C_1_SignupSignIn**.
5. Välj **Programanspråk**.
6. Aktivera **identitet providern åtkomsttoken**.

    ![Programanspråket](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Klicka på **spara** att spara användarflödet.

## <a name="test-the-user-flow"></a>Testa användarflödet

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha en Azure AD B2C-token som returneras till `https://jwt.ms` att granska anspråk i den.

1. På sidan översikt av användarflödet väljer **kör användarflödet**.
2. För **programmet**, Välj ditt program som du tidigare har registrerat. Se token i exemplet nedan, den **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **kör användarflödet**, och sedan logga in med autentiseringsuppgifterna för ditt konto. Du bör se den åtkomst-token för identitetsprovidern i den **idp_access_token** anspråk.

    Du bör se något som liknar följande exempel:

    ![Avkodade token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>Nästa steg

Mer information om token i den [tokenreferens för Azure Active Directory](active-directory-b2c-reference-tokens.md).




