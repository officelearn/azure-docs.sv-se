---
title: Skicka en åtkomst-token via ett användarflöde till ditt program – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du kan passera en åtkomsttoken för OAuth2.0 Identitetsproviders som ett anspråk i ett användarflöde i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7863bea9f3fe6ef146dc1e1f2b29bbfda09a9d6d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683644"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomst-token via ett användarflöde till ditt program i Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.

En [användarflödet](active-directory-b2c-reference-policies.md) i Azure Active Directory (Azure AD) B2C ger ditt programs användare möjlighet att registrera dig eller logga in med en identitetsprovider. När vägen startar Azure AD B2C får en [åtkomsttoken](active-directory-b2c-reference-tokens.md) från identitetsprovidern. Azure AD B2C använder den token för att hämta information om användaren. Du kan aktivera ett anspråk i ditt användarflöde och skicka token via till de program som du registrerar i Azure AD B2C.

Azure AD-B2C stöder för närvarande endast skicka åtkomsttoken [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) identitetsleverantörer, bland annat [Facebook](active-directory-b2c-setup-fb-app.md) och [Google](active-directory-b2c-setup-goog-app.md). För alla andra identitetsleverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Programmet måste använda en [v2 användarflödet](user-flow-versions.md).
- Ditt användarflöde är konfigurerad med en OAuth 2.0-identitetsprovider.

## <a name="enable-the-claim"></a>Aktivera anspråket

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användarflöden (principer)**, och välj sedan ditt användarflöde. Till exempel **B2C_1_signupsignin1**.
5. Välj **Programanspråk**.
6. Aktivera den **åtkomsttoken för identitets-Provider** anspråk.

    ![Aktivera åtkomsttoken för identitet providern anspråk](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Klicka på **spara** att spara användarflödet.

## <a name="test-the-user-flow"></a>Testa användarflödet

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha en Azure AD B2C-token som returneras till `https://jwt.ms` att granska anspråk i den.

1. På sidan översikt av användarflödet väljer **kör användarflödet**.
2. För **programmet**, Välj ditt program som du tidigare har registrerat. Se token i exemplet nedan, den **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **kör användarflödet**, och sedan logga in med autentiseringsuppgifterna för ditt konto. Du bör se den åtkomst-token för identitetsprovidern i den **idp_access_token** anspråk.

    Du bör se något som liknar följande exempel:

    ![Avkodade token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>Nästa steg

Läs mer i den [översikt över Azure AD B2C-token](active-directory-b2c-reference-tokens.md).




