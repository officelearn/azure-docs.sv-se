---
title: Primär Uppdateringstoken (PRT) och Azure AD – Azure Active Directory
description: Vad är rollen och hur hanterar vi primära uppdatera Token (PRT) i Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9ecf6d04056a91f1f9dd62a5238f60177d2bf59
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420595"
---
# <a name="what-is-a-primary-refresh-token"></a>Vad är en primär uppdatera Token?

En primär uppdatera Token (PRT) är en viktig artefakt av Azure AD-autentisering på Windows 10, iOS och Android-enheter. Det är en JSON Web Token (JWT) speciellt utfärdat till Microsoft första part token mäklare att aktivera enkel inloggning (SSO) mellan de program som används på dessa enheter. I den här artikeln har tillhandahåller vi information om hur en PRT utfärdas, används och skyddas på Windows 10-enheter.

Den här artikeln förutsätter att du redan förstår olika enhetstillstånden tillgängliga i Azure AD och hur enkel inloggning fungerar i Windows 10. Mer information om enheter i Azure AD finns i artikeln [vad är enhetshantering i Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Viktiga termer och komponenter

Följande Windows-komponenter spela en viktig roll i begäran och med hjälp av en PRT:

* **Autentiseringsprovider i molnet** (CloudAP): CloudAP är modern autentisering-providern för Windows logga in, som verifierar användare som loggar in till en Windows 10-enhet. CloudAP tillhandahåller ett ramverk för plugin-programmet det identitet som leverantörer kan bygga vidare på Aktivera Windows-autentisering med autentiseringsuppgifter för den identitetsprovider.
* **Web Kontoansvariga** (WAM): WAM är standard resurstokenutjämningen på Windows 10-enheter. WAM innehåller också ett ramverk för plugin-program för den identiteten leverantörer kan bygga vidare på och aktivera enkel inloggning till deras program som förlitar sig på den identitetsprovidern.
* **Azure AD CloudAP plugin**: En Azure AD specifika-pluginprogrammet bygger på CloudAP ramverk, som verifierar användarens autentiseringsuppgifter med Azure AD när de Windows-loggar in.
* **Plugin-programmet med Azure AD-WAM**: En Azure AD specifika plugin-program som bygger på WAM-ramverk, som möjliggör enkel inloggning till program som förlitar sig på Azure AD för autentisering.
* **Dsreg**: En särskild komponent Azure AD på Windows 10, som hanterar enheten registreringen för alla enheter.
* **Trusted Platform Module** (TPM): TPM är en maskinvarukomponent som är inbyggda i en enhet som tillhandahåller maskinvarubaserad säkerhetsfunktioner för användar- och hemligheter. Mer information finns i artikeln [betrodda Platform Module-Tekniköversikt](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Vad innehåller PRT?

En PRT innehåller anspråk som vanligtvis ingår i alla Azure AD-uppdateringstoken. Dessutom finns vissa specifika anspråk som ingår i PRT. De är följande:

* **Enhets-ID**: En PRT utfärdas till en användare på en specifik enhet. Enhetens ID-anspråk `deviceID` avgör PRT utfärdades för användaren på enheten. Detta anspråk utfärdas senare till token som erhållits via PRT. ID-anspråk enheten används för att fastställa auktorisering för villkorlig åtkomst baserat på enhetens tillstånd eller efterlevnad.
* **Sessionsnyckel**: Nyckeln är en krypterad symmetrisk nyckel genereras av tjänsten Azure AD-autentisering som utfärdas som en del av PRT. Sessionsnyckeln fungerar som bevis på tillgång när en PRT används för att hämta token för andra program.

### <a name="can-i-see-whats-in-a-prt"></a>Kan jag se vad som finns i en PRT?

En PRT är en ogenomskinlig blob som skickas från Azure AD vars innehåll inte är känt att alla klientkomponenter. Du kan inte se vad som finns i en PRT.

## <a name="how-is-a-prt-issued"></a>Hur utfärdas en PRT?

Enhetsregistrering är en förutsättning för baserat autentisering i Azure AD. En PRT utfärdas endast på registrerade enheter. Mer detaljerad information om enhetsregistrering finns i artikeln [Windows Hello för företag och Device Registration](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Under enhetsregistreringen genererar komponenten dsreg två uppsättningar kryptografiska nyckelpar:

* Enhetsnyckel (dkpub/dkpriv)
* Transport-nyckel (tkpub/tkpriv)

Privata nycklar är bundna till enhetens TPM om den har en giltig och fungerande TPM, medan de offentliga nycklarna som ska skickas till Azure AD under registreringen för enheten. Dessa nycklar används för att verifiera enhetens tillstånd under PRT begäranden.

PRT utfärdas under autentisering av användare på en Windows 10-enhet i två scenarier:

* **Azure AD-anslutna** eller **Hybrid Azure AD-ansluten**: En PRT utfärdas under Windows-inloggning när en användare loggar in med sina autentiseringsuppgifter för organisationen. En PRT utfärdas med autentiseringsuppgifter för alla Windows 10 som stöds, till exempel lösenord och Windows Hello för företag. I det här scenariot är plugin-programmet för Azure AD CloudAP primära utfärdaren av PRT.
* **Azure AD-registrerade enheter**: En PRT utfärdas när en användare lägger till en sekundär arbetskonto i sina Windows 10-enheter. Användare kan lägga till ett konto Windows 10 på två olika sätt –  
   * Lägga till ett konto via den **använda det här kontot överallt på den här enheten** fråga efter inloggning till en app (till exempel Outlook)
   * Att lägga till ett konto från **inställningar** > **konton** > **åtkomst till arbete eller skola** > **Connect**

I dessa scenarier är plugin-programmet Azure AD WAM primära utfärdaren av PRT eftersom Windows-inloggning inte sker med det här Azure AD-kontot.

## <a name="what-is-the-lifetime-of-a-prt"></a>Vad är livslängden för en PRT?

När utfärdats, en PRT är giltig i 14 dagar och förnyas kontinuerligt så länge användaren är aktivt använder enheten.  

## <a name="how-is-a-prt-used"></a>Hur används en PRT?

En PRT används av två viktiga komponenter i Windows:

* **Azure AD CloudAP plugin**: När de Windows-loggar in begär CloudAP för Azure AD-plugin-programmet en PRT från Azure AD med de autentiseringsuppgifter som anges av användaren. Den cachelagrar också PRT om du vill aktivera cachelagrade inloggning när användaren inte har åtkomst till en internet-anslutning.
* **Plugin-programmet med Azure AD-WAM**: När användare försöker komma åt program, använder plugin-programmet Azure AD WAM PRT för att aktivera enkel inloggning i Windows 10. Azure AD-WAM plugin-programmet använder PRT för att begära uppdatering och åtkomst-token för program som förlitar sig på WAM för token-förfrågningar. Det kan också enkel inloggning på webbläsare genom att infoga PRT i begäran från en webbläsare. Webbläsaren enkel inloggning i Windows 10 stöds i Microsoft Edge (internt) och Chrome (via tillägget Windows 10-konton eller Office Online).

## <a name="how-is-a-prt-renewed"></a>Hur förnyas en PRT?

En PRT förnyas i två olika metoder:

* **Azure AD-CloudAP plugin-programmet var fjärde timme**: Plugin-programmet CloudAP förnyar PRT var fjärde timme under Windows inloggning. Om användaren inte har Internetanslutning under den tiden förnyas PRT CloudAP plugin-programmet när enheten är ansluten till internet.
* **Azure AD-WAM-pluginprogram under app tokenbegäranden**: Plugin-programmet WAM aktiverar enkel inloggning på Windows 10-enheter genom att aktivera tyst tokenbegäranden för program. Plugin-programmet WAM kan förnya PRT under dessa tokenbegäranden på två olika sätt:
   * En app begär WAM för en åtkomsttoken tyst men det finns ingen uppdateringstoken tillgänglig för appen. I det här fallet WAM använder PRT för att begära en token för appen och hämtar en ny PRT i svaret.
   * En app begär WAM för en åtkomsttoken men PRT är ogiltigt eller Azure AD kräver ytterligare auktorisering (till exempel Azure Multi-Factor Authentication). I det här scenariot WAM initierar en interaktiv inloggning eftersom användaren måste autentiseras på nytt eller ange ytterligare verifiering och en ny PRT utfärdas på lyckad autentisering.

### <a name="key-considerations"></a>Viktiga överväganden

* En PRT är endast utfärdas och förnyas under autentiseringen för inbyggd app. En PRT är inte förnyas eller utfärdats under en webbläsarsession.
* I Azure AD ansluten och hybrid Azure AD-anslutna enheter, CloudAP plugin-programmet är primär utfärdaren av ett PRT. Om en PRT förnyas under en WAM-baserade tokenbegäran, skickas PRT tillbaka till CloudAP plugin-programmet, som verifierar giltigheten hos PRT med Azure AD innan du godkänner den.

## <a name="how-is-the-prt-protected"></a>Hur skyddas PRT?

En PRT skyddas av bindning det till enheten användaren har loggat in. Azure AD och Windows 10 aktivera PRT skydd på följande sätt:

* **Vid första inloggningen**: Vid första inloggningen utfärdas en PRT genom att logga begäranden med enhetsnyckeln kryptografiskt genereras under enhetsregistrering. På en enhet med TPM fungerar skyddas enhetsnyckeln av TPM förhindrar obehörig åtkomst. En PRT utfärdas inte om motsvarande enhet nyckel signaturen inte kan verifieras.
* **Under tokenbegäranden och förnyelse**: När en PRT utfärdas, utfärdar Azure AD även en krypterade sessionsnyckeln till enheten. Det är krypterat med offentlig transport nyckeln (tkpub) som genererats och skickats till Azure AD som en del av enhetsregistreringen. Den här nyckeln kan endast dekrypteras av den privata transport-nyckeln (tkpriv) som skyddas av TPM. Sessionsnyckeln är nyckeln bevis tillgång (POP) för alla förfrågningar som skickas till Azure AD.  Sessionsnyckeln också skyddas av TPM och inga andra OS-komponenten kan komma åt den. Tokenbegäranden eller PRT begäranden om förnyelse på ett säkert sätt är signerade av den här nyckeln till TPM och därför inte kan brytas. Azure AD så ogiltigförklaras alla förfrågningar från enheten som inte har signerats av motsvarande sessionsnyckel.

Genom att skydda dessa nycklar med TPM, skadliga aktörer det går inte att stjäla nycklarna eller spela upp PRT någon annanstans när TPM inte är tillgänglig även om angriparen har fysisk tillgång till enheten.  Därför använda en TPM avsevärt förbättrar säkerheten för Azure AD har anslutits, Hybrid Azure AD-anslutna, och Azure AD-registrerade enheter mot stöld av autentiseringsuppgifter. För prestanda och tillförlitlighet är TPM 2.0 den rekommenderade versionen för alla Azure AD device registration scenarier i Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Hur är apptoken och webbläsarcookies skyddas?

**Apptoken**: När en app begär token via WAM, utfärdar Azure AD en uppdateringstoken och en åtkomst-token. Dock WAM endast returnerar åtkomsttoken till appen och skyddar uppdateringstoken i cacheminnet genom att kryptera den med användarens data protection application programming interface (DPAPI) nyckel. WAM använder på ett säkert sätt uppdateringstoken genom att logga begäranden med nyckeln för att utfärda ytterligare åtkomsttoken. DPAPI-nyckeln skyddas av en Azure AD-baserad symmetrisk nyckel i själva Azure AD. När enheten behöver dekryptera användarprofilen med DPAPI-nyckel, tillhandahåller Azure AD DPAPI-nyckel som krypterats av sessionsnyckeln, vilka CloudAP plugin-programmet begär TPM att dekryptera. Den här funktionen utför konsekvenskontroll i skydd av uppdateringstoken och på så sätt undviker program som implementerar sina egna skyddsmekanismer.  

**Webbläsarcookies**: I Windows 10, Azure AD stöder webbläsare enkel inloggning i Internet Explorer och Microsoft Edge internt eller i Google Chrome via tillägg för Windows 10-konton. Säkerheten är utformat inte bara för att skydda cookies som men slutpunkter som cookies som skickas. Webbläsarcookies skyddas på samma sätt är en PRT genom att använda nyckeln för att registrera och skydda cookies.

När en användare startar pekfunktioner webbläsare, anropar en COM-Klientvärd för webbläsare (eller tillägget). Inbyggd klient värden garanterar att sidan är från en av de tillåtna domänerna. Webbläsaren kan skicka andra parametrar till en inbyggd klient, inklusive en nonce värd men intern klient värden garanterar validering av värdnamnet. Värden för inbyggda klienten begär en PRT-cookie från CloudAP plugin-programmet, som skapar och signerar den med TPM-skyddat sessionsnyckeln. Eftersom PRT-cookien är signerat av sessionsnyckeln, går inte att det kunna manipuleras. Cookien PRT ingår i rubriken för Azure AD för att verifiera den enhet som den kommer från. Om du använder webbläsaren Chrome, kan endast tillägget uttryckligen definieras i intern klient värdens manifest anropa det förhindrar att godtyckliga tillägg dessa förfrågningar. När Azure AD verifierar PRT cookien, utfärdar en sessions-cookie till webbläsaren. Den här sessions-cookie innehåller också samma sessionsnyckeln utfärdat med en PRT. Under efterföljande förfrågningar verifieras sessionsnyckeln effektivt bindning cookien till enheten och hindra repetitioner från andra platser.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>När får en MFA-anspråk i en PRT?

En PRT får ett krav för multifaktorautentisering (MFA) i specifika scenarier. När en MFA-baserad PRT används för att begära token för program, överförs MFA-anspråk till dessa apptoken. Den här funktionen ger en sömlös upplevelse för användare genom att förhindra att MFA-kontrollen för varje app som kräver. En PRT kan få en MFA-anspråk på följande sätt:

* **Logga in med Windows Hello för företag**: Windows Hello för företag ersätter lösenord och använder kryptografiska nycklar för autentisering med stark tvåfaktorsautentisering. Windows Hello för företag är specifika för en användare på en enhet och själva kräver MFA för att etablera. När en användare loggar in med Windows Hello för företag, hämtar användarens PRT en MFA-anspråk. Det här scenariot gäller även för användare som loggar in med smartkort om autentisering med smartkort producerar en MFA-anspråk från AD FS.
* **MFA vid WAM interaktiv inloggning i**: Vid en tokenförrfågan via WAM om en användare krävs för att göra MFA för att få åtkomst till appen, är PRT förnyas under denna interaktion imprinted med en MFA-anspråk.
* **MFA vid enhetsregistrering**: Om en administratör har konfigurerat inställningar för enheter i Azure AD för att [kräva MFA att registrera enheter](device-management-azure-portal.md#configure-device-settings), användaren behöver göra MFA för att slutföra registreringen. Under den här processen har PRT som har utfärdats till användaren MFA-anspråk som hämtats vid registrering. Den här funktionen gäller bara för den användare som utförde åtgärden join inte till andra användare som loggar in till enheten.

Windows 10 har en partitionerad lista med PRTs för varje autentiseringsuppgifter. Det finns därför en PRT för var och en av Windows Hello för företag, lösenord eller smartkort. Den här partitionering säkerställer att MFA-anspråk är isolerade baserat på de autentiseringsuppgifter som används och inte blandas under tokenbegäranden.

## <a name="how-is-a-prt-invalidated"></a>Hur ogiltigförklaras en PRT?

En PRT betraktas som inaktuella i följande scenarier:

* **Ogiltig användare**: Om en användare tas bort eller inaktiveras i Azure AD, deras PRT är ogiltig och kan inte användas till att hämta token för program. Om en användare har tagits bort eller är inaktiverad redan är inloggad på en enhet innan skulle cachelagrade inloggning logga dem i, tills CloudAP är medveten om deras ogiltigt tillstånd. När CloudAP avgör att användaren är ogiltig, blockerar efterföljande inloggningar. En ogiltig användare blockeras från logga in automatiskt till nya enheter som inte har cachelagras.
* **Ogiltig enhet**: Om en enhet tas bort eller inaktiveras i Azure AD, PRT på enheten är ogiltig och kan inte användas till att hämta token för andra program. Om en användare redan har loggat in till en ogiltig enhet, kan de fortsätta att göra detta. Men ogiltigförklaras alla token på enheten och användaren har inte enkel inloggning till alla resurser från den enheten.
* **Ändra lösenordet**: När en användare ändrar sina lösenord, PRT fick med det tidigare lösenordet betraktas som inaktuella av Azure AD. Resulterar i att användaren som hämtar en ny PRT om lösenordsändring. Den här ogiltigförklarade kan bero på två olika sätt:
   * Om användaren loggar in på Windows med sitt nya lösenord, tar bort gamla PRT CloudAP och begär Azure AD för att utfärda en ny PRT med sitt nya lösenord. Om användaren inte har en Internetanslutning, det nya lösenordet går inte att verifiera, Windows kan kräva att användarna anger sina gamla lösenord.
   * Om en användare har loggat in med sina gamla lösenord eller ändrat sitt lösenord när du loggar in på Windows, används det gamla PRT för de WAM-baserade tokenbegäranden. I det här scenariot, genereras uppmanas användaren att autentiseras på nytt under WAM tokenbegäran och en ny PRT.
* **TPM-problem**: Ibland kan en enhets TPM falter eller misslyckas, vilket leder till inaccessibility av nycklar som skyddas av TPM. Enheten är i så fall inte klarar komma en PRT eller för att begära token med en befintlig PRT eftersom den inte kan verifiera tillgång de kryptografiska nycklarna. Alla befintliga PRT är därför inte längre giltig av Azure AD. När Windows 10 upptäcker ett fel initierar en återställning flöde för att registrera enheten med nya krypteringsnycklar. Med Hybrid Azure Ad join, precis som den första registreringen sker återställningen tyst utan indata från användaren. För Azure AD-ansluten eller Azure AD-registrerade enheter, måste återställningen utföras av en användare som har administratörsbehörighet på enheten. I det här scenariot skickar recovery flödet ett Windows-meddelande som leder användaren att återställa enheten.

## <a name="detailed-flows"></a>Detaljerad flöden

Följande diagram illustrerar de bakomliggande detaljerna i utfärdande, förnya och använder en PRT begär en åtkomsttoken för ett program. Dessutom kan de här stegen här beskrivs också hur de tidigare nämnda säkerhetsmekanismerna tillämpas under dessa interaktioner.

### <a name="prt-issuance-during-first-sign-in"></a>PRT utfärdande vid första inloggningen

![PRT utfärdande vid första inloggningen detaljerad flöde](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> I Azure AD-anslutna enheter, den här exchange sker synkront för att utfärda en PRT innan användaren kan logga in på Windows. I hybrid Azure AD-anslutna enheter, lokala Active Directory är den primära utfärdaren. Användaren därför endast vänta tills de kan hämta en TGT att logga in medan PRT utfärdande sker asynkront. Det här scenariot gäller inte för Azure AD-registrerade enheter eftersom inloggning inte använder Azure AD-autentiseringsuppgifter.

| Steg | Beskrivning |
| :---: | --- |
| A | Användaren anger sitt lösenord i tecknet i Användargränssnittet. LogonUI skickar autentiseringsuppgifter i en buffert för autentisering till LSA, som i sin tur skickar det internt till CloudAP. CloudAP vidarebefordrar denna begäran till CloudAP plugin-programmet. |
| B | CloudAP plugin-programmet initierar en förfrågan för identifiering av startsfär att identifiera identitetsprovidern för användaren. Om användarens klientorganisation har en federation-installationsprogram, returnerar Azure AD för federation providern Metadata Exchange slutpunkt (MEX) slutpunkt. Om inte, returnerar Azure AD att användaren är hanterad som anger att användaren kan autentisera med Azure AD. |
| C | Om användaren är hanterad får CloudAP temporärt ID från Azure AD. Om användaren är federerad begär CloudAP plugin-programmet en SAML-token från federation providern med användarens autentiseringsuppgifter. När den får SAML-token begär en nonce från Azure AD. |
| D | CloudAP plugin-programmet skapar autentiseringsbegäran med användarens autentiseringsuppgifter, nonce och ett broker omfång, loggar begäran med enhetsnyckeln (dkpriv) och skickar den till Azure AD. I en federerad miljö CloudAP plugin-programmet använder SAML-token som returnerades av providern federation istället för användaren ”autentiseringsuppgifter. |
| E | Azure AD verifierar användarautentiseringsuppgifter, nonce, och enheten signatur, verifierar att enheten är giltig i klienten och utfärdar krypterade PRT. Tillsammans med PRT utfärdar Azure AD även en symmetrisk nyckel som heter sessionsnyckeln som krypterats med Azure AD med hjälp av nyckeln Transport (tkpub). Dessutom är sessionsnyckeln också inbäddad i PRT. Den här sessionsnyckel fungerar som bevis tillgång (PoP)-nyckel för efterföljande begäranden med PRT. |
| F | CloudAP plugin-programmet skickar PRT och sessionen krypteringsnyckeln till CloudAP. CloudAP begära TPM att dekryptera sessionsnyckeln använder Transport-nyckel (tkpriv) och kryptera den med hjälp av TPM-egen nyckel. CloudAP lagrar krypterade sessionsnyckeln i sin cache tillsammans med PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT förnyelse i påföljande inloggningar

![PRT förnyelse i påföljande inloggningar](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Steg | Beskrivning |
| :---: | --- |
| A | Användaren anger sitt lösenord i tecknet i Användargränssnittet. LogonUI skickar autentiseringsuppgifter i en buffert för autentisering till LSA, som i sin tur skickar det internt till CloudAP. CloudAP vidarebefordrar denna begäran till CloudAP plugin-programmet. |
| B | Om användaren har tidigare loggat in för användaren, logga in i Windows startar cachelagras och verifierar autentiseringsuppgifter för att logga in användaren. Var fjärde timme initierar plugin-programmet CloudAP PRT förnyelse asynkront. |
| C | CloudAP plugin-programmet initierar en förfrågan för identifiering av startsfär att identifiera identitetsprovidern för användaren. Om användarens klientorganisation har en federation-installationsprogram, returnerar Azure AD för federation providern Metadata Exchange slutpunkt (MEX) slutpunkt. Om inte, returnerar Azure AD att användaren är hanterad som anger att användaren kan autentisera med Azure AD. |
| D | Om användaren är federerad begär CloudAP plugin-programmet en SAML-token från federation providern med användarens autentiseringsuppgifter. När den får SAML-token begär en nonce från Azure AD. Om användaren är hanterad får CloudAP direkt temporärt ID från Azure AD. |
| E | CloudAP plugin-programmet skapar autentiseringsbegäran med användarens autentiseringsuppgifter, nonce och befintliga PRT, loggar begäran med sessionsnyckeln och skickar den till Azure AD. I en federerad miljö CloudAP plugin-programmet använder SAML-token som returnerades av providern federation istället för användaren ”autentiseringsuppgifter. |
| F | Azure AD verifierar signaturen Session nyckel genom att jämföra mot sessionsnyckeln inbäddad i PRT, verifierar temporärt ID och kontrollerar att enheten är giltig i klienten och utfärdar en ny PRT. Såsom visas innan åtföljs PRT igen av sessionsnyckeln krypterats av Transport nyckel (tkpub). |
| G | CloudAP plugin-programmet skickar PRT och sessionen krypteringsnyckeln till CloudAP. CloudAP begär TPM att dekryptera sessionsnyckeln använder Transport-nyckel (tkpriv) och kryptera den med hjälp av TPM-egen nyckel. CloudAP lagrar krypterade sessionsnyckeln i sin cache tillsammans med PRT. |

### <a name="prt-usage-during-app-token-requests"></a>PRT användning under app tokenbegäranden

![PRT användning under app tokenbegäranden](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Steg | Beskrivning |
| :---: | --- |
| A | Ett program (till exempel Outlook, OneNote osv.) initierar en begäran till WAM-token. WAM, som i sin tur frågar plugin-programmet för Azure AD WAM token begäran. |
| B | Om det finns redan en uppdateringstoken för programmet, använder den i Azure AD WAM plugin-programmet för att begära en åtkomst-token. För att tillhandahålla bevis på enheten bindning signerar WAM plugin-programmet begäran med sessionsnyckeln. Azure AD verifierar sessionsnyckeln och utfärdar en åtkomsttoken och en ny uppdateringstoken för den app som krypterats av sessionsnyckeln. WAM plugin-programmet begär molnet AP-plugin-programmet att dekryptera token, som i sin tur begär TPM att dekryptera med hjälp av sessionsnyckeln, vilket resulterar i WAM plugin-programmet komma båda token. Därefter ger WAM plugin-programmet endast åtkomst-token för programmet, medan den återkrypterar uppdateringstoken med DPAPI och lagrar den i sin egen cache  |
| C |  Om en uppdateringstoken för programmet inte är tillgänglig använder plugin-programmet för Azure AD WAM PRT för att begära en åtkomsttoken. För att tillhandahålla bevis på tillgång loggar WAM plugin-programmet på begäran som innehåller PRT med sessionsnyckeln. Azure AD verifierar signaturen Session nyckel genom att jämföra mot sessionsnyckeln inbäddad i PRT, verifierar att enheten är giltig och utfärdar en åtkomsttoken och en uppdateringstoken för programmet. Azure AD kan dessutom utfärda ett nytt PRT (baserat på uppdateringscykeln), alla krypterats av sessionsnyckeln. |
| D | WAM plugin-programmet begär molnet AP-plugin-programmet att dekryptera token, som i sin tur begär TPM att dekryptera med hjälp av sessionsnyckeln, vilket resulterar i WAM plugin-programmet komma båda token. Därefter ger WAM plugin-programmet endast åtkomst-token för programmet, medan den återkrypterar uppdateringstoken med DPAPI och lagrar den i sin egen cache. WAM plugin-programmet använder uppdateringstoken framöver för det här programmet. WAM plugin-programmet ger också tillbaka nya PRT till molnet AP-plugin-programmet, som validerar PRT med Azure AD innan du uppdaterar den i sin egen cache. Moln AP plugin-programmet använder nya PRT framöver. |
| E | WAM förser WAM, vilket i sin tur ger den tillbaka till det anropande programmet nyligen utfärdade åtkomst-token|

### <a name="browser-sso-using-prt"></a>Webbläsaren SSO med hjälp av PRT

![Webbläsaren SSO med hjälp av PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Steg | Beskrivning |
| :---: | --- |
| A | Användaren loggar in på Windows med sina autentiseringsuppgifter för att få en PRT. När användaren öppnar webbläsaren, webbläsare (eller tillägget) läser in URL: er från registret. |
| B | När en användare öppnar en inloggnings-URL för Azure AD, verifierar webbläsare eller tillägget URL: en med dem som hämtas från registret. Om de matchar anropar native client-värden i webbläsaren för att hämta en token. |
| C | Inbyggd klient värden verifierar att URL: erna tillhör identitetsleverantörer Microsoft (Microsoft-konto eller Azure AD), extraherar en nonce som skickas från URL: en, och gör ett anrop till CloudAP plugin-programmet för att få en PRT-cookie. |
| D | Plugin-programmet CloudAP skapar PRT cookien, logga in med TPM-bunden sessionsnyckeln och skicka tillbaka till intern klient-värden. Eftersom cookien är signerat av sessionsnyckeln, går inte att det kunna manipuleras. |
| E | Inbyggd klient värden returnerar cookien PRT till webbläsaren, som inkluderar den som en del av rubriken kallas x-ms-RefreshTokenCredential och begära token från Azure AD. |
| F | Azure AD verifierar Session nyckel signaturen på cookien PRT, validerar nonce, verifierar att enheten är giltig i klienten och utfärdar en ID-token för webbsidan och en krypterad sessions-cookie för webbläsaren. |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du felsöker PRT-relaterade problem finns i artikeln [felsökning Azure Active Directory-anslutna Windows 10 och Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
