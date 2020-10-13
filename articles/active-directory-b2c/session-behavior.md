---
title: Konfigurera sessionens beteende – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar sessionens beteende i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961042"
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
4. Välj **användar flöden**.
5. Öppna det användar flöde som du skapade tidigare.
6. Välj **Egenskaper**.
7. Konfigurera **livs längd för webbappens session (minuter)**, **tids gräns för webbapp**, **konfiguration av enkel inloggning**och **Kräv ID-token i utloggnings begär Anden** efter behov.

    ![Inställningar för sessionens beteende egenskap i Azure Portal](./media/session-behavior/session-behavior.png)

8. Klicka på **Spara**.

## <a name="configure-sign-out-behavior"></a>Konfigurera utloggnings beteende

### <a name="secure-your-logout-redirect"></a>Skydda din utloggnings omdirigering

Efter utloggning omdirigeras användaren till den URI som anges i `post_logout_redirect_uri` parametern, oavsett vilka svars-URL: er som har angetts för programmet. Men om ett giltigt `id_token_hint` värde skickas och **Kräv ID-token i utloggnings begär Anden** aktive ras, verifierar Azure AD B2C att värdet för `post_logout_redirect_uri` matchar ett av programmets konfigurerade omdirigerings-URI: er innan omdirigeringen utförs. Om ingen matchande svars-URL har kon figurer ATS för programmet visas ett fel meddelande och användaren omdirigeras inte. Så här kräver du en ID-token i utloggnings begär Anden:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden**.
1. Öppna det användar flöde som du skapade tidigare.
1. Välj **Egenskaper**.
1. Aktivera **Kräv ID-token i utloggnings begär Anden**.
1. Gå tillbaka till  **Azure AD B2C**.
1. Välj **Appregistreringar**och välj sedan ditt program.
1. Välj **Autentisering**.
1. I text rutan **utloggnings-URL** skriver du in återutloggning omdirigerings-URI och väljer sedan **Spara**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure AD B2C-sessionen](session-overview.md).
