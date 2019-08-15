---
title: Konfigurera Azure Active Directory autentisering – Azure App Service
description: Lär dig hur du konfigurerar Azure Active Directory autentisering för ditt App Services-program.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: a77a41500a9c22aa25d3de396e73a5b2e4c0c419
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033886"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Konfigurera App Service-appen att använda Azure Active Directory inloggning

> [!NOTE]
> För tillfället stöds inte AAD v2 (inklusive MSAL) för Azure App Services och Azure Functions. Kom tillbaka efter uppdateringar.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Services att använda Azure Active Directory som autentiseringsprovider.

## <a name="express"> </a>Konfigurera med Express inställningar

1. Gå till din App Service-app i [Azure Portal]. Välj **autentisering/auktorisering**i det vänstra navigerings fältet.
2. Om **autentisering/auktorisering** inte är aktiverat väljer du **på**.
3. Välj **Azure Active Directory**och välj sedan **Express** under **hanterings läge**.
4. Välj **OK** för att registrera App Service-appen i Azure Active Directory. Detta skapar en ny app-registrering. Om du vill välja en befintlig app-registrering i stället klickar du på **Välj en befintlig app** och söker sedan efter namnet på en tidigare skapad app-registrering i din klient.
   Klicka på appens registrering för att välja den och klicka på **OK**. Klicka sedan på **OK** på sidan Azure Active Directory inställningar.
   Som standard tillhandahåller App Service autentisering men begränsar inte tillåten åtkomst till ditt webbplats innehåll och API: er. Du måste auktorisera användare i din app-kod.
5. Valfritt Om du vill begränsa åtkomsten till din webbplats till endast användare som autentiserats av Azure Active Directory anger du **åtgärd som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**. Detta kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Azure Active Directory för autentisering.

> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program kan du **tillåta anonyma begär Anden (ingen åtgärd)** , med appen manuellt startar inloggningen, enligt beskrivningen [här](overview-authentication-authorization.md#authentication-flow).

6. Klicka på **Spara**.

## <a name="advanced"> </a>Konfigurera med avancerade inställningar

Du kan också ange konfigurations inställningar manuellt. Detta är den bästa lösningen om den Azure Active Directory klient som du vill använda skiljer sig från den klient som du loggar in på Azure. För att slutföra konfigurationen måste du först skapa en registrering i Azure Active Directory och sedan måste du ange en del av registrerings informationen för att App Service.

### <a name="register"> </a>Registrera din app service-App med Azure Active Directory

1. Logga in på [Azure Portal]och navigera till din app service-app. Kopiera din app- **URL**. Du kommer att använda den för att konfigurera registrering av Azure Active Directory-appen.
2. Gå till **Active Directory**och välj **Appregistreringar**och klicka sedan på **ny program registrering** överst för att starta en ny app-registrering. 
3. På sidan **skapa** anger du ett **namn** för din app-registrering, väljer **webb program/API** -typ i rutan **inloggnings-URL** och klistrar in programmets URL (från steg 1). Klicka sedan på för att **skapa**.
4. Om några sekunder bör du se den nya app-registreringen som du nyss skapade.
5. När du har lagt till app-registreringen klickar du på registrerings namnet för appen, klickar på **Inställningar** högst upp och klickar sedan på **Egenskaper** 
6. I rutan **app-ID-URI** klistrar du in i program-URL: en (från steg 1), även på **Start sidans URL** klistra in i programmets URL (från steg 1) och klicka sedan på **Spara**
7. Klicka på svars-URL: **erna**, redigera svars- **URL**: en, klistra in URL: en för programmet (från steg 1) och Lägg sedan till den i slutet av URL `https://contoso.azurewebsites.net/.auth/login/aad/callback`: en, */.auth/login/AAD/callback* (till exempel). Klicka på **Spara**.

   > [!NOTE]
   > Du kan använda samma app-registrering för flera domäner genom att lägga till ytterligare svars- **URL: er**. Se till att modellera varje App Service instans med en egen registrering, så att den har sina egna behörigheter och sitt medgivande. Överväg också att använda separata registrerings program för olika plats platser. Detta är för att undvika att behörigheter delas mellan miljöer, så att en bugg i ny kod som du testar inte påverkar produktionen.
    
8. I det här läget kopierar du appens **program-ID** . Behåll den för senare användning. Du behöver den för att konfigurera din App Service-app.
9. Stäng sidan **registrerad app** . På sidan **Appregistreringar** klickar du på knappen **slut punkter** överst och kopierar sedan URL-adressen för **WS-Federation-inloggningen** , men tar bort `/wsfed` slut från URL: en. Slut resultatet bör se ut `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`. Domän namnet kan skilja sig åt för ett suveränt moln. Detta fungerar som utfärdar-URL för senare.

### <a name="secrets"> </a>Lägg till Azure Active Directory information till din app service-app

1. Gå tillbaka till din App Service-app i [Azure Portal]. Klicka på **autentisering/auktorisering**. Om funktionen autentisering/auktorisering inte är aktive rad aktiverar du växeln till **på**. Klicka på **Azure Active Directory**under autentiseringsproviders för att konfigurera din app.

    Valfritt Som standard tillhandahåller App Service autentisering men begränsar inte tillåten åtkomst till ditt webbplats innehåll och API: er. Du måste auktorisera användare i din app-kod. Ange **åtgärd som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**. Det här alternativet kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Azure Active Directory för autentisering.
2. I Active Directory konfiguration av autentisering klickar du på **Avancerat** under **hanterings läge**. Klistra in program-ID: t i rutan klient-ID (från steg 8) och klistra in URL: en (från steg 9) i URL-värdet för utfärdaren. Klicka sedan på **OK**.
3. På sidan Active Directory autentiserings konfiguration klickar du på **Spara**.

Du är nu redo att använda Azure Active Directory för autentisering i din App Service-app.

## <a name="configure-a-native-client-application"></a>Konfigurera ett internt klient program
Du kan registrera interna klienter, vilket ger större kontroll över behörighets mappningen. Du behöver detta om du vill utföra inloggningar med hjälp av ett klient bibliotek som **Active Directory-autentiseringsbibliotek**.

1. Navigera till **Azure Active Directory** i [Azure Portal].
2. I det vänstra navigerings fönstret väljer du **Appregistreringar**. Klicka på **ny app-registrering** överst.
4. På sidan **skapa** anger du ett **namn** för din app-registrering. Välj **inbyggd** i **program typ**.
5. I rutan omdirigerings- **URI** anger du webbplatsens */.auth/login/Done* -slutpunkt med https-schemat. Det här värdet bör likna *https://contoso.azurewebsites.net/.auth/login/done* . Om du skapar ett Windows-program ska du i stället använda [paket-sid](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI.
5. Klicka på **Skapa**.
6. När du har lagt till app-registreringen väljer du den för att öppna den. Hitta **program-ID: t** och anteckna det här värdet.
7. Klicka på **alla inställningar** > **nödvändiga behörigheter** > **Lägg till** > **Välj ett API**.
8. Skriv namnet på den App Service app som du registrerade tidigare för att söka efter den, markera den och klicka på **Välj**.
9. Välj **åtkomst \<APP_NAME >** . Klicka sedan på **Välj**. Klicka sedan på **Klar**.

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
