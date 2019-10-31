---
title: 'Få åtkomst till lokala API: er med Azure AD-programproxy'
description: 'Azure Active Directoryens programproxy tillåter inbyggda appar säker åtkomst till API: er och affärs logik som du är värd för lokalt eller på virtuella datorer i molnet.'
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: b741f42bb215df59903fed7ed84094b7d037ce65
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063010"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Säker åtkomst till lokala API: er med Azure AD-programproxy

Du kan ha affärs logik-API: er som körs lokalt eller som finns på virtuella datorer i molnet. Dina inbyggda Android-, iOS-, Mac-eller Windows-appar behöver interagera med API-slutpunkterna för att använda data eller tillhandahålla användar interaktion. Azure AD-programproxy och [Azure Active Directory Authentication libraries (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) gör det möjligt för dina inbyggda appar att få säker åtkomst till dina lokala API: er. Azure Active Directory-programproxy är en snabbare och säkrare lösning än att öppna brand Väggs portar och kontrol lera autentisering och auktorisering på App-lagret. 

Den här artikeln beskriver hur du konfigurerar en Azure AD-programproxy-lösning som är värd för en webb-API-tjänst som interna appar kan komma åt. 

## <a name="overview"></a>Översikt

Följande diagram visar ett traditionellt sätt att publicera lokala API: er. Den här metoden kräver att du öppnar inkommande portar 80 och 443.

![Traditionell API-åtkomst](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Följande diagram visar hur du kan använda Azure-AD-programproxy för att publicera API: er på ett säkert sätt utan att öppna några inkommande portar:

![Azure AD-programproxy API-åtkomst](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD-programproxy bildar lösningens stamnät, arbetar som en offentlig slut punkt för API-åtkomst och tillhandahåller autentisering och auktorisering. Du kan komma åt dina API: er från en mängd olika plattformar med hjälp av [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) -biblioteken. 

Eftersom Azure AD-programproxy-autentisering och-auktorisering bygger på Azure AD kan du använda villkorlig åtkomst för Azure AD för att se till att endast betrodda enheter kan komma åt API: er som publicerats via programproxy. Använd Azure AD Join eller Azure AD hybrid anslutet för Station ära datorer och Intune Hanterat för enheter. Du kan också dra nytta av Azure Active Directory Premium funktioner som Azure Multi-Factor Authentication och datorn inlärnings skydd för [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Krav

För att följa den här genom gången behöver du:

- Administratörs åtkomst till en Azure-katalog med ett konto som kan skapa och registrera appar
- Exempel webb-API och interna klient program från [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publicera API: t via Application Proxy

Om du vill publicera ett API utanför intranätet via Application Proxy följer du samma mönster som för att publicera webbappar. Mer information finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

Så här publicerar du SecretAPI Web API via Application Proxy:

1. Bygg och publicera SecretAPI-projektet som en ASP.NET-webbapp på din lokala dator eller intranät. Se till att du har åtkomst till webbappen lokalt. 
   
1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory** i det vänstra navigerings fältet. På sidan **Översikt** väljer du sedan **företags program**.
   
1. Överst på sidan **företags program – alla program** väljer du **nytt program**.
   
1. På sidan **Bläddra i Azure AD-galleriet** , under **lokala program**, väljer du **Lägg till ett lokalt program**. Sidan **Lägg till ett eget lokalt program** visas.
   
1. Om du inte har någon Application Proxy-anslutning installerad uppmanas du att installera den. Välj **Hämta Application Proxy Connector** för att ladda ned och installera anslutningen. 
   
1. När du har installerat Application Proxy Connector går du till sidan **Lägg till en egen lokal app** :
   
   1. Ange *SecretAPI* bredvid **namn**.
      
   1. Ange den URL som du använder för att få åtkomst till API: et inifrån intranätet bredvid **intern URL**. 
      
   1. Se till att **förautentisering** har angetts till **Azure Active Directory**. 
      
   1. Välj **Lägg till** överst på sidan och vänta tills appen har skapats.
   
   ![Lägg till API-app](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. På sidan **företags program – alla program** väljer du appen **SecretAPI** . 
   
1. På sidan **SecretAPI-Overview** väljer du **Egenskaper** i det vänstra navigerings fältet.
   
1. Du vill inte att API: er ska vara tillgängliga för slutanvändare på panelen mina **appar** , så Ställ in **Visible för användare** till **Nej** längst ned på sidan **Egenskaper** och välj sedan **Spara**.
   
   ![Inte synlig för användare](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Du har publicerat ditt webb-API via Azure AD-programproxy. Lägg nu till användare som har åtkomst till appen. 

1. På sidan **SecretAPI-översikt** väljer **du användare och grupper** i det vänstra navigerings fältet.
   
1. På sidan **användare och grupper** väljer du **Lägg till användare**.  
   
1. På sidan **Lägg till tilldelning** väljer **du användare och grupper**. 
   
1. På sidan **användare och grupper** söker du efter och väljer användare som har åtkomst till appen, inklusive minst själv. När du har valt alla användare väljer du **Välj**. 
   
   ![Välj och tilldela användare](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Tillbaka på sidan **Lägg till tilldelning** väljer du **tilldela**. 

> [!NOTE]
> API: er som använder integrerad Windows-autentisering kan kräva [ytterligare steg](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrera den inbyggda appen och bevilja åtkomst till API: et

Inbyggda appar är program som har utvecklats för att användas på en viss plattform eller enhet. Innan din inbyggda app kan ansluta till och komma åt ett API måste du registrera den i Azure AD. Följande steg visar hur du registrerar en inbyggd app och ger den åtkomst till webb-API: t som du har publicerat via programproxyn.

Registrera den inbyggda AppProxyNativeAppSample-appen:

1. På sidan Azure Active Directory **Översikt** väljer du **Appregistreringar**. överst i fönstret **Appregistreringar** väljer du **ny registrering**.
   
1. På sidan **Registrera ett program** :
   
   1. Under **namn**anger du *AppProxyNativeAppSample*. 
      
   1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton). 
      
   1. Under **omdirigerings-URL**, listruta och välj **offentlig klient (mobilt & skriv bord)** och ange sedan *https:\//appproxynativeapp*. 
      
   1. Välj **register**och vänta tills appen har registrerats. 
      
      ![Ny programregistrering](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Nu har du registrerat AppProxyNativeAppSample-appen i Azure Active Directory. Så här ger du din inbyggda app åtkomst till SecretAPI webb-API:

1. På sidan Azure Active Directory **översikt** > **app Registration** väljer du appen **AppProxyNativeAppSample** . 
   
1. På sidan **AppProxyNativeAppSample** väljer du **API-behörigheter** i det vänstra navigerings fältet. 
   
1. På sidan **API-behörigheter** väljer du **Lägg till en behörighet**.
   
1. På sidan första **API-behörighet för begäran** väljer du fliken **API: er min organisation använder** och söker sedan efter och väljer **SecretAPI**. 
   
1. På sidan nästa **begär ande-API-behörighet** markerar du kryss rutan bredvid **user_impersonation**och väljer sedan **Lägg till behörigheter**. 
   
    ![Välj ett API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. På sidan **API-behörigheter** kan du välja **bevilja administrativt godkännande för Contoso** för att förhindra att andra användare behöver ett individuellt medgivande till appen. 

## <a name="configure-the-native-app-code"></a>Konfigurera koden för den inbyggda appen

Det sista steget är att konfigurera den inbyggda appen. Följande kodfragment från *Form1.cs* -filen i NativeClient-exempel appen gör att ADAL-biblioteket hämtar token för att begära API-anropet och kopplar det som innehavare till app-huvudet. 
   
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
   
Om du vill konfigurera den interna appen så att den ansluter till Azure Active Directory och anropa API app proxy, uppdaterar du plats hållarnas värden i filen *app. config* i NativeClient-exempel appen med värden från Azure AD: 

- Klistra in **katalog-ID: t** i fältet `<add key="ida:Tenant" value="" />`. Du kan hitta och kopiera det här värdet (ett GUID) från sidan **Översikt** i någon av dina appar. 
  
- Klistra in AppProxyNativeAppSample **-program-ID: t (Client)** i fältet `<add key="ida:ClientId" value="" />`. Du kan hitta och kopiera det här värdet (ett GUID) från **översikts** sidan för AppProxyNativeAppSample.
  
- Klistra in **omdirigerings-URI** för AppProxyNativeAppSample i fältet `<add key="ida:RedirectUri" value="" />`. Du kan hitta och kopiera det här värdet (en URI) från sidan AppProxyNativeAppSample- **autentisering** . 
  
- Klistra in SecretAPI **program-ID-URI** i fältet `<add key="todo:TodoListResourceId" value="" />`. Du kan hitta och kopiera det här värdet (en URI) från SecretAPI **exponera en API** -sida.
  
- Klistra in **URL: en för SecretAPI start sida** i fältet `<add key="todo:TodoListBaseAddress" value="" />`. Du kan hitta och kopiera det här värdet (en URL) från sidan för SecretAPI- **anpassning** .

När du har konfigurerat parametrarna skapar och kör du den inbyggda appen. När du väljer knappen **Logga in** kan du använda appen för att logga in och sedan Visa skärmen lyckades för att bekräfta att den har anslutits till SecretAPI.

![Lyckades](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Snabb start: Konfigurera ett klient program för åtkomst till webb-API: er](../develop/quickstart-configure-app-access-web-apis.md)
- [Så här aktiverar du interna klient program för att interagera med proxyprogram](application-proxy-configure-native-client-application.md)
