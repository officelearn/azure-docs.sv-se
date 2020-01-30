---
title: Skicka en åtkomsttoken via ett användar flöde till din app
titleSuffix: Azure AD B2C
description: Lär dig hur du skickar en åtkomsttoken för OAuth 2,0-identitets leverantörer som ett anspråk i ett användar flöde i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 25a8181d404091064c26407c98fe31465cd4f4b3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76838963"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomsttoken via ett användar flöde till ditt program i Azure Active Directory B2C

Ett [användar flöde](user-flow-overview.md) i Azure Active Directory B2C (Azure AD B2C) ger användare av programmet en möjlighet att registrera sig eller logga in med en identitets leverantör. När resan startar får Azure AD B2C en [åtkomsttoken från identitets](tokens-overview.md) leverantören. Azure AD B2C använder denna token för att hämta information om användaren. Du aktiverar ett anspråk i ditt användar flöde för att skicka token till de program som du registrerar i Azure AD B2C.

Azure AD B2C stöder för närvarande bara att skicka åtkomsttoken för [OAuth 2,0](authorization-code-flow.md) -identitets leverantörer, som innehåller [Facebook](identity-provider-facebook.md) och [Google](identity-provider-google.md). För alla andra identitets leverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Krav

* Ditt program måste använda ett [v2-användar flöde](user-flow-versions.md).
* Ditt användar flöde har kon figurer ATS med en identitets leverantör för OAuth 2,0.

## <a name="enable-the-claim"></a>Aktivera anspråket

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användar flöden (principer)** och välj sedan ditt användar flöde. Till exempel **B2C_1_signupsignin1**.
5. Välj **Programanspråk**.
6. Aktivera **åtkomst till token för identitets leverantör** .

    ![Aktivera åtkomst till token för identitets leverantör](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Spara användar flödet genom att klicka på **Spara** .

## <a name="test-the-user-flow"></a>Testa användar flödet

När du testar dina program i Azure AD B2C kan det vara praktiskt att använda den Azure AD B2C token som returneras till `https://jwt.ms` för att granska anspråk i det.

1. På sidan Översikt i användar flödet väljer du **Kör användar flöde**.
2. För **program**väljer du ditt program som du har registrerat tidigare. Om du vill se token i exemplet nedan ska **svars-URL:** en Visa `https://jwt.ms`.
3. Klicka på **Kör användar flöde**och logga in med dina autentiseringsuppgifter för kontot. Du bör se åtkomsttoken för identitets leverantören i **idp_access_token** -anspråket.

    Du bör se något som liknar följande exempel:

    ![Avkodad token i jwt.ms med idp_access_token block markerat](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Nästa steg

Läs mer i [Översikt över Azure AD B2C tokens](tokens-overview.md).
