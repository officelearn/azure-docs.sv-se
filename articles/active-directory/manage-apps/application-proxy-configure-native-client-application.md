---
title: Publicera appar med inbyggd klient - Azure AD | Microsoft Docs
description: Beskriver hur du aktiverar inbyggd klient apps att kommunicera med Azure AD Application Proxy Connector att tillhandahålla säker fjärråtkomst till lokala appar.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 57dc32c8763fe21089e799f042109f2db78275ca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166466"
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Så här aktiverar du ursprungliga klientappar kan interagera med proxy-program

Förutom webbprogram, kan Azure Active Directory Application Proxy också användas att publicera ursprungliga klientappar som har konfigurerats med Azure AD Authentication Library (ADAL). Ursprungliga klientappar skiljer sig från web apps eftersom de är installerade på en enhet, medan webbappar som kan nås via en webbläsare. 

Ursprungliga klientappar av tar emot Azure AD som utfärdade token som skickas i rubriken har stöd för programproxy. Application Proxy-tjänsten utförs en autentisering åt användarna. Den här lösningen använder inte programmet token för autentisering. 

![Förhållandet mellan användare och Azure Active Directory publicerade program](./media/application-proxy-configure-native-client-application/richclientflow.png)

Använd Azure AD Authentication Library, som tar hand om autentisering och har stöd för många klientmiljöer att publicera interna program. Programproxy passar in i den [internt program för webb-API-scenario](../develop/native-app.md). 

Den här artikeln vägleder dig igenom fyra stegen för att publicera ett internt program med Application Proxy och Azure AD-Autentiseringsbiblioteket. 

## <a name="step-1-publish-your-application"></a>Steg 1: Publicera programmet
Publicera dina proxy-program, precis som alla andra program och tilldela användare åtkomst till ditt program. Mer information finns i [publicera program med Application Proxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-configure-your-application"></a>Steg 2: Konfigurera ditt program
Konfigurera ditt interna program enligt följande:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till **Azure Active Directory** > **appregistreringar**.
3. Välj **Ny programregistrering**.
4. Ange ett namn för ditt program, Välj **interna** som programtyp, och ange omdirigerings-URI för ditt program. 

   ![Skapa en ny appregistrering](./media/application-proxy-configure-native-client-application/create.png)
5. Välj **Skapa**.

Mer information om hur du skapar en ny appregistrering finns [integrera program med Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).


## <a name="step-3-grant-access-to-other-applications"></a>Steg 3: Bevilja åtkomst till andra program
Aktivera programspecifik kan exponeras för andra program i din katalog:

1. Fortfarande är i **appregistreringar**, Välj den nya inbyggda program som du nyss skapade.
2. Välj **API-behörigheter**.
3. Välj **lägga till en behörighet**.
4. Öppna det första steget, **Välj en API**.
5. Använd sökfältet för att hitta den Application Proxy-app som du publicerade i det första avsnittet. Välj appen och klicka sedan på **Välj**. 

   ![Sök efter app proxy](./media/application-proxy-configure-native-client-application/select_api.png)
6. Öppna det andra steget **Välj behörigheter**.
7. Använd kryssrutan för att ge din internt programåtkomst till programmets proxy och klicka sedan på **Välj**.

   ![Bevilja åtkomst till app proxy](./media/application-proxy-configure-native-client-application/select_perms.png)
8. Välj **Done** (Klar).


## <a name="step-4-edit-the-active-directory-authentication-library"></a>Steg 4: Redigera Active Directory Authentication Library
Redigera koden internt program i kontexten för autentisering av den Active Directory Authentication Library (ADAL) att inkludera följande text:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Variabler i exempelkoden ska ersättas på följande sätt:

* **Klient-ID** finns i Azure-portalen. Gå till **Azure Active Directory** > **egenskaper** och kopiera den Directory-ID. 
* **Externa URL: en** frontend webbadressen som du angav i Proxy-program. Du hittar det här värdet genom att navigera till den **programproxy** i proxy-appen.
* **App-ID** av den inbyggda appen kan hittas på den **egenskaper** för internt program.
* **Omdirigerings-URI för den inbyggda appen** kan hittas på den **omdirigerings-URI: er** för internt program.

När ADAL har redigerats med följande parametrar måste kunna användarna autentisera till ursprungliga klientappar även när de är utanför företagets nätverk. 

## <a name="next-steps"></a>Nästa steg

Mer information om flödet internt program finns i [internt program till webb-API](../develop/native-app.md)

Läs om hur du konfigurerar [enkel inloggning för Application Proxy](what-is-single-sign-on.md#single- sign-on-methods)
