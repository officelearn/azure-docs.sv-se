---
title: Få åtkomst till lokala API:er med Azure AD Application Proxy
description: Med Azure Active Directorys programproxy kan inbyggda appar komma åt API:er och affärslogik som du är värd för lokala eller virtuella datorer i molnet.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77166017"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Säker åtkomst till lokala API:er med Azure AD Application Proxy

Du kan ha affärslogik API:er som körs lokalt eller finns på virtuella datorer i molnet. Dina inbyggda Android-, iOS-, Mac- eller Windows-appar måste interagera med API-slutpunkterna för att kunna använda data eller tillhandahålla användarinteraktion. Azure AD Application Proxy och [Azure Active Directory Authentication Libraries (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) gör att dina inbyggda appar kan komma åt dina lokala API:er på ett säkert sätt. Azure Active Directory Application Proxy är en snabbare och säkrare lösning än att öppna brandväggsportar och styra autentisering och auktorisering i applagret. 

Den här artikeln går igenom att konfigurera en Azure AD Application Proxy-lösning för att vara värd för en webb-API-tjänst som inbyggda appar kan komma åt. 

## <a name="overview"></a>Översikt

Följande diagram visar ett traditionellt sätt att publicera lokala API:er. Den här metoden kräver att inkommande portar öppnas 80 och 443.

![Traditionell API-åtkomst](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Följande diagram visar hur du kan använda Azure AD Application Proxy för att på ett säkert sätt publicera API:er utan att öppna några inkommande portar:

![API-åtkomst för Azure AD-programproxy](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD Application Proxy utgör ryggraden i lösningen, fungerar som en offentlig slutpunkt för API-åtkomst och tillhandahåller autentisering och auktorisering. Du kan komma åt dina API:er från ett brett utbud av plattformar med hjälp av [ADAL-biblioteken.](/azure/active-directory/develop/active-directory-authentication-libraries) 

Eftersom Azure AD Application Proxy-autentisering och auktorisering bygger på Azure AD kan du använda Azure AD Conditional Access för att säkerställa att endast betrodda enheter kan komma åt API:er som publiceras via Programproxy. Använd Azure AD Join eller Azure AD Hybrid Joined for desktops och Intune Managed for devices. Du kan också dra nytta av Azure Active Directory Premium-funktioner som Azure Multi-Factor Authentication och machine learning-backed security of [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Krav

För att följa den här genomgången behöver du:

- Administratörsåtkomst till en Azure-katalog med ett konto som kan skapa och registrera appar
- Exempel på webb-API och inbyggda klientappar från[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publicera API:et via programproxy

Om du vill publicera ett API utanför intranätet via Programproxy följer du samma mönster som för publicering av webbappar. Mer information finns i [Självstudiekurs: Lägg till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

Så här publicerar du SecretAPI-webb-API:et via Programproxy:

1. Skapa och publicera exempelt SecretAPI-projektet som en ASP.NET webbapp på din lokala dator eller intranät. Se till att du kan komma åt webbappen lokalt. 
   
1. Välj Azure Active **Directory**i [Azure-portalen](https://portal.azure.com). Välj sedan **Företagsprogram**.
   
1. Högst upp på sidan **Företagsprogram - Alla program** väljer du **Nytt program**.
   
1. På sidan **Lägg till ett program** väljer du Lokala **program**. Sidan **Lägg till ett eget lokalt program** visas.
   
1. Om du inte har installerat en Programproxyanslutning uppmanas du att installera den. Välj **Hämta Programproxyanslutning** för att hämta och installera anslutningen. 
   
1. När du har installerat Application Proxy Connector finns på sidan **Lägg till din egen lokala programsida:**
   
   1. Ange *SecretAPI* **bredvid Namn**.
      
   1. Bredvid **Intern url**anger du den URL som du använder för att komma åt API:et från intranätet.
      
   1. Kontrollera att **förautentisering** är inställt på **Azure Active Directory**. 
      
   1. Välj **Lägg till** högst upp på sidan och vänta tills appen har skapats.
   
   ![Lägg till API-app](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. På sidan **Företagsprogram - Alla program** väljer du **SecretAPI-appen.** 
   
1. På sidan **SecretAPI - Översikt** väljer du **Egenskaper** i den vänstra navigeringen.
   
1. Du vill inte att API:er ska vara tillgängliga för slutanvändare på panelen **Mina Program,** så ställ in **Synlig för användare** på **Nej** längst ned på **sidan Egenskaper** och välj sedan **Spara**.
   
   ![Inte synlig för användare](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Du har publicerat ditt webb-API via Azure AD Application Proxy. Lägg nu till användare som kan komma åt appen. 

1. På sidan **SecretAPI - Översikt** väljer du **Användare och grupper** i den vänstra navigeringen.
   
1. På sidan **Användare och grupper** väljer du Lägg till **användare**.  
   
1. På sidan **Lägg till tilldelning** väljer du Användare och **grupper**. 
   
1. På sidan **Användare och grupper** söker du efter och väljer användare som kan komma åt appen, inklusive åtminstone dig själv. När du har valt alla användare väljer du **Välj**. 
   
   ![Välj och tilldela användare](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Tillbaka på sidan **Lägg till tilldelning** väljer du **Tilldela**. 

> [!NOTE]
> API:er som använder integrerad Windows-autentisering kan kräva [ytterligare steg](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrera den inbyggda appen och bevilja åtkomst till API:et

Inbyggda appar är program som utvecklats för att använda på en viss plattform eller enhet. Innan din inbyggda app kan ansluta och komma åt ett API måste du registrera den i Azure AD. Följande steg visar hur du registrerar en inbyggd app och ger den åtkomst till webb-API:et som du publicerade via Programproxy.

Så här registrerar du den inbyggda appen AppProxyNativeAppSample:

1. På sidan Översikt **över** Azure väljer du **Appregistreringar**och väljer **Ny registrering**högst upp i fönstret **Appregistreringar** .
   
1. På sidan **Registrera en ansökan:**
   
   1. Under **Namn**anger du *AppProxyNativeAppSample*. 
      
   1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton). 
      
   1. Under **Omdirigera URL**kan du lista ned och välja Offentlig klient **(mobilt &-skrivbordet)** och ange sedan *https:\//appproxynativeapp*. 
      
   1. Välj **Registrera**och vänta tills appen har registrerats. 
      
      ![Ny programregistrering](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Du har nu registrerat appen AppProxyNativeAppSample i Azure Active Directory. Så här ger du din inbyggda app åtkomst till SecretAPI-webb-API:et:

1. På sidan Azure Active Directory **Overview** > **App Registrations** väljer du **appen AppProxyNativeAppSample.** 
   
1. På sidan **AppProxyNativeAppSample** väljer du **API-behörigheter** i den vänstra navigeringen. 
   
1. På sidan **API-behörigheter** väljer du **Lägg till en behörighet**.
   
1. På den första sidan **Förbehåll api-behörigheter** väljer du de **API:er som min organisation använder** på fliken och söker sedan efter och väljer **SecretAPI**. 
   
1. På sidan **Begär API-behörighet markerar** du kryssrutan bredvid **user_impersonation**och väljer sedan Lägg **till behörigheter**. 
   
    ![Välj ett API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Tillbaka på **api-behörighetssidan** kan du välja **Bevilja administratörsgodkännande för Contoso för** att förhindra att andra användare måste behöva göra sitt medgivande för appen individuellt. 

## <a name="configure-the-native-app-code"></a>Konfigurera den inbyggda appkoden

Det sista steget är att konfigurera den inbyggda appen. Följande kodavsnitt från *Form1.cs-filen* i exempelappen NativeClient gör att ADAL-biblioteket hämtar token för att begära API-anropet och bifoga det som bärare till apphuvudet. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Om du vill konfigurera den inbyggda appen för att ansluta till Azure Active Directory och anropa API-appproxyn uppdaterar du platshållarvärdena i *app.config-filen* i NativeClient-exempelappen med värden från Azure AD: 

- Klistra in **katalog-ID:t (klient)** i `<add key="ida:Tenant" value="" />` fältet . Du kan hitta och kopiera det här värdet (ett GUID) från sidan **Översikt i** någon av dina appar. 
  
- Klistra in AppProxyNativeAppSample **Application (klient) ID** i fältet `<add key="ida:ClientId" value="" />` . Du kan hitta och kopiera det här värdet (ett GUID) från sidan Översikt **för** AppProxyNativeAppSample.
  
- Klistra in AppProxyNativeAppSample Redirect `<add key="ida:RedirectUri" value="" />` **URI** i fältet . Du kan hitta och kopiera det här värdet (en URI) från sidan AppProxyNativeAppSample **Authentication.** 
  
- Klistra in SecretAPI **Application ID URI** i fältet `<add key="todo:TodoListResourceId" value="" />` . Du kan hitta och kopiera det här värdet (en URI) från SecretAPI **Expose en API-sida.**
  
- Klistra in Url:en för `<add key="todo:TodoListBaseAddress" value="" />` SecretAPI-startsidan i fältet . **Home Page URL** Du kan hitta och kopiera det här värdet (en URL) från SecretAPI **Branding-sidan.**

När du har konfigurerat parametrarna skapar och kör du den inbyggda appen. När du väljer knappen **Logga in** kan du logga in appen i appen och visar sedan en skärm som lyckades för att bekräfta att den har anslutits till SecretAPI.

![Lyckades](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Snabbstart: Konfigurera ett klientprogram för åtkomst till webb-API:er](../develop/quickstart-configure-app-access-web-apis.md)
- [Så här aktiverar du inbyggda klientprogram för att interagera med proxyprogram](application-proxy-configure-native-client-application.md)
