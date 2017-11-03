---
title: "Azure Active Directory direkt-autentisering säkerhet djupdykning | Microsoft Docs"
description: "Den här artikeln beskrivs hur Azure Active Directory (AD Azure) direktautentisering skyddar dina lokala konton."
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: a5feadd851b166d0a9a77bd1d124cdd599d5d701
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory direkt-autentisering säkerhet djupdykning

Den här artikeln innehåller en mer detaljerad beskrivning på hur direkt autentisering fungerar. Den fokuserar mer om säkerhetsaspekterna av funktionen. Det här avsnittet ska vara av intresse för säkerhet och IT-administratörer, marknadsföringschef efterlevnad och säkerhet polis och andra IT-tekniker som är ansvariga för IT-säkerhet och regelefterlevnad på små och medelstora organisationer eller stora företag.

Avsnitt som beskrivs är:
- Detaljerad teknisk information om hur autentisering agenter installerats och registrerats.
- Detaljerad teknisk information om kryptering av lösenord vid användarinloggning.
- Säkerheten för kanaler mellan lokal autentisering agenter och Azure Active Directory (AD Azure).
- Detaljerad teknisk information om hur autentisering agenter hålls inaktiv säker.
- Andra säkerhetsrelaterade avsnitt.

## <a name="key-security-capabilities"></a>Viktiga säkerhetsfunktioner

Dessa är nyckelsäkerhet aspekter av den här funktionen:
- De bygger på en säker flera innehavare arkitektur som ger isolering av inloggning begäranden mellan klienter behålls.
- Lokala lösenord lagras aldrig i molnet i någon form.
- Lokal autentisering agenter som lyssna och svara på förfrågningar om verifiering lösenord, kan du bara göra utgående anslutningar från i nätverket. Det finns inga krav för att installera dessa agenter för autentisering i ett perimeternätverk (DMZ).
- Endast standardportar (80 och 443) används för utgående kommunikation från agenter för autentisering till Azure AD. Inga inkommande portar måste öppnas i brandväggen. 
  - Port 443 används för alla autentiserade utgående kommunikation
  - Port 80 används bara för att ladda ned den listor över återkallade certifikat (CRL) för att säkerställa att ingen av de certifikat som används av funktionen har återkallats.
  - Se [här](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites) för en fullständig lista över nätverkskrav.
- Lösenord som anges av användaren under inloggning krypteras i molnet innan som har godkänts av lokal autentisering agenter för verifiering mot Active Directory.
- HTTPS-kanal mellan Azure AD och ett lokalt autentiseringsagent skyddas av ömsesidig autentisering.
- Det integreras sömlöst med funktionerna i Azure AD cloud skydd, till exempel principer för villkorlig åtkomst (inklusive Multifaktorautentisering), identitetsskydd och smarta kontoutelåsning.

## <a name="components-involved"></a>Komponenter som ingår

