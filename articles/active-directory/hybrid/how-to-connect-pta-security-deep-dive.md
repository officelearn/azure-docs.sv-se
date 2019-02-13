---
title: Azure Active Directory-direktautentisering djupgående om säkerhet | Microsoft Docs
description: Den här artikeln beskriver hur Azure Active Directory (Azure AD)-direktautentisering skyddar dina lokala konton
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e1d3058600bca6630c3efcbb860b7f8eeeaf57
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176840"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory-direktautentisering djupgående om säkerhet

Den här artikeln innehåller en detaljerad beskrivning av hur Azure Active Directory (Azure AD)-direktautentisering fungerar. Den fokuserar på säkerhetsaspekter i funktionen. Den här artikeln är för säkerhet och IT-administratörer, chief efterlevnad och säkerhet införlivande och andra IT-proffs som är ansvariga för IT-säkerhet och regelefterlevnad i små till medelstora storlek organisationer eller stora företag.

Ämnen som tas upp inkluderar:
- Detaljerad teknisk information om hur du installerar och registrerar den Autentiseringsagenter.
- Detaljerad teknisk information om kryptering av lösenord under inloggningen.
- Säkerheten för kanaler mellan lokala Autentiseringsagenter och Azure AD.
- Detaljerad teknisk information om hur du kan skydda den Autentiseringsagenter aktiv drift.
- Andra säkerhetsrelaterade ämnen.

## <a name="key-security-capabilities"></a>Viktiga säkerhetsfunktioner

