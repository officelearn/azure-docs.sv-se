---
title: Enkel inloggning med Application Proxy | Microsoft-dokument
description: Beskriver hur du tillhandahåller enkel inloggning med Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5948fba67d3f071d77192f9ad89bc696fdc0c3cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253460"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos Begränsad delegering för enkel inloggning till dina appar med programproxy

Du kan tillhandahålla enkel inloggning för lokala program som publiceras via Programproxy som är skyddade med integrerad Windows-autentisering. Dessa program kräver en Kerberos-biljett för åtkomst. Programproxy använder Kerberos Constrained Delegation (KCD) för att stödja dessa program. 

Du kan aktivera enkel inloggning till dina program med integrerad Windows-autentisering (IWA) genom att ge Application Proxy-kopplingar behörighet i Active Directory för att personifiera användare. Kopplingarna använder den här behörigheten för att skicka och ta emot token för deras räkning.

## <a name="how-single-sign-on-with-kcd-works"></a>Så här fungerar enkel inloggning med KCD
Det här diagrammet förklarar flödet när en användare försöker komma åt ett lokalt program som använder IWA.

![Flödesdiagram för Microsoft AAD-autentisering](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Användaren anger URL:en för att komma åt det lokala programmet via Programproxy.
2. Programproxy omdirigerar begäran till Azure AD-autentiseringstjänster för att förauktorisera. Nu tillämpar Azure AD alla tillämpliga autentiserings- och auktoriseringsprinciper, till exempel multifaktorautentisering. Om användaren valideras skapar Azure AD en token och skickar den till användaren.
3. Användaren skickar token till Programproxy.
4. Programproxy validerar token och hämtar UPN (User Principal Name) från den, och sedan drar anslutningen UPN och SERVICE Principal Name (SPN) via en vederbörligen autentiserade säker kanal.
5. Anslutningsappen utför CCD-förhandling (Kerberos Constrained Delegation) med den lokala AD:en och personifierar användaren för att hämta en Kerberos-token till programmet.
6. Active Directory skickar Kerberos-token för programmet till connectorn.
7. Anslutningen skickar den ursprungliga begäran till programservern med den Kerberos-token som den tog emot från AD.
8. Programmet skickar svaret till anslutningen, som sedan returneras till application proxy-tjänsten och slutligen till användaren.

## <a name="prerequisites"></a>Krav
Innan du börjar med enkel inloggning för IWA-program kontrollerar du att din miljö är klar med följande inställningar och konfigurationer:

* Dina appar, till exempel SharePoint Web-appar, är inställda på att använda integrerad Windows-autentisering. Mer information finns i [Aktivera stöd för Kerberos-autentisering](https://technet.microsoft.com/library/dd759186.aspx)eller för SharePoint se [Planera för Kerberos-autentisering i SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Alla dina appar har [tjänsthuvudnamn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Servern som kör Anslutningsappen och servern som kör appen är domänansluten och en del av samma domän eller betrodda domäner. Mer information om domänanslutning finns i [Ansluta till en dator till en domän](https://technet.microsoft.com/library/dd807102.aspx).
* Servern som kör anslutningen har åtkomst till attributet TokenGroupsGlobalAndUniversal för användare. Den här standardinställningen kan ha påverkats av säkerhetshärdning av miljön.

### <a name="configure-active-directory"></a>Konfigurera Active Directory
Active Directory-konfigurationen varierar beroende på om programproxy-anslutningen och programservern finns i samma domän eller inte.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Anslutnings- och programserver i samma domän
1. I Active Directory går du till > **Verktygsanvändare och datorer**. **Tools**
2. Välj den server som kör anslutningen.
3. Högerklicka och välj > **Egenskaperdelegering**. **Properties**
4. Välj **Lita på den här datorn för delegering till angivna tjänster .** 
5. Välj **Använd alla autentiseringsprotokoll**.
6. Under **Tjänster som det här kontot kan presentera delegerade autentiseringsuppgifter** lägga till värdet för SPN-identiteten för programservern. Detta gör det möjligt för Application Proxy Connector att personifiera användare i AD mot de program som definierats i listan.

   ![Skärmbild av fönstret Connector-SVR-egenskaper](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Anslutnings- och programserver i olika domäner
1. En lista över förutsättningar för att arbeta med KCD över domäner finns i [Kerberos Constrained Delegation över domäner](https://technet.microsoft.com/library/hh831477.aspx).
2. Använd `principalsallowedtodelegateto` egenskapen för tjänstkontot (dator eller dedikerat domänanvändarkonto) för webbprogrammet för att aktivera Kerberos-autentiseringsdelegering från Application Proxy (connector). Programservern körs i kontexten `webserviceaccount` och den `connectorcomputeraccount`delegerande servern är . Kör kommandona nedan på en domänkontrollant (med Windows Server 2012 `webserviceaccount`R2 eller senare) i domänen . Använd platta namn (icke UPN) för båda kontona.

   Om `webserviceaccount` det är ett datorkonto använder du följande kommandon:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Om `webserviceaccount` det är ett användarkonto använder du följande kommandon:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning 
1. Publicera din ansökan enligt instruktionerna som beskrivs i [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md). Se till att välja **Azure Active Directory** som **förautentiseringsmetod**.
2. När programmet visas i listan över företagsprogram markerar du det och klickar **på Enkel inloggning**.
3. Ställ in det enda inloggningsläget på **integrerad Windows-autentisering**.  
4. Ange det **interna programsstr-programmet** för programservern. I det här exemplet är SPN för vår publicerade ansökan http/www.contoso.com. Det här SPN:et måste finnas i listan över tjänster som anslutningen kan presentera delegerade autentiseringsuppgifter till. 
5. Välj den **delegerade inloggningsidentiteten** för kopplingen som ska användas för användarnas räkning. Mer information finns i [Arbeta med olika lokala och molnidentiteter](#working-with-different-on-premises-and-cloud-identities)

   ![Avancerad programkonfiguration](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO för appar som inte kommer från Windows

Kerberos-delegeringsflödet i Azure AD Application Proxy startar när Azure AD autentiserar användaren i molnet. När begäran anländer lokalt utfärdar Azure AD Application Proxy-anslutningen en Kerberos-biljett för användarens räkning genom att interagera med den lokala Active Directory. Den här processen kallas Kerberos Constrained Delegation (KCD). I nästa fas skickas en begäran till serverd-programmet med den här Kerberos-biljetten. 

Det finns flera protokoll som definierar hur sådana begäranden ska skickas. De flesta servrar som inte tillhör Windows förväntar sig att förhandla med SPNEGO. Det här protokollet stöds på Azure AD Application Proxy, men är inaktiverat som standard. En server kan konfigureras för SPNEGO eller standard KCD, men inte båda.

Om du konfigurerar en anslutningsmaskin för SPNEGO kontrollerar du att alla andra kopplingar i anslutningsgruppen också är konfigurerade med SPNEGO. Program som förväntar sig standard-KCD ska dirigeras via andra kopplingar som inte är konfigurerade för SPNEGO.
 

Så här aktiverar du SPNEGO:

1. Öppna en kommandotolk som körs som administratör.
2. Kör följande kommandon på anslutningsservrarna som behöver SPNEGO i kommandotolken.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Mer information om Kerberos finns i [Alla du vill veta om Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Icke-Windows-appar vanligtvis användaranvändarnamn eller SAM-kontonamn i stället för domän-e-postadresser. Om den situationen gäller för dina program måste du konfigurera fältet delegerad inloggningsidentitet för att ansluta dina molnidentiteter till dina programidentiteter. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Arbeta med olika lokala och molnidentiteter
Programproxy förutsätter att användarna har exakt samma identitet i molnet och lokalt. Men i vissa miljöer, på grund av företagsprinciper eller programberoenden, kan organisationer behöva använda alternativa ID:n för inloggning. I sådana fall kan du fortfarande använda KCD för enkel inloggning. Konfigurera en **delegerad inloggningsidentitet** för varje program för att ange vilken identitet som ska användas när du utför enkel inloggning.  

Med den här funktionen kan många organisationer som har olika lokala och molnidentiteter ha SSO från molnet till lokala appar utan att användarna behöver ange olika användarnamn och lösenord. Detta inkluderar organisationer som:

* Har flera domänerjoe@us.contoso.cominternt joe@eu.contoso.com( , ) ochjoe@contoso.comen enda domän i molnet ( ).
* Har icke-dirigerbart domännamnjoe@contoso.usainternt ( ) och ett lagligt i molnet.
* Använd inte domännamn internt (joe)
* Använd olika alias lokalt och i molnet. Till exempel, joe-johns@contoso.com jämfört medjoej@contoso.com  

Med Application Proxy kan du välja vilken identitet som ska användas för att hämta Kerberos-biljetten. Den här inställningen är per program. Några av dessa alternativ är lämpliga för system som inte accepterar e-postadress format, andra är utformade för alternativ inloggning.

![Skärmbild av den delegerade inloggningsidentitetsparametern](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Om delegerad inloggningsidentitet används kanske värdet inte är unikt i alla domäner eller skogar i organisationen. Du kan undvika det här problemet genom att publicera dessa program två gånger med två olika Anslutningsgrupper. Eftersom varje program har olika användarmålningar kan du ansluta dess kopplingar till en annan domän.

### <a name="configure-sso-for-different-identities"></a>Konfigurera SSO för olika identiteter
1. Konfigurera Azure AD Connect-inställningar så att huvudidentiteten är e-postadressen (e-postadressen). Detta görs som en del av anpassningsprocessen genom att ändra fältet **Användarnamn** i synkroniseringsinställningarna. De här inställningarna avgör också hur användare loggar in på Office365, Windows10-enheter och andra program som använder Azure AD som identitetsbutik.  
   ![Identifierar användar skärmdump - Listrutan Användarnamn för användare](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. I inställningarna för programkonfiguration för det program som du vill ändra väljer du den **delegerade inloggningsidentitet** som ska användas:

   * Användarens huvudnamn (till exempel) joe@contoso.com
   * Alternativt namn på användarnamn joed@contoso.local(till exempel)
   * Användarnamnsdel av Användarnamn (till exempel joe)
   * Användarnamn del av alternativa användarnamn (till exempel joed)
   * Lokalt SAM-kontonamn (beror på domänkontrollantkonfigurationen)

### <a name="troubleshooting-sso-for-different-identities"></a>Felsöka SSO för olika identiteter
Om det finns ett fel i SSO-processen visas det i händelseloggen för anslutningsmaskinen enligt beskrivningen i [Felsökning](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Men i vissa fall skickas begäran till serverd-programmet medan den här ansökan svarar i flera andra HTTP-svar. Felsökning av dessa ärenden bör börja med att undersöka händelsenummer 24029 på anslutningsmaskinen i händelseloggen för programproxysession. Användaridentiteten som användes för delegering visas i fältet "användare" i händelseinformationen. Om du vill aktivera sessionsloggen väljer du **Visa analytiska och felsökningsloggar** på loggningsvymenyn.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera ett programproxyprogram för att använda Kerberos Constrained Delegation](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)


Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](https://blogs.technet.com/b/applicationproxyblog/)
