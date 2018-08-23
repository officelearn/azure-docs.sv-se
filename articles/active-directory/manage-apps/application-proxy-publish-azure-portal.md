---
title: Publicera appar med Azure AD Application Proxy | Microsoft Docs
description: Publicera lokala program till molnet med Azure AD Application Proxy på Azure-portalen.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 973a6201a227e6c2e295e6e5ea2f40c302572504
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056480"
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicera program med Azure AD Application Proxy

Azure Active Directory (AD) Application Proxy kan du stödja fjärranvändare genom att publicera lokala program som kan nås via internet. Du kan publicera dessa program via Azure portal för att tillhandahålla säker fjärråtkomst utanför nätverket.

Den här artikeln vägleder dig igenom stegen för att publicera en lokal app med Application Proxy. När du har slutfört den här artikeln kommer användarna att kunna få fjärråtkomst till din app. Så är du redo att konfigurera extra funktioner för programmet som enkel inloggning, personlig information och säkerhetskrav.

Om du inte har använt Application Proxy kan läsa mer om den här funktionen med artikeln [att tillhandahålla säker fjärråtkomst till lokala program](application-proxy.md).

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du redan har installerats och registrerats en koppling. Om du behöver göra de här stegen finns i [Kom igång med Application Proxy och installera Connector](application-proxy-enable.md).

## <a name="publish-an-on-premises-app-for-remote-access"></a>Publicera en lokal app för fjärråtkomst

Följ dessa steg om du vill publicera dina appar med Application Proxy. Om du inte redan har hämtat och konfigurerat en anslutning för din organisation, gå till [Kom igång med Application Proxy och installera connector](application-proxy-enable.md) först och sedan publicera din app.

> [!TIP]
> Om du testar Application Proxy för första gången, väljer du ett program som har ställts in för lösenordsbaserad autentisering. Programproxyn har stöd för andra typer av autentisering, men lösenordsbaserad appar är enklast att komma igång och snabbt. 

1. Logga in som administratör i den [Azure-portalen](https://portal.azure.com/).
2. Välj **Azure Active Directory** > **företagsprogram** > **nytt program**.

  ![Lägga till ett enterprise-program](./media/application-proxy-publish-azure-portal/add-app.png)

3. Välj **alla**och välj sedan **lokala program**.  

  ![Lägg till ditt eget program](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Ange följande information om ditt program:

   - **Namn på**: namnet på programmet som ska visas på åtkomstpanelen och Azure-portalen. 

   - **Intern URL**: URL: en som används för att komma åt programmet inifrån ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika webbplatser på samma server som olika appar och ge var och en egna namn och regler.

     > [!TIP]
     > Om du publicerar en sökväg, så se till att den innehåller alla bilder, skript och formatmallar som krävs för ditt program. Exempel: om din app ligger i https://yourapp/app och använder bilder som finns på https://yourapp/media, och sedan bör du publicera https://yourapp/ som sökväg. Den här interna URL: en måste inte vara den landningssida som användarna ser. Mer information finns i [ange en anpassad startsida för publicerade appar](application-proxy-configure-custom-home-page.md).

   - **Externa URL: en**: adressen användarna skickas till för att komma åt programmet från utanför nätverket. Om du inte vill använda Application Proxy standarddomän, Läs om [anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
   - **Förautentisering**: hur programproxyn verifierar användare innan de får åtkomst till ditt program. 

     - Azure Active Directory: Programproxyn omdirigerar användarna till att logga in med Azure AD, som autentiserar deras katalog- och programbehörigheter. Vi rekommenderar att det här alternativet som standard, så att du kan dra nytta av Azure AD-säkerhetsfunktioner, t.ex. villkorlig åtkomst och Multifaktorautentisering.
     - Genomströmning: Användarna inte autentisera mot Azure Active Directory för att få åtkomst till programmet. Du kan fortfarande konfigurera autentiseringskrav på serverdelen.
   - **Anslutningsgrupp**: kopplingar bearbeta fjärråtkomst till ditt program och anslutningen grupperar hjälper dig att ordna kopplingar och appar efter region, nätverk eller syfte. Om du inte har någon anslutningsapp-grupper som har skapats än din app har tilldelats **standard**.

>[!NOTE]
>Om ditt program använder websockets för att ansluta, använder Kontrollera att du har connector-version 1.5.612.0 eller högre med websocket-stöd och tilldelade Anslutningsgruppen endast dessa anslutningar.

   ![Konfigurera ditt program](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Om det behövs kan du konfigurera ytterligare inställningar. De flesta fall bör du behålla de här inställningarna i sina standardtillstånd. 
   - **Tidsgräns för backend-programmet**: det här värdet till **lång** endast om ditt program är långsamt att autentisera och ansluta. 
   - **Använd HTTP-Only Cookie**: det här värdet till **Ja** ha Application Proxy cookies innehåller flaggan HTTPOnly i HTTP-Svarsrubrik.
   - **Översätt URL: er i rubriker**: behålla det här värdet som **Ja** såvida inte programmet ursprungliga värdhuvudet i autentiseringsbegäran.
   - **Översätt URL: er i brödtexten för programmet**: behålla det här värdet som **nr** förutsatt att du har hårdkodad HTML länkar till andra lokala program och Använd inte anpassade domäner. Mer information finns i [länka translation med Application Proxy](application-proxy-configure-hard-coded-link-translation.md).
   
   ![Konfigurera ditt program](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Välj **Lägg till**.


## <a name="add-a-test-user"></a>Lägg till en testanvändare 

Testa din app har publicerats korrekt genom att lägga till ett test-användarkonto. Kontrollera att det här kontot redan har behörighet att komma åt programmet från inuti företagsnätverket.

1. Tillbaka på Snabbstart-bladet och välj **tilldela en användare för att testa**.

  ![Tilldela en användare för testning](./media/application-proxy-publish-azure-portal/assign-user.png)

2. De användare och gruppbladet Välj **Lägg till**.

  ![Lägg till en användare eller grupp](./media/application-proxy-publish-azure-portal/add-user.png)

3. På bladet Lägg till tilldelning väljer **användare och grupper** sedan välja det konto som du vill lägga till. 
4. Välj **Tilldela**.

## <a name="test-your-published-app"></a>Testa din publicerade app

Navigera till den externa URL: en som du konfigurerade under steget publicera i din webbläsare. Du bör se startskärmen och logga in med kontot test som du har konfigurerat.

![Testa din publicerade app](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Nästa steg
- [Ladda ned anslutningsappar](application-proxy-enable.md) och [skapa anslutningsappgrupper](application-proxy-connector-groups.md) att publicera program på separata nätverk och platser.

- [Konfigurera enkel inloggning](application-proxy-configure-single-sign-on-password-vaulting.md) för nyligen publicerade appen
