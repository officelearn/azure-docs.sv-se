---
title: Rubrikbaserad autentisering med PingAccess för Azure AD Application Proxy | Microsoft Docs
description: Publicera program med PingAccess och App Proxy som stöd för rubrikbaserad autentisering.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5d93eb08b81f6507bc8bc023ceab7c8677546c6a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191966"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Rubrikbaserad autentisering för enkel inloggning med Application Proxy och PingAccess

Azure Active Directory Application Proxy och PingAccess samarbetar tillsammans för att ge åtkomst till fler program till och med Azure Active Directory-kunder. PingAccess expanderar den [befintliga Application Proxy-erbjudanden](application-proxy.md) med enkel inloggning till program som använder rubriker för autentisering.

## <a name="what-is-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

PingAccess för Azure Active Directory är ett erbjudande pingaccess som hjälper dig att ge användare åtkomst till och enkel inloggning till program som använder rubriker för autentisering. Application Proxy behandlar apparna som någon annan, med Azure AD för att autentisera åtkomst och sedan skicka trafik via kopplingstjänsten. PingAccess är placerad framför apparna och översätter åtkomsttoken från Azure AD till en rubrik så att programmet tar emot autentiseringen i det format som den kan läsa.

Användarna ser inte något annorlunda när de loggar in du använder företagets appar. De kan fortsätta att arbeta från var som helst på valfri enhet. 

Eftersom programproxyanslutningar dirigera remote trafik till alla appar oavsett deras autentiseringstyp, fortsätter de att belastningsutjämna automatiskt, och även.

## <a name="how-do-i-get-access"></a>Hur gör jag för att få åtkomst?

Eftersom det här scenariot erbjuds via ett partnerskap mellan Azure Active Directory och PingAccess, behöver du licenser för båda tjänsterna. Azure Active Directory Premium-prenumerationer innehåller dock en grundläggande PingAccess-licens som omfattar upp till 20 program. Om du vill publicera fler än 20 rubrikbaserad program kan köpa du ytterligare en licens från PingAccess. 

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicera dina program i Azure

