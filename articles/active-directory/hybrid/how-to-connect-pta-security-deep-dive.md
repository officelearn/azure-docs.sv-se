---
title: Azure Active Directory Genomströmningssäkerhet djupdykning| Microsoft-dokument
description: I den här artikeln beskrivs hur Azure Active Directory (Azure AD) direktautentisering skyddar dina lokala konton
services: active-directory
keywords: Azure AD Connect Pass-through-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ddce8d4d7ca1f03c0a57d0f0c8c41ac122973e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185561"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory Direktautentisering säkerhet djupdykning

Den här artikeln innehåller en mer detaljerad beskrivning av hur Azure Active Directory (Azure AD) Direktautentisering fungerar. Den fokuserar på säkerhetsaspekterna av funktionen. Den här artikeln är för säkerhets- och IT-administratörer, chefefterlevnads- och säkerhetsansvariga och andra IT-tekniker som ansvarar för IT-säkerhet och efterlevnad hos små och medelstora organisationer eller stora företag.

De ämnen som behandlas är:
- Detaljerad teknisk information om hur du installerar och registrerar autentiseringsagenterna.
- Detaljerad teknisk information om kryptering av lösenord under användarloggning.
- Säkerheten för kanalerna mellan lokala autentiseringsagenter och Azure AD.
- Detaljerad teknisk information om hur du håller autentiseringsagenterna i drift säker.
- Andra säkerhetsrelaterade ämnen.

## <a name="key-security-capabilities"></a>Viktiga säkerhetsfunktioner

