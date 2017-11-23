---
title: "Konfigurera Azure Active Directory-autentisering för tillämpningsprogrammet Apptjänster"
description: "Lär dig hur du konfigurerar Azure Active Directory-autentisering för ditt program med App-tjänster."
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 990fab9aeea71b8cf344b9a49a5ed438db6663c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Konfigurera App Service-appen för att använda Azure Active Directory-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Services att använda Azure Active Directory som en autentiseringsprovider.

## <a name="express"></a>Konfigurera Azure Active Directory med standardinställningar
1. I den [Azure-portalen], navigera till din Apptjänst-app. I det vänstra navigeringsfönstret väljer **autentisering / auktorisering**.
2. Om **autentisering / auktorisering** är inte aktiverad, Välj **på**.
3. Välj **Azure Active Directory**, och välj sedan **Express** under **hanteringsläge**.
4. Välj **OK** att registrera Apptjänst-app i Azure Active Directory. Detta skapar en ny appregistrering. Om du vill välja en befintlig appregistrering i stället, klickar du på **väljer en befintlig app** och sök sedan efter namnet på en tidigare skapad appregistrering i din klient.
   Klicka på appregistrering markerar du den och klicka sedan på **OK**. Klicka på **OK** på inställningssidan för Azure Active Directory.
   Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod.
5. (Valfritt) Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiseras av Azure Active Directory, ange **åtgärd att vidta när begäran inte har autentiserats** till **logga in med Azure Active Directory**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Azure Active Directory för autentisering.
6. Klicka på **Spara**.

Du är nu redo att använda Azure Active Directory för autentisering i Apptjänst-app.

## <a name="advanced"></a>(Alternativ metod) manuellt konfigurera Azure Active Directory med avancerade inställningar
Du kan också välja att ange konfigurationsinställningar manuellt. Detta är den bästa lösningen om AAD-klient som du vill använda skiljer sig från den klient som du loggar in i Azure. Om du vill slutföra konfigurationen måste du först skapa en registrering i Azure Active Directory och du måste ange några av registreringsinformation till App Service.

### <a name="register"></a>Registrera din Apptjänst-app med Azure Active Directory
1. Logga in på den [Azure-portalen], och navigera till din Apptjänst-app. Kopiera appen **URL**. Du använder detta för att konfigurera Azure Active Directory app registreringen.
2. Gå till **Active Directory**och välj den **App registreringar**, klicka på **nya appregistrering** längst upp för att starta en ny appregistrering. 
3. I den **skapa** anger en **namn** registreringen appen, Välj den **Web App / API** Skriv i den **inloggnings-URL** rutan klistra in den programmets URL (från steg 1). Klicka på **skapa**.
4. Du bör se den nya app registreringen som du just har skapat några sekunder.
5. När appregistrering har lagts till, klicka på namnet på appen registrering, klickar du på **inställningar** överst och klicka sedan på **egenskaper** 
6. I den **App-ID URI** klistra in i programmets URL (från steg 1), även i den **URL-Adressen** klistra in programmets URL (från steg 1), klicka på **spara**
7. Nu klickar du på den **Reply URL: er**, redigera den **Reply URL**, klistra in i programmets URL (från steg 1), ändra vilket protokoll som ska du kontrollera att du har **https://** protocol (inte http://) sedan läggas till i slutet av URL-Adressen */.auth/login/aad/callback* (till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Klicka på **Spara**.   
8.  Nu kopiera den **program-ID** för appen. Spara den för senare användning. Du behöver det för att konfigurera din App Service-appen.
9. Stäng den **registrerad app** sidan. På den **App registreringar** klickar du på den **slutpunkter** knappen överst och sedan kopiera den **Federation Metadata dokumentet** URL. 
10. Öppna ett nytt webbläsarfönster och navigera till URL: en genom att klistra in och bläddra till XML-sidan. Längst upp i dokumentet är ett **EntityDescriptor** element, bör det finnas en **ID för entiteterna** attribut med formatet `https://sts.windows.net/` följt av ett GUID som är specifika för din klient (kallas en ”klient-ID”). Kopiera det här värdet – den fungerar som din **utfärdar-URL**. Du konfigurerar ditt program för senare användning.

### <a name="secrets"></a>Lägg till Azure Active Directory-information till din Apptjänst-app
1. I den [Azure-portalen], navigera till din Apptjänst-app. Klicka på **autentisering/auktorisering**. Om funktionen autentisering/auktorisering inte är aktiverad, aktivera växeln **på**. Klicka på **Azure Active Directory**, under autentiseringsproviders, konfigurera din app. (Valfritt) Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod. Ange **åtgärd att vidta när begäran inte har autentiserats** till **logga in med Azure Active Directory**. Det här alternativet kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Azure Active Directory för autentisering.
2. i Active Directory-autentisering-konfigurationen klickar du på **Avancerat** under **hanteringsläge**. Klistra in program-ID i rutan klient-ID (från steg 8) och klistra in i ID för entiteterna (från steg 10) i utfärdar-URL-värdet. Klicka sedan på **OK**.
3. På sidan för konfiguration av Active Directory-autentisering **spara**.

Du är nu redo att använda Azure Active Directory för autentisering i Apptjänst-app.

## <a name="optional-configure-a-native-client-application"></a>(Valfritt) Konfigurera en native client-program
Azure Active Directory kan du också registrera interna klienter, vilket ger dig större kontroll över behörigheter mappning. Du behöver den här om du vill utföra inloggningar som använder ett bibliotek som den **Active Directory Authentication Library**.

1. Gå till **Azure Active Directory** i den [Azure-portalen].
2. I det vänstra navigeringsfönstret väljer **App registreringar**. Klicka på **nya appregistrering** längst upp.
4. I den **skapa** anger en **namn** för appregistrering av din. Välj **interna** i **programtyp**.
5. I den **omdirigerings-URI** Ange webbplatsens */.auth/login/done* slutpunkten, med hjälp av HTTPS-schema. Det här värdet ska vara liknar *https://contoso.azurewebsites.net/.auth/login/done*. Om du skapar ett Windows-program i stället använda den [paket-SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI: N.
5. Klicka på **Skapa**.
6. När appen registreringen har lagts till, väljer du den så att den öppnas. Hitta de **program-ID** och anteckna värdet.
7. Klicka på **alla inställningar** > **nödvändiga behörigheter** > **Lägg till** > **väljer en API**.
8. Skriv namnet på den Apptjänst-app som du tidigare har registrerat att söka efter den, sedan markerar du den och klicka på **Välj**. 
9. Välj **åtkomst \<appnamn >**. Klicka på **Välj**. Klicka sedan på **Klar**.

Du har nu konfigurerat native client-program som har åtkomst till din Apptjänst-app.

## <a name="related-content"></a>Relaterat innehåll
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

[Azure-portalen]: https://portal.azure.com/
[alternative method]:#advanced