Dessa är viktiga säkerhetsaspekter i den här funktionen:
- Det är byggt på en säker med flera innehavare arkitektur som ger isolering av inloggningsförfrågningar mellan klienter.
- Lokala lösenord lagras aldrig i molnet i någon form.
- Lokala Autentiseringsagenter som lyssnar efter och svara på, lösenord verifiering begäranden endast göra utgående anslutningar från ditt nätverk. Det finns inga krav för att installera agenterna autentisering i ett perimeternätverk (DMZ). Bra tips är att behandla alla servrar som kör Autentiseringsagenter som nivå 0-system (se [referens](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Endast standardportarna (80 och 443) används för utgående kommunikation från agenter för autentisering till Azure AD. Du behöver inte öppna ingående portar i brandväggen. 
  - Port 443 används för alla autentiserade utgående kommunikation.
  - Port 80 används endast för att ladda ned den listor över återkallade certifikat (CRL) för att se till att ingen av de certifikat som används av den här funktionen har återkallats.
  - Läs den fullständiga listan med nätverkskraven [Azure Active Directory-direktautentisering: Snabbstart](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Lösenord som användare anger under inloggning krypteras i molnet innan Autentiseringsagenter lokala godkänner dem för verifiering mot Active Directory.
- HTTPS-kanal mellan Azure AD och lokala autentiseringsagent är skyddat med hjälp av ömsesidig autentisering.
- Skyddar dina användarkonton genom att arbeta smidigt med [principer för Azure AD villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md), inklusive Multi-Factor Authentication (MFA), [blockera äldre](../conditional-access/conditions.md) och av [ Filtrera bort råstyrkeattacker för lösenord](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Komponenter som ingår

Allmän information om Azure AD fungerar, tjänst- och data finns i den [Säkerhetscenter](https://azure.microsoft.com/support/trust-center/). Följande komponenter ingår när du använder direktautentisering för att logga in användare:
- **Azure AD STS**: En tillståndslös säkerhetstokentjänst (STS) som bearbetar inloggningsförfrågningar och utfärdar säkerhetstoken till användarnas webbläsare, klienter eller tjänster som krävs.
- **Azure Service Bus**: Ger molnbaserad kommunikation med företagsmeddelandetjänster och reläer kommunikation som hjälper dig att ansluta dina lokala lösningar till molnet.
- **Azure AD Connect-Autentiseringsagenten**: En komponent i den lokala som lyssnar efter och svarar på begäranden för verifiering av lösenord.
- **Azure SQL Database**: Innehåller information om klientens Autentiseringsagenter, inklusive deras metadata och krypteringsnycklar.
- **Active Directory**: En lokal Active Directory, där dina användarkonton och deras lösenord lagras.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Installation och registrering av den Autentiseringsagenter

Autentiseringsagenter är installerad och registrerad i Azure AD när du antingen:
   - [Aktivera direktautentisering via Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Lägg till flera Autentiseringsagenter för att garantera hög tillgänglighet för inloggningsförfrågningar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Få en fungerande autentiseringsagent omfattar tre huvudsakliga faser:

1. Agentinstallationen för autentisering
2. Agentregistreringen för autentisering
3. Agentinitieringen för autentisering

I följande avsnitt beskrivs de här faserna i detalj.

### <a name="authentication-agent-installation"></a>Agentinstallationen för autentisering

Endast globala administratörer kan installera en Agent för autentisering (med hjälp av Azure AD Connect eller fristående) på en lokal server. Installationen lägger till två nya poster till den **Kontrollpanelen** > **program** > **program och funktioner** lista:
- Autentiseringsagent själva programmet. Det här programmet körs med [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) privilegier.
- Updater-programmet som används för att automatiskt uppdatera den Autentiseringsagenten. Det här programmet körs med [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilegier.

### <a name="authentication-agent-registration"></a>Agentregistreringen för autentisering

När du har installerat agenten autentisering behöver så registrera sig med Azure AD. Azure AD tilldelar varje Autentiseringsagenten ett unikt, digitala identitet certifikat som kan användas för säker kommunikation med Azure AD.

Förfarande vid registrering Binder också autentiseringsagent med din klient. Detta säkerställer att Azure AD vet att den här specifika autentiseringsagent är den enda som har behörighet att hantera begäranden för verifiering av lösenord för din klient. Den här proceduren upprepas för varje ny Autentiseringsagenten att du registrerar.

Autentisering-agenter använder du följande steg för att registrera sig med Azure AD:

![Agentregistreringen](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD först begär att en global administratör logga in på Azure AD med sina autentiseringsuppgifter. Under inloggning skaffar Autentiseringsagenten en åtkomsttoken som kan användas för den globala administratören.
2. Autentisering-agenten sedan genererar ett nyckelpar: en offentlig nyckel och en privat nyckel.
    - Nyckelparet genereras genom standard RSA 2048-bitars kryptering.
    - Den privata nyckeln som är kvar på den lokala servern där den autentiseringsagent finns.
3. Autentisering-agenten begär ”registrering” till Azure AD via HTTPS med följande komponenter ingår i begäran:
    - Åtkomsttoken som anskaffats i steg 1.
    - Den offentliga nyckeln som genererats i steg 2.
    - En förfrågan om certifikatsignering (CSR eller certifikatbegäran). Den här begäran gäller en digital identitetscertifikat med Azure AD som dess certifikatutfärdare (CA).
4. Azure AD verifierar åtkomsttoken i begäran om registrering och kontrollerar att begäran kommer från en global administratör.
5. Azure AD sedan loggar och skickar en digital identitetscertifikat tillbaka till agenten autentisering.
    - Rot-CA i Azure AD används för att signera certifikatet. 

      > [!NOTE]
      > Denna Certifikatutfärdare är _inte_ i betrodda rotcertifikatutfärdare för Windows.
    - CA: N används endast av funktionen direktautentisering. CA: N används bara för att signera CSRs under registreringen av autentisering.
    -  Ingen av de andra tjänsterna i Azure AD använda denna Certifikatutfärdare.
    - Certifikatets ämne (unikt namn eller Uppringningsrummer) är inställd på din klient-ID. Den här DN är ett GUID som unikt identifierar din klient. Den här DN scope certifikat för användning endast med din klient.
6. Azure AD lagrar den offentliga nyckeln för autentisering-agenten i en Azure SQL-databas, vilket endast Azure AD har åtkomst till.
7. Certifikatet (utfärdats i steg 5) lagras på den lokala servern i Windows-certifikatarkiv (särskilt i den [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) plats). Den används av agenten för autentisering och Updater-program.

### <a name="authentication-agent-initialization"></a>Agentinitieringen för autentisering

När Autentiseringsagenten startar för första gången efter registrering eller efter en server startas om, behövs ett sätt att kommunicera säkert med Azure AD-tjänsten och starta accepterar begäranden för verifiering av lösenord.

![Agentinitieringen](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Här är hur Autentiseringsagenter initieras:

1. Autentisering-agenten skickar en utgående bootstrap förfrågan till Azure AD. 
    - Den här begäran har gjorts via port 443 och är via en ömsesidigt autentiserad HTTPS-kanal. Begäran använder samma certifikat som utfärdades under registreringen av autentisering.
2. Azure AD svarar på begäran genom att tillhandahålla en åtkomstnyckel i en Azure Service Bus-kö som är unik för din klient och som identifieras med ditt klientorganisations-ID.
3. Autentisering-agenten ansluter beständiga utgående HTTPS (via port 443) till kön. 
    - Autentisering-agenten är nu klar att hämta och hantera begäranden om verifiering av lösenord.

Om du har flera Autentiseringsagenter registrerad på din klient och sedan initieringsprocessen säkerställer att var och en ansluter till samma Service Bus-kö. 

## <a name="process-sign-in-requests"></a>Bearbeta inloggningsförfrågningar 

Följande diagram visar hur direktautentisering bearbetar begäranden för användare logga in.

![Process-inloggning](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Direktautentisering hanterar en användare loggar in begäran enligt följande: 

1. En användare försöker komma åt ett program, till exempel [Outlook Web App](https://outlook.office365.com/owa).
2. Om du inte redan har signerats dirigerar programmet om webbläsaren till inloggningssidan för Azure AD.
3. Azure AD-STS-tjänsten svarar igen med den **användarinloggning** sidan.
4. Användarna anger sina användarnamn i den **användarinloggning** sidan och väljer den **nästa** knappen.
5. Användaren anger sitt lösenord till den **användarinloggning** sidan och väljer den **inloggning** knappen.
6. Det användarnamn och lösenord skickas till Azure AD-STS i en POST för HTTPS-begäran.
7. Azure AD STS hämtar offentliga nycklar för alla Autentiseringsagenter som är registrerad på din klient från Azure SQL-databasen och krypterar lösenordet genom att använda dem. 
    - Den genererar ”N” krypterade lösenord värden för ”N” autentisering agenter som har registrerats på din klient.
8. Azure AD STS placerar verifieringsbegäran lösenord som består av användarnamnet och krypterat lösenord-värden, till den Service Bus-kö som är specifik för din klient.
9. Eftersom initierad Autentiseringsagenter är kontinuerligt anslutna till Service Bus-kö, hämtar ett av de tillgängliga Autentiseringsagenter begäran om verifiering av lösenord.
10. Autentisering-agenten söker efter det krypterade lösenord-värde som är specifik för den offentliga nyckeln med hjälp av en identifierare och dekrypterar den med hjälp av dess privata nyckel.
11. Autentisering-agenten försöker verifiera användarnamnet och lösenordet mot en lokal Active Directory med hjälp av den [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) med den **dwLogonType** parameteruppsättning till **LOGON32_LOGON_NETWORK**. 
    - Detta API är samma API som används av Active Directory Federation Services (AD FS) för att logga in användare i ett scenario med federerad inloggning.
    - Detta API är beroende av lösningsprocessen som standard i Windows Server för att hitta domänkontrollanten.
12. Autentiseringsagenten tar emot resultatet från Active Directory, till exempel lyckades, användarnamn eller felaktigt lösenord eller lösenordet har upphört att gälla.
13. Autentisering-agenten vidarebefordrar resultatet tillbaka till Azure AD-STS via en utgående HTTPS-kanal ömsesidigt autentiserad via port 443. Ömsesidig autentisering använder det certifikat som tidigare utfärdat till den Autentiseringsagenten under registreringen.
14. Azure AD STS verifierar att det här resultatet kopplat till den specifika inloggningsbegäranden på din klient.
15. Azure AD STS fortsätter med proceduren logga in som konfigurerats. Exempelvis om valideringen av lösenordet kan användaren vara på samma för multi-Factor Authentication eller omdirigeras tillbaka till programmet.

## <a name="operational-security-of-the-authentication-agents"></a>Driftsäkerhet autentisering-agenter

För att säkerställa att direktautentisering förblir aktiv drift säker, förnyar Azure AD med jämna mellanrum certifikat för Autentiseringsagenter. Azure AD utlöser förnyelser. Förnyelser regleras inte av Autentiseringsagenter själva.

![Driftsäkerhet](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Förnya ett autentiseringsagent förtroende med Azure AD:

1. Autentisering-Agent pingar regelbundet Azure AD var några timmar för att se om det är dags att förnya sina certifikat. Certifikatet förnyas 30 dagar innan det upphör att gälla.
    - Den här kontrollen görs via en ömsesidigt autentiserad HTTPS-kanal och använder samma certifikat som utfärdades under registreringen.
2. Om tjänsten indikerar att det är dags att förnya, autentisering-agenten genererar ett nytt nyckelpar: en offentlig nyckel och en privat nyckel.
    - Nycklarna skapas via standard RSA 2048-bitars kryptering.
    - Den privata nyckeln lämnar aldrig en lokal server.
3. Autentisering-agenten sedan begär ”certifikatförnyelse” till Azure AD via HTTPS med följande komponenter ingår i begäran:
    - Det befintliga certifikatet som hämtas från CERT_SYSTEM_STORE_LOCAL_MACHINE platsen på den Windows-certifikatarkivet. Det finns ingen global administratör i den här proceduren så att det finns ingen åtkomsttoken som behövs för den globala administratören.
    - Den offentliga nyckeln som genererats i steg 2.
    - En förfrågan om certifikatsignering (CSR eller certifikatbegäran). Den här begäran gäller för ett nytt digitala identitets-certifikat med Azure AD som dess certifikatutfärdare.
4. Azure AD verifierar det befintliga certifikatet i förfrågan om certifikatförnyelse. Den kontrollerar sedan att begäran kommer från en Agent för autentisering som registrerats på din klient.
5. Om det befintliga certifikatet är giltigt Azure AD sedan registrerar ett nytt certifikat för digital identitet och problem med det nya certifikatet tillbaka till agenten autentisering. 
6. Om det befintliga certifikatet har upphört att gälla bort Azure AD-Autentiseringsagenten från klientens lista över registrerade Autentiseringsagenter. En global administratör måste manuellt installera och registrera en ny Agent för autentisering.
    - Använda Azure AD-rot-CA för att signera certifikatet.
    - Ange certifikatets ämne (unikt namn eller Uppringningsrummer) till ditt klient-ID, ett GUID som unikt identifierar din klient. Det unika namnet scope certifikatet till din klient.
6. Azure AD lagrar den nya publika nyckeln av Autentiseringsagenten i en Azure SQL-databas som endast den har åtkomst till. Den också upphäver den gamla offentliga nyckeln som är kopplad till agenten för autentisering.
7. Det nya certifikatet (utfärdats i steg 5) lagras på servern i Windows-certifikatarkiv (särskilt i den [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) plats).
    - Eftersom förtroende förnyelse proceduren händer icke-interaktivt (utan förekomst av den globala administratören), har den autentiseringsagent inte längre åtkomst att uppdatera det befintliga certifikatet på CERT_SYSTEM_STORE_LOCAL_MACHINE plats. 
    
   > [!NOTE]
   > Den här proceduren tar inte bort själva certifikaten från CERT_SYSTEM_STORE_LOCAL_MACHINE plats.
8. Det nya certifikatet används för autentisering från och med nu på. Varje efterföljande förnyat certifikat görs ersätter certifikatet i CERT_SYSTEM_STORE_LOCAL_MACHINE plats.

## <a name="auto-update-of-the-authentication-agents"></a>Automatisk uppdatering av Autentiseringsagenter

Updater-programmet uppdateras automatiskt den autentiseringsagent när en ny version har släppts. Programmet hanterar inte några förfrågningar för verifiering av lösenord för din klient. 

Azure AD är värd för den nya versionen av programvaran som loggat **Windows Installer-paketet (MSI)**. MSI signeras med hjälp av [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) med SHA256 som digest-algoritm. 

![Automatisk uppdatering](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Att automatiskt uppdatera en autentiseringsagent:

1. Updater programmet pingar Azure AD varje timme för att kontrollera om det finns en ny version av Autentiseringsagenten. 
    - Den här kontrollen görs via en ömsesidigt autentiserad HTTPS-kanal genom att använda samma certifikat som utfärdades under registreringen. Autentisering-agenten och uppdateringsfilen delar du det certifikat som lagras på servern.
2. Om det finns en ny version returnerar signerade MSI i Azure AD tillbaka till uppdateringsfilen.
3. Uppdateringsfilen verifierar att MSI är signerat av Microsoft.
4. Uppdateringsfilen kör MSI. Den här åtgärden omfattar följande steg:

 > [!NOTE]
 > Uppdateringsfilen körs med [lokalt System](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilegier.

    - Stoppar tjänsten autentiseringsagent
    - Installeras den nya versionen av Autentiseringsagenten på servern
    - Startar om tjänsten autentiseringsagent

>[!NOTE]
>Om du har flera Autentiseringsagenter registrerad på din klient, Azure AD inte förnya sina certifikat och uppdatera dem på samma gång. Azure AD gör i stället så gradvis för att garantera hög tillgänglighet för inloggningsförfrågningar.
>


## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Läs om vilka scenarier som stöds och vilka som inte är.
- [Snabbstart](how-to-connect-pta-quick-start.md): Kom igång på Azure AD-direktautentisering.
- [Migrera från AD FS till direktautentisering](https://aka.ms/adfstoptadpdownload) -en detaljerad vägledning för att migrera från AD FS (eller andra tekniker för federation) till direktautentisering.
- [Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md): Konfigurera funktionen för smarta kontoutelåsning på din klient för att skydda användarkonton.
- [Så här fungerar det](how-to-connect-pta-how-it-works.md): Lär dig grunderna i hur fungerar Azure AD-direktautentisering.
- [Vanliga frågor och svar](how-to-connect-pta-faq.md): Få svar på vanliga frågor och svar.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig hur du löser vanliga problem med funktionen direktautentisering.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Läs mer om den här tilläggsfunktionen.