Den här artikeln är avsedd för personer som publicerar en app med det här scenariot för första gången. Vi går igenom hur du kommer igång med både program- och PingAccess, utöver publicering stegen. Om du redan har konfigurerat båda tjänsterna, men du vill uppdatera dig om publicering stegen, kan du hoppa över kopplingsinstallationen av och gå vidare till [lägga till din app i Azure AD med Application Proxy](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Eftersom det här scenariot är ett partnerskap mellan Azure AD och PingAccess, några av instruktionerna finns på webbplatsen Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installera en Application Proxy connector

Om du redan har programproxyn har aktiverats och har en anslutningstjänsten installerad, kan du hoppa över det här avsnittet och gå vidare till [lägga till din app i Azure AD med Application Proxy](#add-your-app-to-azure-ad-with-application-proxy).

Programproxy-kopplingen är en Windows Server-tjänst som dirigerar trafiken från fjärranslutna anställda till dina publicerade appar. Mer detaljerade instruktioner för installation finns i [aktivera Application Proxy på Azure-portalen](application-proxy-add-on-premises-application.md).

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Välj **Azure Active Directory** > **programproxy**.
3. Välj **hämta Connector** att starta Application Proxy connector nedladdningen. Följ installationsanvisningarna.

   ![Aktivera Application Proxy och hämta connector](./media/application-proxy-configure-single-sign-on-with-ping-access/install-connector.png)

4. Hämta anslutningsappen bör automatiskt aktivera Application Proxy för din katalog, men om inte du kan välja **aktivera Application Proxy**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Lägg till din app till Azure AD med Application Proxy

Det finns två åtgärder som du behöver göra i Azure-portalen. Först måste du publicera ditt program med programproxy. Måste du samla in viss information om den app som du kan använda under PingAccess-steg.

Följ dessa steg om du vill publicera din app. En mer detaljerad genomgång av steg 1-8, se [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md).

1. Om du inte gjort i det sista avsnittet loggar du in den [Azure-portalen](https://portal.azure.com) som global administratör.
2. Välj **Azure Active Directory** > **företagsprogram**.
3. Välj **Lägg till** överst på bladet.
4. Välj **lokala program**.
5. Fyll i de obligatoriska fälten med information om den nya appen. Använd följande riktlinjer för inställningar:
   - **Intern URL**: Normalt kan du ange den URL som tar dig till inloggningssidan för appens när du är i företagsnätverket. Anslutningen måste behandla PingAccess-proxy som förstasida i appen för det här scenariot. Använd det här formatet: `https://<host name of your PA server>:<port>`. Porten är 3000 som standard, men du kan konfigurera det i PingAccess.

    > [!WARNING]
    > För den här typen av enkel inloggning, den interna URL: en måste använda https och kan inte använda http.

   - **Förautentiseringsmetod**: Azure Active Directory
   - **Översätt URL i rubriker**: Nej

   >[!NOTE]
   >Om det är ditt första program kan använda port 3000 att starta och gå tillbaka till att uppdatera den här inställningen om du ändrar din PingAccess-konfiguration. Om det här är din app med andra eller senare behöver detta så att den matchar den lyssnare som du har konfigurerat i PingAccess. Läs mer om [lyssnare inom PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Välj **Lägg till** längst ned på bladet. Programmet har lagts till och snabb start-menyn öppnas.
7. Snabb start-menyn, Välj **tilldela en användare för att testa**, och Lägg till minst en användare till programmet. Kontrollera att det här testkontot har åtkomst till dina lokala program.
8. Välj **tilldela** att spara Användartilldelning test.
9. På bladet hantering väljer **enkel inloggning**.
10. Välj **rubrikbaserad inloggning** från den nedrullningsbara menyn. Välj **Spara**.

   >[!TIP]
   >Om det här är första gången du använder rubrikbaserad enkel inloggning måste du installera PingAccess. Kontrollera din Azure-prenumeration associeras automatiskt med PingAccess-installationen genom att använda länken på den här sidan för enkel inloggning för att hämta PingAccess. Du kan öppna hämtningsplatsen nu eller kommer till den här sidan tillbaka senare. 

   ![Välj rubrikbaserad inloggning](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.PNG)

11. Stäng bladet för Enterprise-program eller rulla längst till vänster för att gå tillbaka till Azure Active Directory-menyn.
12. Välj **Appregistreringar**.

   ![Välj appregistreringar](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)

13. Välj den app som du just lade till, sedan **Svarswebbadresser**.

   ![Välj svars-URL](./media/application-proxy-configure-single-sign-on-with-ping-access/reply-urls.png)

14. Kontrollera om externa URL: en som tilldelats din app i steg 5 är i listan över svars-URL. Om den inte gör du det nu.
15. På inställningsbladet väljer **behörigheter som krävs för**.

  ![Välj behörigheter som krävs](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

16. Välj **Lägg till**. API: et, Välj **Windows Azure Active Directory**, sedan **Välj**. Behörighet, Välj **läsa och skriva alla program** och **logga in och läsa användarprofil**, sedan **Välj** och **klar**.  

  ![Valda behörigheter](./media/application-proxy-configure-single-sign-on-with-ping-access/select-permissions.png)

17. Bevilja behörigheter innan du stänger skärmen behörigheter. 
![Bevilja behörigheter](./media/application-proxy-configure-single-sign-on-with-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Samla in information för PingAccess-steg

1. På bladet för din app-inställningar, väljer **egenskaper**. 

  ![Välj egenskaper](./media/application-proxy-configure-single-sign-on-with-ping-access/properties.png)

2. Spara den **program-Id** värde. Detta används för klient-ID när du konfigurerar PingAccess.
3. På inställningsbladet väljer **nycklar**.

  ![Välj nycklar](./media/application-proxy-configure-single-sign-on-with-ping-access/Keys.png)

4. Skapa en nyckel genom att ange en nyckelbeskrivning och välja ett förfallodatum från den nedrullningsbara menyn.
5. Välj **Spara**. Ett GUID som visas i den **värdet** fält.

  Spara det här värdet nu, eftersom du inte längre att se den igen när du har stängt det här fönstret.

  ![Skapa en ny nyckel](./media/application-proxy-configure-single-sign-on-with-ping-access/create-keys.png)

6. Stäng bladet registreringar eller rulla längst till vänster för att gå tillbaka till Azure Active Directory-menyn.
7. Välj **egenskaper**.
8. Spara den **katalog-ID** GUID.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Valfritt - uppdatering GraphAPI att skicka anpassade fält

En lista över säkerhetstoken som Azure AD skickar för autentisering, se [tokenreferens för Azure AD](../develop/v1-id-and-access-tokens.md). Om du behöver ett anpassat anspråk som skickar andra token kan använda Graph-testaren eller manifestet för programmet i Azure-portalen att ställa in fältet app *acceptMappedClaims* till **SANT**.    

Det här exemplet använder Graph-testaren:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
Det här exemplet används den [Azure-portalen](https://portal.azure.com) att uppdatera den *acceptedMappedClaims* fält:
1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Välj **Azure Active Directory** > **appregistreringar**.
3. Välj ditt program > **Manifest**.
4. Välj **redigera**, Sök efter den *acceptedMappedClaims* fältet och ändra värdet till **SANT**.
![Appmanifestet](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-ping-access-manifest.PNG)
1. Välj **Spara**.

>[!NOTE]
>Om du vill använda ett anpassat anspråk måste du också ha en anpassad princip definieras och tilldelas programmet.  Den här principen ska inkludera alla nödvändiga anpassade attribut.
>
>Principdefinitionen och tilldelningen kan göras via PowerShell, Azure AD Graph Explorer eller MS Graph.  Om du vill göra detta i PowerShell, kan du behöva använda först `New-AzureADPolicy `och tilldela den till programmet med `Set-AzureADServicePrincipalPolicy`.  Mer information finns i den [dokumentation om Azure AD-Policy](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Valfritt – Använd ett anpassat anspråk
För att göra ditt program använder ett anpassat anspråk och ta med ytterligare fält, bör du se till att även [skapas en anpassad princip för Anspråksmappning och den tilldelas programmet](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Hämta PingAccess och konfigurera din app

Nu när du har slutfört alla steg för Azure Active Directory-installationen, kan du gå vidare till att konfigurera PingAccess. 

Detaljerade anvisningar för PingAccess-delen av det här scenariot fortsätter i Ping Identity-dokumentationen [konfigurera PingAccess för Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

De här stegen vägleder dig genom processen för att skaffa en PingAccess-konto om du inte redan har ett, installera PingAccess-servern och skapa en Azure AD OIDC-Provider-anslutning med katalog-ID som du kopierade från Azure-portalen. Sedan använder du värdet program-ID och nyckel för att skapa en webbsession på PingAccess. Efter det kan du konfigurera identitetsmappning och skapa en virtuell värd, plats och program.

### <a name="test-your-app"></a>Testa din app

När du har slutfört de här stegen, men det är din app ska vara igång. Testa den genom att öppna en webbläsare och navigera till den externa URL: en som du skapade när du har publicerat appen i Azure. Logga in med kontot test som du tilldelade till appen.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera PingAccess för Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Hur tillhandahåller Azure AD Application Proxy enkel inloggning?](application-proxy-single-sign-on.md)
- [Felsöka programproxyn](application-proxy-troubleshoot.md)
