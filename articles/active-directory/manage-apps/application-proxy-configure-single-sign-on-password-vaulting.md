---
title: Enkel inloggning till appar med Azure AD Application Proxy | Microsoft Docs
description: Aktivera enkel inloggning för dina publicerade lokala program med Azure AD Application Proxy på Azure-portalen.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025749"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Lösenord vaulting för enkel inloggning med programproxy

Azure Active Directory Application Proxy kan du förbättra produktiviteten genom att publicera lokala program så att fjärranslutna anställda kan få säker åtkomst till dem, för. I Azure-portalen kan du också ställa in enkel inloggning (SSO) till de här apparna. Användarna behöver bara för att autentisera med Azure AD och de kan komma åt dina företagsprogram utan att behöva logga in igen.

Programproxyn har stöd för flera [enkel inloggning lägen](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Lösenordsbaserad inloggning är avsedd för program som använder en kombination av användarnamn/lösenord för autentisering. När du konfigurerar lösenordsbaserad inloggning för ditt program kan måste användarna logga in på dina lokala program en gång. Efter det Azure Active Directory lagrar inloggningsinformation och tillhandahåller det automatiskt till programmet när dina användare få fjärråtkomst till den.

Du bör redan har publicerats och testas din app med Application Proxy. Om inte, följer du stegen i [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md) Kom sedan tillbaka hit.

## <a name="set-up-password-vaulting-for-your-application"></a>Konfigurera lösenord vaulting för ditt program

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Välj **Azure Active Directory** > **företagsprogram** > **alla program**.
1. Välj den app som du vill ställa in med enkel inloggning i listan.  
1. Välj **programproxy**. 
1. Ändra **typen för autentisering** till **passthrough** och välj **Spara**. Senare kan du växla tillbaka till **Azure Active Directory** typ igen! 
1. Välj **enkel inloggning**.

   ![Välj enkel inloggning på appens översikts sida](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. SSO-läge, Välj **lösenordsbaserad inloggning**.
1. För inloggnings-URL: en, anger du URL: en för sidan där användarna anger sina användarnamn och lösenord för att logga in på din app utanför företagets nätverk. Detta kan vara en extern URL som du skapade när du har publicerat appen via programproxyn.

   ![Välj lösenordsbaserad inloggning och anger en URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Välj **Spara**.
1. Välj **programproxy**. 
1. Ändra **typen för förautentisering** till **Azure Active Directory** och välj **Spara**. 
1. Välj **användare och grupper**.
1. Tilldela användare till programmet med att välja **Lägg till användare**. 
1. Om du vill fördefiniera autentiseringsuppgifter för en användare markerar du kryss rutan framför användar namnet och väljer **uppdatera autentiseringsuppgifter**.
1. Välj **Azure Active Directory** > **Appregistreringar** > **alla program**.
1. I listan väljer du den app som du konfigurerade med lösen ordet SSO.
1. Välj **anpassning**. 
1. Uppdatera **Start sidans URL** med **inloggnings-URL:** en från inloggnings sidan för lösen ord och välj **Spara**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testa din app

Gå till portalen Mina appar. Logga in med dina autentiseringsuppgifter (eller autentiseringsuppgifterna för ett test konto som du har konfigurerat med åtkomst). När du har loggat in klickar du på ikonen för appen. Detta kan utlösa installationen av tillägget Mina appar säker inloggnings webbläsare. Om din användare hade fördefinierade autentiseringsuppgifter ska autentiseringen till appen ske automatiskt, annars måste du ange användar namnet eller lösen ordet för första gången. 

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra sätt att implementera [enkel inloggning](what-is-single-sign-on.md)
- Lär dig mer om [säkerhetsöverväganden för att komma åt appar med Azure AD Application Proxy](application-proxy-security.md)
