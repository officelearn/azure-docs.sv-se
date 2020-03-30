---
title: Konfigurera token – Azure Active Directory B2C | Microsoft-dokument
description: Lär dig hur du konfigurerar inställningarna för tokenlivstid och kompatibilitet i Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189625"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurera token i Azure Active Directory B2C

I den här artikeln får du lära dig hur du konfigurerar [livslängden och kompatibiliteten för en token](tokens-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Krav

[Skapa ett användarflöde](tutorial-create-user-flows.md) så att användarna kan registrera sig och logga in på ditt program.

## <a name="configure-token-lifetime"></a>Konfigurera tokenlivslängd

Du kan konfigurera tokenlivstiden för alla användarflöden.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din Azure AD B2C-klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Användarflöden (principer)**.
5. Öppna användarflödet som du tidigare skapade.
6. Välj **egenskaper**.
7. Under **Tokenlivslängd**justerar du följande egenskaper så att de passar behoven i ditt program:

    ![Egenskapsinställningar för tokenlivstid i Azure-portalen](./media/configure-tokens/token-lifetime.png)

8. Klicka på **Spara**.

## <a name="configure-token-compatibility"></a>Konfigurera tokenkompatibilitet

1. Välj **Användarflöden (principer)**.
2. Öppna användarflödet som du tidigare skapade.
3. Välj **egenskaper**.
4. Justera följande egenskaper under inställningar för **tokenkompatibilitet**så att de passar programmets behov:

    ![Egenskapsinställningar för tokenkompatibilitet i Azure-portalen](./media/configure-tokens/token-compatibility.png)

5. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [använder åtkomsttoken](access-tokens.md).



