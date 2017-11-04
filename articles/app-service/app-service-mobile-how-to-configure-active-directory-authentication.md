---
title: "Hur du konfigurerar Azure Active Directory-autentisering för tillämpningsprogrammet Apptjänster"
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
ms.openlocfilehash: 25f0578a9e273c30ecc98af5b66c6dd43305aa03
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Så här konfigurerar du App Service-programmet för att använda Azure Active Directory-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Services att använda Azure Active Directory som en autentiseringsprovider.

## <a name="express"></a>Konfigurera Azure Active Directory med standardinställningar
1. I den [Azure-portalen], navigera till programmet. Klicka på **inställningar**, och sedan **autentisering/auktorisering**.
2. Om autentisering / auktorisering är inte aktiverad, aktivera växeln **på**.
3. Klicka på **Azure Active Directory**, och klicka sedan på **Express** under **hanteringsläge**.
4. Klicka på **OK** registrera programmet i Azure Active Directory. Detta skapar en ny registrering. Om du vill välja en befintlig registrering i stället, klickar du på **väljer en befintlig app** och sök sedan efter namnet på en tidigare skapad registrering i din klient.
   Klicka på registrering för att markera den och klicka på **OK**. Klicka på **OK** på inställningsbladet för Azure Active Directory.
   Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod.
5. (Valfritt) Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiseras av Azure Active Directory, ange **åtgärd att vidta när begäran inte har autentiserats** till **logga in med Azure Active Directory**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Azure Active Directory för autentisering.
6. Klicka på **Spara**.

Du är nu redo att använda Azure Active Directory för autentisering i appen.

## <a name="advanced"></a>(Alternativ metod) manuellt konfigurera Azure Active Directory med avancerade inställningar
Du kan också välja att ange konfigurationsinställningar manuellt. Detta är den bästa lösningen om AAD-klient som du vill använda skiljer sig från den klient som du loggar in i Azure. Om du vill slutföra konfigurationen måste du först skapa en registrering i Azure Active Directory och du måste ange några av registreringsinformation till App Service.

### <a name="register"></a>Registrera ditt program med Azure Active Directory
1. Logga in på den [Azure-portalen], och navigera till programmet. Kopierar du programmets **URL**. Du använder detta för att konfigurera din app i Azure Active Directory.
2. Gå till **Active Directory**och välj den **App registreringar**, klicka på **nya appregistrering** längst upp för att starta en ny appregistrering. 
3. Ange i dialogrutan Skapa program registrering en **namn** för programmet, Välj den **Web App API** Skriv i den **inloggnings-URL** rutan klistra in programmets URL (från steg 1). Klicka på **skapa**.
4. Om några sekunder bör du se den nya Appregistrering som du just har skapat visas.
5. När programmet har lagts till, klicka på registrera programmet namn, klickar du på **inställningar** överst och klicka sedan på **egenskaper** 
6. I den **App-ID URI** klistra in i programmets URL (från steg 1), även i den **URL-Adressen** klistra in programmets URL (från steg 1), klicka på **spara**
7. Nu klickar du på den **Reply URL: er**, redigera den **Reply URL**, klistra in i programmets URL (från steg 1), ändra vilket protokoll som ska du kontrollera att du har **https://** protocol (inte http://) sedan läggas till i slutet av URL-Adressen */.auth/login/aad/callback* . (Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`.) Klicka på **Spara**.   
8.  Nu kopiera den **program-ID** för appen. Ha detta för senare användning. Du behöver detta för att konfigurera ditt webbprogram.
9. Stäng informationsbladet programregistrering. Du bör gå tillbaka till Azure Active Directory App Registration-sammanfattning klickar du på den **slutpunkter** knappen överst och sedan kopiera den **Federation Metadata dokumentet** URL. 
10. Öppna ett nytt webbläsarfönster och navigera till URL: en genom att klistra in och bläddra till XML-sidan. AT upp i dokumentet kommer att vara en **EntityDescriptor** element, bör det finnas en **ID för entiteterna** attribut med formatet `https://sts.windows.net/` följt av ett GUID som är specifika för din klient (kallas en ”klient-ID”). Kopiera det här värdet – det ska fungera som din **utfärdar-URL**. Du ska konfigurera programmet så att det senare.

### <a name="secrets"></a>Lägg till Azure Active Directory-information för ditt program
1. I den [Azure-portalen], navigera till programmet. Klicka på **autentisering/auktorisering**. Om funktionen autentisering/auktorisering inte är aktiverad, aktivera växeln **på**. Klicka på **Azure Active Directory**, under autentiseringsproviders att konfigurera ditt program. (Valfritt) Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod. Välj den **åtgärd att vidta när begäran inte har autentiserats**, anger du **logga in med Azure Active Directory**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Azure Active Directory för autentisering.
2. i Active Directory-autentisering-konfigurationen klickar du på **Avancerat** under **hanteringsläge**. Klistra in program-ID i rutan klient-ID (från steg 8) och klistra in i ID för entiteterna (från steg 10) i utfärdar-URL-värdet. Klicka sedan på **OK**.
3. Klicka på bladet för konfiguration av Active Directory-autentisering **spara**.

Du är nu redo att använda Azure Active Directory för autentisering i appen.

## <a name="optional-configure-a-native-client-application"></a>(Valfritt) Konfigurera en native client-program
Azure Active Directory kan du också registrera interna klienter, vilket ger dig större kontroll över behörigheter mappning. Du behöver den här om du vill utföra inloggningar som använder ett bibliotek som den **Active Directory Authentication Library**.

1. Gå till **Active Directory** i den [klassiska Azure-portalen].
2. Välj din katalog och välj sedan den **program** högst upp. Klicka på **lägga till** längst ned för att skapa en ny appregistrering.
3. Klicka på **Lägg till ett program som min organisation utvecklar**.
4. I guiden Lägg till program och ange en **namn** för ditt program och klicka på den **internt klientprogram** typen. Klicka om du vill fortsätta.
5. I den **omdirigerings-URI** Ange webbplatsens */.auth/login/done* slutpunkten, med hjälp av HTTPS-schema. Det här värdet ska vara liknar *https://contoso.azurewebsites.net/.auth/login/done*. Om du skapar ett Windows-program i stället använda den [paket-SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI: N.
6. När det ursprungliga programmet har lagts till, klickar du på den **konfigurera** fliken. Hitta de **klient-ID** och anteckna värdet.
7. Rulla ned till sidan den **behörigheter för andra program** avsnittet och klicka på **lägga till program**.
8. Sök efter webbprogrammet som du tidigare har registrerat och klicka på plusikonen. Sedan klicka på Sök om du vill stänga dialogrutan. Om webbprogrammet inte kan hitta, navigera till dess registrering och lägga till en ny reply URL (t.ex. den HTTP-versionen av din aktuella URL), klicka på Spara och sedan upprepa stegen - programmet ska visas i listan.
9. Öppna på den nya posten som du just lagt till den **delegerade behörigheter** listrutan och välj **åtkomst (appName)**. Klicka sedan på **Spara**.

Du har nu konfigurerat native client-program som har åtkomst till din App-tjänstprogrammet.

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
[klassiska Azure-portalen]: https://manage.windowsazure.com/
[alternative method]:#advanced
