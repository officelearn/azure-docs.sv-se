---
title: Azure Active Directory direkt-autentisering säkerhet ingående | Microsoft Docs
description: Den här artikeln beskrivs hur Azure Active Directory (AD Azure) direkt autentisering skyddar dina lokala konton
services: active-directory
keywords: Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cb8382a9801c3570a190259416d846fe518cc6ea
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595044"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory direkt-autentisering säkerhet ingående

Den här artikeln innehåller en mer detaljerad beskrivning av hur Azure Active Directory (AD Azure) direkt autentisering fungerar. Den fokuserar på säkerhetsaspekterna av funktionen. Den här artikeln är för säkerhet och IT-administratörer, marknadsföringschef efterlevnad och säkerhet polis och andra IT-tekniker som är ansvariga för IT-säkerhet och efterlevnad i små till medelstora storleksändras organisationer eller stora företag.

Avsnitt som beskrivs är:
- Detaljerad teknisk information om hur du installerar och registrerar agenter för autentisering.
- Detaljerad teknisk information om kryptering av lösenord vid användarinloggning.
- Säkerheten för kanaler mellan lokal autentisering agenter och Azure AD.
- Detaljerad teknisk information om hur du kan skydda autentisering agenter inaktiv.
- Andra säkerhetsrelaterade avsnitt.

## <a name="key-security-capabilities"></a>Viktiga säkerhetsfunktioner

Dessa är nyckelsäkerhet aspekter av den här funktionen:
- De bygger på en säker flera innehavare arkitektur som ger isolering av inloggning begäranden mellan klienter behålls.
- Lokala lösenord lagras aldrig i molnet i någon form.
- Lokal autentisering agenter som lyssna efter och svara på, lösenord validering begäranden endast upprätta utgående anslutningar från i nätverket. Det finns inga krav för att installera dessa agenter för autentisering i ett perimeternätverk (DMZ).
- Endast standardportar (80 och 443) används för utgående kommunikation från agenter för autentisering till Azure AD. Du behöver inte öppna ingående portar i brandväggen. 
  - Port 443 används för alla autentiserade utgående kommunikation.
  - Port 80 används bara för att ladda ned den listor över återkallade certifikat (CRL) för att säkerställa att ingen av de certifikat som används av den här funktionen har återkallats.
  - En fullständig lista över nätverkskraven finns [Azure Active Directory direkt-autentisering: Snabbstart](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).
- Lösenord som användare anger under inloggning krypteras i molnet innan lokal autentisering agenter acceptera villkoren för verifiering mot Active Directory.
- HTTPS-kanal mellan Azure AD och lokala autentiseringsagent skyddas med hjälp av ömsesidig autentisering.
- Funktionen integreras sömlöst med Azure AD-molnskydd funktioner, till exempel principer för villkorlig åtkomst (inklusive Azure Multi-Factor Authentication) identitetsskydd och smarta kontoutelåsning.

## <a name="components-involved"></a>Komponenter som ingår

