---
title: Konfigurera sessionens beteende – Azure Active Directory B2C | Microsoft Docs
description: Konfigurera sessionens beteende i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed10a9258590f8f9037a574bd0322a82dd309a5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385254"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurera sessionens beteende i Azure Active Directory B2C

Hantering av [enkel inloggning (SSO)](session-overview.md) i Azure Active Directory B2C (Azure AD B2C) gör det möjligt för en administratör att styra interaktionen med en användare när användaren redan har autentiserats. Administratören kan till exempel kontrol lera om valet av identitets leverantörer visas eller om konto information måste anges igen. I den här artikeln beskrivs hur du konfigurerar SSO-inställningarna för Azure AD B2C.

## <a name="session-behavior-properties"></a>Egenskaper för sessionens beteende

Du kan använda följande egenskaper för att hantera WebApplication-sessioner:

- **Web App session livs längd (minuter)** – livs längden för Azure AD B2C's-sessionens cookie som lagras i användarens webbläsare vid lyckad autentisering.
    - Standard = 1440 minuter.
    - Minst (inklusive) = 15 minuter.
    - Max (inklusive) = 1440 minuter.
- **Timeout för webbapp** – [sessionens utgångs typ](session-overview.md#session-expiry-type), *rullande*eller *absolut*. 
- **Konfiguration av enkel inloggning** – [sessionens omfång](session-overview.md#session-scope) för beteendet enkel inloggning (SSO) i flera appar och användar flöden i din Azure AD B2C klient. 


## <a name="configure-the-properties"></a>Konfigurera egenskaperna

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användar flöden (principer)**.
5. Öppna det användar flöde som du skapade tidigare.
6. Välj **Egenskaper**.
7. Konfigurera **livs längd för webbappens session (minuter)**, **tids gräns för webbapp**, **konfiguration av enkel inloggning**och **Kräv ID-token i utloggnings begär Anden** efter behov.

    ![Inställningar för sessionens beteende egenskap i Azure Portal](./media/session-behavior/session-behavior.png)

8. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure AD B2C-sessionen](session-overview.md).