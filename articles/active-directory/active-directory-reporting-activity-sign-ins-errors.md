---
title: Rapporter om inloggningfelkoder i Azure Active Directory-portalen | Microsoft Docs
description: Referens för felkoder för inloggningsaktivitet.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: aab03c92bee0d3b69062cdcb179eebbb5c0fc8f8
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160254"
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Rapporter om inloggningfelkoder i Azure Active Directory-portalen

Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

- Vem har loggat in med Azure Active Directory?
- Vilka appar loggade de in på?
- Vilka inloggningar misslyckades och varför?

I den här artikeln visas felkoder och relaterade beskrivningar. 

## <a name="how-can-i-display-failed-sign-ins"></a>Hur visar jag misslyckade inloggningar? 

Din startpunkt för alla granskningsdata är **[Inloggningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** i avsnittet **Aktivitet** i **Azure Active**.


![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins-errors/61.png "inloggningsaktivitet")

I rapporten inloggningar, kan du visa alla misslyckade inloggningar genom att välja **Misslyckad** som **Inloggningsstatus**.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins-errors/06.png "inloggningsaktivitet")

Om du klickar på ett objekt i listan öppnas bladet **Aktivitetsinformation: inloggningar**. Den här vyn ger dig den information som Azure Active Directory spårar om inloggningar, inklusive **inloggningfelkod** och **felorsak**.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins-errors/05.png "inloggningsaktivitet")


Som ett alternativ till Azure-portalen för att komma åt inloggningsdat, kan du också använda [rapport-API](active-directory-reporting-api-getting-started-azure-portal.md).


Följande avsnitt ger en fullständig översikt över alla eventuella fel och relaterade beskrivningar. 

## <a name="error-codes"></a>Felkoder


