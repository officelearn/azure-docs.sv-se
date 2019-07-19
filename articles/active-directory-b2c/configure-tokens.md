---
title: Konfigurera tokens – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar livs längd och kompatibilitetsinställningar för token i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2ed1426bea920d1ef30fb28ff4a975d1268c12ff
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846933"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurera tokens i Azure Active Directory B2C

I den här artikeln får du lära dig hur du konfigurerar [livs längd och kompatibilitet för en token](active-directory-b2c-reference-tokens.md) i Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett användar flöde](tutorial-create-user-flows.md) så att användarna kan registrera sig och logga in i programmet.

## <a name="configure-token-lifetime"></a>Konfigurera livs längd för token

Du kan konfigurera livs längden för token för ett användar flöde.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog-och prenumerations filter** på den översta menyn och välj den katalog som innehåller Azure AD B2C klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **användar flöden (principer)** .
5. Öppna det användar flöde som du skapade tidigare.
6. Välj **egenskaper**.
7. Under **token livs längd**justerar du följande egenskaper så att de passar dina programs behov:

    ![Egenskaps inställningar för token-livstid i Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klicka på **Spara**.

## <a name="configure-token-compatibility"></a>Konfigurera token-kompatibilitet

1. Välj **användar flöden (principer)** .
2. Öppna det användar flöde som du skapade tidigare.
3. Välj **egenskaper**.
4. Under **Inställningar**för token-kompatibilitet justerar du följande egenskaper efter dina programs behov:

    ![Egenskaps inställningar för token-kompatibilitet i Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [använder åtkomsttoken](active-directory-b2c-access-tokens.md).