Dessa är de viktigaste säkerhetsaspekterna av den här funktionen:
- Den bygger på en säker arkitektur med flera innehavare som ger isolering av inloggningsbegäranden mellan klienter.
- Lokala lösenord lagras aldrig i molnet i någon form.
- Lokala autentiseringsagenter som lyssnar efter och svarar på lösenordsverifieringsbegäranden gör bara utgående anslutningar inifrån nätverket. Det finns inget krav på att installera dessa autentiseringsagenter i ett perimeternätverk (DMZ). Som bästa praxis kan du behandla alla servrar som kör autentiseringsagenter som nivå 0-system (se [referens](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Endast standardportar (80 och 443) används för utgående kommunikation från autentiseringsagenter till Azure AD. Du behöver inte öppna inkommande portar på brandväggen. 
  - Port 443 används för all autentiserat utgående meddelande.
  - Port 80 används endast för att hämta listor över återkallade certifikat (CRL: er) för att säkerställa att inget av de certifikat som används av den här funktionen har återkallats.
  - En fullständig lista över nätverkskraven finns i [Azure Active Directory Direktautentisering: Snabbstart](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Lösenord som användare anger under inloggning krypteras i molnet innan de lokala autentiseringsagenterna accepterar dem för validering mot Active Directory.
- HTTPS-kanalen mellan Azure AD och den lokala autentiseringsagenten skyddas med hjälp av ömsesidig autentisering.
- Skyddar dina användarkonton genom att arbeta sömlöst med [Azure AD Conditional Access-principer,](../active-directory-conditional-access-azure-portal.md)inklusive MFA (Multi-Factor Authentication), [blockera äldre autentisering](../conditional-access/concept-conditional-access-conditions.md) och genom [att filtrera bort brute force-lösenordsattacker](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Berörda komponenter

Allmän information om Azure AD-drift, tjänst och datasäkerhet finns i [Säkerhetscenter](https://azure.microsoft.com/support/trust-center/). Följande komponenter är involverade när du använder direktautentisering för användarloggning:
- **Azure AD STS**: En tillståndslös security token-tjänst (STS) som bearbetar inloggningsbegäranden och utfärdar säkerhetstoken till användarnas webbläsare, klienter eller tjänster efter behov.
- **Azure Service Bus**: Tillhandahåller molnbaserad kommunikation med företagsmeddelanden och reläkommunikation som hjälper dig att ansluta lokala lösningar med molnet.
- **Azure AD Connect Authentication Agent**: En lokal komponent som lyssnar efter och svarar på begäranden om lösenordsverifiering.
- **Azure SQL Database**: Innehåller information om klientens autentiseringsagenter, inklusive deras metadata och krypteringsnycklar.
- **Active Directory**: Lokal Active Directory, där dina användarkonton och deras lösenord lagras.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Installation och registrering av autentiseringsagenter

Autentiseringsagenter installeras och registreras med Azure AD när du antingen:
   - [Aktivera direktautentisering via Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Lägg till fler autentiseringsagenter för att säkerställa hög tillgänglighet för inloggningsbegäranden](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Att få en autentiseringsagent att fungera innebär tre huvudfaser:

1. Installation av autentiseringsagent
2. Registrering av autentiseringsagent
3. Initiering av autentiseringsagent

I följande avsnitt beskrivs dessa faser i detalj.

### <a name="authentication-agent-installation"></a>Installation av autentiseringsagent

Endast globala administratörer kan installera en autentiseringsagent (med hjälp av Azure AD Connect eller fristående) på en lokal server. Installation lägger till två nya poster i listan**Programs** > **Program och funktioner på** **Kontrollpanelen:** > 
- Själva autentiseringsagentprogrammet. Det här [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) programmet körs med NetworkService-privilegier.
- Updater-programmet som används för att automatiskt uppdatera autentiseringsagenten. Det här [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) programmet körs med localsystem-privilegier.

### <a name="authentication-agent-registration"></a>Registrering av autentiseringsagent

När du har installerat autentiseringsagenten måste den registrera sig med Azure AD. Azure AD tilldelar varje autentiseringsagent ett unikt, digitalt identitetscertifikat som den kan använda för säker kommunikation med Azure AD.

Registreringsproceduren binder också autentiseringsagenten till din klient. Detta säkerställer att Azure AD vet att den här specifika autentiseringsagenten är den enda som har behörighet att hantera begäranden om lösenordsverifiering för din klient. Den här proceduren upprepas för varje ny autentiseringsagent som du registrerar.

Autentiseringsagenterna använder följande steg för att registrera sig med Azure AD:

![Registrering av ombud](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD begär först att en global administratör loggar in på Azure AD med sina autentiseringsuppgifter. Under inloggningen hämtar autentiseringsagenten en åtkomsttoken som den kan använda för den globala administratörens räkning.
2. Autentiseringsagenten genererar sedan ett nyckelpar: en offentlig nyckel och en privat nyckel.
    - Nyckelparet genereras via standard RSA 2048-bitars kryptering.
    - Den privata nyckeln finns kvar på den lokala servern där autentiseringsagenten finns.
3. Autentiseringsagenten gör en "registreringsbegäran" till Azure AD via HTTPS, med följande komponenter inkluderade i begäran:
    - Åtkomsttoken som förvärvades i steg 1.
    - Den offentliga nyckeln som genereras i steg 2.
    - En begäran om certifikatsignering (CSR eller certifikatbegäran). Den här begäran gäller för ett digitalt identitetscertifikat, med Azure AD som certifikatutfärdarcertifikatutfärdning.
4. Azure AD validerar åtkomsttoken i registreringsbegäran och verifierar att begäran kom från en global administratör.
5. Azure AD signerar och skickar sedan tillbaka ett digitalt identitetscertifikat till autentiseringsagenten.
    - Rotcertifikatutfärdaren i Azure AD används för att signera certifikatet. 

      > [!NOTE]
      > Den här certifikatutfärdaren finns _inte_ i arkivet betrodda rotcertifikatutfärdar.
    - Certifikatutfärdaren används endast av direktautentiseringsfunktionen. Certifikatutfärdaren används endast för att signera kundtjänstrepresentanter under registreringen av autentiseringsagenten.
    -  Ingen av de andra Azure AD-tjänsterna använder den här certifikatutfärdaren.
    - Certifikatets ämne (Distinguished Name eller DN) är inställt på ditt klient-ID. Den här DN är en GUID som unikt identifierar din klient. Den här DN-scopen för certifikatet för användning endast med din klient.
6. Azure AD lagrar den offentliga nyckeln för autentiseringsagenten i en Azure SQL-databas, som endast Azure AD har åtkomst till.
7. Certifikatet (utfärdat i steg 5) lagras på den lokala servern i Windows-certifikatarkivet (särskilt på [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) plats). Den används av både autentiseringsagenten och Updater-programmen.

### <a name="authentication-agent-initialization"></a>Initiering av autentiseringsagent

När autentiseringsagenten startar, antingen för första gången efter registreringen eller efter en omstart av servern, behöver den ett sätt att kommunicera säkert med Azure AD-tjänsten och börja acceptera begäranden om lösenordsverifiering.

![Initiering av agent](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Så här initieras autentiseringsagenter:

1. Autentiseringsagenten gör en utgående bootstrap-begäran till Azure AD. 
    - Den här begäran görs via port 443 och är över en ömsesidigt autentiserat HTTPS-kanal. Begäran använder samma certifikat som utfärdades under registreringen av autentiseringsagenten.
2. Azure AD svarar på begäran genom att tillhandahålla en åtkomstnyckel till en Azure Service Bus-kö som är unik för din klient och som identifieras av ditt klient-ID.
3. Autentiseringsagenten gör en beständig utgående HTTPS-anslutning (över port 443) till kön. 
    - Autentiseringsagenten är nu redo att hämta och hantera begäranden om lösenordsverifiering.

Om du har flera autentiseringsagenter registrerade på din klient, säkerställer initieringsproceduren att var och en ansluter till samma Service Bus-kö. 

## <a name="process-sign-in-requests"></a>Bearbeta inloggningsbegäranden 

Följande diagram visar hur direktautentisering bearbetar användaråtkomstbegäranden.

![Bearbeta inloggning](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Direktautentisering hanterar en begäran om användarloggning enligt följande: 

1. En användare försöker komma åt ett program, till exempel [Outlook Web App](https://outlook.office365.com/owa).
2. Om användaren inte redan är inloggad omdirigerar programmet webbläsaren till inloggningssidan för Azure AD.
3. Azure AD STS-tjänsten svarar tillbaka med **inloggningssidan för användare.**
4. Användaren anger sitt användarnamn på inloggningssidan **för användare** och väljer sedan knappen **Nästa.**
5. Användaren anger sitt lösenord på inloggningssidan **för användare** och väljer sedan inloggningsknappen. **Sign-in**
6. Användarnamnet och lösenordet skickas till Azure AD STS i en HTTPS POST-begäran.
7. Azure AD STS hämtar offentliga nycklar för alla autentiseringsagenter som är registrerade på din klientorganisation från Azure SQL-databasen och krypterar lösenordet med hjälp av dem.
    - Den producerar "N" krypterade lösenordsvärden för "N" Autentiseringsagenter registrerade på din klient.
8. Azure AD STS placerar begäran om lösenordsverifiering, som består av användarnamnet och de krypterade lösenordsvärdena, i den servicebusskö som är specifik för din klient.
9. Eftersom de initierade autentiseringsagenterna är beständigt anslutna till servicebusskön hämtar en av de tillgängliga autentiseringsagenterna begäran om lösenordsverifiering.
10. Autentiseringsagenten lokaliserar det krypterade lösenordsvärdet som är specifikt för dess offentliga nyckel, med hjälp av en identifierare och dekrypterar det med hjälp av dess privata nyckel.
11. Autentiseringsagenten försöker validera användarnamnet och lösenordet mot lokal Active Directory med hjälp av [Win32 LogonUser-API:et](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) med parametern **dwLogonType** inställd på **LOGON32_LOGON_NETWORK**. 
    - Det här API:et är samma API som används av AD FS (Active Directory Federation Services) för att logga in användare i ett federerat inloggningsscenario.
    - Det här API:et är beroende av standardmatchningsprocessen i Windows Server för att hitta domänkontrollanten.
12. Autentiseringsagenten får resultatet från Active Directory, till exempel lyckade, användarnamn eller lösenord som har upphört att gälla eller att lösenordet har upphört att gälla.

   > [!NOTE]
   > Om autentiseringsagenten misslyckas under inloggningsprocessen tas hela inloggningsbegäran bort. Det finns ingen hand-off av inloggningsbegäranden från en autentiseringsagent till en annan autentiseringsagent lokalt. Dessa agenter kommunicerar bara med molnet och inte med varandra.
   
13. Autentiseringsagenten vidarebefordrar resultatet tillbaka till Azure AD STS över en utgående ömsesidigt autentiserade HTTPS-kanal över port 443. Ömsesidig autentisering använder certifikatet som tidigare utfärdats till autentiseringsagenten under registreringen.
14. Azure AD STS verifierar att det här resultatet korrelerar med den specifika inloggningsbegäran på din klientorganisation.
15. Azure AD STS fortsätter med inloggningsproceduren som konfigurerad. Om lösenordsvalideringen till exempel lyckades kan användaren utmanas om multifaktorautentisering eller omdirigeras tillbaka till programmet.

## <a name="operational-security-of-the-authentication-agents"></a>Autentiseringsagenternas operativa säkerhet

Azure AD förnyar regelbundet autentiseringsagenternas certifikat för att säkerställa att direktautentiseringsautentisering förblir operativt säker. Azure AD utlöser förnyelser. Förnyelserna styrs inte av själva autentiseringsagenterna.

![Driftsäkerhet](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Så här förnyar du en autentiseringsagents förtroende med Azure AD:

1. Autentiseringsagenten pingar regelbundet Azure AD med några timmars mellanrum för att kontrollera om det är dags att förnya certifikatet. Certifikatet förnyas 30 dagar innan det upphör att gälla.
    - Den här kontrollen görs via en ömsesidigt autentiserat HTTPS-kanal och använder samma certifikat som utfärdades under registreringen.
2. Om tjänsten anger att det är dags att förnya genererar autentiseringsagenten ett nytt nyckelpar: en offentlig nyckel och en privat nyckel.
    - Dessa nycklar genereras via standard RSA 2048-bitars kryptering.
    - Den privata nyckeln lämnar aldrig den lokala servern.
3. Autentiseringsagenten gör sedan en begäran om förnyelse av certifikat till Azure AD via HTTPS, med följande komponenter inkluderade i begäran:
    - Det befintliga certifikat som hämtas från CERT_SYSTEM_STORE_LOCAL_MACHINE plats i Windows-certifikatarkivet. Det finns ingen global administratör inblandad i den här proceduren, så det finns ingen åtkomsttoken som behövs för den globala administratörens räkning.
    - Den offentliga nyckeln som genereras i steg 2.
    - En begäran om certifikatsignering (CSR eller certifikatbegäran). Den här begäran gäller för ett nytt digitalt identitetscertifikat, med Azure AD som certifikatutfärdar.
4. Azure AD validerar det befintliga certifikatet i begäran om förnyelse av certifikat. Sedan verifieras att begäran kom från en autentiseringsagent som är registrerad på din klientorganisation.
5. Om det befintliga certifikatet fortfarande är giltigt signerar Azure AD sedan ett nytt digitalt identitetscertifikat och utfärdar det nya certifikatet tillbaka till autentiseringsagenten. 
6. Om det befintliga certifikatet har upphört att gälla tar Azure AD bort autentiseringsagenten från klientens lista över registrerade autentiseringsagenter. Sedan måste en global administratör installera och registrera en ny autentiseringsagent manuellt.
    - Använd Azure AD-rotcertifikatutfärdaren för att signera certifikatet.
    - Ange certifikatets ämne (Unikt namn eller DN) till ditt klient-ID, ett GUID som unikt identifierar din klient. DN omfattar endast certifikatet till din klient.
6. Azure AD lagrar den nya offentliga nyckeln för autentiseringsagenten i en Azure SQL-databas som bara den har åtkomst till. Den ogiltigförklarar också den gamla offentliga nyckeln som är associerad med autentiseringsagenten.
7. Det nya certifikatet (utfärdat i steg 5) lagras sedan på servern i Windows-certifikatarkivet (särskilt på [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) plats).
    - Eftersom förtroendeförnyelsen sker icke-interaktivt (utan den globala administratörens närvaro) har autentiseringsagenten inte längre åtkomst till att uppdatera det befintliga certifikatet på den CERT_SYSTEM_STORE_LOCAL_MACHINE platsen. 
    
   > [!NOTE]
   > Den här proceduren tar inte bort själva certifikatet från CERT_SYSTEM_STORE_LOCAL_MACHINE plats.
8. Det nya certifikatet används för autentisering från och med nu. Varje efterföljande förnyelse av certifikatet ersätter certifikatet på den CERT_SYSTEM_STORE_LOCAL_MACHINE platsen.

## <a name="auto-update-of-the-authentication-agents"></a>Automatisk uppdatering av autentiseringsagenterna

Updater-programmet uppdaterar automatiskt autentiseringsagenten när en ny version (med buggfixar eller prestandaförbättringar) släpps. Updater-programmet hanterar inga begäranden om lösenordsverifiering för din klient.

Azure AD är värd för den nya versionen av programvaran som ett signerat **Windows Installer-paket (MSI)**. MSI signeras med [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) med SHA256 som digest-algoritm. 

![Uppdatera automatiskt](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Så här uppdaterar du en autentiseringsagent automatiskt:

1. Updater-programmet pingar Azure AD varje timme för att kontrollera om det finns en ny version av autentiseringsagenten tillgänglig. 
    - Den här kontrollen görs via en ömsesidigt autentiserat HTTPS-kanal med samma certifikat som utfärdades under registreringen. Autentiseringsagenten och updater-resursen delar certifikatet som lagras på servern.
2. Om en ny version är tillgänglig returnerar Azure AD den signerade MSI-versionen tillbaka till Updater.
3. Updater verifierar att MSI är signerat av Microsoft.
4. Updater kör MSI. Den här åtgärden omfattar följande steg:

   > [!NOTE]
   > Updater körs med [behörigheten Lokalt system.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

    - Stoppar tjänsten Authentication Agent
    - Installerar den nya versionen av autentiseringsagenten på servern
    - Startar om tjänsten Autentiseringsagent

>[!NOTE]
>Om du har flera autentiseringsagenter registrerade på din klientorganisation förnyar Azure AD inte sina certifikat eller uppdaterar dem samtidigt. I stället gör Azure AD det en i taget för att säkerställa hög tillgänglighet för inloggningsbegäranden.
>


## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är det.
- [Snabbstart:](how-to-connect-pta-quick-start.md)Komma igång med Azure AD Pass-through-autentisering.
- [Migrera från AD FS till Direktautentisering](https://aka.ms/adfstoptadpdownload) – En detaljerad guide för att migrera från AD FS (eller annan federationsteknik) till direktautentisering.
- [Smart Utelåsning:](../authentication/howto-password-smart-lockout.md)Konfigurera smart utelåsningsfunktionen på din klient för att skydda användarkonton.
- [Så här fungerar det:](how-to-connect-pta-how-it-works.md)Lär dig grunderna i hur Azure AD-direktautentisering fungerar.
- [Vanliga frågor och svar](how-to-connect-pta-faq.md): Hitta svar på vanliga frågor.
- [Felsöka:](tshoot-connect-pass-through-authentication.md)Lär dig hur du löser vanliga problem med direktautentiseringsfunktionen.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Läs mer om den här kompletterande funktionen.
