---
title: 'Autentisera med Mobile Engagement REST API: er: manuell installation'
description: 'Beskriver hur du manuellt konfigurera autentisering för Mobile Engagement REST API: er'
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0d71908b34ddf8313aa45014420c9e63a00078c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autentisera med Mobile Engagement REST API - manuell installation
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Den här dokumentationen är ett tillägg till [autentisera med Mobile Engagement REST API: er](mobile-engagement-api-authentication.md). Kontrollera att den artikeln först för att förstå kontexten. Här beskrivs också ett annat sätt att göra en autentisering installationen för Mobile Engagement REST-API: er med hjälp av Azure-portalen.

> [!NOTE]
> Följande instruktioner är baserade på [handboken Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). De är anpassade för autentiseringskraven för Mobile Engagement-API: er. Hänvisa till dem om du vill förstå följande steg i detalj.

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com/).
2. Välj **Active Directory** i den vänstra rutan.

   ![Välj Active Directory][1]

3. Om du vill visa program i din katalog, Välj **App registreringar**.

   ![Visa program][3]

4. Välj **nya appregistrering**.

   ![Lägga till ett program][4]

5. Fyll i namnet på programmet. Lämna typ av program som **webb-app/API**, och välj sedan den **nästa** knappen. Du kan ange eventuella dummy URL: er för **SIGN-ON-URL: en**. De används inte för det här scenariot och URL: er själva valideras inte.

   När du är klar har en Azure Active Directory (Azure AD)-app med det namn du angett. Det är din **AD\_APP\_namn**, så bör du anteckna den.

   ![Appnamn][8]

7. Välj namnet på appen.

8. Hitta **program-ID** och anteckna den. Det är klient-ID som ska användas som **klienten\_ID** för din API-anrop.

   ![Hitta program-ID][10]

9. Hitta de **nycklar** avsnittet till höger.

   ![Nycklar avsnitt][11]

10. Skapa en ny nyckel och kopiera den direkt. Det är inte visas igen.

    ![Nycklar rutan med viktig information][12]

    > [!IMPORTANT]
    > Den här nyckeln upphör att gälla i slutet av den varaktighet som du angav. Se till att förnya det när dessa, annars API-autentisering fungerar inte längre. Om du tror att den här nyckeln har komprometterats bör du ta bort och skapa den igen.
    >
    
11. Välj den **slutpunkter** längst upp på sidan. Kopiera den **OAUTH 2.0-TOKEN för SLUTPUNKT**.

    ![Kopiera slutpunkten][14]

16. Den här slutpunkten är i följande format, där GUID i URL-Adressen är ditt **TENANT_ID**: `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. Därefter måste konfigurera du behörigheter på den här appen. Starta processen, gå till den [Azure-portalen](https://portal.azure.com).

18. Välj **resursgrupper**, och leta reda på den **MobileEngagement** resursgruppen.

    ![Find MobileEngagement][15]

19. Välj den **MobileEngagement** resurs gruppen och välj sedan **alla inställningar**.

    ![Gå till inställningar för MobileEngagement][16]

20. Välj **användare** i den **inställningar** avsnitt. Om du vill lägga till en användare väljer **Lägg till**.

    ![Lägga till en användare][17]

21. Klicka på **Välj en roll**.

    ![Välj en roll][18]

22. Välj **ägare**.

    ![Välj en ägare som rollen][19]

23. Sök efter namnet på ditt program **AD\_APP\_namn**, i sökrutan. Det här namnet är inte här som standard. När du har hittat väljer du den. Klicka på **Välj** längst ned i avsnittet.

    ![Välj namnet][20]

24. I den **Lägg till åtkomst** avsnittet visas den som **1 användare, 0 grupper**. Om du vill bekräfta ändringen, Välj **OK**.

    ![Bekräfta tillagda användare][21]

Nu har du slutfört de nödvändiga Azure AD-konfiguration och är inställd på att anropa API: erna.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



