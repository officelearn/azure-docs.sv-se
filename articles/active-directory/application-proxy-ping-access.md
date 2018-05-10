---
title: Rubrik-baserad autentisering med PingAccess för Azure AD Application Proxy | Microsoft Docs
description: Publicera program med PingAccess och App-Proxy som stöder huvud-baserad autentisering.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: bb341c5700f573e61cdbbe992aa0b0eb99389b2f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Rubrik-baserad autentisering för enkel inloggning med Application Proxy och PingAccess

Azure Active Directory Application Proxy och PingAccess samarbetar tillsammans för att ge åtkomst till fler program med Azure Active Directory-kunder. PingAccess expanderar den [befintliga Application Proxy-erbjudanden](active-directory-application-proxy-get-started.md) med enkel inloggning åtkomst till program som använder huvuden för autentisering.

## <a name="what-is-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

PingAccess för Azure Active Directory är en uppsättning PingAccess som gör att du kan ge användare åtkomst till och enkel inloggning till program som använder huvuden för autentisering. Programmet Proxy behandlar apparna som någon annan, med hjälp av Azure AD för att autentisera åtkomst och skicka trafik via kopplingstjänsten. PingAccess placeras framför apparna och översätter åtkomst-token från Azure AD till ett sidhuvud så att programmet tar emot autentiseringen i det format som den kan läsa.

Användarna kommer inte se något annat när de loggar in att använda din företags-appar. De kan fortsätta att arbeta från var som helst på vilken enhet som helst. 

Eftersom Application Proxy-kopplingar dirigera remote trafik till alla appar oavsett deras autentiseringstyp, kommer de fortsätta att belastningsutjämna automatiskt, samt.

## <a name="how-do-i-get-access"></a>Hur skaffar åtkomst?

Eftersom det här scenariot erbjuds via ett partnerskap mellan Azure Active Directory och PingAccess, behöver du licenser för båda tjänsterna. Azure Active Directory Premium prenumerationer innehåller emellertid en grundläggande PingAccess licens som täcker upp till 20 program. Om du vill publicera fler än 20 huvud-baserade program kan du köpa ytterligare en licens från PingAccess. 

Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).

## <a name="publish-your-application-in-azure"></a>Publicera programmet i Azure

