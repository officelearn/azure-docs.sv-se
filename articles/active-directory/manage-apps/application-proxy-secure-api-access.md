---
title: 'Få tillgång till lokala API: er med Azure AD Application Proxy'
description: 'Azure Active Directory Application Proxy kan inbyggda appar på ett säkert sätt få åtkomst till API: er och affärslogik som värd för en lokal eller på virtuella datorer.'
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 47f6678f8d18d734176d964f18a6febecea957ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481421"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Säker åtkomst till lokala API: er med Azure AD Application Proxy

Du kan ha affärslogik API: er som körs lokalt eller körs på virtuella datorer i molnet. Dina interna Android, iOS, Mac eller Windows-appar måste interagera med API-slutpunkter som används av data och ge interaktion från användaren. Azure AD Application Proxy och [Azure Active Directory Authentication Libraries (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) nu kan dina interna appar säker åtkomst till din lokala API: er. Azure Active Directory Application Proxy är en lösning för snabbare och säkrare än att öppna portar i brandväggen och styra autentisering och auktorisering i appnivån. 

Den här artikeln vägleder dig genom att ställa in en Azure AD Application Proxy-lösning för värd för en webb-API-tjänst som har åtkomst till inbyggda appar. 

## <a name="overview"></a>Översikt

I följande diagram visas ett traditionellt sätt att publicera lokala API: er. Den här metoden kräver att öppna inkommande portarna 80 och 443.

![Traditionella API-åtkomst](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Följande diagram visar hur du kan använda Azure AD-programproxy för att säkert publicera API: er utan att öppna några inkommande portar:

![Azure AD Application Proxy API-åtkomst](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD-programproxyn utgör ryggraden för lösningen, som fungerar som en offentlig slutpunkt för API-åtkomst och att tillhandahålla autentisering och auktorisering. Du kan komma åt dina API: er från ett brett utbud av plattformar med hjälp av den [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) bibliotek. 

Eftersom Azure AD Application Proxy-autentisering och auktorisering bygger på Azure AD, kan du kan använda Azure AD villkorsstyrd åtkomst för att se till att endast betrodda enheter kan komma åt API: er som publicerats via programproxy. Använd Azure AD Join- eller Azure AD-Hybrid anslutits för stationära datorer och hanteras av Intune för enheter. Du kan också dra nytta av Azure Active Directory Premium-funktioner som Azure Multi-Factor Authentication och machine learning-stödda säkerheten i [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa den här genomgången behöver du:

- Administratörsåtkomst till en Azure-katalog, med ett konto som kan skapa och registrera appar
- Exemplet webb-API eller interna klientprogram från [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publicera API: et via programproxy

Om du vill publicera en API utanför intranätet via programproxy följer samma mönster som publicerar webbappar. Mer information finns i [Självstudie: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

Publicera på webben SecretAPI API via programproxy:

1. Skapa och publicera SecretAPI exempelprojektet som en ASP.NET-webbapp på din lokala dator eller intranätet. Kontrollera att du kan komma åt webbappen lokalt. 
   
1. I den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory** i det vänstra navigeringsfönstret. Klicka på den **översikt** väljer **företagsprogram**.
   
1. Överst på den **företagsprogram – alla program** väljer **nytt program**.
   
1. På den **lägga till ett program** sidan under **lägga till din egen app**väljer **lokala program**. 
   
1. Om du inte har en Application Proxy Connector installerat, uppmanas du att installera den. Välj **ladda ned Application Proxy Connector** att ladda ned och installera anslutningen. 
   
1. När du har installerat Application Proxy Connector på den **lägga till dina egna lokala program** sidan:
   
   1. Ange *SecretAPI* bredvid **namn**.
      
   1. Ange URL: en som du använder för att få åtkomst till API från i intranätet bredvid **interna URL: en**. 
      
   1. Se till att **förautentisering** är inställd på **Azure Active Directory**. 
      
   1. Välj **Lägg till** överst på sidan och vänta tills appen som ska skapas.
   
   ![Lägg till API-app](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. På den **företagsprogram – alla program** väljer den **SecretAPI** app. 
   
1. På den **SecretAPI - översikt** väljer **egenskaper** i det vänstra navigeringsfönstret.
   
1. Du inte vill att API: er ska vara tillgängliga för slutanvändare i den **MyApps** panelen, så ange **synlig för användarna** till **nr** längst ned på den **egenskaper**och välj sedan **spara**.
   
   ![Inte synligt för användarna](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Du har publicerat ditt webb-API via Azure AD-programproxy. Lägg till användare med åtkomst till appen. 

1. På den **SecretAPI - översikt** väljer **användare och grupper** i det vänstra navigeringsfönstret.
   
1. På den **användare och grupper** väljer **Lägg till användare**.  
   
1. På den **Lägg till tilldelning** väljer **användare och grupper**. 
   
1. På den **användare och grupper** , söka efter och Välj användare med åtkomst till appen, inklusive minst själv. När du har valt alla användare, Välj **Välj**. 
   
   ![Välj och tilldela användare](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Gå tillbaka till den **Lägg till tilldelning** väljer **tilldela**. 

> [!NOTE]
> API: er som använder integrerad Windows-autentisering kan kräva [ytterligare steg](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrera den inbyggda appen och bevilja åtkomst till API: et

Inbyggda appar är program som har utvecklats för att använda på en viss plattform eller enhet. Innan den inbyggda appen kan ansluta och få åtkomst till ett API, måste du registrera den i Azure AD. Följande steg visar hur du registrerar en inbyggd app och ge åtkomst till webb-API som du har publicerats via programproxy.

Registrera den inbyggda appen AppProxyNativeAppSample:

1. På Azure Active Directory **översikt** väljer **appregistreringar**, och överst i den **appregistreringar** väljer **ny registrering** .
   
1. På den **registrera ett program** sidan:
   
   1. Under **namn**, ange *AppProxyNativeAppSample*. 
      
   1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton). 
      
   1. Under **omdirigerings-URL**, nedrullningsbar listruta och välj **offentlig klient (mobilappar och skrivbordsappar)** , och ange sedan *https:\//appproxynativeapp*. 
      
   1. Välj **registrera**, och vänta tills appen registreras har. 
      
      ![Ny programregistrering](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Du har nu registrerats AppProxyNativeAppSample-app i Azure Active Directory. Ge din inbyggd app-åtkomst till SecretAPI webb-API:

1. På Azure Active Directory **översikt** > **Appregistreringar** väljer den **AppProxyNativeAppSample** app. 
   
1. På den **AppProxyNativeAppSample** väljer **API-behörigheter** i det vänstra navigeringsfönstret. 
   
1. På den **API-behörigheter** väljer **lägga till en behörighet**.
   
1. Första **begär API-behörigheter** väljer den **API: er som min organisation använder** fliken och sök sedan efter och välj **SecretAPI**. 
   
1. På nästa **begär API-behörigheter** markerar du kryssrutan bredvid **user_impersonation**, och välj sedan **Lägg till behörigheter**. 
   
    ![Välj ett API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Gå tillbaka till den **API-behörigheter** kan du välja **bevilja administratörens godkännande för Contoso** att förhindra att andra användare behöva individuellt godkänna appen. 

## <a name="configure-the-native-app-code"></a>Konfigurera inbyggd app-kod

Det sista steget är att konfigurera den inbyggda appen. Följande kodavsnitt från den *Form1.cs* fil i exempelappen NativeClient orsakar ADAL-biblioteket att hämta token för att begära API-anrop och kopplar den som ägar till app-rubrik. 
   
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
   
Om du vill konfigurera den inbyggda appen för att ansluta till Azure Active Directory och anropa API App Proxy, uppdatera platshållarvärdena i den *App.config* fil för NativeClient exempelapp med värden från Azure AD: 

- Klistra in den **katalog (klient)-ID** i den `<add key="ida:Tenant" value="" />` fält. Du kan hitta och kopiera det här värdet (en GUID) från den **översikt** för någon av dina appar. 
  
- Klistra in AppProxyNativeAppSample **(klient)-ID: T** i den `<add key="ida:ClientId" value="" />` fält. Du kan hitta och kopiera det här värdet (en GUID) från AppProxyNativeAppSample **översikt** sidan.
  
- Klistra in AppProxyNativeAppSample **omdirigerings-URI** i den `<add key="ida:RedirectUri" value="" />` fält. Du kan hitta och kopiera det här värdet (en URI) från AppProxyNativeAppSample **autentisering** sidan. 
  
- Klistra in SecretAPI **program-ID-URI** i den `<add key="todo:TodoListResourceId" value="" />` fält. Du kan hitta och kopiera det här värdet (en URI) från SecretAPI **exponerar ett API** sidan.
  
- Klistra in SecretAPI **URL-Adressen** i den `<add key="todo:TodoListBaseAddress" value="" />` fält. Du kan hitta och kopiera det här värdet (en URL) från SecretAPI **varumärkesriktlinjer** sidan.

När du har konfigurerat parametrarna, skapa och köra den inbyggda appen. När du väljer den **logga In** knappen appen kan du logga in och sedan visas en skärm för lyckad att bekräfta att den har anslutit SecretAPI.

![Klart](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Snabbstart: Konfigurera ett klientprogram för att få åtkomst till webb-API: er](../develop/quickstart-configure-app-access-web-apis.md)
- [Så här aktiverar du interna klientprogram att interagera med proxy-program](application-proxy-configure-native-client-application.md)
