---
title: Enkel inloggning med Application Proxy | Microsoft Docs
description: Beskriver hur du ger enkel inloggning med Azure AD Application Proxy.
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
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: ae79d081cc171fe904bf50b2341d7abd8f58e4f5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594507"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos-begränsad delegering för enkel inloggning till dina appar med Application Proxy

Du kan tillhandahålla enkel inloggning för lokalt program som publicerats via programproxy som skyddas med integrerad Windows-autentisering. Dessa program kräver en Kerberos-biljett för åtkomst till. Programproxy använder Kerberos-begränsad delegering (KCD) som stöd för dessa program. 

Du kan aktivera enkel inloggning för ditt program med integrerad autentisering IWA (Windows) genom att ge Application Proxy kopplingar behörigheter i Active Directory för att personifiera användare. Kopplingar kan du använda den här behörigheten att skicka och ta emot token åt.

## <a name="how-single-sign-on-with-kcd-works"></a>Hur enkel inloggning med KCD fungerar
Det här diagrammet beskriver flödet när en användare försöker få åtkomst till ett lokalt program som använder IWA.

![Flödesdiagram för Microsoft AAD-autentisering](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Användaren anger Webbadressen för att komma åt lokala program via Application Proxy.
2. Application Proxy dirigerar begäran till Azure AD autentiseringstjänster till preauthenticate. Nu är gäller Azure AD alla tillämpliga autentiserings- och auktoriseringsprinciper, till exempel flerfunktionsautentisering. Om användaren har verifierats, Azure AD skapar en token och skickar det till användaren.
3. Användaren skickar token till Application Proxy.
4. Programproxy validerar token och hämtar den UPN (User Principal Name) från den och skickar sedan begäran UPN-namnet och den namnet SPN (Service Principal) till anslutningen via en säker kanal dually autentiserad.
5. Kopplingen utför Kerberos-begränsad delegering (KCD)-förhandling med lokalt AD, Personifiera användare för att få en Kerberos-token till programmet.
6. Active Directory skickar Kerberos-token för programmet till anslutningstjänsten.
7. Kopplingen skickar den ursprungliga begäranden till programservern, med hjälp av Kerberos-token som togs emot från AD.
8. Programmet skickar svar till Connector, som då returneras till tjänsten Application Proxy och slutligen till användaren.

## <a name="prerequisites"></a>Förutsättningar
Kontrollera din miljö är redo med följande inställningar och konfigurationer innan du börjar med enkel inloggning för IWA program:

* Dina appar, t.ex SharePoint Web apps är inställd på att använda integrerad Windows-autentisering. Mer information finns i [aktivera stöd för Kerberos-autentisering](https://technet.microsoft.com/library/dd759186.aspx), eller för SharePoint finns [planera för Kerberos-autentisering i SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Alla dina appar har [Service Principal Names](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Den server som kör anslutningstjänsten och den server som kör appen är ansluten till en domän och en del av samma domän eller betrodda domäner. Mer information om domänanslutning finns [ansluta en dator till en domän](https://technet.microsoft.com/library/dd807102.aspx).
* Den server som kör anslutningstjänsten har behörighet att läsa attributet TokenGroupsGlobalAndUniversal för användare. Den här standardinställningen kanske har påverkats av säkerhet Härdning av miljön.

### <a name="configure-active-directory"></a>Konfigurera Active Directory
Active Directory-konfigurationen varierar beroende på om din Application Proxy connector och programserver är i samma domän eller inte.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Koppling och programserver i samma domän
1. I Active Directory, går du till **verktyg** > **användare och datorer**.
2. Välj den server som kör anslutningstjänsten.
3. Högerklicka och välj **egenskaper** > **delegering**.
4. Välj **förtroende för den här datorn för delegering till angivna tjänster**. 
5. Under **tjänster som det här kontot kan ge delegerade autentiseringsuppgifter** lägga till värdet för SPN-identitet på programservern. Detta gör Application Proxy Connector att personifiera användare i AD mot de program som anges i listan.

   ![Skärmbild av Connector SVR egenskaper](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Koppling och programservrar i olika domäner
1. En lista över förutsättningar för att arbeta med KCD över domäner finns [Kerberos-begränsad delegering över domäner](https://technet.microsoft.com/library/hh831477.aspx).
2. Använd den `principalsallowedtodelegateto` egenskapen på Connector-servern för att aktivera Application Proxy att delegera för Connector-server. Programservern är `sharepointserviceaccount` och delegera servern är `connectormachineaccount`. Använd den här koden som ett exempel för Windows 2012 R2:

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Sharepointserviceaccount kan vara Service Pack datorkontot eller ett tjänstkonto som Service Pack-programpoolen körs under.

## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning 
1. Publicera programmet enligt instruktionerna i [publicera program med programproxy](application-proxy-publish-azure-portal.md). Se till att välja **Azure Active Directory** som den **förautentisering metoden**.
2. När programmet visas i listan över företagsprogram, markerar du den och klickar på **enkel inloggning**.
3. Anger läget för enkel inloggning till **integrerad Windows-autentisering**.  
4. Ange den **interna program SPN** på programservern. I det här exemplet är SPN för vårt publicerade program http/www.contoso.com. Detta SPN måste finnas i listan över tjänster som anslutningen kan ge delegerade autentiseringsuppgifter. 
5. Välj den **delegerad inloggningen identitet** för anslutningen ska använda för dina användare. Mer information finns i [arbeta med olika lokala och molnidentiteter](#Working-with-different-on-premises-and-cloud-identities)

   ![Avancerad konfiguration](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Enkel inloggning för Windows-appar

Flöde för Kerberos-delegering i Azure AD Application Proxy startar när Azure AD autentiserar användaren i molnet. När begäran kommer lokalt, utfärdar Azure AD Application Proxy connector en Kerberos-biljett för användarens räkning genom att interagera med din lokala Active Directory. Den här processen kallas för som Kerberos-begränsad delegering (KCD). I nästa fas skickas en begäran till backend-program med Kerberos-biljett. 

Det finns flera protokoll som definierar hur du skickar denna begäran. De flesta Windows-servrar förvänta dig att förhandla med SPNEGO. Detta protokoll stöds på Azure AD Application Proxy, men är inaktiverad som standard. En server kan vara konfigurerade för SPNEGO eller standard KCD, men inte båda.

Om du konfigurerar en koppling dator för SPNEGO se till att alla andra kopplingar i gruppen anslutningen konfigureras också med SPNEGO. Program förväntas standard KCD ska dirigeras via andra kopplingar som inte är konfigurerade för SPNEGO.
 

Aktivera SPNEGO:

1. Öppna en kommandotolk som kör som administratör.
2. Kör följande kommandon på anslutningstjänstservrarna som behöver SPNEGO från Kommandotolken.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Mer information om Kerberos finns [alla du vill veta om Kerberos-begränsad delegering (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Appar för icke-Windows vanligtvis användaren användarnamn eller SAM-namnen i stället för domänen e-postadresser. Om denna situation gäller för dina program, måste du konfigurera delegerade inloggningsfältet identitet för att ansluta din molnidentiteter till programmet identiteter. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Arbeta med olika lokala och molnidentiteter
Programproxy förutsätter att användarna har exakt samma identitet i molnet och lokalt. Om detta inte är fallet kan du fortfarande använda KCD för enkel inloggning. Konfigurera en **delegerade identitet för nätverksinloggning** för varje program för att ange vilken identitet som ska användas när du utför enkel inloggning.  

Den här funktionen kan många organisationer som har olika lokalt och molnidentiteter har enkel inloggning från molnet till lokala appar utan att användaren att ange olika användarnamn och lösenord. Detta inkluderar organisationer som:

* Har flera domäner internt (joe@us.contoso.com, joe@eu.contoso.com) och en enda domän i molnet (joe@contoso.com).
* Ha icke-dirigerbara domännamn internt (joe@contoso.usa) och ett giltigt lösenord i molnet.
* Använd inte domännamn internt (Johan)
* Använd olika alias på lokalt och i molnet. Till exempel joe-johns@contoso.com vs. joej@contoso.com  

Du kan välja vilken identitet du ska använda för att hämta Kerberos-biljetten med Application Proxy. Den här inställningen är per program. Vissa av dessa alternativ är lämpliga för system som inte accepterar e-postadressformat, andra har utformats för alternativa inloggningen.

![Delegerad inloggningen identitet parametern skärmbild](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Om delegerade inloggningen identitet används kanske värdet inte unikt över alla domäner eller skogar i organisationen. Du kan undvika det här problemet genom att publicera programmen två gånger med två olika Connector-grupper. Eftersom varje program har en annan målgrupp, kan du ansluta till dess kopplingar till en annan domän.

### <a name="configure-sso-for-different-identities"></a>Konfigurera enkel inloggning för olika identiteter
1. Konfigurera Azure AD Connect-inställningar så att den huvudsakliga identiteten är e-postadress (e-post). Detta görs som en del av anpassa-processen genom att ändra den **användarens huvudnamn** i inställningarna för synkronisering. De här inställningarna avgör också hur användare loggar in på Office 365, Windows10 enheter och andra program som använder Azure AD som deras identitet store.  
   ![Identifiera användare skärmbild - UPN-namnet listrutan](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Välj i programmet konfigurationsinställningarna för programmet som du vill ändra den **delegerad inloggningen identitet** som ska användas:

   * Användarens huvudnamn (till exempel joe@contoso.com)
   * Alternativa UPN-namnet (till exempel joed@contoso.local)
   * Användarnamnet som en del av användarens huvudnamn (till exempel Johan)
   * Användarnamnet som en del av alternativt UPN-namn (till exempel joed)
   * Lokala SAM-kontonamn (beroende på konfigurationen av domänkontrollanten)

### <a name="troubleshooting-sso-for-different-identities"></a>Felsökning av enkel inloggning för olika identiteter
Om det finns ett fel i SSO-processen, det visas i händelseloggen för koppling datorn enligt beskrivningen i [felsökning](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Men i vissa fall skickas har begäran till backend-programmet när det här programmet svarar i olika HTTP-svar. Felsökning av dessa fall ska starta genom att undersöka händelsenummer 24029 på connector-dator i händelseloggen för Application Proxy-sessionen. Det användar-ID som användes för delegering visas i fältet ”användare” i händelseinformationen. Om du vill aktivera sessionsloggen Välj **visa analytiska loggar och felsökningsloggar** i event viewer Visa-menyn.

## <a name="next-steps"></a>Nästa steg

* [Så här konfigurerar du ett Application Proxy-program att använda Kerberos-begränsad delegering](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Felsökning av problem med Application Proxy](../active-directory-application-proxy-troubleshoot.md)


Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

