---
title: Skicka en åtkomsttoken via ett användarflöde till din app
titleSuffix: Azure AD B2C
description: Lär dig hur du skickar en åtkomsttoken för OAuth 2.0-identitetsleverantörer som ett anspråk i ett användarflöde i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187796"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomsttoken via ett användarflöde till ditt program i Azure Active Directory B2C

Ett [användarflöde](user-flow-overview.md) i Azure Active Directory B2C (Azure AD B2C) ger användare av ditt program en möjlighet att registrera dig eller logga in med en identitetsprovider. När färden startar får Azure AD B2C en [åtkomsttoken](tokens-overview.md) från identitetsprovidern. Azure AD B2C använder den token för att hämta information om användaren. Du aktiverar ett anspråk i ditt användarflöde för att skicka token till de program som du registrerar i Azure AD B2C.

Azure AD B2C stöder för närvarande endast att skicka åtkomsttoken för [OAuth 2.0-identitetsleverantörer,](authorization-code-flow.md) som inkluderar [Facebook](identity-provider-facebook.md) och [Google](identity-provider-google.md). För alla andra identitetsleverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Krav

* Ditt program måste använda ett [v2-användarflöde](user-flow-versions.md).
* Ditt användarflöde konfigureras med en OAuth 2.0-identitetsprovider.

## <a name="enable-the-claim"></a>Aktivera anspråket

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Användarflöden (principer)** och välj sedan ditt användarflöde. Till exempel **B2C_1_signupsignin1**.
5. Välj **Programanspråk**.
6. Aktivera anspråket **åtkomsttoken för identitetsprovider.**

    ![Aktivera anspråk på åtkomsttoken för identitetsprovider](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Klicka på **Spara** om du vill spara användarflödet.

## <a name="test-the-user-flow"></a>Testa användarflödet

När du testar dina program i Azure AD B2C kan det vara bra `https://jwt.ms` att ha Azure AD B2C-token returnerad för att granska anspråken i den.

1. På sidan Översikt för användarflödet väljer du **Kör användarflöde**.
2. För **Ansökan**väljer du det program som du tidigare har registrerat. Om du vill se token i exemplet `https://jwt.ms`nedan ska **svars-URL:en** visa .
3. Klicka på **Kör användarflöde**och logga sedan in med dina kontouppgifter. Du bör se åtkomsttoken för **idp_access_token** identitetsprovidern i idp_access_token-anspråket.

    Du bör se något som liknar följande exempel:

    ![Avkodad token i jwt.ms med idp_access_token block markerat](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Nästa steg

Läs mer i [översikten över Azure AD B2C-tokens](tokens-overview.md).
