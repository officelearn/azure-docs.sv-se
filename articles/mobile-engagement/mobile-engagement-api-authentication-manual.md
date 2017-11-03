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
ms.openlocfilehash: 9d6132e1a01be489b8e8e28a0219cf8a0b50b318
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autentisera med Mobile Engagement REST API - manuell installation
Detta är ett tillägg dokumentationen till [autentisera med Mobile Engagement REST API: er](mobile-engagement-api-authentication.md). Kontrollera att du läser den först för att hämta kontext. Här beskrivs ett annat sätt att göra en installationen för att konfigurera din autentisering för Mobile Engagement REST API: erna i Azure Portal. 

> [!NOTE]
> Anvisningarna nedan baseras på detta [guide för Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) och anpassas för vad som krävs för autentisering för Mobile Engagement API: er. Därför finns det om du vill förstå stegen nedan i detalj. 
> 
> 

1. Logga in på ditt Azure-konto via den [klassiska portalen](https://manage.windowsazure.com/).
2. Välj **Active Directory** i den vänstra rutan.
   
     ![Välj Active Directory][1]
3. Välj den **standard Active Directory** i Azure-portalen. 
   
     ![Välj katalog][2]
   
   > [!IMPORTANT]
   > Den här metoden fungerar bara när du arbetar i standard Active Directory för ditt konto och fungerar inte om du vill göra detta i en Active Directory som du har skapat i ditt konto. 
   > 
   > 
4. Om du vill visa program i din katalog klickar du på **program**.
   
     ![Visa program][3]
5. Klicka på **lägga till**. 
   
     ![Lägg till program][4]
6. Klicka på **Lägg till ett program som min organisation utvecklar**
   
     ![nytt program][5]
7. Fyll i namnet på programmet och välj typ av program som **WEB APPLICATION och/eller webb-API** och klicka på Nästa.
   
     ![namn på program][6]
8. Du kan ange eventuella dummy URL: er för **SIGN-ON-URL** och **APP-ID URI**. Används inte för vårt scenario och URL: er själva valideras inte.  
   
     ![Egenskaper för program][7]
9. I slutet av det här har du en AAD-app med det namn du angav tidigare ungefär så här. Det här är din **AD\_APP\_namn** och anteckna den.  
   
     ![Appnamn][8]
10. Klicka på namnet på appen och klicka på **konfigurera**.
    
      ![Konfigurera appen][9]
11. Anteckna klient-ID som ska användas som **klienten\_ID** för din API-anrop. 
    
     ![Konfigurera appen][10]
12. Rulla ned till den **nycklar** avsnittet och lägga till en nyckel med helst 2 år (upphör att gälla) varaktighet och på **spara**. 
    
     ![Konfigurera appen][11]
13. Kopiera värdet som visas för nyckeln som det visas bara nu och lagras inte så visas inte någonsin igen omedelbart. Om du tappar bort har att generera en ny nyckel. Det här är den **CLIENT_SECRET** för din API-anrop. 
    
     ![Konfigurera appen][12]
    
    > [!IMPORTANT]
    > Den här nyckeln upphör att gälla i slutet av den varaktighet som du har angett så se till att förnya det när dessa annars API-autentisering inte fungerar längre. Du kan också ta bort och återskapa den här nyckeln om du tror att den har komprometterats.
    > 
    > 
14. Klicka på **visa SLUTPUNKTER** knappen nu som öppnas i **App slutpunkter** dialogrutan. 
    
    ![][13]
15. Kopiera från dialogrutan App slutpunkter i **OAUTH 2.0-TOKEN för SLUTPUNKT**. 
    
    ![][14]
16. Den här slutpunkten kommer att vara i formatet där GUID i URL-Adressen är ditt **TENANT_ID** så att anteckna den: 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. Nu ska vi fortsätta med att konfigurera behörigheterna för den här appen. För detta behöver du öppna den [Azure-portalen](https://portal.azure.com). 
18. Klicka på **resursgrupper** och Sök efter den **Mobile Engagement** resursgruppen.  
    
    ![][15]
19. Klicka på den **Mobile Engagement** resurs gruppen och navigera till den **inställningar** här bladet. 
    
    ![][16]
20. Klicka på **användare** i bladet inställningar och sedan klicka på **Lägg till** lägga till en användare. 
    
    ![][17]
21. Klicka på **Välj en roll**
    
    ![][18]
22. Klicka på **ägare**
    
    ![][19]
23. Sök efter namnet på ditt program **AD\_APP\_namn** i sökrutan. Du kommer inte se det som standard här. När du har hittat markerar du den och klicka på **Välj** längst ned på bladet. 
    
    ![][20]
24. På den **Lägg till åtkomst** bladet den visas som **1 användare, 0 grupper**. Klicka på **OK** på det här bladet för att bekräfta ändringen. 
    
    ![][21]

Nu har du slutfört den AAD konfigurationen och du är inställda att anropa API: erna. 

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
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
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