|Fel|Beskrivning|
|---|---|
|16000|Detta är en intern implementeringsdetalj och inte ett feltillstånd. Du kan ignorera den här referensen.|
|20001|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|20012|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|20033|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|40008|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|40009|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|40014|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|50000|Det är problem med vår inloggningstjänst. [Skapa ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) för att lösa problemet.|
|50001|Tjänstens huvudnamn hittades inte i den här klientorganisationen. Detta kan hända om programmet inte har installerats av administratören för klientorganisationen. Alternativt kunde huvudresursen inte hittas i katalogen eller är ogiltig.|
|50002|Inloggningen misslyckades på grund av begränsad proxy-åtkomst på klientorganisationen. Om det är din egen klientorganisationsprincip kan du åtgärda problemet genom att ändra inställningarna för den begränsade klientorganisationen|
|50003|Inloggningen misslyckades på grund av att signeringsnyckel eller certifikat saknas. Detta kan bero på att ingen signeringsnyckel konfigurerats i programmet. Läs igenom lösningarna som beskrivs i [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Kontakta programägaren eller appadministratören om du fortfarande har problem|
|50005|Användaren försökte logga in på en enhet från en plattform som för närvarande inte stöds av principen för villkorlig åtkomst|
|50006| Signaturverifieringen misslyckades på grund av en ogiltig signatur. Se lösningen som beskrivs i [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Kontakta programägaren eller appadministratören om du fortfarande har problem|
|50007|Partnerkrypteringscertifikatet hittades inte för det här programmet. [Skapa ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) hos Microsoft för att lösa detta|
|50008|SAML-kontrollen saknas eller är felkonfigurerad i token. Kontakta den federerade providern.|
|50010|Verifiering av målgrupps-URI för programmet misslyckades eftersom ingen tokenmålgrupp har konfigurerats. Kontakta programägaren|
|50011|Svarsadressen saknas, är felkonfigurerad eller matchar inte svarsadresserna som har konfigurerats för programmet. Prova att använda lösningen som listas vid [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Kontakta programägaren eller appadministratören om du fortfarande har problem|
|50012| Det här är ett allmänt felmeddelande som anger att autentiseringen misslyckades. Detta kan inträffa av skäl som saknas eller är ogiltig autentiseringsuppgifter eller anspråk i begäran. Se till att begäran skickas med rätt autentiseringsuppgifter och anspråk. |
|50013|Kontrollen är ogiltig av olika skäl – tokenutfärdaren matchar inte API-versionen inom det giltiga tidsintervallet – har upphört att gälla – felaktig – uppdateringstoken i försäkran är inte en primär uppdateringstoken.|
|50017|Verifieringen av certifikatutfärdaren misslyckades, av följande skäl:<ul><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Det gick inte att hitta förväntat CrlSegment</li><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Delta CRL-distributionspunkten har konfigurerats utan motsvarande CRL-distributionspunkt</li><li>Det gick inte att hämta giltiga CRL-segment på grund av timeout-problem</li><li>Det gick inte att ladda ned CRL</li></ul>Kontakta administratören för klientorganisationen.|
|50020|Användaren är obehörig för någon av följande orsaker.<ul><li>Användaren försöker logga in med ett konto för MSA med v1-slutpunkten</li><li>Användaren finns inte i klienten.</li></ul> Kontakta appägaren.|
|50027|Ogiltig JWT-token på grund av följande skäl:<ul><li>innehåller inte temporärt nonce-anspråk, sub-anspråk</li><li>felaktig matchning av ämnesidentifierare</li><li>duplicerade anspråk i idToken-anspråk</li><li>oväntad utfärdare</li><li>oväntad målgrupp</li><li>inte inom giltigt tidsintervall </li><li>fel tokenformat</li><li>Signaturverifieringen för externa ID-token från utfärdare misslyckades.</li></ul>Kontakta programägaren|
|50029|Ogiltig URI – domännamnet innehåller ogiltiga tecken. Kontakta administratören för klientorganisationen.|
|50034|Användaren finns inte i katalogen. Kontakta administratören för din klientorganisation.|
|50042|Saltet som krävs för att skapa en paridentifierare saknas i huvudkontot. Kontakta administratören för klientorganisationen.|
|50048|Ämnet överensstämmer inte med utfärdaranspråket i klientförsäkran. Kontakta administratören för klientorganisationen.|
|50050|Felaktig förfrågan. Kontakta appägaren.|
|50053|Ditt konto är låst eftersom du har försökt logga in för många gånger med ett felaktigt användar-ID eller lösenord.|
|50055|Ogiltigt lösenord, ange lösenordet som har gått ut.|
|50056|Ogiltigt lösenord eller null-lösenord – Lösenordet finns inte i arkivet för den här användaren|
|50057|Användarkontot är inaktiverat. Kontot har inaktiverats av en administratör.|
|50058|Programmet försökte göra en tyst inloggning men användaren kan inte vara tyst inloggad. Programmet måste att starta ett interaktivt flöde och ge användarna ett alternativ för att logga in. Kontakta appägaren.|
|50059|Användaren finns inte i katalogen. Kontakta administratören för din klientorganisation|
|50061|Utloggningsbegäran är ogiltig. Kontakta programägaren|
|50072|Användaren måste registrera sig för tvåfaktorsautentisering (interaktiv)|
|50074|Användaren godkändes inte av MFA-kontrollen.|
|50076|Användaren godkändes inte av MFA-kontrollen (inte interaktiv)|
|50079|Användaren måste registrera sig för tvåfaktorsautentisering (icke-interaktiv inloggning)|
|50085|Det krävs social IDP-inloggning för att token ska kunna uppdateras. Låt användaren försöka logga in igen med användarnamn och lösenord|
|50089|Flödestoken har upphört att gälla – autentiseringen misslyckades. Låt användaren försöka logga in igen med användarnamn och lösenord|
|50097|Enhetsautentisering krävs – DeviceId – DeviceAltSecId-anspråk är null ELLER så finns det ingen enhet som motsvarar enhets-ID|
|50099|JWT-signaturen är ogiltig. Kontakta appägaren.|
|50105|Den inloggade användaren har inte tilldelats en roll för programmet där personen är inloggad. Tilldela användaren till programmet. Mer information: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|Det begärda objektet för federationssfären finns inte. Kontakta administratören för klientorganisationen.|
|50120|Utfärda med JWT-rubrik. Kontakta administratören för klientorganisationen.|
|50124|Anspråkstransformeringen innehåller ogiltiga indataparametrar. Kontakta administratören för klientorganisationen för att uppdatera principen.|
|50125|Inloggningen avbröts på grund av en lösenordsåterställning eller en lösenordsregistrering|
|50126|Ogiltigt användarnamn eller lösenord eller ogiltigt lokalt användarnamn eller lösenord.|
|50127|Användaren måste installera en asynkron meddelandekö för att få åtkomst till det här innehållet.|
|50128|Ogiltigt domännamn – ingen klientidentifierande information finns i begäran eller impliceras av angivna autentiseringsuppgifter|
|50129|Enheten är inte arbetsplatsansluten – arbetsplatsanslutning krävs för registrering av enheten.|
|50130|Anspråksvärdet kan inte tolkas som känd autentiseringsmetod|
|50131|Används i olika fel för villkorlig åtkomst. T.ex. Felaktigt Windows-enhetstillstånd, begäran som blockerats på grund av misstänkt aktivitet, åtkomstprincip och beslut baserade på säkerhetsprinciper.|
|50132|Autentiseringsuppgifter har återkallats på grund av följande skäl:<ul><li>SSO-artefakt är ogiltig eller har upphört att gälla</li><li>Sessionen är inte tillräckligt ny för programmet</li><li>En tyst inloggningsbegäran har skickats men användarens session med Azure AD är ogiltig eller har upphört att gälla.</li></ul>|
|50133|Sessionen är ogiltig på grund av förfallotid eller lösenordsändring.|
|50135|Lösenordet måste ändras på grund av risken för kontot|
|50136|Omdirigera MSA-session till en app – enkel MSA-session har identifierats |
|50140|Det här felet uppstod på grund av att den kontinuerliga inloggningen avbröts när användaren loggade in. [Skapa ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, ID för begäran och felkod för mer information. |
|50143|Felaktig matchning av session – sessionen är ogiltig eftersom användarens klientorganisation inte matchar domäntipset på grund av en annan resurs. [Skapa ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, ID för begäran och felkod för mer information.|
|50144|Användarens Active Directory-lösenord har upphört att gälla. Skapa ett nytt lösenord för användaren eller låt slutanvändaren använda verktyget för återställning själv|
|50146|Det här programmet måste konfigureras med en programspecifik signeringsnyckel. Det har antingen inte konfigurerats med en sådan eller så har nyckeln upphört att gälla/är inte giltig än. Kontakta programägaren|
|50148|Code_verifier matchar inte code_challenge som anges i autentiseringsbegäran för PKCE. Kontakta apputvecklaren. |
|50155|Det gick inte att autentisera enheten för begäran|
|50158|Den externa säkerhetskontrollen uppfylldes inte|
|50161|Anspråk som skickas av en extern provider är inte tillräckliga eller så saknas anspråket till den externa providern|
|50166|Det gick inte att skicka begäran till påståendeprovider|
|50169|Sfären är inte en konfigurerad sfär för det aktuella tjänstnamnområdet.|
|50172|Den externa påståendeprovidern är inte godkänd. Kontakta administratören för klientorganisationen|
|50173|Ny säkerhetstoken krävs. Låt användaren signera igen med nya autentiseringsuppgifter|
|50177|Extern fråga stöds inte för genomströmningsanvändare|
|50178|Sessionskontroll stöds inte för genomströmningsanvändare|
|50180|Windows-integrerad autentisering behövs. Aktivera klientorganisationen för sömlös SSO.|
|51001|Domäntipset finns inte med den lokala säkerhetsidentifieraren – lokalt UPN|
|51004|Användarkontot finns inte i katalogen.|
|51006|Windows-integrerad autentisering behövs. Användaren har loggat in med sessionstoken som saknar WIA-anspråk. Begär att användaren loggar in igen.|
|52004|Användaren har inte gett medgivande för åtkomst till LinkedIn-resurser. |
|53000|För en princip för villkorsstyrd åtkomst krävs en kompatibel enhet, och enheten är inte kompatibel. Låt användaren registrera sin enhet med en godkänd MDM-provider som Intune|
|53001|För en princip för villkorsstyrd åtkomst krävs en domänansluten enhet, och enheten är inte domänansluten. Låt användaren använda en domänansluten enhet|
|53002|Programmet som används är inte ett godkänt program för villkorlig åtkomst. Användaren måste använda en av apparna i listan över godkända program för att få åtkomst.|
|53003|Åtkomsten har blockerats på grund av principer för villkorsstyrd åtkomst.|
|53004|Användaren måste slutföra registreringen för multifaktorautentisering innan personen får åtkomst till innehållet. Användaren bör registrera sig för multifaktorautentisering.|
|65001|Programmet X har inte behörighet att komma åt programmet Y eller behörigheten har återkallats. Eller användaren eller administratören har inte godkänt att använda programmet med ID X. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen. Eller användaren eller administratören har inte godkänt att använda programmet med ID X. Skicka en auktoriseringsbegäran till din klientadministratör som gäller i appens ställe: Y för resursen: Z.|
|65004|Användaren nekade samtycke till att komma åt appen. Låt användaren logga in igen och ge samtycke till appen|
|65005|Resursåtkomstlistan som krävs av programmet innehåller inte program som kan upptäckas av resursen, eller så har klientprogrammet begärt åtkomst till en resurs som inte har angetts i listan över resurser med åtkomst, eller så returnerade diagramtjänsten en ogiltig begäran, eller så hittades inte resursen. Om programmet stöder SAML har du kanske konfigurerat programmet med fel identifierare (entitet). Testa lösningen för SAML via länken nedan: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list)|
|70000|Ogiltigt beviljande på grund av följande skäl:<ul><li>Den begärda SAML 2.0-försäkran har en ogiltig ämnesbekräftelsemetod</li><li>Flödet i appen OnBehalfOf stöds inte på V2</li><li>Den primära uppdateringstoken är inte signerad med en sessionsnyckel</li><li>Ogiltig extern uppdateringstoken</li><li>Åtkomstbeviljandet hämtades för en annan klientorganisation.</li></ul>|
|70001|Programmet X hittades inte i klienten Y. Detta kan inträffa om programmet med identifierare X inte har installerats av administratören för klienten eller godkänts av någon användare i klientorganisationen. Du kanske har felkonfigurerat identifierarvärdet för programmet eller skickat din autentiseringsbegäran till fel klientorganisation|
|70002|Programmet returnerade ogiltiga autentiseringsuppgifter för klienten. Kontakta appägaren|
|70003|Programmet returnerade en beviljandetyp som inte stöds. Kontakta appägaren|
|70004|Programmet returnerade en ogiltig omdirigerings-URI. Adressen för omdirigering som angetts av klienten matchar inte några konfigurerade adresser eller någon adress på godkännandelistan för OIDC. Kontakta appägaren|
|70005|Programmet returnerade en typ för svar som inte stöds på grund av följande skäl:<ul><li>svarstypen "token" är inte aktiverad för programmet</li><li>svarstypen "id_token" kräver OpenID-omfång – innehåller ett OAuth-parametervärde som inte stöds i den kodade wctx</li></ul>Kontakta programägaren|
|70007|Programmet returnerade ett värde som inte stöds av "response_mode" när du begär en token. Kontakta appägaren|
|70008|Den angivna autentiseringskoden eller uppdateringstoken har upphört att gälla – återkallad. Låt användaren prova att logga in igen|
|70011|Omfånget som programmet begärde är ogiltigt. Kontakta programägaren|
|70012|Ett serverfel uppstod under autentisering av en MSA-användare (konsument). Försök igen. Om det fortfarande misslyckas [skapar du ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Ogiltig verifieringskod på grund av att användaren skriver in fel användarkod för kodflödet för enheten. Autentiseringen godkänns inte|
|70019|Verifieringskoden har upphört att gälla. Låt användaren prova att logga in igen|
|70037|Felaktigt utmaningssvar har angetts. Fjärrsessionen för autentisering nekad.|
|75001|Ett fel uppstod under bindningen för SAML-meddelande.|
|75003|Programmet returnerade ett fel relaterat till bindning som inte stöds (SAML-protokollsvar kan inte skickas via andra bindningar än HTTP POST). Kontakta appägaren|
|75005|Azure AD stöder inte SAML-begäran som skickades av programmet för enkel inloggning. Kontakta appägaren|
|75008|Begäran från programmet nekades eftersom SAML-förfrågan hade ett oväntat mål. Kontakta appägaren|
|75011|Autentiseringsmetoden som användaren autentiserades med för tjänsten matchar inte den begärda autentiseringsmetoden. Kontakta appägaren|
|75016|SAML2-autentiseringsbegäran har ogiltig NameIdPolicy. Kontakta appägaren|
|80001|Det gick inte att ansluta autentiseringsagenten till Active Directory. Kontrollera att autentiseringsagenten är installerad på en domänansluten dator som har åtkomst till en DC som kan hantera inloggningen av användaren.|
|80002|Internt fel. Tidsgränsen uppnåddes för begäran av lösenordsverifiering. Det gick inte att skicka autentiseringsbegäran till den interna Hybrid Identity-tjänsten. [Skapa ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet|
|80003|Ogiltigt svar har tagits emot av autentiseringsagenten. Ett okänt fel uppstod vid autentiseringen mot Active Directory lokalt. [Skapa ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet.|
|80005|Autentiseringsagent: Ett okänt fel uppstod under bearbetningen av svaret från autentiseringsagenten. [Skapa ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet.|
|80007|Autentiseringsagenten kunde inte verifiera användarens lösenord.|
|80010|Autentiseringsagenten kan inte dekryptera lösenordet. |
|80011|Autentiseringsagenten kunde inte hämta krypteringsnyckeln.|
|80012|Användarna försökte logga in utanför tillåtet tidsintervall (anges i AD)|
|80013|Autentiseringsförsöket kunde inte slutföras på grund av tidsförskjutning mellan den dator som kör autentiseringsagenten och AD. Åtgärda problem med synkronisering av tid|
|80014|Tidsgränsen uppnåddes för autentiseringsagenten. [Skapa ett supportärende](fundamentals/active-directory-troubleshooting-support-howto.md) med felkod, korrelations-ID och datum för att få mer information|
|81001|Användarens Kerberos-biljett är för stor. Detta kan inträffa om användaren har för många grupper och Kerberos-biljetten därmed innehåller för många gruppmedlemskap. Minska användarens gruppmedlemskap och försök igen.|
|81005|Autentiseringspaketet stöds inte|
|81007|Klienten har inte aktiverats för sömlös SSO|
|81012|Detta är inte ett feltillstånd. Anger den användare som försöker logga in på Azure AD skiljer sig från användaren som har loggat in på enheten. Du kan ignorera den här koden i loggarna.|
|90010|Begäran stöds inte av olika anledningar. Till exempel begäran görs med hjälp av en metod för begäran stöds inte (endast POST-metoden stöds) eller token Signeringsalgoritm som begärdes stöds inte. Kontakta apputvecklaren.|
|90014| Ett obligatoriskt fält för ett protokollmeddelande var saknas, Kontakta programmets ägare. Om du är programmets ägare kan du kontrollera att du har alla nödvändiga parametrar för inloggningsbegäran. |
|90072| Kontot måste läggas till som en extern användare i klientorganisationen först. Logga ut och logga in igen med en annan Azure AD konto.|
|90094| Beviljande måste ha administratörsbehörighet. Be klientorganisationens administratör att ge medgivande för det här programmet.|

## <a name="next-steps"></a>Nästa steg

Mer information finns i [inloggningsaktivitetsrapporterna i Azure Active Directory-portalen](active-directory-reporting-activity-sign-ins.md).
