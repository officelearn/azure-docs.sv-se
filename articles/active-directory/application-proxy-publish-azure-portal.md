---
title: Publicera appar med Azure AD Application Proxy | Microsoft Docs
description: Publicera lokala program till molnet med Azure AD Application Proxy på Azure-portalen.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1bf72b450747e98e254db77514fc7a902bbe70cc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicera program med Azure AD Application Proxy

Azure Active Directory (AD) Application Proxy hjälper dig att stöd för fjärranvändare genom att publicera lokala program som kan nås via internet. Du kan publicera programmen via Azure portal för att tillhandahålla säker fjärråtkomst utanför nätverket.

Den här artikeln vägleder dig genom stegen för att publicera en lokal app med Application Proxy. När du har slutfört den här artikeln kommer användarna att kunna fjärransluta till din app. Och du kan redo att konfigurera extra funktioner för programmet som enkel inloggning, personlig information och säkerhetskrav.

Lär dig mer om den här funktionen med artikeln om du har använt Application Proxy [ge säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Publicera en lokal app för fjärråtkomst

Följ dessa steg för att publicera dina appar med Application Proxy. Om du inte har redan hämtats och konfigurerat en koppling för din organisation, gå till [komma igång med Application Proxy och installera connector](active-directory-application-proxy-enable.md) först och sedan publicera din app.

> [!TIP]
> Om du testar programproxy för första gången, väljer du ett program som har ställts in för lösenordsbaserad autentisering. Application Proxy stöder andra typer av autentisering, men lösenordsbaserade appar är den enklaste att komma igång snabbt och köra. 

1. Logga in som administratör i den [Azure-portalen](https://portal.azure.com/).
2. Välj **Azure Active Directory** > **företagsprogram** > **nytt program**.

  ![Lägg till ett företagsprogram](./media/application-proxy-publish-azure-portal/add-app.png)

3. Välj **alla**och välj **lokalt program**.  

  ![Lägg till ditt eget program](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Ange följande information om ditt program:

   - **Namnet**: namnet på programmet som ska visas på åtkomstpanelen och i Azure-portalen. 

   - **Intern URL**: URL: en som används för att komma åt programmet inifrån ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika platser på samma server som olika appar och ge vart och ett eget namn och regler.

     > [!TIP]
     > Om du publicerar en sökväg, så se till att den innehåller alla bilder, skript och formatmallar som krävs för ditt program. Om din app är i till exempel https://yourapp/app och använder avbildningar som finns på https://yourapp/media, och du bör publicera https://yourapp/ som sökväg. Intern URL: en måste inte vara landningssida användarna finns. Mer information finns i [ange en anpassad hemsida för publicerade appar](application-proxy-office365-app-launcher.md).

   - **Extern URL**: adressen användarna skickas till för att komma åt appen från utanför nätverket. Om du inte vill använda Application Proxy standarddomänen Läs om [anpassade domäner i Azure AD Application Proxy](active-directory-application-proxy-custom-domains.md).
   - **Förautentisering**: hur Application Proxy verifierar användare innan de får åtkomst till ditt program. 

     - Azure Active Directory: Programproxyn omdirigerar användarna till att logga in med Azure AD, som autentiserar deras katalog- och programbehörigheter. Vi rekommenderar detta alternativ som standard, så att du kan dra nytta av säkerhetsfunktionerna i Azure AD som villkorlig åtkomst och Multi-Factor Authentication.
     - Genomströmning: Användarna inte behöver autentiseras mot Azure Active Directory för att få åtkomst till programmet. Du kan fortfarande installera autentiseringskrav på serverdelen.
   - **Kopplingen grupp**: kopplingar bearbeta fjärråtkomst till ditt program och connector grupper hjälper dig att ordna kopplingar och appar efter region, nätverk eller syfte. Om du inte har någon koppling grupper som skapats ännu din app har tilldelats **standard**.

>[!NOTE]
>Om programmet använder websockets för att ansluta, används till att du har connector-version 1.5.612.0 eller senare med stöd för websocket och Connector grupp endast dessa kopplingar.

   ![Konfigurera ditt program](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Om det behövs kan du konfigurera ytterligare inställningar. De flesta fall bör du behålla inställningarna i sina standardtillstånd. 
   - **Tidsgränsen för backend-programmet**: det här värdet till **lång** endast om ditt program går långsamt att autentisera och ansluta. 
   - **Översätta URL: er i sidhuvuden**: behålla det här värdet som **Ja** om ursprungliga värdhuvudet i begäran om autentisering krävs för ditt program.
   - **Översätta URL: er i programmet brödtext**: behålla det här värdet som **nr** om du har hårdkodad HTML länkar till andra lokala program och Använd inte anpassade domäner. Mer information finns i [länka översättning med Application Proxy](application-proxy-link-translation.md).
   
   ![Konfigurera ditt program](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Välj **Lägg till**.


## <a name="add-a-test-user"></a>Lägg till en testanvändare 

Lägga till ett användarkonto för testet om du vill testa att appen har publicerats korrekt. Kontrollera att det här kontot redan har behörighet att komma åt appen från inuti företagsnätverket.

1. Tillbaka i bladet Snabbstart Välj **tilldela en användare för att testa**.

  ![Tilldela en användare för testning](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Om användare och grupper bladet välj **Lägg till**.

  ![Lägg till en användare eller grupp](./media/application-proxy-publish-azure-portal/add-user.png)

3. På bladet Lägg till tilldelningen väljer **användare och grupper** Välj det konto som du vill lägga till. 
4. Välj **Tilldela**.

## <a name="test-your-published-app"></a>Testa din publicerade app

Navigera till den externa URL som du konfigurerade under steget publicera i din webbläsare. Du bör se startskärmen och kunna logga in med kontot test som du konfigurerar.

![Testa din publicerade app](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Nästa steg
- [Hämta kopplingar](active-directory-application-proxy-enable.md) och [skapa grupper för anslutningstjänsten](active-directory-application-proxy-connectors-azure-portal.md) att publicera program på separata nätverk och platser.

- [Konfigurera enkel inloggning](application-proxy-sso-azure-portal.md) för din nyligen publicerade app
