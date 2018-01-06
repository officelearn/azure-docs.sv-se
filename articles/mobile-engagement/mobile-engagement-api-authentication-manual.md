---
title: Autentisera med Mobile Engagement REST API - manuell installation
description: "Beskriver hur du manuellt konfigurera autentisering för Mobile Engagement REST API: er"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autentisera med Mobile Engagement REST API - manuell installation
Den här dokumentationen är ett tillägg dokumentationen till [autentisera med Mobile Engagement REST API: er](mobile-engagement-api-authentication.md). Kontrollera att du läser den först för att hämta kontext.
Beskriver ett annat sätt att göra enstaka installationen för att konfigurera din autentisering för Mobile Engagement REST API: erna med Azure-portalen.

> [!NOTE]
> Följande instruktioner är baserade på den här [guide för Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) och anpassas för vad som krävs för autentisering för Mobile Engagement API: er. Därför finns det om du vill förstå stegen nedan i detalj.

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com/).
2. Välj **Active Directory** i den vänstra rutan.

     ![Välj Active Directory][1]

3. Om du vill visa program i din katalog klickar du på **App registreringar**.

     ![Visa program][3]

4. Klicka på **nya appregistrering**.

     ![Lägg till program][4]

5. Fyll i namnet på programmet och lämna typ av program som **webb-app/API** och klicka på Nästa. Du kan ange eventuella dummy URL: er för **SIGN-ON-URL**: de används inte för det här scenariot och URL: er själva inte valideras.
6. När du har gjort, har en Azure AD-app med det namn du angett. Det är din **AD\_APP\_namn**, Lägg märke till den.

     ![appnamn][8]

7. Klicka på namnet på appen.
8. Hitta **program-ID**, notera den, är det att klient-ID som ska användas som **klienten\_ID** för din API-anrop.

     ![Konfigurera appen][10]

9. Hitta de **nycklar** avsnittet till höger.

     ![Konfigurera appen][11]

10. Skapa en ny nyckel och kopiera den omedelbart och spara den för användning. Det visas aldrig igen.

     ![Konfigurera appen][12]

    > [!IMPORTANT]
    > Den här nyckeln upphör att gälla i slutet av den varaktighet som du har angett så se till att förnya det när dessa annars API-autentisering inte fungerar längre. Du kan också ta bort och återskapa den här nyckeln om du tror att den har komprometterats.
    >
    >
11. Klicka på **slutpunkter** längst upp på sidan och kopiera den **OAUTH 2.0-TOKEN för SLUTPUNKT**.

    ![][14]

16. Den här slutpunkten kommer att vara i formatet där GUID i URL-Adressen är ditt **TENANT_ID** så att anteckna den:`https://login.microsoftonline.com/<GUID>/oauth2/token`
17. Nu ska vi fortsätta med att konfigurera behörigheterna för den här appen. För detta behöver du öppna den [Azure-portalen](https://portal.azure.com). 
18. Klicka på **resursgrupper** och Sök efter den **Mobile Engagement** resursgruppen.

    ![][15]

19. Klicka på den **Mobile Engagement** resurs gruppen och navigera till den **inställningar** avsnittet här.

    ![][16]

20. Klicka på **användare** i inställningarna för avsnittet och klicka sedan på **Lägg till** lägga till en användare.

    ![][17]

21. Klicka på **Välj en roll**.

    ![][18]

22. Klicka på **ägare**.

    ![][19]

23. Sök efter namnet på ditt program **AD\_APP\_namn** i sökrutan. Du kommer inte se det som standard här. När du har hittat markerar du den och klicka på **Välj** längst ned i avsnittet.

    ![][20]

24. På den **Lägg till åtkomst** avsnittet den visas som **1 användare, 0 grupper**. Klicka på **OK** på det här avsnittet om du vill bekräfta ändringen.

    ![][21]

Nu har du slutfört de nödvändiga Azure AD-konfiguration och du är klar att anropa API: erna.

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



