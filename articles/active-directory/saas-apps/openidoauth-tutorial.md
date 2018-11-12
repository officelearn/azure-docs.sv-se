---
title: Konfigurera ett OpenID/OAuth-program från Azure AD-appgalleri | Microsoft Docs
description: Steg för att konfigurera ett OpenID/OAuth-program från Azure AD app-galleriet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 04639e6d27854d9c25b97936b163cfaaa25fc375
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287446"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurera ett OpenID/OAuth-program från Azure AD-appgalleri

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processen att lägga till ett OpenID-program från galleriet

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**. 

    ![Azure Active Directory-knappen](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Gå till **företagsprogram** > **alla program**.

    ![Bladet för Enterprise-program](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Välj **nytt program** överst i dialogrutan.

    ![Knapp för ny program](./media/openidoauth-tutorial/tutorial_general_03.png)

4. I sökrutan skriver du namnet på programmet. Välj ett alternativ från panelen resultatet och registrera dig för programmet.

    ![Lägger till program](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > För OpenID Connect och OAuth-appar i **Lägg till** knappen är inaktiverad som standard. Här administratör bör välja registreringen knappen och ge medgivande till programmet. Programmet läggs sedan till kundklient, där du kan göra konfigurationerna. Det finns inget behov att uttryckligen lägga till programmet.

    ![Knappen Lägg till](./media/openidoauth-tutorial/addbutton.png)

5. När du väljer länken registrera dig, är du omdirigeras till sidan Azure Active Directory (Azure AD) för autentiseringsuppgifter.

6. Efter lyckad autentisering skapar acceptera du medgivande från sidan medgivande. Sedan kan visas med programmets startsida.

    > [!NOTE]
    > Du kan lägga till endast en instans av programmet. Om du redan har lagt till en och ett försök gjordes att ge samtycke igen, läggs den därmed inte igen i klienten. Därför logiskt kan du använda endast en app-instansen i klienten.

## <a name="authentication-flow-using-openid-connect"></a>Autentiseringsflödet med OpenID Connect

Det mest grundläggande inloggning flödet innehåller följande steg:

![Autentiseringsflödet med OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Program med flera klienter 
Ett program för flera är avsedd att användas i många organisationer är inte bara en organisation. Det här är vanligtvis software-as-a-service (SaaS)-program som skrivits av en oberoende programvaruleverantör (ISV). 

Program med flera klienter måste tillhandahållas i varje katalog där de kommer att användas. De kräver att användaren eller administratören tillstånd att registrera dem. Förfarandet medgivande startar när ett program som har registrerats i katalogen och ges åtkomst till Graph API eller kanske en annan webb-API. När en användare eller administratör från en annan organisation registrerar sig att använda programmet, visas en dialogruta de behörigheter som programmet behöver. 

Användaren eller administratören kan sedan ge samtycke till programmet. Medgivandet ger programmet åtkomst till den angivna data och slutligen registrerar programmet i katalogen.

> [!NOTE]
> Om du gör ditt program tillgängligt för användare i flera kataloger, du behöver en metod för att avgöra vilken klientorganisation som de finns i. En enda klient program behöver bara söka i en egen katalog för en användare. Ett program för flera behöver identifiera en viss användare från alla kataloger i Azure AD.
> 
> Om du vill utföra den här uppgiften, tillhandahåller Azure AD en gemensam autentiseringsslutpunkt där alla program med flera klienter kan dirigera inloggningsförfrågningar, i stället för en klientspecifik slutpunkt. Den här slutpunkten är [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) för alla kataloger i Azure AD. En klientspecifik slutpunkt kan vara [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> Vanliga slutpunkten är viktigt att tänka på när du utvecklar ditt program. Du behöver den nödvändiga logiken för att hantera flera klienter under inloggning, utloggning, och tokenvalidering.

Som standard främjar program med flera klienter i Azure AD. De är lätt att komma åt mellan olika organisationer och de är lätt att använda när du har godkänt samtycke.

## <a name="consent-framework"></a>Ramverk för medgivande

Du kan använda Azure AD-ramverket för medgivande för att utveckla med flera klienter webb- och interna klientprogram. Dessa program kan logga in genom att användarkonton från en Azure AD-klient, som skiljer sig från det där programmet har registrerats. De kan också behöva komma åt webb API: er som:
- Microsoft Graph API för Azure AD, Intune, och tjänster i Office 365. 
- API: er med andra Microsoft-tjänster.
- Ditt egna webb-API: er. 

Ramverket är baserad på en användare eller administratör ge ditt medgivande till ett program som frågar registreras i sin katalog. Registreringen kan handla om åtkomst till katalogdata. När tillstånd ges kan klientprogrammet anropa Microsoft Graph API för användarens räkning och använda informationen vid behov.

Den [Microsoft Graph API](https://developer.microsoft.com/graph/) ger åtkomst till data i Office 365, som:

- Kalendrar och meddelanden från Exchange.
- Platser och listor från SharePoint.
- Dokument från OneDrive.
- Bärbara datorer från OneNote.
- Uppgifter från Planner.
- Arbetsböcker från Excel.

Graph API: et tillhandahåller även åtkomst till användare och grupper från Azure AD och andra dataobjekt från flera Microsoft-molntjänster.

Följande steg visar hur samtycke uppleva fungerar för programutvecklare och användaren:

1. Anta att du har ett webbprogram för klienten som behöver begära särskild behörighet för att komma åt en resurs eller API: et. Azure-portalen används för att deklarera behörighetsbegäranden vid konfigurationen. De blir en del av programmets Azure AD-registrering som andra konfigurationsinställningar:

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Överväg att behörigheter för ditt program har uppdaterats. Programmet körs och en användare är håller på att använda den för första gången. Programmet måste först hämta en auktoriseringskod från Azure AD / tillåta slutpunkt. Auktoriseringskoden kan sedan användas för att få en ny tillgång och uppdatera token.

3. Om användaren inte redan autentiserats med Azure AD / tillåta slutpunkt anvisningarna för att logga in.

    ![Autentisering](./media/openidoauth-tutorial/authentication.png)

4. När användaren har loggat in Azure AD som avgör om användaren måste visas en sida för godkännande. Det här fastställs baserat på om användaren (eller organisationens administratör) har redan gett samtycke för programmet.

   Om medgivande inte har beviljats, Azure AD efterfrågar medgivande och visar de nödvändiga behörigheterna som krävs för att fungera. De behörigheter som visas i dialogrutan för medgivande överensstämmer med de som valts i de delegerade behörigheterna i Azure-portalen.

    ![Samtyckessida](./media/openidoauth-tutorial/consentpage.png)

En vanlig användare kan godkänna vissa behörigheter. Andra behörigheter kräver godkännande för en Innehavaradministratör.

## <a name="difference-between-admin-consent-and-user-consent"></a>Skillnaden mellan administratörens godkännande och användargodkännande

Som administratör kan också samtycker du till ett programs delegerade behörigheter för alla användare i din klient. Administratörs godkännande förhindrar dialogrutan för medgivande visas för varje användare i klienten. Användare som har administratörsrollen kan ge samtycke i Azure-portalen. Från den **inställningar** sidan för ditt program, Välj **nödvändiga behörigheter** > **bevilja behörigheter**.

![Bevilja behörigheter knappen](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Bevilja uttryckliga medgivande med hjälp av den **bevilja behörigheter** är nu krävs för enkelsidigt program (SPA) som använder ADAL.js. Annars misslyckas programmet när åtkomsttoken begärs.

Appspecifika behörigheter kräver alltid en Innehavaradministratör godkännande. Om ditt program begär en appspecifik behörighet och en användare försöker logga in på programmet, visas ett felmeddelande. Meddelandet säger att användaren inte kan godkänna.

Om programmet använder behörigheter som kräver administratörens godkännande, måste du ha en gest, till exempel en knapp eller länk där administratören kan starta åtgärden. Den begäran som programmet skickar för den här åtgärden är auktoriseringsbegäran vanligt OAuth2/OpenID Connect. Denna begäran innehåller den *uppmana = admin_consent* frågesträngparametern. 

När administratören har godkänt och tjänstens huvudnamn har skapats i kundens klient, senare inloggningsförfrågningar inte behöver den *uppmana = admin_consent* parametern. Eftersom administratören har bestämt dig att behörigheterna som krävs är godtagbara kan tillfrågas inga andra användare i klienten om samtycke från den tidpunkten och framåt.

En Innehavaradministratör kan inaktivera möjligheten för vanliga användare att samtycka till program. Om den här funktionen har inaktiverats krävs alltid administratörens godkännande för program som ska användas i klienten. Om du vill testa programmet med slutanvändarens medgivande inaktiverad, du kan hitta configuration-växeln i den [Azure-portalen](https://portal.azure.com/). Det är den [användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) avsnittet **företagsprogram**.

Den *uppmana = admin_consent* parametern kan även användas av program som begär behörighet som inte kräver administratörens godkännande. Ett exempel är ett program som kräver en upplevelse där klienten admin ”som registrerar sig” en tid och inte andra användarna tillfrågas om samtycke från den tidpunkten på.

Anta att ett program kräver administratörens godkännande och en administratör loggar in utan den *uppmana = admin_consent* parameter som skickas. När administratören godkänner har programmet, tillämpas endast för sitt användarkonto. Vanliga användare kommer fortfarande att det går inte att logga in eller godkänna programmet. Den här funktionen är användbart om du vill ge klientadministratören möjlighet att utforska ditt program innan åtkomst ges andra användare.