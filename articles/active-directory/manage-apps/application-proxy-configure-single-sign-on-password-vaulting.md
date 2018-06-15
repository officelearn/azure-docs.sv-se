---
title: Enkel inloggning för appar med Azure AD Application Proxy | Microsoft Docs
description: Aktivera enkel inloggning för din publicerade lokala program med Azure AD Application Proxy på Azure-portalen.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fef163e258f797fc57d391a353b1acc148a3ef49
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161982"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Lösenord vaulting för enkel inloggning med Application Proxy

Azure Active Directory Application Proxy hjälper dig att öka produktiviteten genom att publicera lokala program så att fjärranslutna anställda kan på ett säkert sätt komma åt dem för. I Azure-portalen kan du också ställa in enkel inloggning (SSO) för dessa appar. Användarna behöver bara för att autentisera med Azure AD och de kan komma åt enterprise-programmet utan att behöva logga in igen.

Application Proxy stöder flera [enkel inloggning lägen](application-proxy-single-sign-on.md). Lösenordsbaserade inloggning är avsedd för program som använder en kombination av användarnamn/lösenord för autentisering. När du konfigurerar lösenordsbaserade inloggning för tillämpningsprogrammet har användarna att logga in på en gång programmet lokalt. Efter det Azure Active Directory lagrar information för inloggning och ger den automatiskt till programmet när användarna få fjärråtkomst till den. 

Du bör redan har publicerats och testat din app med Application Proxy. Om inte, följer du stegen i [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md) sedan återvända hit. 

## <a name="set-up-password-vaulting-for-your-application"></a>Konfigurera lösenord vaulting för ditt program

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Välj **Azure Active Directory** > **företagsprogram** > **alla program**.
3. Välj den app som du vill konfigurera med enkel inloggning i listan.  
4. Välj **enkel inloggning**.

   ![Välja enkel inloggning](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. Läget för enkel inloggning väljer **lösenordsbaserade inloggning**.
6. För URL inloggning anger du Webbadressen för sidan där användarna anger sina användarnamn och lösenord för att logga in på din app utanför företagsnätverket. Detta kan vara en extern URL som du skapade när du har publicerat appen via Application Proxy. 

   ![Välj lösenordsbaserade inloggning och ange URL: en](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. Välj **Spara**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testa din app

Gå till externa URL: en som du konfigurerade för fjärråtkomst till ditt program. Logga in med dina autentiseringsuppgifter för appen (eller autentiseringsuppgifterna för ett testkonto som du skapat med åtkomst). När du loggar in har bör du kunna lämna appen och gå tillbaka utan att ange dina autentiseringsuppgifter igen. 

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra sätt att implementera [enkel inloggning med Application Proxy](application-proxy-single-sign-on.md)
- Lär dig mer om [säkerhetsaspekter för att komma åt appar med Azure AD Application Proxy](application-proxy-security.md)