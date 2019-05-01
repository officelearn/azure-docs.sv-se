---
title: Konfigurera token - Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar inställningarna för token livslängd och kompatibilitet i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 69a6284ea7b8905bb5efdb1f4c9f26027bd6f9d9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689615"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurera token i Azure Active Directory B2C

I den här artikeln får du lära dig hur du konfigurerar den [livstid och kompatibilitet för en token](active-directory-b2c-reference-tokens.md) i Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Nödvändiga komponenter

[Skapa ett användarflöde](tutorial-create-user-flows.md) så att användarna kan registrera dig och logga in på ditt program.

## <a name="configure-token-lifetime"></a>Konfigurera livslängd för token

Du kan konfigurera livslängd för token på alla användarflödet.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din Azure AD B2C-klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **användarflöden (principer)**.
5. Öppna det användarflöde som du skapade tidigare. 
6. Välj **egenskaper**.
7. Under **Token livslängd**, justera följande egenskaper så att de passar behoven i ditt program:

    ![Konfigurera livslängd för token](./media/configure-tokens/token-lifetime.png)

8. Klicka på **Spara**.

## <a name="configure-token-compatibility"></a>Konfigurera tokenkompatibilitet

1. Välj **användarflöden (principer)**.
2. Öppna det användarflöde som du skapade tidigare. 
3. Välj **egenskaper**.
4. Under **Token kompatibilitetsinställningar**, justera följande egenskaper så att de passar behoven i ditt program:

    ![Konfigurera tokenkompatibilitet](./media/configure-tokens/token-compatibility.png)

5. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [använder åtkomsttoken](active-directory-b2c-access-tokens.md).



