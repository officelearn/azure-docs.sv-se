---
title: Rapporter om inloggningfelkoder i Azure Active Directory-portalen | Microsoft Docs
description: Referens för felkoder för inloggningsaktivitet.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ebad9304c38333173cec66c6b5574a9b45b17cd1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Rapporter om inloggningfelkoder i Azure Active Directory-portalen

Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

- Vem har loggat in med Azure Active Directory?
- Vilka appar loggade de in på?
- Vilka inloggningar misslyckades och varför?

I det här avsnittet visas felkoder och relaterade beskrivningar. 

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
|50001|Tjänstens huvudnamn med namnet X hittades inte i klienten Y. Detta kan inträffa om programmet inte har installerats av administratören för klienten. Alternativt kunde huvudresursen inte hittas i katalogen eller är ogiltig.|
|50008|SAML-kontrollen saknas eller är felkonfigurerad i token.|
|50011|Svarsadressen saknas, är felkonfigurerad eller matchar inte svarsadresserna som har konfigurerats för programmet.|
|50012|Användarrapporterat bedrägeri under multifaktorautentisering.|
|50053|Ditt konto är låst eftersom du har försökt logga in för många gånger med ett felaktigt användar-ID eller lösenord.|
|50054|Det gamla lösenordet krävs för autentiseringen.|
|50055|Ogiltigt lösenord, ange lösenordet som har gått ut.|
|50057|Användarkontot är inaktiverat.|
|50058|Det gick inte att hitta information om användarens identitet finns bland angivna autentiseringsuppgifter eller användaren i klienten eller En tyst inloggningsbegäran har skickats men ingen användare är inloggad eller det gick inte att autentisera användaren.|
|50072|Användaren måste registrera sig för tvåfaktorsautentisering (interaktiv)|
|50074|Användaren godkändes inte av MFA-kontrollen.|
|50076|Användaren godkändes inte av MFA-kontrollen (inte interaktiv)|
|50079|Användaren måste registrera sig för tvåfaktorsautentisering.|
|50089|Verifiering av flödestoken misslyckades på grund av att flödestoken upphörde att gälla.|
|50097|Enheten är inte autentiserad.|
|50105|Den inloggade användaren har inte tilldelats en roll för det här programmet.|
|50125|Inloggningen avbröts på grund av en lösenordsåterställning eller en lösenordsregistrering|
|50126|Ogiltigt användarnamn eller lösenord eller ogiltigt lokalt användarnamn eller lösenord.|
|50127|Användaren måste installera en asynkron meddelandekö för att få åtkomst till det här innehållet.|
|50129|Enheten är inte arbetsplatsansluten – arbetsplatsanslutning krävs för registrering av enheten.|
|50131|Används i olika fel för villkorlig åtkomst. T.ex felaktigt Windows-enhettillstånd, begäran som blockerats på grund av misstänkt aktivitet, åtkomstprincip och säkerhetsprincipbeslut.|
|50133|Sessionen är ogiltig på grund av förfallotid eller lösenordsändring.|
|50140|Användaren tillfrågas om tillstånd att hållas inloggad på enheten|
|50144|Användarens Active Directory-lösenord har upphört att gälla.|
|53000|För en princip för villkorsstyrd åtkomst krävs en kompatibel enhet, och enheten är inte kompatibel.|
|53003|Åtkomsten har blockerats på grund av principer för villkorsstyrd åtkomst.|
|65001|Programmet X har inte behörighet att komma åt programmet Y eller behörigheten har återkallats. Eller användaren eller administratören har inte godkänt att använda programmet med ID X. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen. Eller användaren eller administratören har inte godkänt att använda programmet med ID X. Skicka en auktoriseringsbegäran till din klientadministratör som gäller i appens ställe: Y för resursen: Z.|
|65005|Programmet som krävs resursåtkomstlistan innehåller inte program som kan upptäckas av resursen eller Klientprogrammet har begärt åtkomst till resurs som inte har angetts i listan över resurser som med åtkomst eller Diagramtjänsten returnerade ogiltig begäran eller resursen hittades inte.|
|70001|Programmet X hittades inte i klienten Y. Detta kan inträffa om programmet inte har installerats av administratör för klienten eller godkänts av någon användare på klienten. Du kanske har skickat din begäran om autentisering till fel klient.|
|80001|Det gick inte att ansluta autentiseringsagenten till Active Directory.|
|80002|Den tillåtna tiden för autentiseringsagentens lösenordsvalidering överskreds.|
|80003|Ogiltigt svar har tagits emot av autentiseringsagenten.|
|80004|Felaktig UPN (User Principal Name) används i begäran om inloggning.|
|80005|Autentiseringsagent: Fel uppstod.|
|80007|Autentiseringsagenten kunde inte verifiera användarens lösenord.|
|80010|Autentiseringsagenten kan inte dekryptera lösenordet.|
|80011|Autentiseringsagenten kunde inte hämta dekrypteringsnyckeln.|
|81001|Användarens Kerberos-biljett är för stor.|
|81002|Det gick inte att verifiera användarens Kerberos-biljett.|
|81003|Det gick inte att verifiera användarens Kerberos-biljett.|
|81004|Kerberos-autentiseringsförsök misslyckades.|
|81008|Det gick inte att verifiera användarens Kerberos-biljett.|
|81009|Det gick inte att verifiera användarens Kerberos-biljett.|
|81010|Sömlös SSO misslyckades eftersom användarens Kerberos-biljett har upphört att gälla eller är ogiltig.|
|81011|Det gick inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett.|
|81012|Användaren som försöker logga in på Azure AD skiljer sig från användaren som har loggat in på enheten.|
|81013|Det gick inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett.|
|90014|Används i olika fall när ett förväntat fält inte finns i autentiseringsuppgifterna.|
|90093|Diagram som returneras med otillåten felkod för begäran.|
|90094|En administratör måste godkänna.|
## <a name="next-steps"></a>Nästa steg

Mer information finns i [inloggningsaktivitetsrapporter i Azure Active Directory-portalen](active-directory-reporting-activity-sign-ins.md).
