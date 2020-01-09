---
title: Felkoder i Azure Active Directory Portal | Microsoft Docs
description: Referens för felkoder för inloggningsaktivitet.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa19425de41b182db8c0a8c3b1a7940dbdf5701f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429457"
---
# <a name="sign-in-activity-report-error-codes"></a>Felkoder för inloggnings aktivitet 

Med den information som tillhandahålls av [rapporten med användar inloggningar](concept-sign-ins.md)hittar du svar på frågor som:

- Vem är inloggad i mitt program?
- Vilka program var inloggade på?
- Vilka inloggningar misslyckades och varför?

När en inloggning Miss lyckas visas en felkod som motsvarar felet. Den här artikeln innehåller felkoderna och deras beskrivningar, tillsammans med en föreslagen åtgärd där tillämpligt. 

## <a name="how-can-i-display-failed-sign-ins"></a>Hur visar jag misslyckade inloggningar? 

Välj **Azure Active Directory**på [Azure Portal](https://portal.azure.com) -menyn eller sök efter och välj **Azure Active Directory** från vilken sida som helst.

![Välj Azure Active Directory](./media/reference-sign-ins-error-codes/select-azure-active-directory.png "Azure Active Directory")

Öppna [inloggnings rapporten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)genom att välja **inloggningar** under **övervakning**.

![Inloggnings aktivitet](./media/reference-sign-ins-error-codes/monitoring-sign-ins-in-azure-active-directory.png "Inloggningsaktivitet")

Filtrera rapporten för att visa alla misslyckade inloggningar genom att välja **fel** från List rutan **inloggnings status** .

![Inloggnings aktivitet](./media/reference-sign-ins-error-codes/06.png "Inloggningsaktivitet")

Om du markerar ett objekt i den filtrerade listan öppnas **aktivitets information: inloggnings** bladet. I den här vyn får du ytterligare information om inloggnings händelsen misslyckades, inklusive **felkoden för inloggning** och **fel orsaken**.

![Inloggnings aktivitet](./media/reference-sign-ins-error-codes/05.png "Inloggningsaktivitet")

Du kan också program mässigt komma åt inloggnings data med hjälp av [rapporterings-API: et](concept-reporting-api.md).

## <a name="error-codes"></a>Felkoder


|Fel|Beskrivning|
|---|---|
|16000|Detta är en intern implementerings information och inte ett fel tillstånd. Du kan ignorera den här referensen på ett säkert sätt.|
|20001|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|20012|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|20033|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|40008|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|40009|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|40014|Det är problem med den federerade identitetsprovidern. Kontakta din IDP för att lösa problemet.|
|50000|Det är problem med vår inloggningstjänst. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att lösa problemet.|
|50001|Tjänstens huvudnamn hittades inte i den här klientorganisationen. Detta kan inträffa om programmet inte har installerats av klientens administratör, eller om resurs huvud namnet inte hittades i katalogen eller är ogiltigt.|
|50002|Inloggningen misslyckades på grund av begränsad proxy-åtkomst på klientorganisationen. Om det är din egen klient princip kan du ändra inställningarna för den begränsade klient organisationen för att åtgärda problemet.|
|50003|Inloggningen misslyckades på grund av att signeringsnyckel eller certifikat saknas. Detta kan bero på att ingen signeringsnyckel konfigurerats i programmet. Läs igenom lösningarna som beskrivs i [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Kontakta program ägaren eller program administratören om problemet kvarstår.|
|50005|Användaren försökte logga in på en enhet från en plattform som för närvarande inte stöds via principen för villkorlig åtkomst.|
|50006| Signaturverifieringen misslyckades på grund av en ogiltig signatur. Se lösningen som beskrivs i [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Kontakta program ägaren eller program administratören om problemet kvarstår.|
|50007|Partnerkrypteringscertifikatet hittades inte för det här programmet. [Öppna ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med Microsoft för att få detta åtgärdat.|
|50008|SAML-kontrollen saknas eller är felkonfigurerad i token. Kontakta den federerade providern.|
|50010|Verifiering av målgrupps-URI för programmet misslyckades eftersom ingen tokenmålgrupp har konfigurerats. Kontakta program ägaren för att lösa problemet.|
|50011|Svarsadressen saknas, är felkonfigurerad eller matchar inte svarsadresserna som har konfigurerats för programmet. Prova lösningarna som visas på [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Kontakta program ägaren eller program administratören om problemet kvarstår.|
|50012| Detta är ett allmänt fel meddelande som anger att autentiseringen misslyckades. Detta kan inträffa av skäl som saknade eller ogiltiga autentiseringsuppgifter eller anspråk i begäran. Se till att begäran skickas med rätt autentiseringsuppgifter och anspråk. |
|50013|Kontrollen är ogiltig på grund av olika orsaker. Till exempel matchar inte token utfärdaren API-versionen inom sitt giltiga tidsintervall, token har upphört att gälla eller är felaktigt formaterat, eller så är uppdateringstoken i försäkran inte en primär uppdateringstoken.|
|50017|Verifieringen av certifikatutfärdaren misslyckades, av följande skäl:<ul><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Det gick inte att hitta förväntat CrlSegment</li><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Delta CRL-distributionspunkten har konfigurerats utan motsvarande CRL-distributionspunkt</li><li>Det gick inte att hämta giltiga CRL-segment på grund av timeout-problem</li><li>Det gick inte att ladda ned CRL</li></ul>Kontakta klient organisationens administratör.|
|50020|Användaren har inte behörighet av något av följande skäl.<ul><li>Användaren försöker logga in med ett MSA-konto med v1-slutpunkten</li><li>Användaren finns inte i klient organisationen.</li></ul> Kontakta appägaren.|
|50027|Ogiltig JWT-token på grund av följande skäl:<ul><li>innehåller inte temporärt nonce-anspråk, sub-anspråk</li><li>felaktig matchning av ämnesidentifierare</li><li>duplicerade anspråk i idToken-anspråk</li><li>oväntad utfärdare</li><li>oväntad målgrupp</li><li>inte inom giltigt tidsintervall </li><li>fel tokenformat</li><li>Signaturverifieringen för externa ID-token från utfärdare misslyckades.</li></ul>Kontakta programägaren|
|50029|Ogiltig URI – domännamnet innehåller ogiltiga tecken. Kontakta klient organisationens administratör.|
|50034|Användaren finns inte i katalogen. Kontakta klient organisationens administratör.|
|50042|Det salt som krävs för att generera en-ID saknas i principen. Kontakta klient organisationens administratör.|
|50048|Ämnet överensstämmer inte med utfärdaranspråket i klientförsäkran. Kontakta klient organisationens administratör.|
|50050|Felaktig förfrågan. Kontakta appägaren.|
|50053|Kontot är låst eftersom användaren försökte logga in för många gånger med ett felaktigt användar-ID eller lösen ord.|
|50055|Ogiltigt lösenord, ange lösenordet som har gått ut.|
|50056|Ogiltigt eller null lösen ord – lösen ordet finns inte i arkivet för den här användaren.|
|50057|Användarkontot är inaktiverat. Kontot har inaktiverats av en administratör.|
|50058|Programmet försökte göra en tyst inloggning men användaren kan inte vara tyst inloggad. Programmet måste starta ett interaktivt flöde som ger användarna möjlighet att logga in. Kontakta program ägaren.|
|50059|Användaren finns inte i katalogen. Kontakta klient organisationens administratör.|
|50061|Utloggningsbegäran är ogiltig. Kontakta appägaren.|
|50072|Användaren måste registrera sig för tvåfaktorautentisering (interaktiv).|
|50074|Användaren godkändes inte av MFA-kontrollen.|
|50076|Användaren godkände inte MFA-utmaningen (icke-interaktiv).|
|50079|Användaren måste registrera sig för två Factor Authentication (icke-interaktiva inloggningar).|
|50085|Det krävs social IDP-inloggning för att token ska kunna uppdateras. Låt användaren försöka logga in igen med sitt användar namn och lösen ord.|
|50089|Flow-token har gått ut-autentisering misslyckades. Låt användaren försöka logga in igen med sitt användar namn och lösen ord|
|50097|Enhetsautentisering krävs. Detta kan inträffa på grund av att DeviceId-eller DeviceAltSecId-anspråk är **Null**, eller om det inte finns någon enhet som motsvarar enhets identifieraren.|
|50099|JWT-signaturen är ogiltig. Kontakta appägaren.|
|50105|Den inloggade användaren har inte tilldelats en roll för programmet där personen är inloggad. Tilldela användaren till programmet. Mer information: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|Det begärda objektet för federationssfären finns inte. Kontakta klient organisationens administratör.|
|50120|Utfärda med JWT-rubrik. Kontakta klient organisationens administratör.|
|50124|Anspråkstransformeringen innehåller ogiltiga indataparametrar. Kontakta klient organisationens administratör för att uppdatera principen.|
|50125|Inloggningen avbröts på grund av en lösen ords återställning eller post för lösen ords registrering.|
|50126|Ogiltigt användar namn eller lösen ord eller ogiltigt lokalt användar namn eller lösen ord.|
|50127|Användaren måste installera ett Broker-program för att få åtkomst till det här innehållet.|
|50128|Ogiltigt domän namn – ingen klient identifierings information hittades i begäran eller underförstådd av angivna autentiseringsuppgifter.|
|50129|Enheten är inte ansluten till arbets platsen – **anslutning till arbets plats** krävs för att registrera enheten.|
|50130|Anspråk svärdet kan inte tolkas som en känd autentiseringsmetod.|
|50131|Används i olika fel för villkorlig åtkomst. T.ex. Felaktigt Windows-enhetstillstånd, begäran som blockerats på grund av misstänkt aktivitet, åtkomstprincip och beslut baserade på säkerhetsprinciper.|
|50132|Autentiseringsuppgifter har återkallats på grund av följande skäl:<ul><li>SSO-artefakt är ogiltig eller har upphört att gälla</li><li>Sessionen är inte tillräckligt ny för programmet</li><li>En tyst inloggningsbegäran har skickats men användarens session med Azure AD är ogiltig eller har upphört att gälla.</li></ul>|
|50133|Sessionen är ogiltig på grund av förfallotid eller lösenordsändring.|
|50135|Lösen ords ändring krävs på grund av konto risk.|
|50136|Omdirigera MSA-sessionen till program – enkel MSA-session har identifierats. |
|50140|Det här felet uppstod på grund av att den kontinuerliga inloggningen avbröts när användaren loggade in. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, ID för begäran och felkod för mer information. |
|50143|Felaktig matchning av sessioner – sessionen är ogiltig eftersom användar klienten inte matchar domän tipset på grund av en annan resurs.  [Öppna ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med KORRELATIONS-ID, fråge-ID och felkod för att få mer information.|
|50144|Användarens Active Directory-lösenord har upphört att gälla. Generera ett nytt lösen ord för användaren eller låt användaren använda verktyget för självbetjänings återställning.|
|50146|Det här programmet måste konfigureras med en programspecifik signeringsnyckel. Det har antingen inte konfigurerats med en sådan eller så har nyckeln upphört att gälla/är inte giltig än. Kontakta appägaren.|
|50148|Code_verifier matchar inte code_challenge som anges i autentiseringsbegäran för PKCE. Kontakta programutvecklaren. |
|50155|Det gick inte att autentisera enheten för den här användaren.|
|50158|Den externa säkerhets kontrollen uppfylldes inte.|
|50161|Anspråk som skickas av extern provider är inte tillräckligt, eller så saknar anspråk begärs för extern provider.|
|50166|Det gick inte att skicka begäran till anspråks leverantören.|
|50169|Sfären är inte en konfigurerad sfär för det aktuella tjänstnamnområdet.|
|50172|Den externa påståendeprovidern är inte godkänd. Kontakta klient organisationens administratör|
|50173|Ny säkerhetstoken krävs. Låt användaren logga in igen med nya autentiseringsuppgifter.|
|50177|Extern utmaning stöds inte för passthrough-användare.|
|50178|Session Control stöds inte för passthrough-användare.|
|50180|Windows-integrerad autentisering behövs. Aktivera klientorganisationen för sömlös SSO.|
|50181|Det gick inte att logga in på eng ång slö sen ord. |
|50201|Detta meddelande visas vid inloggning när ytterligare information ska ges till användaren.|
|51001|Domän tipset finns inte med lokal säkerhets identifierare – lokalt UPN.|
|51004|Användarkontot finns inte i katalogen.|
|51006|Windows-integrerad autentisering behövs. Användaren loggade in med sessionstoken som saknas via anspråk. Begär att användaren loggar in igen.|
|52004|Användaren har inte gett medgivande för åtkomst till LinkedIn-resurser. |
|53000|För en princip för villkorsstyrd åtkomst krävs en kompatibel enhet, och enheten är inte kompatibel. Låt användaren registrera sin enhet med en godkänd MDM-provider som Intune.|
|53001|För en princip för villkorsstyrd åtkomst krävs en domänansluten enhet, och enheten är inte domänansluten. Låt användaren använda en domänansluten enhet.|
|53002|Det program som används är inte ett godkänt program för villkorlig åtkomst. Användaren måste använda en av apparna i listan över godkända program för att få åtkomst.|
|53003|Åtkomsten har blockerats på grund av principer för villkorlig åtkomst.|
|53004|Användaren måste slutföra registreringen för multifaktorautentisering innan personen får åtkomst till innehållet. Användaren bör registrera sig för multifaktorautentisering.|
|65001|Programmet X har inte behörighet att komma åt programmet Y eller behörigheten har återkallats. Eller användaren eller administratören har inte godkänt att använda programmet med ID X. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen. Eller användaren eller administratören har inte godkänt att använda programmet med ID X. Skicka en auktoriseringsbegäran till din klientadministratör som gäller i appens ställe: Y för resursen: Z.|
|65004|Användaren nekade samtycke till att komma åt appen. Låt användaren logga in igen och ge samtycke till appen|
|65005|Resursåtkomstlistan som krävs av programmet innehåller inte program som kan upptäckas av resursen, eller så har klientprogrammet begärt åtkomst till en resurs som inte har angetts i listan över resurser med åtkomst, eller så returnerade diagramtjänsten en ogiltig begäran, eller så hittades inte resursen. Om programmet stöder SAML har du kanske konfigurerat programmet med fel identifierare (entitet). Testa lösningen för SAML via länken nedan: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Ogiltigt beviljande på grund av följande skäl:<ul><li>Den begärda SAML 2.0-försäkran har en ogiltig ämnesbekräftelsemetod</li><li>Flödet i appen OnBehalfOf stöds inte på V2</li><li>Den primära uppdateringstoken är inte signerad med en sessionsnyckel</li><li>Ogiltig extern uppdateringstoken</li><li>Åtkomstbeviljandet hämtades för en annan klientorganisation.</li></ul>|
|70001|Programmet X hittades inte i klienten Y. Detta kan inträffa om programmet med identifierare X inte har installerats av administratören för klienten eller godkänts av någon användare i klientorganisationen. Du kan ha felkonfigurerat ID-värdet för programmet eller skickat autentiseringsbegäran till fel klient.|
|70002|Programmet returnerade ogiltiga autentiseringsuppgifter för klienten. Kontakta appägaren.|
|70003|Programmet returnerade en beviljandetyp som inte stöds. Kontakta appägaren.|
|70004|Programmet returnerade en ogiltig omdirigerings-URI. Adressen för omdirigering som angetts av klienten matchar inte några konfigurerade adresser eller någon adress på godkännandelistan för OIDC. Kontakta appägaren.|
|70005|Programmet returnerade en typ för svar som inte stöds på grund av följande skäl:<ul><li>svarstypen "token" är inte aktiverad för programmet</li><li>svarstypen "id_token" kräver OpenID-omfång – innehåller ett OAuth-parametervärde som inte stöds i den kodade wctx</li></ul>Kontakta appägaren.|
|70007|Programmet returnerade ett värde som inte stöds av "response_mode" när du begär en token. Kontakta appägaren.|
|70008|Den angivna auktoriseringsregeln eller uppdateringstoken har förfallit eller återkallats. Låt användaren försöka logga in igen.|
|70011|Omfånget som programmet begärde är ogiltigt. Kontakta appägaren.|
|70012|Ett serverfel uppstod under autentisering av en MSA-användare (konsument). Försök logga in igen och [öppna ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md) om problemet kvarstår |
|70018|Ogiltig verifieringskod på grund av att användaren skriver in fel användarkod för kodflödet för enheten. Auktorisering är inte godkänd.|
|70019|Verifieringskoden har upphört att gälla. Låt användaren försöka logga in igen.|
|70037|Felaktigt utmaningssvar har angetts. Fjärrsessionen för autentisering nekad.|
|70043|Hantering av Azure villkorlig åtkomst tvingar sessionen att gå ut|
|70044|Hantering av Azure villkorlig åtkomst tvingar sessionen att gå ut|
|75001|Ett fel uppstod under bindningen för SAML-meddelande.|
|75003|Programmet returnerade ett fel relaterat till bindning som inte stöds (SAML-protokollsvar kan inte skickas via andra bindningar än HTTP POST). Kontakta appägaren.|
|75005|Azure AD stöder inte SAML-begäran som skickades av programmet för enkel inloggning. Kontakta appägaren.|
|75008|Begäran från programmet nekades eftersom SAML-förfrågan hade ett oväntat mål. Kontakta appägaren.|
|75011|Autentiseringsmetoden som användaren autentiserades med för tjänsten matchar inte den begärda autentiseringsmetoden. Kontakta appägaren.|
|75016|SAML2-autentiseringsbegäran har ogiltig NameIdPolicy. Kontakta appägaren.|
|80001|Det gick inte att ansluta autentiseringsagenten till Active Directory. Kontrollera att autentiseringsagenten är installerad på en domänansluten dator som har åtkomst till en DC som kan hantera inloggningen av användaren.|
|80002|Internt fel. Tids gränsen nåddes för begäran om lösen ords validering. Det gick inte att skicka autentiseringsbegäran till den interna hybrid identitets tjänsten. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet.|
|80003|Ogiltigt svar har tagits emot av autentiseringsagenten. Ett okänt fel uppstod vid autentiseringen mot Active Directory lokalt. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet.|
|80005|Autentiseringsagent: Ett okänt fel uppstod under bearbetningen av svaret från autentiseringsagenten. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet.|
|80007|Autentiseringsagenten kunde inte verifiera användarens lösenord.|
|80010|Autentiseringsagenten kan inte dekryptera lösenordet. |
|80011|Autentiseringsagenten kunde inte hämta krypteringsnyckeln.|
|80012|Användarna försökte logga in utanför de tillåtna timmarna (det anges i AD).|
|80013|Autentiseringsförsöket kunde inte slutföras på grund av tidsförskjutning mellan den dator som kör autentiseringsagenten och AD. Åtgärda problem med synkronisering av tid|
|80014|Tids gränsen nåddes för Autentiseringstjänsten. [Öppna ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med felkod, KORRELATIONS-ID och datetime för att få mer information om det här felet.|
|81001|Användarens Kerberos-biljett är för stor. Detta kan inträffa om användaren har för många grupper och Kerberos-biljetten därmed innehåller för många gruppmedlemskap. Minska användarens gruppmedlemskap och försök igen.|
|81005|Autentiseringspaket stöds inte.|
|81007|Klient organisationen är inte aktive rad för sömlös SSO.|
|81012|Detta är inte ett fel tillstånd. Det anger att användaren som försöker logga in på Azure AD skiljer sig från den användare som är inloggad på enheten. Du kan ignorera den här koden i loggarna på ett säkert sätt.|
|90010|Begäran stöds inte av olika orsaker. Begäran görs till exempel med en begäran-metod som inte stöds (endast POST-metoden stöds) eller så stöds inte den token-Signeringsalgoritm som begärdes. Kontakta apputvecklaren.|
|90014| Ett obligatoriskt fält för ett protokoll meddelande saknas, kontakta program ägaren. Om du är program ägaren ser du till att du har alla nödvändiga parametrar för inloggningsbegäran. |
|90051| Ogiltig Delegerings-token. Ogiltigt nationellt moln-ID ({cloudId}) har angetts.|
|90072| Kontot måste läggas till som en extern användare i klientorganisationen först. Logga ut och logga in igen med ett annat Azure AD-konto.|
|90094| Tilldelningen kräver administratörs behörighet. Be klient administratören att tillhandahålla medgivande för det här programmet.|
|500011| Det gick inte att hitta resurs huvud namnet <site address> i klient organisationen med namnet <tenant ID>. Detta kan inträffa om programmet inte har installerats av administratören för klienten eller om någon användare i klient organisationen har godkänt detta. Du kanske har skickat din begäran om autentisering till fel klient.|
|500021| Klienten är begränsad av företagets proxy. Neka resurs åtkomst.|
|500121| Autentiseringen misslyckades under begäran om stark autentisering.|
|500133| Kontrollen ligger inte inom det giltiga tidsintervallet. Se till att åtkomsttoken inte har upphört att gälla innan du använder den för användar kontroll eller begär en ny token.|
|530021|Programmet uppfyller inte kraven för godkända appar för villkorlig åtkomst.|
|530032|Blockerad av säkerhets princip.| 
|700016|Det gick inte att hitta programmet med identifieraren {appIdentifier} i katalogen {tenantName}. Detta kan inträffa om programmet inte har installerats av administratören för klienten eller om någon användare i klient organisationen har godkänt detta. Du kan ha skickat din autentiseringsbegäran till fel klient.|
|900432|Konfidentiell klient stöds inte i en begäran mellan moln.|
|7000218|Begär ande texten måste innehålla följande parameter: client_assertion eller client_secret.|


## <a name="next-steps"></a>Nästa steg

* [Översikt över inloggnings rapporter](concept-sign-ins.md)
* [Programmerings åtkomst till Azure AD-rapporter](concept-reporting-api.md)