Allmän information om fungerar, Azure AD-tjänsten och datasäkerhet finns det [Säkerhetscenter](https://azure.microsoft.com/support/trust-center/). Följande komponenter ingår när du använder direkt autentisering för användarens inloggning:
- **Azure AD-STS**: en tillståndslös säkerhetstokentjänst (STS) som bearbetar begäranden för inloggning och utfärdar säkerhetstoken till användarens webbläsare, klienter eller tjänster som krävs.
- **Azure Service Bus**: här moln-aktiverat kommunikation med meddelandetjänster för företag och reläer kommunikation som hjälper dig att ansluta lokala lösningar med molnet.
- **Azure AD Connect autentiseringsagent**: en lokal komponenten som lyssnar efter och svarar på begäran för verifiering av lösenord.
- **Azure SQL Database**: innehåller information om din klient autentisering agenter, inklusive deras metadata och kryptering nycklar.
- **Active Directory**: lokala Active Directory, där dina användarkonton och lösenord lagras.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Installation och registrering av agenter för autentisering

Agenter för autentisering är installerad och registrerad med Azure AD när du antingen:
   - [Aktivera direkt-autentisering via Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Lägg till fler agenter för autentisering för att säkerställa hög tillgänglighet för inloggningsförfrågningar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Hämtar en fungerande autentiseringsagent omfattar tre huvudsakliga faser:

1. Agentinstallationen för autentisering
2. Agentregistreringen för autentisering
3. Agentinitieringen för autentisering

I följande avsnitt beskrivs de här faserna i detalj.

### <a name="authentication-agent-installation"></a>Agentinstallationen för autentisering

Endast globala administratörer kan installera en Agent för autentisering (med hjälp av Azure AD Connect eller fristående) på en lokal server. Installationen lägger till två nya poster till den **Kontrollpanelen** > **program** > **program och funktioner** lista:
- Autentiseringsagent själva programmet. Det här programmet körs med [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) privilegier.
- Update-programmet som används för automatisk uppdatering Authentication Agent. Det här programmet körs med [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilegier.

### <a name="authentication-agent-registration"></a>Agentregistreringen för autentisering

När du har installerat agenten autentisering måste det registreras med Azure AD. Azure AD tilldelar varje autentiseringsagent ett unikt, digitala identitet certifikat som kan användas för säker kommunikation med Azure AD.

Förfarande vid registrering Binder också autentiseringsagent med din klient. Detta säkerställer att Azure AD vet att den här specifika autentiseringsagent är den enda behörighet att hantera begäranden för verifiering av lösenord för din klient. Den här proceduren upprepas för varje ny autentiseringsagent att du registrerar.

Autentisering-agenter använder följande steg för att registrera sig med Azure AD:

![Agentregistreringen](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD först begär att en global administratör logga in på Azure AD med sina autentiseringsuppgifter. Under inloggning får autentisering-agenten en åtkomst-token som kan användas för den globala administratören.
2. Autentisering agenten sedan genererar ett nyckelpar: en offentlig nyckel och en privat nyckel.
    - Nyckelparet skapas via standard RSA 2048-bitars kryptering.
    - Den privata nyckeln finns kvar på den lokala servern där Authentication Agent finns.
3. Autentisering agenten begär ”registrering” till Azure AD via HTTPS med följande komponenter ingår i denna begäran:
    - Åtkomst-token har införskaffade i steg 1.
    - Den offentliga nyckeln som skapats i steg 2.
    - En förfrågan om certifikatsignering (CSR eller certifikatbegäran). Den här förfrågan gäller för en digital identitetscertifikat med Azure AD som dess certifikatutfärdare (CA).
4. Azure AD validerar åtkomst-token i begäran om registrering och kontrollerar att begäran kommer från en global administratör.
5. Azure AD sedan loggar och skickar en digital identitetscertifikat tillbaka till Authentication Agent.
    - Rot-CA i Azure AD används för att signera certifikatet. 

     >[!NOTE]
     > Denna Certifikatutfärdare är _inte_ i betrodda rotcertifikatutfärdare för Windows.
    - CA: N används endast av funktionen direkt-autentisering. CA: N används bara för att signera kundtjänstrepresentanter under agentregistreringen för autentisering.
    -  Ingen av de andra Azure AD-tjänsterna använder denna Certifikatutfärdare.
    - Certifikatets ämne (unika namn eller Uppringningsrummer) är inställd på klient-ID. Den här DN är ett GUID som identifierar din klient. Den här DN scope certifikatet för din klient.
6. Azure AD lagrar den offentliga nyckeln för agenten autentisering i en Azure SQL-databas, vilket bara Azure AD har åtkomst till.
7. Certifikatet (utfärdats i steg 5) lagras på den lokala servern i Windows certifikatarkiv (särskilt i den [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) plats). Den används av agenten för autentisering och Updater-program.

### <a name="authentication-agent-initialization"></a>Agentinitieringen för autentisering

När autentisering agenten startas för första gången efter registreringen eller efter en server startas om, behövs ett sätt att kommunicera säkert med Azure AD-tjänsten och starta accepterar begäranden för verifiering av lösenord.

![Agentinitieringen](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Här är hur autentisering agenter har initierats:

1. Autentisering-agenten är en utgående bootstrap-begäran till Azure AD. 
    - Den här förfrågan görs via port 443 och är via en ömsesidigt autentiserad HTTPS-kanal. Denna begäran använder samma certifikat som utfärdades under agentregistreringen för autentisering.
2. Azure AD svarar på begäran genom att tillhandahålla en snabbtangent för en Azure Service Bus-kö som är unik för din klient och som identifieras av klient-ID.
3. Autentisering-agenten ansluter beständiga utgående HTTPS (via port 443) till kön. 
    - Autentisering-agenten är nu redo att hämta och hantera begäranden om verifiering av lösenord.

Om du har flera autentisering agenter som har registrerats för din klient och initieringsprocessen garanterar att var och en ansluter till samma Service Bus-kö. 

## <a name="process-sign-in-requests"></a>Bearbeta inloggning begäranden 

Följande diagram visar hur direkt autentisering bearbetar begäranden om användare loggar in.

![Process-inloggning](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

Direkt-autentisering hanterar en inloggning begäran på följande sätt: 

1. En användare försöker komma åt ett program, till exempel [Outlook Web App](https://outlook.office365.com/owa).
2. Om användaren inte redan har signerats omdirigerar programmet webbläsaren till inloggningssidan för Azure AD.
3. Azure AD-STS-tjänsten svarar tillbaka med den **användarinloggning** sidan.
4. Användaren anger sitt användarnamn och lösenord i den **användarinloggning** sidan och väljer den **inloggning** knappen.
5. Användarnamn och lösenord skickas till Azure AD-STS i en POST för HTTPS-begäran.
6. Azure AD STS hämtar offentliga nycklar för alla autentisering agenter som registrerats på din klient från Azure SQL-databasen och lösenordet krypteras med hjälp av dem. 
    - Den genererar ”N” krypterade lösenord för ”N” autentisering agenter som är registrerad på din klient.
7. Azure AD STS placerar verifieringsbegäran lösenord som består av användarnamn och lösenord för krypterade värden till Service Bus-kö specifik för din klient.
8. Eftersom initierad autentisering agenterna beständigt är ansluten till Service Bus-kö är hämtar en av de tillgängliga agenter för autentisering verifieringsbegäran lösenord.
9. Autentisering-agenten söker efter det krypterade lösenord-värde som är specifik för den offentliga nyckeln med hjälp av en identifierare och dekrypterar den med hjälp av den privata nyckeln.
10. Autentisering agenten försöker verifiera användarnamnet och lösenordet mot lokala Active Directory med hjälp av den [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) med den **dwLogonType** parametern värdet **LOGON32_LOGON_NETWORK**. 
    - Detta API är samma API som används av Active Directory Federation Services (AD FS) för att logga in användare i ett scenario med federerad inloggning.
    - Detta API är beroende av lösningsprocessen som standard i Windows Server för att hitta domänkontrollanten.
11. Autentisering-agenten tar emot resultatet från Active Directory, till exempel lyckades, användarnamn eller felaktigt lösenord eller lösenordet har upphört att gälla.
12. Authentication Agent vidarebefordrar resultatet tillbaka till Azure AD STS via en utgående ömsesidigt autentiserad HTTPS-kanal via port 443. Ömsesidig autentisering använder certifikat som utfärdats för den autentiseringsagent tidigare under registreringen.
13. Azure AD STS verifierar att resultatet för den här korrelerar med specifika inloggning begäran på din klient.
14. Azure AD STS fortsätter med proceduren inloggning som konfigurerats. Till exempel om valideringen av lösenordet lyckades kan användaren vara anropas för multi-Factor Authentication eller omdirigeras till programmet.

## <a name="operational-security-of-the-authentication-agents"></a>Operativ säkerhet autentisering agenter

För att säkerställa att direkt-autentisering är inaktiv säker, förnyar Azure AD med jämna mellanrum certifikat för autentisering agenter. Azure AD utlöser förnyelser. Förnyelser regleras inte av autentisering agenterna sig själva.

![Driftsäkerhet](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Förnya ett autentiseringsagent förtroende med Azure AD:

1. Authentication Agent pingar regelbundet Azure AD varje några timmar för att kontrollera om det är dags att förnya sina certifikat. 
    - Den här kontrollen görs via en ömsesidigt autentiserad HTTPS-kanal och använder samma certifikat som utfärdades under registreringen.
2. Om tjänsten visar att det är dags att förnya, autentisering-agenten genererar ett nytt nyckelpar: en offentlig nyckel och en privat nyckel.
    - Dessa nycklar skapas via standard RSA 2048-bitars kryptering.
    - Den privata nyckeln lämnar aldrig lokal server.
3. Autentisering agenten sedan begär ”certifikatförnyelse” till Azure AD via HTTPS med följande komponenter ingår i denna begäran:
    - Det befintliga certifikatet som har hämtats från CERT_SYSTEM_STORE_LOCAL_MACHINE plats i Windows certifikatarkiv. Det finns ingen global administratör ingår i den här proceduren så att det finns ingen åtkomst-token som behövs för den globala administratören.
    - Den offentliga nyckeln som skapats i steg 2.
    - En förfrågan om certifikatsignering (CSR eller certifikatbegäran). Den här förfrågan gäller för en ny digitala identitetscertifikat med Azure AD som dess certifikatutfärdare.
4. Azure AD verifierar det befintliga certifikatet i förfrågan om certifikatförnyelse. Sedan kontrollerar att begäran kommer från en Agent för autentisering som registrerats på din klient.
5. Om det befintliga certifikatet fortfarande är giltigt, Azure AD sedan signerar ett nytt digitala identitets-certifikat och skickar det nya certifikatet till agenten autentisering. 
6. Om det befintliga certifikatet har upphört att gälla bort Azure AD Authentication Agent från din klient listan med agenter som registrerade autentisering. En global administratör måste manuellt installera och registrera en ny Agent för autentisering.
    - Använda Azure AD-rot-CA för att signera certifikatet.
    - Ange certifikatets ämne (unika namn eller Uppringningsrummer) till din klient-ID, ett GUID som identifierar din klient. Det unika namnet scope certifikatet till bara din klient.
6. Azure AD lagrar den offentliga nyckeln för agenten autentisering i en Azure SQL database som endast har den åtkomst till. Den gamla offentliga nyckeln som associeras med den autentiseringsagent blir också ogiltig.
7. Det nya certifikatet (utfärdats i steg 5) lagras på servern i Windows certifikatarkiv (särskilt i den [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) plats).
    - Eftersom förtroende förnyelse proceduren händer icke-interaktivt (utan förekomst av den globala administratören), har autentisering-agenten inte längre åtkomst till uppdatering av befintligt certifikat på CERT_SYSTEM_STORE_LOCAL_MACHINE plats. 
    
   > [!NOTE]
   > Den här proceduren tar inte bort själva certifikaten från CERT_SYSTEM_STORE_LOCAL_MACHINE plats.
8. Det nya certifikatet används för autentisering från och med nu på. Varje efterföljande förnyat certifikat ersätter certifikatet i CERT_SYSTEM_STORE_LOCAL_MACHINE plats.

## <a name="auto-update-of-the-authentication-agents"></a>Automatisk uppdatering av agenterna för autentisering

Update-programmet uppdateras automatiskt agenten autentisering när en ny version släpps. Programmet hanterar inte alla begäranden för verifiering av lösenord för din klient. 

Azure AD är värd för den nya versionen av programvara som en signerad **Windows Installer-paketet (MSI)**. MSI-filerna signeras med hjälp av [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) med SHA256 som digest-algoritm. 

![Automatisk uppdatering](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Att uppdateras automatiskt en autentiseringsagent:

1. Updater programmet pingar Azure AD varje timme för att kontrollera om det finns en ny version av agenten för autentisering. 
    - Den här kontrollen görs via en ömsesidigt autentiserad HTTPS-kanal genom att använda samma certifikat som utfärdades under registreringen. Agenten för autentisering och uppdateringsfilen dela det certifikat som lagras på servern.
2. Om det finns en ny version returnerar signerade MSI i Azure AD tillbaka till uppdateringsfilen.
3. Uppdateringsfilen verifierar att MSI-filerna är signerat av Microsoft.
4. Uppdateringsfilen kör MSI-filerna. Den här åtgärden omfattar följande steg:

 > [!NOTE]
 > Uppdateringsfilen körs med [lokalt System](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilegier.

    - Stoppar tjänsten autentiseringsagent
    - Installerar den nya versionen av agenten för autentisering på servern
    - Startar om Authentication Agent-tjänsten

>[!NOTE]
>Om du har flera autentisering agenter registrerad på din klient, Azure AD inte förnya sina certifikat och uppdatera dem på samma gång. I stället har Azure AD så gradvis för att säkerställa hög tillgänglighet för inloggningsförfrågningar.
>


## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är.
- [Snabbstart](active-directory-aadconnect-pass-through-authentication-quick-start.md): komma igång direkt i Azure AD för autentisering.
- [Smartkort kontoutelåsning](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Konfigurera Smart kontoutelåsning kapaciteten på din klient skydda användarkonton.
- [Så här fungerar](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Lär dig grunderna för hur Azure AD direkt autentisering fungerar.
- [Vanliga frågor och svar](active-directory-aadconnect-pass-through-authentication-faq.md): få svar på vanliga frågor och svar.
- [Felsöka](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direkt-autentisering.
- [Azure AD sömlös SSO](active-directory-aadconnect-sso.md): Lär dig mer om den här funktionen.
