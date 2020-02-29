---
title: Konfigurera tokens – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar livs längd och kompatibilitetsinställningar för token i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189625"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurera tokens i Azure Active Directory B2C

I den här artikeln får du lära dig hur du konfigurerar [livs längd och kompatibilitet för en token](tokens-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett användar flöde](tutorial-create-user-flows.md) så att användarna kan registrera sig och logga in i programmet.

## <a name="configure-token-lifetime"></a>Konfigurera livs längd för token

Du kan konfigurera livs längden för token för ett användar flöde.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **användar flöden (principer)** .
5. Öppna det användar flöde som du skapade tidigare.
6. Välj **Egenskaper**.
7. Under **token livs längd**justerar du följande egenskaper så att de passar dina programs behov:

    ![Egenskaps inställningar för token-livstid i Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klicka på **Save** (Spara).

## <a name="configure-token-compatibility"></a>Konfigurera token-kompatibilitet

1. Välj **användar flöden (principer)** .
2. Öppna det användar flöde som du skapade tidigare.
3. Välj **Egenskaper**.
4. Under **Inställningar för token-kompatibilitet**justerar du följande egenskaper efter dina programs behov:

    ![Egenskaps inställningar för token-kompatibilitet i Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klicka på **Save** (Spara).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [använder åtkomsttoken](access-tokens.md).