Allmän information om fungerar, Azure AD-tjänsten och datasäkerhet finns det [Säkerhetscenter](https://azure.microsoft.com/support/trust-center/). Följande komponenter ingår när används direkt autentisering för användarens inloggning:
- **Azure AD-STS**: en tillståndslös säkerhet säkerhetstokentjänst (STS) som bearbetar begäranden för inloggning och utfärdar säkerhetstoken till användarens webbläsare, klienter eller tjänster som krävs.
- **Azure Service Bus**: här moln-aktiverat kommunikation med meddelandetjänster för företag och reläer kommunikation som hjälper dig att ansluta lokala lösningar med molnet.
- **Azure AD Connect autentiseringsagent (autentiseringsagent)**: en lokal komponenten som lyssnar efter och svarar på begäran för verifiering av lösenord.
- **Azure SQL Database**: innehåller information om din klient autentisering agenter, inklusive dess metadata och kryptering nycklar.
- **Active Directory (AD)**: din lokala Active Directory, som var dina användarkonton (och deras lösenord) lagras.

## <a name="installation-and-registration-of-authentication-agents"></a>Installation och registrering av autentisering agenter

Agenter för autentisering är installerad och registrerad med Azure AD när du [aktivera direkt-autentisering med Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature) eller när du [lägga till ytterligare autentisering agenter för att säkerställa hög tillgänglighet inloggningsförfrågningar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability). Hämtar en fungerande autentiseringsagent omfattar tre huvudsakliga faser:

- Agentinstallationen för autentisering
- Agentregistreringen för autentisering
- Agentinitieringen för autentisering

Var och en av dessa kommer att diskuteras i detalj i följande avsnitt.

### <a name="authentication-agent-installation"></a>Agentinstallationen för autentisering

Endast globala administratörer kan installera en Agent för autentisering (med Azure AD Connect eller fristående) på en lokal server. Installationen lägger till dessa två nya poster till den **Kontrollpanelen -> program -> program och funktioner** lista:
- Autentiseringsagent själva programmet. Detta körs med [nätverkstjänsten](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) privilegier.
- Updater-program som används för automatisk uppdatering av agenten för autentisering. Detta körs med [lokalt System](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilegier.


### <a name="authentication-agent-registration"></a>Agentregistreringen för autentisering

När du har installerat agenten autentisering måste det registreras med Azure AD. Azure AD gör detta genom att tilldela varje autentiseringsagent ett unikt digitala identitets-certifikat som kan användas för säker kommunikation med Azure AD.

Förfarande vid registrering Binder också autentiseringsagent med din klient så att Azure AD vet att den här specifika autentiseringsagent är den enda behörighet att hantera begäranden för verifiering av lösenord för din klient. Den här proceduren upprepas för varje ny autentiseringsagent att du registrerar.

Här är hur autentisering agenter registrera sig med Azure AD:

![Agentregistreringen](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD först begär en Global administratör för att logga in på Azure AD med sina autentiseringsuppgifter. Under inloggning får autentisering-agenten en åtkomst-Token som kan användas för den globala administratören.
2. Autentisering-agenten genererar sedan ett nyckelpar – en offentlig nyckel och en privat nyckel.
    - Den här nyckelpar genereras med standard **RSA 2048-bitars** kryptering.
    - Den privata nyckeln lämnar aldrig lokala servern där autentisering-agenten har installerats.
3.  Autentisering agenten begär ”registrering” till Azure AD via HTTPS med följande komponenter ingår i denna begäran:
    - Den åtkomst-Token har införskaffade i steg 1.
    - Den offentliga nyckeln som skapats i steg 2.
    - En **certifikatsignering begäran** (CSR eller certifikatbegäran). Detta är att ansöka om ett digitalt identitetscertifikat med Azure AD som dess certifikatutfärdare.
4. Azure AD validerar åtkomst-Token i begäran om registrering och kontrollerar att begäran kommer från en Global administratör.
5. Azure AD signerar och utfärdar ett certifikat för digitala identitet tillbaka till agenten autentisering.
    - Certifikatet har signerats med **Azure AD roten certifikatutfärdaren (CA)**. Observera att denna Certifikatutfärdare är _inte_ i Windows **betrodda rotcertifikatutfärdare** lagras.
    - Den här Certifikatutfärdaren används endast av funktionen direkt-autentisering. Den används bara för signering kundtjänstrepresentanter under agentregistreringen för autentisering.
    - Den här Certifikatutfärdaren används inte av andra tjänster i Azure AD.
    - Certifikatets ämne (**unika namn eller Uppringningsrummer**) är inställd på din **klient-ID**. Detta är ett GUID som identifierar din klient. Detta scope i certifikat för användning med bara din klient.
6. Azure AD lagrar den offentliga nyckeln för agenten autentisering i en Azure SQL-databas, vilket bara Azure AD har åtkomst till.
7. Certifikatet (utfärdats i steg 5) lagras på den lokala servern i den **Windows certifikatarkiv** (särskilt i den  **[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)**  plats), och används av både Authentication Agent och Updater-program.

### <a name="authentication-agent-initialization"></a>Agentinitieringen för autentisering

När autentisering agenten startas för första gången efter registreringen eller efter en server startas om, behövs ett sätt att kommunicera med Azure AD-tjänsten och starta accepterar begäranden för verifiering av lösenord på ett säkert sätt.

![Agentinitieringen](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Här är hur autentisering agenter initieras:



1. Autentisering-Agent startas genom att göra en utgående ”bootstrap” begäran till Azure AD. 
    - Den här bootstrap begäran görs via port 443 och är via en ömsesidigt autentiserad HTTPS-kanal (med samma certifikat som utfärdats under agentregistreringen autentisering).
2. Azure AD besvarar bootstrap begäran genom att tillhandahålla en **åtkomstnyckeln** för en Azure Service Bus-kö som är unik för din klient (identifieras med klient-ID).
3. Autentisering-agenten ansluter beständiga utgående HTTPS (via port 443) till kön. 
    - Det är nu redo att hämta och hantera förfrågningar för verifiering av lösenord.

Om du har flera autentisering agenter som har registrerats för din klient och initieringsprocessen garanterar att var och en ansluter till samma Azure Service Bus-kö. 

## <a name="processing-sign-in-requests"></a>Logga in begäranden 

Diagrammet nedan visar hur direkt autentisering bearbetar begäranden om användare loggar in.

![Bearbetning av inloggning](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

Direkt-autentisering hanterar en inloggning begäran på följande sätt: 

1. En användare försöker komma åt ett program (till exempel Outlook Web App - [https://outlook.office365.com/owa](https://outlook.office365.com/owa).)
2. Om användaren inte redan har signerats omdirigerar programmet webbläsaren till inloggningssidan för Azure AD.
3. Azure AD STS-tjänsten svarar tillbaka med sidan för användare.
4. Användaren anger sitt användarnamn och lösenord i Azure AD-inloggningssida och klickar på knappen ”Logga in”.
5. Användarnamn och lösenord skickas till Azure AD-STS i en POST för HTTPS-begäran.
6. Azure AD STS hämtar offentliga nycklar för alla autentisering agenter registreras i din klientorganisation från Azure SQL-databasen och krypterar lösenord med dem. 
    - Den genererar ”n” krypterade lösenordet värdena för ”n” autentisering agenter som har registrerats på din klient.
7. Azure AD STS placerar verifieringsbegäran lösenord (användarnamn och lösenord för krypterade värden) till Azure Service Bus-kö specifik för din klient.
8. Eftersom autentisering initierad agenterna beständigt är anslutna till Azure Service Bus-kö, hämtar en av de tillgängliga autentisering agenter verifieringsbegäran lösenord.
9. Autentisering-agenten söker efter det krypterade lösenord-värde som är specifik för den offentliga nyckeln (med en identifierare) och dekrypterar den med sin privata nyckel.
10. Autentisering agenten försöker verifiera användarnamnet och lösenordet mot din lokala Active Directory med hjälp av den  **[Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)**  (med den **dwLogonType** parametern **LOGON32_LOGON_NETWORK**). 
    - Det här är samma API som används av Active Directory Federation Services (AD FS) för att logga in användare i ett federerat inloggning scenario.
    - Detta är beroende av lösningsprocessen som standard i Windows Server för att hitta Domain Controller.
11. Autentisering agenten får resultatet från Active Directory (lyckades, användarnamn eller felaktigt lösenord lösenord har upphört att gälla, användaren låses upp, och så vidare).
12. Authentication Agent vidarebefordrar resultatet tillbaka till Azure AD STS via en utgående ömsesidigt autentiserad HTTPS-kanal via port 443. Ömsesidig autentisering använder samma certifikat som utfärdats tidigare autentisering-agenten under registreringen.
13. Azure AD STS verifierar att resultatet för den här korrelerar med specifika inloggning begäran på din klient.
14. Azure AD STS fortsätter med proceduren inloggning som konfigurerats. Till exempel om valideringen av lösenordet lyckades kan användaren vara anropas för multi-Factor Authentication eller omdirigeras till programmet.

## <a name="operational-security-of-authentication-agents"></a>Operativ säkerhet autentisering agenter

För att säkerställa att direkt-autentisering är inaktiv säker förnyar Azure AD med jämna mellanrum sina certifikat. Dessa uppdateringar utlöses från Azure AD och regleras inte av autentisering agenterna sig själva.

![Driftsäkerhet](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Här visas hur en autentiseringsagent förnyar sin förtroende med Azure AD:

1. Authentication Agent pingar regelbundet Azure AD (några timmars mellanrum) för att kontrollera om det är dags att förnya sina certifikat. 
    - Den här kontrollen görs via en ömsesidigt autentiserad HTTPS-kanal (med samma certifikat som utfärdats under registrering).
2. Om tjänsten visar att det är dags att förnya, autentisering-agenten genererar ett nytt nyckelpar: en offentlig nyckel och en privat nyckel.
    - Dessa nycklar genereras med standard **RSA 2048-bitars** kryptering.
    - Den privata nyckeln lämnar aldrig lokal server.
3. Autentisering agenten sedan begär ”certifikatförnyelse” till Azure AD via HTTPS med följande komponenter ingår i denna begäran:
    - Det befintliga certifikatet (hämtades från **CERT_SYSTEM_STORE_LOCAL_MACHINE** plats på Windows certifikatarkiv). Det finns ingen Global administratör ingår i den här proceduren så att det finns ingen åtkomst-Token behövs för den globala administratören.
    - Den offentliga nyckeln som skapats i steg 2.
    - En **certifikatsignering begäran** (CSR eller certifikatbegäran). Detta är att ansöka om ett nytt digitala identitets-certifikat med Azure AD som dess certifikatutfärdare.
4. Azure AD verifierar det befintliga certifikatet i förfrågan om certifikatförnyelse. Sedan kontrollerar att begäran kommer från en Agent för autentisering som registrerats på din klient.
5. Om det befintliga certifikatet fortfarande är giltigt, Azure AD sedan signerar ett nytt digitala identitets-certifikat och skickar det nya certifikatet till agenten autentisering. 
6. Om det befintliga certifikatet har upphört att gälla bort Azure AD Authentication Agent från din klient listan med agenter som registrerade autentisering. En Global administratör måste manuellt installera och registrera en ny Agent för autentisering.
    - Certifikatet har signerats med **Azure AD roten certifikatutfärdaren (CA)**.
    - Certifikatets ämne (**unika namn eller Uppringningsrummer**) är inställd på din **klient-ID** ett GUID som identifierar din klient. Certifikatet är som är begränsad till endast din klient.
6. Azure AD lagrar Authentication Agent ”new” publika nyckel i en Azure SQL database som endast har den åtkomst till. Och upphäver ”gamla” offentliga nyckeln som associeras med agenten autentisering.
7. Det nya certifikatet (som har utfärdats i steg 5) lagras på servern i den **Windows certifikatarkiv** (särskilt i den  **[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)**  plats).
    - Eftersom förtroende förnyelse proceduren händer icke-interaktivt (utan förekomst av den globala administratören), autentisering-agenten inte längre har åtkomst till uppdatera det befintliga certifikatet i den **CERT_SYSTEM_STORE_LOCAL_MACHINE** plats. Observera att certifikatet i den **CERT_SYSTEM_STORE_LOCAL_MACHINE** plats tas inte bort under den här proceduren.
8. Det nya certifikatet används för autentisering från och med nu på. Varje efterföljande förnyat certifikat ersätter certifikatet i den **CERT_SYSTEM_STORE_LOCAL_MACHINE** plats.

## <a name="auto-update-of-authentication-agents"></a>Automatisk uppdatering av autentisering agenter

Update-programmet uppdateras automatiskt agenten autentisering när en ny version släpps. Den hanterar inte alla begäranden för verifiering av lösenord för din klient. 

Azure AD är värd för den nya versionen av programvara som en signerad **Windows Installer-paketet (MSI)**. MSI-filerna signeras med hjälp av [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) med **SHA256** som digest-algoritm. 

![Automatisk uppdatering](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Här visas hur en autentiseringsagent hämtar automatiskt uppdaterad:

1. Uppdateringsfilen pingar regelbundet Azure AD (varje timme) för att kontrollera om det finns en ny version av agenten för autentisering. 
    - Den här kontrollen görs via en ömsesidigt autentiserad HTTPS-kanal (med samma certifikat som utfärdats under registrering). Agenten för autentisering och uppdateringsfilen dela det certifikat som lagras på servern.
2. Om det finns en ny version returnerar signerade MSI i Azure AD tillbaka till uppdateringsfilen.
3. Uppdateringsfilen verifierar att MSI-filerna är signerat av Microsoft.
4. Uppdateringsfilen kör MSI-filerna. Den här åtgärden utför följande steg (Observera att uppdateringsfilen körs med [lokalt System](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilegier):
    - Stoppar tjänsten autentiseringsagent.
    - Installerar den nya versionen av agenten för autentisering på servern.
    - Startar om Authentication Agent-tjänsten.

>[!NOTE]
>Om du har flera autentisering agenter registrerad på din klient, Azure AD inte förnya sina certifikat och uppdatera dem på samma gång. I stället har Azure AD så gradvis för att säkerställa hög tillgänglighet för inloggning-begäranden.


## <a name="next-steps"></a>Nästa steg
- [**Aktuella begränsningar** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – Lär dig vilka scenarier som stöds och vilka som inte är.
- [**Snabbstart** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) – komma igång och körs direkt i Azure AD-autentisering.
- [**Smartkort kontoutelåsning** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -konfigurerar Smart kontoutelåsning kapaciteten på din klient för att skydda användarkonton.
- [**Så här fungerar** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -lär du dig grunderna för hur Azure AD direkt autentisering fungerar.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-pass-through-authentication-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
- [**Azure AD sömlös SSO** ](active-directory-aadconnect-sso.md) -mer information om den här funktionen.