Den här artikeln är avsedd för personer som publicerar en app med det här scenariot för första gången. Det går igenom hur du kommer igång med både program- och PingAccess, utöver publicering stegen. Om du redan har konfigurerat båda tjänsterna men vill en uppdaterare för publishing steg du kan hoppa över kopplingsinstallationen av och gå vidare till [lägga till din app i Azure AD med Application Proxy](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Eftersom det här scenariot är en koppling mellan Azure AD och PingAccess, vissa anvisningarna finns på webbplatsen Ping identitet.

### <a name="install-an-application-proxy-connector"></a>Installera en Application Proxy connector

Om du redan har Application Proxy är aktiverat och har en koppling installeras, kan du hoppa över det här avsnittet och gå vidare till [lägga till din app i Azure AD med Application Proxy](#add-your-app-to-azure-ad-with-application-proxy).

Application Proxy connector är en Windows Server-tjänst som dirigerar trafik från fjärranslutna anställda till publicerade appar. Mer detaljerad Installationsinstruktioner finns [aktivera Application Proxy på Azure-portalen](active-directory-application-proxy-enable.md).

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Välj **Azure Active Directory** > **programproxy**.
3. Välj **hämta anslutning** att starta Application Proxy connector hämtningen. Följ installationsanvisningarna.

   ![Aktivera Application Proxy och hämtar connector](./media/application-proxy-ping-access/install-connector.png)

4. Hämtar anslutningen automatiskt aktivera Application Proxy för din katalog, men om inte du väljer **aktivera Application Proxy**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Lägg till din app i Azure AD med Application Proxy

Det finns två åtgärder du måste vidta i Azure-portalen. Först måste du publicera ditt program med programproxy. Måste du samla in information om den app som du kan använda under PingAccess steg.

Följ dessa steg om du vill publicera en app. En mer detaljerad genomgång av steg 1 – 8, se [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md).

1. Om du inte i det sista avsnittet, logga in på den [Azure-portalen](https://portal.azure.com) som global administratör.
2. Välj **Azure Active Directory** > **företagsprogram**.
3. Välj **Lägg till** längst upp på bladet.
4. Välj **lokalt program**.
5. Fyll i de obligatoriska fälten med information om den nya appen. Använd följande riktlinjer för inställningarna:
   - **Intern URL**: normalt du ange en URL som tar dig till inloggningssidan för appens när du är på företagsnätverket. I det här scenariot måste kopplingen behandla PingAccess-proxy som sidan främre i appen. Använd följande format: `https://<host name of your PA server>:<port>`. Porten är 3000 som standard, men du kan konfigurera i PingAccess.

    > [!WARNING]
    > Intern URL måste använda https för den här typen av enkel inloggning och kan inte använda http.

   - **Förautentiseringsmetoden**: Azure Active Directory
   - **Översätta URL: en i sidhuvuden**: Nej

   >[!NOTE]
   >Om det här är ditt första program Använd port 3000 att starta och gå tillbaka till att uppdatera den här inställningen om du ändrar konfigurationen PingAccess. Om det här är en app i andra behöver detta matcha lyssnare som du har konfigurerat i PingAccess. Lär dig mer om [lyssnare i PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Välj **Lägg till** längst ned på bladet. Programmet har lagts till och snabb start-menyn öppnas.
7. Snabb startmenyn väljer du **tilldela en användare för att testa**, och Lägg till minst en användare till programmet. Kontrollera att det här testet kontot har åtkomst till lokala program.
8. Välj **tilldela** spara Användartilldelning test.
9. På bladet hantering väljer **enkel inloggning**.
10. Välj **huvud-baserade inloggning** från den nedrullningsbara menyn. Välj **Spara**.

   >[!TIP]
   >Om det här är första gången du använder huvud-baserade enkel inloggning måste du installera PingAccess. Kontrollera att din Azure-prenumeration associeras automatiskt med PingAccess installationen genom att använda länken på den här sidan för enkel inloggning för att hämta PingAccess. Du kan öppna hämtningsplatsen nu eller försöka till den här sidan igen senare. 

   ![Välj huvud-baserade inloggning](./media/application-proxy-ping-access/sso-header.PNG)

11. Stäng bladet för Enterprise-program eller rulla längst till vänster om du vill gå tillbaka till Azure Active Directory-menyn.
12. Välj **Appregistreringar**.

   ![Välj App-registreringar](./media/application-proxy-ping-access/app-registrations.png)

13. Välj den app som du just lagt till, sedan **Reply URL: er**.

   ![Välj svars-URL: er](./media/application-proxy-ping-access/reply-urls.png)

14. Kontrollera om det finns externa URL: en som tilldelats din app i steg 5 i Reply URL-listan. Om den inte gör du det nu.
15. På inställningsbladet väljer **nödvändiga behörigheter**.

  ![Välj behörigheter som krävs](./media/application-proxy-ping-access/required-permissions.png)

16. Välj **Lägg till**. API: et, Välj **Windows Azure Active Directory**, sedan **Välj**. Behörigheter, Välj **läsa och skriva alla program** och **logga in och Läs användarprofil**, sedan **Välj** och **klar**.  

  ![Valda behörigheter](./media/application-proxy-ping-access/select-permissions.png)

17. Bevilja behörigheter innan du stänger fönstret behörigheter. 
![Bevilja behörighet](media/application-proxy-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Samla in information för stegen PingAccess

1. På inställningsbladet din app väljer **egenskaper**. 

  ![Välj egenskaper](./media/application-proxy-ping-access/properties.png)

2. Spara den **program-Id** värde. Detta används för klient-ID när du konfigurerar PingAccess.
3. På inställningsbladet väljer **nycklar**.

  ![Välj nycklar](./media/application-proxy-ping-access/Keys.png)

4. Skapa en nyckel genom att ange en beskrivning av nyckeln och väljer ett förfallodatum nedrullningsbara menyn.
5. Välj **Spara**. Ett GUID som visas i den **värdet** fältet.

  Spara det här värdet nu, eftersom du inte kommer att kunna se den igen när du har stängt det här fönstret.

  ![Skapa en ny nyckel](./media/application-proxy-ping-access/create-keys.png)

6. Stäng appen registreringar bladet eller rulla åt vänster gå tillbaka till Azure Active Directory-menyn.
7. Välj **egenskaper**.
8. Spara den **katalog-ID** GUID.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Valfritt: uppdatera GraphAPI att skicka anpassade fält

En lista över säkerhetstoken som Azure AD skickar för autentisering, se [Azure AD tokenreferens](./develop/active-directory-token-and-claims.md). Om du behöver ett anpassat anspråk som skickar andra token kan använda diagrammet Utforskaren eller manifestet för programmet i Azure Portal för att ange fältet app *acceptMappedClaims* till **SANT**.    

Det här exemplet använder diagram Explorer:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
Det här exemplet används den [Azure-portalen](https://portal.azure.com) till udpate den *acceptedMappedClaims* fält:
1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Välj **Azure Active Directory** > **App registreringar**.
3. Markera programmet > **Manifest**.
4. Välj **redigera**, söka efter den *acceptedMappedClaims* fältet och ändra värdet till **SANT**.
![App-manifest](media/application-proxy-ping-access/application-proxy-ping-access-manifest.PNG)
1. Välj **Spara**.

>[!NOTE]
>Du måste ha en anpassad princip definieras och tilldelas programmet om du vill använda ett anpassat anspråk.  Den här principen ska innehålla alla obligatoriska anpassade attribut.
>
>Principdefinitionen och tilldelning kan göras via PowerShell, Azure AD Graph Explorer eller MS Graph.  Om du vill göra detta i PowerShell, kan du behöva använda först `New-AzureADPolicy `och tilldela den till programmet med `Set-AzureADServicePrincipalPolicy`.  Mer information finns i [principen i Azure AD-dokumentationen](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Valfritt: Använd ett anpassat anspråk
Om du vill att ditt program använder ett anpassat anspråk och lägga till ytterligare fält, bör du se till att även [skapas en anpassad anspråk mappning av principen och tilldelats programmet](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Hämta PingAccess och konfigurera din app

Nu när du har slutfört alla steg för Azure Active Directory-installationen, kan du gå vidare till Konfigurera PingAccess. 

Detaljerade anvisningar för PingAccess en del av det här scenariot fortsätter i dokumentationen för Ping identitet [konfigurera PingAccess för Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Dessa steg vägleder dig genom processen att skaffa en PingAccess-konto om du inte redan har ett, installerar PingAccess-servern och skapa en Azure AD OIDC Provider-anslutning med katalog-ID som du kopierade från Azure-portalen. Sedan kan du använda program-ID och nyckel värden, för att skapa en webbsessionen på PingAccess. Sedan kan du konfigurera identitetsmappning och skapa en virtuell värd, plats och program.

### <a name="test-your-app"></a>Testa din app

När du har slutfört de här stegen kan ska din app vara igång. Prova genom att öppna en webbläsare och gå till den externa URL som du skapade när du har publicerat appen i Azure. Logga in med kontot test som du tilldelade till appen.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera PingAccess för Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Hur tillhandahåller Azure AD Application Proxy enkel inloggning?](application-proxy-sso-overview.md)
- [Felsöka Application Proxy](active-directory-application-proxy-troubleshoot.md)
