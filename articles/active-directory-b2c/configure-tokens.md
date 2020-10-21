---
title: Konfigurera tokens – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar livs längd och kompatibilitetsinställningar för token i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67bc9d6b35d4841999721a00592a6bbe23bff10f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340231"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurera tokens i Azure Active Directory B2C

I den här artikeln får du lära dig hur du konfigurerar [livs längd och kompatibilitet för en token](tokens-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett användarflöde](tutorial-create-user-flows.md) så att användare kan registrera sig och logga in i ditt program.

## <a name="configure-jwt-token-lifetime"></a>Konfigurera livs längd för JWT-token

Du kan konfigurera livs längden för token för ett användar flöde.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användar flöden (principer)**.
5. Öppna det användar flöde som du skapade tidigare.
6. Välj **Egenskaper**.
7. Under **token livs längd**justerar du följande egenskaper så att de passar dina programs behov:

    ![Egenskaps inställningar för token-livstid i Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klicka på **Spara**.

> [!NOTE]
> Program med en enda sida som använder auktoriseringskod med PKCE har alltid en livs längd på 24 timmar. [Lär dig mer om säkerhets konsekvenserna av uppdateringstoken i webbläsaren](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-jwt-token-compatibility"></a>Konfigurera kompatibilitet med JWT-token

1. Välj **användar flöden (principer)**.
2. Öppna det användar flöde som du skapade tidigare.
3. Välj **Egenskaper**.
4. Under **Inställningar för token-kompatibilitet**justerar du följande egenskaper efter dina programs behov:

    ![Egenskaps inställningar för token-kompatibilitet i Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klicka på **Spara**.

## <a name="provide-optional-claims-to-your-app"></a>Tillhandahålla valfria anspråk till din app

Program anspråk är värden som returneras till programmet. Uppdatera ditt användar flöde så att det innehåller önskade anspråk.

1. Välj **användar flöden (principer)**.
1. Öppna det användar flöde som du skapade tidigare.
1. Välj **Programanspråk**.
1. Välj de anspråk och attribut som du vill skicka tillbaka till ditt program.
1. Klicka på **Spara**.


## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [begär åtkomsttoken](access-tokens.md).



