---
title: Enkel inloggning till appar med Azure AD Application Proxy | Microsoft-dokument
description: Aktivera enkel inloggning för dina publicerade lokala program med Azure AD Application Proxy i Azure-portalen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0259a8d9fcb4c9c513ab2c31103c9a8488e90ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025749"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Lösenordsvalv för enkel inloggning med Programproxy

Azure Active Directory Application Proxy hjälper dig att förbättra produktiviteten genom att publicera lokala program så att fjärranställda kan komma åt dem på ett säkert sätt också. I Azure-portalen kan du också konfigurera enkel inloggning (SSO) till dessa appar. Användarna behöver bara autentisera med Azure AD och de kan komma åt ditt företagsprogram utan att behöva logga in igen.

Application Proxy stöder flera [enkla inloggningslägen](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Lösenordsbaserad inloggning är avsedd för program som använder en kombination av användarnamn/lösenord för autentisering. När du konfigurerar lösenordsbaserad inloggning för ditt program måste användarna logga in på det lokala programmet en gång. Därefter lagrar Azure Active Directory inloggningsinformationen och tillhandahåller den automatiskt till programmet när användarna fjärransluter den.

Du borde redan ha publicerat och testat din app med Application Proxy. Om inte, följ stegen i [Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md) och kom sedan tillbaka hit.

## <a name="set-up-password-vaulting-for-your-application"></a>Konfigurera lösenordsvalv för ditt program

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
1. Välj **Azure Active Directory** > **Enterprise-program** > **Alla program**.
1. Välj den app som du vill konfigurera med SSO i listan.  
1. Välj **Programproxy**. 
1. Ändra **typen Pre Authentication** till **Passthrough** och välj **Spara**. Senare kan du växla tillbaka till **Azure Active Directory** typ igen! 
1. Välj **Enkel inloggning**.

   ![Välj Enkel inloggning på appens översiktssida](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. För SSO-läget väljer du **Lösenordsbaserad inloggning**.
1. För inloggnings-URL:en anger du webbadressen till sidan där användarna anger sitt användarnamn och lösenord för att logga in på din app utanför företagsnätverket. Det kan vara den externa URL:en som du skapade när du publicerade appen via Programproxy.

   ![Välj lösenordsbaserad inloggning och ange webbadressen](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Välj **Spara**.
1. Välj **Programproxy**. 
1. Ändra **typen Före autentisering** till **Azure Active Directory** och välj **Spara**. 
1. Välj **Användare och grupper**.
1. Tilldela användare till programmet med att välja **Lägg till användare**. 
1. Om du vill fördefiniera autentiseringsuppgifter för en användare markerar du rutan längst fram i användarnamnet och väljer **Uppdatera autentiseringsuppgifter**.
1. Välj **Azure Active Directory** > **App-registreringar** > **Alla program**.
1. Välj den app som du konfigurerade med Lösenord SSO i listan.
1. Välj **Branding**. 
1. Uppdatera **webbadressen till startsidan** med **inloggningsadressen på** sidan Lösenord SSO och välj **Spara**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testa din app

Gå till portalen Mina appar. Logga in med dina autentiseringsuppgifter (eller autentiseringsuppgifterna för ett testkonto som du har konfigurerat med åtkomst). När du har loggat in klickar du på ikonen för appen. Detta kan utlösa installationen av webbläsartillägget För säker inloggning för Mina appar. Om användaren hade fördefinierade autentiseringsuppgifter ska autentiseringen till appen ske automatiskt, annars måste du ange användarnamnet eller lösenordet för första gången. 

## <a name="next-steps"></a>Nästa steg

- Läs om andra sätt att implementera [Enkel inloggning](what-is-single-sign-on.md)
- Lär dig mer om [säkerhetsaspekter för åtkomst till appar på distans med Azure AD Application Proxy](application-proxy-security.md)
