---
title: Konfigurera Azure Active Directory-autentisering för App Services-appen
description: Lär dig mer om att konfigurera Azure Active Directory-autentisering för App Services-appen.
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
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 0c2ae7e4cf0b19ab9e1c276504892a2a8aaa8dfc
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297391"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Konfigurera App Service-appen för att använda Azure Active Directory-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Services för att använda Azure Active Directory som en autentiseringsprovider.

## <a name="express"> </a>Konfigurera Azure Active Directory med standardinställningar
1. I den [Azure Portal], gå till din App Service-app. I det vänstra navigeringsfönstret väljer **autentisering / auktorisering**.
2. Om **autentisering / auktorisering** är inte aktiverad, väljer **på**.
3. Välj **Azure Active Directory**, och välj sedan **Express** under **hanteringsläge**.
4. Välj **OK** att registrera App Service-app i Azure Active Directory. Detta skapar en ny appregistrering. Om du vill välja en befintlig appregistrering i stället, klickar du på **väljer en befintlig app** och söker sedan efter namnet på en tidigare skapad appregistreringen i din klient.
   Klicka på appregistreringen för att markera den och klicka på **OK**. Klicka sedan på **OK** på inställningssidan för Azure Active Directory.
   Som standard, App Service ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din kod.
5. (Valfritt) Om du vill begränsa åtkomsten till din plats för att endast användare som autentiseras av Azure Active Directory, ange **åtgärd att vidta när begäran inte har autentiserats** till **logga in med Azure Active Directory**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Azure Active Directory för autentisering.
6. Klicka på **Spara**.

Du är nu redo att använda Azure Active Directory för autentisering i din App Service-app.

## <a name="advanced"> </a>(Alternativ metod) Manuellt konfigurera Azure Active Directory med avancerade inställningar
Du kan också välja att ange konfigurationsinställningar manuellt. Detta är det en bättre lösningen om AAD-klient som du vill använda skiljer sig från den klient som du loggar in på Azure. Om du vill slutföra konfigurationen, måste du först skapa en registrering i Azure Active Directory och du måste ange några av de här registreringsinformationen till App Service.

### <a name="register"> </a>Registrera din App Service-app med Azure Active Directory
1. Logga in på den [Azure Portal], och gå till App Service-appen. Kopiera din app **URL**. Du kommer använda detta för att konfigurera din Azure Active Directory-appregistrering.
2. Gå till **Active Directory**och välj sedan den **appregistreringar**, klicka sedan på **ny programregistrering** överst för att starta en ny appregistrering. 
3. I den **skapa** anger en **namn** för din appregistrering, väljer du den **Webbapp / API** skriver i den **inloggnings-URL** rutan klistra in den programmets URL (från steg 1). Klicka sedan på att **skapa**.
4. Du bör se den nya appregistreringen som du nyss skapade på några sekunder.
5. När appregistreringen har lagts till, klicka på namnet på registrering, klickar du på **inställningar** överst, klicka sedan på **egenskaper** 
6. I den **Appidentitets-URI** rutan, klistra in i programmets URL (från steg 1), även i den **URL-Adressen** klistra in program-URL: en (från steg 1), klicka sedan på **spara**
7. Nu klickar du på den **Svarswebbadresser**, redigera den **svars-URL**, klistra in i programmets URL (från steg 1), ändra vilket protokoll som ska kontrollera att du har **https://** protocol (inte http://) sedan läggas till i slutet av URL: en, */.auth/login/aad/callback* (till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Klicka på **Spara**.   
8.  Nu kan kopiera den **program-ID** för appen. Spara den för senare bruk. Du behöver den för att konfigurera din App Service-app.
9. Stäng den **registrerad app** sidan. På den **appregistreringar** klickar du på den **slutpunkter** knappen överst, kopiera den **Federationsmetadatadokumentet** URL: en. 
10. Öppna ett nytt webbläsarfönster och gå till URL: en genom att klistra in och bläddra till XML-sidan. Överst i dokumentet är en **EntityDescriptor** element. Hitta den **entityID** attributet och kopiera värdet. Den fungerar som din **utfärdar-URL**. Du vill konfigurera ditt program för senare användning.

### <a name="secrets"> </a>Lägga till Azure Active Directory-information i din App Service-app
1. I den [Azure Portal], gå till din App Service-app. Klicka på **autentisering/auktorisering**. Autentisering/auktorisering-funktionen inte är aktiverad, aktivera växeln **på**. Klicka på **Azure Active Directory**, under autentiseringsproviders, konfigurerar du din app. (Valfritt) Som standard, App Service ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din kod. Ange **åtgärd att vidta när begäran inte har autentiserats** till **logga in med Azure Active Directory**. Det här alternativet kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Azure Active Directory för autentisering.
2. I Active Directory-autentisering-konfigurationen klickar du på **Avancerat** under **hanteringsläge**. Klistra in program-ID i rutan klient-ID (från steg 8) och klistra in entityId (från steg 10) till utfärdar-URL-värde. Klicka sedan på **OK**.
3. På konfigurationssidan för Active Directory-autentisering klickar du på **spara**.

Du är nu redo att använda Azure Active Directory för autentisering i din App Service-app.

## <a name="optional-configure-a-native-client-application"></a>(Valfritt) Konfigurera ett native client-program
Azure Active Directory kan du också registrera interna klienter som ger större kontroll över behörigheter för mappar. Du behöver detta om du vill utföra inloggningar som använder ett bibliotek som den **Active Directory Authentication Library**.

1. Gå till **Azure Active Directory** i den [Azure Portal].
2. I det vänstra navigeringsfönstret väljer **appregistreringar**. Klicka på **ny appregistrering** högst upp.
4. I den **skapa** anger en **namn** för appregistrering av din. Välj **interna** i **programtyp**.
5. I den **omdirigerings-URI** Ange webbplatsens */.auth/login/done* slutpunkten, med hjälp av HTTPS-schema. Det här värdet ska vara liknar *https://contoso.azurewebsites.net/.auth/login/done*. Om du skapar ett Windows-program, i stället använda den [paketera SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI: N.
5. Klicka på **Skapa**.
6. När appregistreringen har lagts till, väljer du den för att öppna den. Hitta den **program-ID** och anteckna det här värdet.
7. Klicka på **alla inställningar** > **nödvändiga behörigheter** > **Lägg till** > **Välj en API**.
8. Skriv namnet på den App Service-app som du registrerade tidigare att söka efter den, och sedan markera den och klicka på **Välj**. 
9. Välj **åtkomst \<app_name >**. Klicka sedan på **Välj**. Klicka sedan på **Klar**.

Du har nu konfigurerat ett internt klientprogram som kan komma åt din App Service-app.

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
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
