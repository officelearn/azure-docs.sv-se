---
title: Konfigurera Azure Active Directory autentisering – Azure App Service
description: Lär dig hur du konfigurerar Azure Active Directory autentisering för din App Service-app.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8b4b6549f9553773cc44c311f49befbb3eec9dc9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233093"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Konfigurera App Service-appen att använda Azure Active Directory inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> För tillfället stöds inte AAD v2 (inklusive MSAL) för Azure App Service och Azure Functions.
>

Den här artikeln beskriver hur du konfigurerar Azure App Service att använda Azure Active Directory som autentiseringsprovider.

Vi rekommenderar att du konfigurerar varje App Service app med en egen registrering, så att den har sina egna behörigheter och sitt medgivande. Överväg också att använda separata registrerings program för olika distributions platser. På så sätt undviker du behörighets delning mellan miljöer, så att ett problem i den nya koden som du testar inte påverkar produktionen.

## <a name="express"> </a>Konfigurera med Express inställningar

1. Gå till din App Service-app i [Azure Portal]. Välj **autentisering/auktorisering**i det vänstra navigerings fältet.
2. Om **autentisering/auktorisering** inte är aktiverat väljer du **på**.
3. Välj **Azure Active Directory**och välj sedan **Express** under **hanterings läge**.
4. Välj **OK** för att registrera App Service-appen i Azure Active Directory. Detta skapar en ny app-registrering. Om du vill välja en befintlig app-registrering i stället klickar du på **Välj en befintlig app** och söker sedan efter namnet på en tidigare skapad app-registrering i din klient. Klicka på appens registrering för att välja den och klicka på **OK**. Klicka sedan på **OK** på sidan Azure Active Directory inställningar.
Som standard tillhandahåller App Service autentisering men begränsar inte tillåten åtkomst till ditt webbplats innehåll och API: er. Du måste auktorisera användare i din app-kod.
5. Valfritt Om du vill begränsa åtkomsten till din app till endast användare som autentiserats av Azure Active Directory anger du **åtgärd som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**. Detta kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Azure Active Directory för autentisering.

    > [!NOTE]
    > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program kan du **tillåta anonyma begär Anden (ingen åtgärd)** , med appen manuellt startar inloggningen, enligt beskrivningen [här](overview-authentication-authorization.md#authentication-flow).
6. Klicka på **Spara**.

## <a name="advanced"> </a>Konfigurera med avancerade inställningar

Du kan också ange konfigurations inställningar manuellt, om den Azure Active Directory klient som du vill använda skiljer sig från den klient som du loggar in på Azure. För att slutföra konfigurationen måste du först skapa en registrering i Azure Active Directory och sedan måste du ange en del av registrerings informationen för att App Service.

### <a name="register"> </a>Skapa en app-registrering i Azure AD för din app service-app

När du skapar en app-registrering manuellt noterar du tre delar av information som du behöver senare när du konfigurerar din App Service-app: klient-ID, klient-ID och eventuellt klient hemlighet och program-ID-URI.

1. I [Azure Portal]navigerar du till din app service app och noterar appens **URL**. Du kommer att använda den för att konfigurera din Azure Active Directory app-registrering.
1. I [Azure Portal]väljer du **Active Directory** > **Appregistreringar** > **ny registrering**på menyn till vänster. 
1. På sidan **Registrera ett program** anger du ett **namn** för din app-registrering.
1. I omdirigerings- **URI**väljer du **webb** och anger URL: en för din app service- `/.auth/login/aad/callback`app och lägger till sökvägen. Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Välj sedan **Skapa**.
1. När appens registrering har skapats kopierar du **program-ID: t** och **katalogen (klient)-ID:** t för senare.
1. Välj **anpassning**. Ange URL: en för din App Service-app på **Start sidans URL**och välj **Spara**.
1. Välj **exponera en API** > -**uppsättning**. Klistra in URL: en för din App Service-app och välj **Spara**.

    > [!NOTE]
    > Det här värdet är **program-ID-URI: n** för appens registrering. Om du vill ha en frontend-webbapp för att komma åt ett Server dels-API, till exempel och du vill att Server delen för att uttryckligen bevilja åtkomst till klient delen, behöver du **program-ID-URI** för *klient delen* när du konfigurerar app service app-resursen för < C2 > Serverdel.
1. Välj **Lägg till omfång**. I **omfångs namn**skriver du *user_impersonation*. I text rutorna anger du namn och beskrivning för medgivande omfånget som du vill att användarna ska se på sidan medgivande, till exempel *åtkomst till min app*. När du är färdig klickar du på **Lägg till omfattning**.
1. Valfritt Om du vill skapa en klient hemlighet väljer du **certifikat & hemligheter** > **ny klient hemlighet** > **Lägg till**. Kopiera klientens hemliga värde som visas på sidan. När du har navigerat visas den inte igen.
1. Valfritt Om du vill lägga till flera svars- **URL: er**väljer du **autentisering** i menyn.

### <a name="secrets"> </a>Lägg till Azure Active Directory information till din app service-app

1. Gå till din App Service-app i [Azure Portal]. Välj **autentisering/auktorisering**på den vänstra menyn. Om funktionen autentisering/auktorisering inte är aktive rad väljer du **på**. 
1. Valfritt Som standard tillåter App Service autentisering oautentiserad åtkomst till din app. Om du vill framtvinga användarautentisering anger du **åtgärden som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**.
1. Under autentiseringsproviders väljer du **Azure Active Directory**.
1. I **hanterings läge**väljer du **avancerat** och konfigurerar app service autentisering enligt följande tabell:

    |Fält|Beskrivning|
    |-|-|
    |Klientorganisations-ID| Använd **program-ID: t (klient)** för appens registrering. |
    |Utfärdar-ID| Använd `https://login.microsoftonline.com/<tenant-id>`och  *Ersätt\<klient-ID >* med **katalog-ID** för appens registrering. |
    |Klient hemlighet (valfritt)| Använd den klient hemlighet som du genererade i appens registrering.|
    |Tillåtna tokenmålgrupper| Om det här är en *backend-* app och du vill tillåta autentiseringstoken från en frontend-app lägger du till **program-ID-URI** för *klient delen* här. |
1. Välj **OK**och välj sedan **Spara**.

Du är nu redo att använda Azure Active Directory för autentisering i din App Service-app.

## <a name="configure-a-native-client-application"></a>Konfigurera ett internt klient program
Du kan registrera interna klienter om du vill utföra inloggningar med hjälp av ett klient bibliotek som **Active Directory-autentiseringsbibliotek**.

1. I [Azure Portal]väljer du **Active Directory** > **Appregistreringar** > **ny registrering**på menyn till vänster. 
1. På sidan **Registrera ett program** anger du ett **namn** för din app-registrering.
1. I omdirigerings- **URI**väljer du **offentlig klient (mobil & Station ära datorer)** och anger URL: en för din `/.auth/login/aad/callback`App Service-app och lägger till sökvägen. Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Välj sedan **Skapa**.

    > [!NOTE]
    > Använd [paket-sid](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI i stället för ett Windows-program.
1. När appens registrering har skapats kopierar du värdet för **program-ID (klient)** .
1. På den vänstra menyn väljer du **API-behörigheter** > **Lägg till en behörighet** > **Mina API: er**.
1. Välj den app-registrering som du skapade tidigare för din App Service-app. Om du inte ser appens registrering kontrollerar du att du har lagt till **user_impersonation** -omfånget i [skapa en app-registrering i Azure AD för din app service-app](#register).
1. Välj **user_impersonation** och klicka på **Lägg till behörigheter**.

Nu har du konfigurerat ett internt klient program som har åtkomst till din App Service-app.

## <a name="related-content"> </a>Relaterat innehåll

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
