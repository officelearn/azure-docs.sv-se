---
title: Enkel inloggning med programproxy | Microsoft Docs
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
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 4ce76f1156d4d8d85f5e10bb750b012f93ba7afb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366688"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos-begränsad delegering för enkel inloggning till dina appar med Application Proxy

Du kan tillhandahålla enkel inloggning för lokala program som publicerats via programproxy som skyddas med integrerad Windows-autentisering. Dessa program kräver en Kerberos-biljett för åtkomst. Programproxy använder Kerberos-begränsad delegering (KCD) som stöd för dessa program. 

Du kan aktivera enkel inloggning för dina program med integrerad Windows autentisering (IWA) genom att ge Application Proxy-kopplingar behörighet i Active Directory för att personifiera användare. Anslutningarna använda den här behörigheten för att skicka och ta emot token för deras räkning.

## <a name="how-single-sign-on-with-kcd-works"></a>Hur enkel inloggning med KCD fungerar
Det här diagrammet beskriver flödet när en användare försöker få åtkomst till ett lokalt program som använder IWA.

![Flödesdiagram för Microsoft AAD-autentisering](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Användaren anger URL: en för att komma åt lokala program via programproxy.
2. Programproxyn omdirigerar begäran till Azure AD-autentiseringstjänster till preauthenticate. Nu kan gäller Azure AD alla tillämpliga autentisering och auktoriseringsprinciper, till exempel multifaktorautentisering. Om användaren har verifierats, Azure AD skapar en token och skickar det till användaren.
3. Användaren skickar token till Application Proxy.
4. Programproxyn verifierar token och hämtar User Principal Name (UPN) från den och skickar sedan begäran, UPN och Service Principal Name (SPN) till anslutningstjänsten via en autentiserad dually säker kanal.
5. Anslutningen utför Kerberos-begränsad delegering (KCD)-förhandling med lokalt AD, Personifiera användare för att få en Kerberos-token för programmet.
6. Active Directory skickar Kerberos-token för programmet till anslutningstjänsten.
7. Kopplingen skickar den ursprungliga begäran till programservern, med hjälp av Kerberos-token som togs emot från AD.
8. Programmet skickar svaret till anslutningstjänsten, som sedan returneras till Application Proxy-tjänsten och slutligen till användaren.

## <a name="prerequisites"></a>Förutsättningar
Innan du sätter igång med enkel inloggning för IWA program, kontrollera att din miljö är redo med följande inställningar och konfigurationer:

* Dina appar, t.ex SharePoint Web apps är inställda på att använda integrerad Windows-autentisering. Mer information finns i [aktivera stöd för Kerberos-autentisering](https://technet.microsoft.com/library/dd759186.aspx), eller SharePoint finns i [planera för Kerberos-autentisering i SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Alla appar har [Service Principal Names](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Den server som kör anslutningstjänsten och den server som kör appen är domänanslutna som ingår i samma domän eller domäner. Mer information om domänanslutning finns [ansluta en dator till en domän](https://technet.microsoft.com/library/dd807102.aspx).
* Servern som kör anslutningstjänsten har behörighet att läsa attributet TokenGroupsGlobalAndUniversal för användare. Den här standardinställningen kanske har påverkats av security Härdning av miljön.

### <a name="configure-active-directory"></a>Konfigurera Active Directory
Active Directory-konfigurationen varierar beroende på om programproxy-kopplingen och programserver är i samma domän eller inte.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Anslutningen och programserver i samma domän
1. I Active Directory, går du till **verktyg** > **användare och datorer**.
2. Välj den server som kör anslutningstjänsten.
3. Högerklicka och välj **egenskaper** > **delegering**.
4. Välj **lita på den här datorn för delegering till angivna tjänster**. 
5. Under **tjänster som det här kontot kan ge delegerade autentiseringsuppgifter** mervärde SPN-identitet för programservern. På så sätt kan Application Proxy Connector att personifiera användare i AD mot de program som definierats i listan.

   ![Egenskaper för Server Connector Instanser skärmbild](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Anslutningen och programservrar i olika domäner
1. En lista över förutsättningar för att arbeta med KCD över domäner finns i [Kerberos-begränsad delegering över domäner](https://technet.microsoft.com/library/hh831477.aspx).
2. Använd den `principalsallowedtodelegateto` egenskapen Connector-server för att aktivera Application Proxy för att delegera för Connector-server. Programservern är `sharepointserviceaccount` och delegerat servern är `connectormachineaccount`. Använd den här koden för Windows 2012 R2, som exempel:

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Sharepointserviceaccount kan vara datorkontot Service Pack eller ett tjänstkonto som Service Pack-programpoolen körs under.

## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning 
1. Publicera programmet enligt instruktionerna i [publicera program med Application Proxy](application-proxy-publish-azure-portal.md). Se till att välja **Azure Active Directory** som den **förautentisering metoden**.
2. När ditt program visas i listan över företagsprogram, markerar du det och klickar på **enkel inloggning**.
3. Ange läge för enkel inloggning **integrerad Windows-autentisering**.  
4. Ange den **intern program-SPN** på programservern. I det här exemplet är SPN för vårt publicerade program http/www.contoso.com. Den här SPN måste finnas i listan över tjänster som anslutningen kan ge delegerade autentiseringsuppgifter. 
5. Välj den **delegerad inloggningsidentitet** för anslutningstjänsten att använda för användare. Mer information finns i [arbeta med olika lokala och molnidentiteter](#Working-with-different-on-premises-and-cloud-identities)

   ![Avancerade programkonfiguration](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Enkel inloggning för icke-Windows-appar

Flödet för Kerberos-delegering i Azure AD Application Proxy startar när Azure AD autentiserar användaren i molnet. När begäran kommer lokala, utfärdar Azure AD Application Proxy connector en Kerberos-biljett för användarens räkning genom att interagera med din lokala Active Directory. Den här processen kallas som Kerberos-begränsad delegering (KCD). I nästa fas skickas en begäran till backend-applikationer med Kerberos-biljetten. 

Det finns flera protokoll som definierar hur du skickar sådana begäranden. De flesta icke-Windows-servrar som förväntar sig att förhandla med SPNEGO. Detta protokoll stöds på Azure AD Application Proxy, men är inaktiverad som standard. En server kan vara konfigurerade för SPNEGO eller standard KCD, men inte båda.

Om du konfigurerar en connector-dator för SPNEGO, se till att alla andra kopplingar i gruppen Connector konfigureras också med SPNEGO. Program förväntas standard KCD ska dirigeras via andra kopplingar som inte är konfigurerade för SPNEGO.
 

För att aktivera SPNEGO:

1. Öppna en kommandotolk som kör som administratör.
2. Från Kommandotolken kör du följande kommandon på anslutningstjänstservrarna som behöver SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Mer information om Kerberos finns i [alla du vill veta om Kerberos-begränsad delegering (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Appar för icke-Windows vanligtvis användaren användarnamn eller SAM-kontonamn i stället för domänen e-postadresser. Om denna situation gäller för dina program, måste du konfigurera delegerade inloggningen identitetsfältet för att ansluta dina molnidentiteter till dina program-identiteter. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Arbeta med olika lokala och molnidentiteter
Programproxy förutsätter att användarna har exakt samma identitet i molnet och lokalt. Om detta inte är fallet kan du fortfarande använda KCD för enkel inloggning. Konfigurera en **delegerad inloggningsidentitet** för programmen för att ange vilken identitet som ska användas när du utför enkel inloggning.  

Den här funktionen kan många organisationer som har olika lokala och molnidentiteter att använda enkel inloggning från molnet till lokala appar utan att användaren ange olika användarnamn och lösenord. Detta inkluderar organisationer som:

* Har flera domäner internt (joe@us.contoso.com, joe@eu.contoso.com) och en enda domän i molnet (joe@contoso.com).
* Har icke-dirigerbara domännamn internt (joe@contoso.usa) och en juridiska i molnet.
* Använd inte domännamn internt (joe)
* Använd olika alias på lokalt och i molnet. Till exempel joe-johns@contoso.com vs. joej@contoso.com  

Du kan välja vilka identitet för att använda för att hämta Kerberos-biljetten med Application Proxy. Den här inställningen är per program. Vissa av dessa alternativ är lämpliga för system som inte accepterar e-postadressformat, andra är utformade för den alternativa inloggningen.

![Delegerad inloggningen identitet parametern skärmbild](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Om delegerad inloggningsidentitet används, kan värdet inte vara unika mellan alla domäner och skogar i organisationen. Du kan undvika det här problemet genom att publicera dessa program två gånger med två olika Anslutningsapp-grupper. Eftersom varje program har en annan målgrupp, kan du ansluta till dess anslutningar till en annan domän.

### <a name="configure-sso-for-different-identities"></a>Konfigurera enkel inloggning för olika identiteter
1. Konfigurera Azure AD Connect-inställningar så att den viktigaste identiteten är e-postadress (e-post). Detta görs som en del av anpassa-processen genom att ändra den **User Principal Name** i sync-inställningar. De här inställningarna avgör också hur användarna loggar in till Office 365, Windows 10-enheter, och andra program som använder Azure AD som deras identitet store.  
   ![Identifiera användare skärmbild - User Principal Name listrutan](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. I program-konfigurationsinställningarna för programmet som du vill ändra väljer du den **delegerad inloggningsidentitet** som ska användas:

   * Användarens huvudnamn (till exempel joe@contoso.com)
   * Alternativt UPN-namnet (till exempel joed@contoso.local)
   * Användarnamnet är del av användarens huvudnamn (till exempel Johan)
   * Användarnamnet är del av alternativa UPN-namnet (till exempel joed)
   * Den lokala SAM-kontonamn (beroende på konfigurationen av domänkontrollanten)

### <a name="troubleshooting-sso-for-different-identities"></a>Felsökning av enkel inloggning för olika identiteter
Om det finns ett fel i processen för enkel inloggning, den visas i händelseloggen för connector dator enligt beskrivningen i [felsökning](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Men i vissa fall kan begäran skickas har till backend-applikationer medan det här programmet svarar i olika andra HTTP-svar. Felsökning av dessa fall bör börja genom att undersöka händelsenummer 24029 på connector-dator i händelseloggen för Application Proxy-session. Användar-ID som användes för delegering visas i fältet ”användare” i händelseinformationen. Om du vill aktivera sessionsloggen Välj **visa analytiska loggar och felsökningsloggar** i event viewer Visa-menyn.

## <a name="next-steps"></a>Nästa steg

* [Så här konfigurerar du ett programproxy-program att använda Kerberos-begränsad delegering](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)


Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

