---
title: Rapporter om inloggningfelkoder i Azure Active Directory-portalen | Microsoft Docs
description: Referens för felkoder för inloggningsaktivitet.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 54d2c08a0055e580105ba3b1da66c53617dfcb7d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151898"
---
# <a name="sign-in-activity-report-error-codes"></a>Felkoder för inloggningsaktivitet rapport 

Med den information som tillhandahålls av den [inloggningar rapport](concept-sign-ins.md), du kan hitta svar på frågor som:

- Vem har loggat in till mitt program?
- Vilka program har loggat in på?
- Vilka inloggningar misslyckades varför?

Om en inloggning misslyckas visas en felkod som motsvarar felet. Den här artikeln visas felkoder och beskrivningar, tillsammans med att åtgärden om tillämpligt. 

## <a name="how-can-i-display-failed-sign-ins"></a>Hur visar jag misslyckade inloggningar? 

Navigera till den [rapporten inloggningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) i den [Azure-portalen](https://portal.azure.com).

![Inloggningsaktivitet](./media/reference-sign-ins-error-codes/61.png "inloggningsaktivitet")

Filtrera rapporten för att visa alla misslyckade inloggningar genom att välja **fel** från den **inloggningsstatus** nedrullningsbara listrutan.

![Inloggningsaktivitet](./media/reference-sign-ins-error-codes/06.png "inloggningsaktivitet")

Om du väljer ett objekt i den filtrerade listan öppnas den **aktivitetsinformation: Inloggningar** bladet. Den här vyn ger dig ytterligare information om den misslyckade inloggning händelsen, inklusive den **felkod för inloggning** och **felorsak**.

![Inloggningsaktivitet](./media/reference-sign-ins-error-codes/05.png "inloggningsaktivitet")

Du kan även programmässigt komma åt inloggningsdata med hjälp av den [reporting API](concept-reporting-api.md).

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
|50000|Det är problem med vår inloggningstjänst. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att lösa problemet.|
|50001|Tjänstens huvudnamn hittades inte i den här klientorganisationen. Detta kan inträffa om programmet inte har installerats av administratör för klienten, eller om resursen huvudnamn kunde inte hittas i katalogen eller är ogiltig.|
|50002|Inloggningen misslyckades på grund av begränsad proxy-åtkomst på klientorganisationen. Du kan ändra din begränsade klientinställningar för att åtgärda problemet om det är ditt eget Klientprincipen.|
|50003|Inloggningen misslyckades på grund av att signeringsnyckel eller certifikat saknas. Detta kan bero på att ingen signeringsnyckel konfigurerats i programmet. Läs igenom lösningarna som beskrivs i [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Om problemet kvarstår, Kontakta programmets ägare eller administratör för programmet.|
|50005|Användaren försökte logga in på en enhet från en plattform som inte stöds för närvarande via princip för villkorlig åtkomst.|
|50006| Signaturverifieringen misslyckades på grund av en ogiltig signatur. Se lösningen som beskrivs i [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Om problemet kvarstår kontaktar du programmets ägare eller programadministratören.|
|50007|Partnerkrypteringscertifikatet hittades inte för det här programmet. [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med Microsoft för att få det åtgärdat.|
|50008|SAML-kontrollen saknas eller är felkonfigurerad i token. Kontakta den federerade providern.|
|50010|Verifiering av målgrupps-URI för programmet misslyckades eftersom ingen tokenmålgrupp har konfigurerats. Kontakta programmets ägare för matchning.|
|50011|Svarsadressen saknas, är felkonfigurerad eller matchar inte svarsadresserna som har konfigurerats för programmet. Prova lösningen på [ https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application ](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Om problemet kvarstår kontaktar du programmets ägare eller programadministratören.|
|50012| Det här är ett allmänt felmeddelande som anger att autentiseringen misslyckades. Detta kan inträffa av skäl som saknas eller är ogiltig autentiseringsuppgifter eller anspråk i begäran. Se till att begäran skickas med rätt autentiseringsuppgifter och anspråk. |
|50013|Kontrollen är ogiltig på grund av olika anledningar. Exempelvis tokenutfärdaren matchar inte den api-versionen i dess giltigt tidsintervall, token har upphört att gälla eller har fel format eller uppdateringstoken i kontrollen är inte en primär uppdateringstoken.|
|50017|Verifieringen av certifikatutfärdaren misslyckades, av följande skäl:<ul><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Det gick inte att hitta förväntat CrlSegment</li><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Delta CRL-distributionspunkten har konfigurerats utan motsvarande CRL-distributionspunkt</li><li>Det gick inte att hämta giltiga CRL-segment på grund av timeout-problem</li><li>Det gick inte att ladda ned CRL</li></ul>Kontakta klientorganisationens administratör.|
|50020|Användaren är obehörig för någon av följande orsaker.<ul><li>Användaren försöker logga in med ett konto för MSA med v1-slutpunkten</li><li>Användaren finns inte i klienten.</li></ul> Kontakta appägaren.|
|50027|Ogiltig JWT-token på grund av följande skäl:<ul><li>innehåller inte temporärt nonce-anspråk, sub-anspråk</li><li>felaktig matchning av ämnesidentifierare</li><li>duplicerade anspråk i idToken-anspråk</li><li>oväntad utfärdare</li><li>oväntad målgrupp</li><li>inte inom giltigt tidsintervall </li><li>fel tokenformat</li><li>Signaturverifieringen för externa ID-token från utfärdare misslyckades.</li></ul>Kontakta programägaren|
|50029|Ogiltig URI – domännamnet innehåller ogiltiga tecken. Kontakta klientorganisationens administratör.|
|50034|Användaren finns inte i katalogen. Kontakta din Innehavaradministratör.|
|50042|Saltet som krävs för att skapa en paridentifierare saknas i huvudkontot. Kontakta klientorganisationens administratör.|
|50048|Ämnet överensstämmer inte med utfärdaranspråket i klientförsäkran. Kontakta klientorganisationens administratör.|
|50050|Felaktig förfrågan. Kontakta appägaren.|
|50053|Kontot är låst eftersom användaren försökte logga in för många gånger med ett felaktigt användar-ID eller lösenord.|
|50055|Ogiltigt lösenord, ange lösenordet som har gått ut.|
|50056|Ogiltig eller null-lösenord: lösenordet finns inte i arkivet för den här användaren.|
|50057|Användarkontot är inaktiverat. Kontot har inaktiverats av en administratör.|
|50058|Programmet försökte göra en tyst inloggning men användaren kan inte vara tyst inloggad. Programmet måste starta en interaktiv flöde och ge användare möjlighet att logga in. Kontakta programmets ägare.|
|50059|Användaren finns inte i katalogen. Kontakta din Innehavaradministratör.|
|50061|Utloggningsbegäran är ogiltig. Kontakta appägaren.|
|50072|Användaren måste registrera sig för tvåfaktorsautentisering (Interaktiv).|
|50074|Användaren godkändes inte av MFA-kontrollen.|
|50076|Användaren godkändes inte MFA-kontrollen (icke-interaktiv).|
|50079|Användaren måste registrera sig för tvåfaktorsautentisering (icke-interaktiva inloggningar).|
|50085|Det krävs social IDP-inloggning för att token ska kunna uppdateras. Låt användare försöka logga in igen med sitt användarnamn och lösenord.|
|50089|Flow-token har upphört att gälla - autentiseringen misslyckades. Har användare som försöker logga in igen med sitt användarnamn och lösenord|
|50097|Autentisering krävs. Detta kan inträffa DeviceId eller DeviceAltSecId anspråk är **null**, eller om det finns ingen enhet som motsvarar enhets-ID.|
|50099|JWT-signaturen är ogiltig. Kontakta appägaren.|
|50105|Den inloggade användaren har inte tilldelats en roll för programmet där personen är inloggad. Tilldela användaren till programmet. Mer information: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|Det begärda objektet för federationssfären finns inte. Kontakta klientorganisationens administratör.|
|50120|Utfärda med JWT-rubrik. Kontakta klientorganisationens administratör.|
|50124|Anspråkstransformeringen innehåller ogiltiga indataparametrar. Kontakta Innehavaradministratör om du vill uppdatera principen.|
|50125|Inloggningen avbröts på grund av en återställning av lösenord eller en post för registrering av lösenord.|
|50126|Ogiltigt användarnamn eller lösenord eller ogiltigt lokalt användarnamn eller lösenord.|
|50127|Användaren måste installera en broker-appen för att få åtkomst till det här innehållet.|
|50128|Ogiltigt domännamn – ingen information om identifiering av klient finns i antingen begäran eller underförstådda med angivna autentiseringsuppgifter.|
|50129|Enheten är inte arbetsplatsanslutna - **arbetsplatsanslutning** krävs för att registrera enheten.|
|50130|Anspråkets värde kan inte tolkas som kända auth-metod.|
|50131|Används i olika fel för villkorlig åtkomst. T.ex. Felaktigt Windows-enhetstillstånd, begäran som blockerats på grund av misstänkt aktivitet, åtkomstprincip och beslut baserade på säkerhetsprinciper.|
|50132|Autentiseringsuppgifter har återkallats på grund av följande skäl:<ul><li>SSO-artefakt är ogiltig eller har upphört att gälla</li><li>Sessionen är inte tillräckligt ny för programmet</li><li>En tyst inloggningsbegäran har skickats men användarens session med Azure AD är ogiltig eller har upphört att gälla.</li></ul>|
|50133|Sessionen är ogiltig på grund av förfallotid eller lösenordsändring.|
|50135|Ändring av lösenord krävs på grund av risken för kontot.|
|50136|Omdirigera MSA-session till program – enkel MSA-session har identifierats. |
|50140|Det här felet uppstod på grund av att den kontinuerliga inloggningen avbröts när användaren loggade in. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, ID för begäran och felkod för mer information. |
|50143|Felaktig matchning av Session - Session är ogiltig eftersom användaren klientorganisationen inte matchar tips för domänen på grund av en annan resurs.  [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med Korrelations-ID, ID för begäran och fel kod att få mer information.|
|50144|Användarens Active Directory-lösenord har upphört att gälla. Skapa ett nytt lösenord för användaren eller så har användaren med hjälp av verktyget för självbetjänad återställning.|
|50146|Det här programmet måste konfigureras med en programspecifik signeringsnyckel. Det har antingen inte konfigurerats med en sådan eller så har nyckeln upphört att gälla/är inte giltig än. Kontakta appägaren.|
|50148|Code_verifier matchar inte code_challenge som anges i autentiseringsbegäran för PKCE. Kontakta apputvecklaren. |
|50155|Autentisering misslyckades för den här användaren.|
|50158|Externa säkerhetskontrollen uppfylldes inte.|
|50161|Anspråk som skickas av en extern provider är inte tillräckligt eller saknas anspråk som har begärt att få extern provider.|
|50166|Det gick inte att skicka begäran till anspråksleverantör.|
|50169|Sfären är inte en konfigurerad sfär för det aktuella tjänstnamnområdet.|
|50172|Den externa påståendeprovidern är inte godkänd. Kontakta klientorganisationens administratör|
|50173|Ny säkerhetstoken krävs. Har den användaren logga in igen med nya autentiseringsuppgifter.|
|50177|Externa utmaning stöds inte för genomströmning användare.|
|50178|Sessionskontrollen stöds inte för genomströmning användare.|
|50180|Windows-integrerad autentisering behövs. Aktivera klientorganisationen för sömlös SSO.|
|51001|Tips för domänen finns inte med den lokala säkerhetsidentifierare - lokala UPN.|
|51004|Användarkontot finns inte i katalogen.|
|51006|Windows-integrerad autentisering behövs. Användaren har loggat in med sessionstoken som saknar WIA-anspråk. Begär att användaren loggar in igen.|
|52004|Användaren har inte gett medgivande för åtkomst till LinkedIn-resurser. |
|53000|För en princip för villkorsstyrd åtkomst krävs en kompatibel enhet, och enheten är inte kompatibel. Låt användare registrera sina enheter med en godkänd MDM-provider som Intune.|
|53001|För en princip för villkorsstyrd åtkomst krävs en domänansluten enhet, och enheten är inte domänansluten. Att användaren använder en domän har anslutit enheten.|
|53002|Programmet som används är inte ett godkänt program för villkorlig åtkomst. Användaren måste använda en av apparna i listan över godkända program för att få åtkomst.|
|53003|Åtkomsten har blockerats på grund av principer för villkorsstyrd åtkomst.|
|53004|Användaren måste slutföra registreringen för multifaktorautentisering innan personen får åtkomst till innehållet. Användaren bör registrera sig för multifaktorautentisering.|
|65001|Programmet X har inte behörighet att komma åt programmet Y eller behörigheten har återkallats. Eller användaren eller administratören har inte godkänt att använda programmet med ID X. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen. Eller användaren eller administratören har inte godkänt att använda programmet med ID X. Send en auktoriseringsbegäran till din klientadministratör för att appens: Y för resursen: Z.|
|65004|Användaren nekade samtycke till att komma åt appen. Låt användaren logga in igen och ge samtycke till appen|
|65005|Resursåtkomstlistan som krävs av programmet innehåller inte program som kan upptäckas av resursen, eller så har klientprogrammet begärt åtkomst till en resurs som inte har angetts i listan över resurser med åtkomst, eller så returnerade diagramtjänsten en ogiltig begäran, eller så hittades inte resursen. Om programmet stöder SAML har du kanske konfigurerat programmet med fel identifierare (entitet). Testa lösningen för SAML via länken nedan: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list)|
|70000|Ogiltigt beviljande på grund av följande skäl:<ul><li>Den begärda SAML 2.0-försäkran har en ogiltig ämnesbekräftelsemetod</li><li>Flödet i appen OnBehalfOf stöds inte på V2</li><li>Den primära uppdateringstoken är inte signerad med en sessionsnyckel</li><li>Ogiltig extern uppdateringstoken</li><li>Åtkomstbeviljandet hämtades för en annan klientorganisation.</li></ul>|
|70001|Programmet X hittades inte i klienten Y. Detta kan inträffa om programmet med identifierare X inte har installerats av administratören för klienten eller godkänts av någon användare i klientorganisationen. Du kanske har felaktigt konfigurerat identifierarvärde för programmet eller skickat din autentiseringsbegäran om till fel klient.|
|70002|Programmet returnerade ogiltiga autentiseringsuppgifter för klienten. Kontakta appägaren.|
|70003|Programmet returnerade en beviljandetyp som inte stöds. Kontakta appägaren.|
|70004|Programmet returnerade en ogiltig omdirigerings-URI. Adressen för omdirigering som angetts av klienten matchar inte några konfigurerade adresser eller någon adress på godkännandelistan för OIDC. Kontakta appägaren.|
|70005|Programmet returnerade en typ för svar som inte stöds på grund av följande skäl:<ul><li>svarstypen "token" är inte aktiverad för programmet</li><li>svarstypen "id_token" kräver OpenID-omfång – innehåller ett OAuth-parametervärde som inte stöds i den kodade wctx</li></ul>Kontakta appägaren.|
|70007|Programmet returnerade ett värde som inte stöds av "response_mode" när du begär en token. Kontakta appägaren.|
|70008|Den angivna Auktoriseringskoden eller uppdatera token har upphört att gälla eller har återkallats. Har återförsök för användare som loggar in.|
|70011|Omfånget som programmet begärde är ogiltigt. Kontakta appägaren.|
|70012|Ett serverfel uppstod under autentisering av en MSA-användare (konsument). Gör inloggningen, och om problemet kvarstår [öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Ogiltig verifieringskod på grund av att användaren skriver in fel användarkod för kodflödet för enheten. Auktorisering godkänns inte.|
|70019|Verifieringskoden har upphört att gälla. Låt användaren försök logga in.|
|70037|Felaktigt utmaningssvar har angetts. Fjärrsessionen för autentisering nekad.|
|75001|Ett fel uppstod under bindningen för SAML-meddelande.|
|75003|Programmet returnerade ett fel relaterat till bindning som inte stöds (SAML-protokollsvar kan inte skickas via andra bindningar än HTTP POST). Kontakta appägaren.|
|75005|Azure AD stöder inte SAML-begäran som skickades av programmet för enkel inloggning. Kontakta appägaren.|
|75008|Begäran från programmet nekades eftersom SAML-förfrågan hade ett oväntat mål. Kontakta appägaren.|
|75011|Autentiseringsmetoden som användaren autentiserades med för tjänsten matchar inte den begärda autentiseringsmetoden. Kontakta appägaren.|
|75016|SAML2-autentiseringsbegäran har ogiltig NameIdPolicy. Kontakta appägaren.|
|80001|Det gick inte att ansluta autentiseringsagenten till Active Directory. Kontrollera att autentiseringsagenten är installerad på en domänansluten dator som har åtkomst till en DC som kan hantera inloggningen av användaren.|
|80002|Internt fel. Tidsgränsen uppnåddes för begäran av lösenordsverifiering. Det gick inte att skicka autentiseringsbegäran till den interna Hybrid Identity-tjänsten. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet.|
|80003|Ogiltigt svar har tagits emot av autentiseringsagenten. Ett okänt fel uppstod vid autentiseringen mot Active Directory lokalt. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet.|
|80005|Autentiseringsagent: Ett okänt fel uppstod under bearbetningen av svaret från den Autentiseringsagenten. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet.|
|80007|Autentiseringsagenten kunde inte verifiera användarens lösenord.|
|80010|Autentiseringsagenten kan inte dekryptera lösenordet. |
|80011|Autentiseringsagenten kunde inte hämta krypteringsnyckeln.|
|80012|Användarna försökte logga in utanför det tillåtna timmar (det har angetts i AD).|
|80013|Autentiseringsförsöket kunde inte slutföras på grund av tidsförskjutning mellan den dator som kör autentiseringsagenten och AD. Åtgärda problem med synkronisering av tid|
|80014|Tidsgränsen uppnåddes för autentiseringsagenten. [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med felkoden, Korrelations-ID och datum/tid för att få mer information om felet.|
|81001|Användarens Kerberos-biljett är för stor. Detta kan inträffa om användaren har för många grupper och Kerberos-biljetten därmed innehåller för många gruppmedlemskap. Minska användarens gruppmedlemskap och försök igen.|
|81005|Autentiseringspaket stöds inte.|
|81007|Klienten har inte aktiverats för sömlös enkel inloggning.|
|81012|Detta är inte ett feltillstånd. Anger den användare som försöker logga in på Azure AD skiljer sig från användaren som har loggat in på enheten. Du kan ignorera den här koden i loggarna.|
|90010|Begäran stöds inte av olika anledningar. Till exempel begäran görs med hjälp av en metod för begäran stöds inte (endast POST-metoden stöds) eller token Signeringsalgoritm som begärdes stöds inte. Kontakta apputvecklaren.|
|90014| Ett obligatoriskt fält för ett protokollmeddelande var saknas, Kontakta programmets ägare. Om du är programmets ägare kan du kontrollera att du har alla nödvändiga parametrar för inloggningsbegäran. |
|90072| Kontot måste läggas till som en extern användare i klientorganisationen först. Logga ut och logga in igen med en annan Azure AD konto.|
|90094| Beviljande måste ha administratörsbehörighet. Be klientorganisationens administratör att ge medgivande för det här programmet.|
|500133| Kontrollen är inte inom räckvidden giltig tid. Se till att den åtkomst-token inte har gått ut innan du använder den för användaren assertion eller begära en ny token.|

## <a name="next-steps"></a>Nästa steg

* [Översikt över inloggningar rapporter](concept-sign-ins.md)
* [Programmatisk åtkomst till Azure AD-rapporter](concept-reporting-api.md)
