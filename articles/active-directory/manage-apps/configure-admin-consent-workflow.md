---
title: Konfigurera arbetsflödet för administratörsgodkännande - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du konfigurerar ett sätt för slutanvändare att begära åtkomst till program som kräver administratörsmedgivande.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430208"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Konfigurera arbetsflödet för administratörsgodkännande (förhandsgranskning)

I den här artikeln beskrivs hur du aktiverar funktionen för förhandsversionen av administratörsgodkännande arbetsflödet (förhandsversion), vilket ger slutanvändarna ett sätt att begära åtkomst till program som kräver administratörsmedgivande.

Utan ett arbetsflöde för administratörsmedgivande blockeras en användare i en klient där användarens medgivande är inaktiverat när de försöker komma åt alla appar som kräver behörighet att komma åt organisationsdata. Användaren ser ett allmänt felmeddelande som säger att de är obehöriga att komma åt appen och de bör be sin administratör om hjälp. Men ofta vet användaren inte vem som ska kontaktas, så de ger antingen upp eller skapar ett nytt lokalt konto i programmet. Även när en administratör meddelas finns det inte alltid en strömlinjeformad process för att hjälpa administratören bevilja åtkomst och meddela sina användare.
 
Arbetsflödet för administratörsgodkännande ger administratörer ett säkert sätt att bevilja åtkomst till program som kräver administratörsgodkännande. När en användare försöker komma åt ett program men inte kan ge sitt samtycke kan de skicka en begäran om administratörsgodkännande. Begäran skickas via e-post till administratörer som har utsetts till granskare. En granskare vidtar åtgärder på begäran och användaren meddelas om åtgärden.

För att godkänna begäranden måste en granskare vara en global administratör, molnprogramadministratör eller programadministratör. Granskaren måste redan ha en av dessa administratörsroller tilldelad. helt enkelt utse dem som en granskare inte höja sina privilegier.

## <a name="enable-the-admin-consent-workflow"></a>Aktivera arbetsflödet för administratörsgodkännande

Så här aktiverar du arbetsflödet för administratörssamtycke och väljer granskare:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Klicka på **Alla tjänster** högst upp på menyn för vänster navigering. **Azure Active Directory-tillägget** öppnas.
3. Skriv " Azure Active**Directory**" i sökrutan för filter och välj **Azure Active Directory-objektet.**
4. Klicka på Företagsprogram på **navigeringsmenyn**. 
5. Under **Hantera**väljer du **Användarinställningar**.
6. Under **Begäran om administratörsmedgivande (förhandsversion)** kan du **ange att användare kan begära administratörsgodkännande till appar som de inte kan samtycka till** **ja**.

   ![Konfigurera arbetsflödesinställningar för administratörsgodkännande](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Konfigurera följande inställningar:

   * **Välj användare om du vill granska begäranden om administratörssamtycke**. Välj granskare för det här arbetsflödet från en uppsättning användare som har den globala administratören, molnprogramadministratören och programadministratörsrollerna.
   * **Valda användare får e-postmeddelanden för begäranden**. Aktivera eller inaktivera e-postmeddelanden till granskarna när en begäran görs.  
   * **Valda användare får påminnelser om förfallodatum**för begäran. Aktivera eller inaktivera e-postmeddelanden om påminnelser till granskarna när en begäran håller på att upphöra att gälla.  
   * **Begäran om samtycke upphör att gälla efter (dagar)**. Ange hur länge begäranden ska vara giltiga.

7. Välj **Spara**. Det kan ta upp till en timme innan funktionen aktiveras.

> [!NOTE]
> Du kan lägga till eller ta bort granskare för det här arbetsflödet genom att ändra listan **Välj granskare för godkännandebegäranden** för administratörer. Observera att en aktuell begränsning av den här funktionen är att granskare kan behålla möjligheten att granska begäranden som gjordes medan de har utsetts till granskare.

## <a name="how-users-request-admin-consent"></a>Så här begär användare administratörsmedgivande

När arbetsflödet för administratörsgodkännande har aktiverats kan användare begära administratörsgodkännande för ett program som de inte har tillstånd att godkänna. I följande steg beskrivs användarens upplevelse när du begär godkännande. 

1. Användaren försöker logga in på programmet.

2. Meddelandet **Godkännande krävs** visas. Användaren skriver en motivering för att behöva åtkomst till appen och väljer sedan **Begäran godkännande**.

   ![Begäran och motivering för administratörsmedgivande](media/configure-admin-consent-workflow/end-user-justification.png)

3. Ett skickat meddelande **bekräftar** att begäran skickades till administratören. Om användaren skickar flera begäranden skickas endast den första begäran till administratören.

   ![Begäran och motivering för administratörsmedgivande](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Användaren får ett e-postmeddelande när deras begäran godkänns, nekas eller blockeras. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Granska och vidta åtgärder för begäranden om administratörsmedgivande

Så här granskar du begäranden om administratörsgodkännande och vidtar åtgärder:

1. Logga in på [Azure-portalen](https://portal.azure.com) som en av de registrerade granskarna av arbetsflödet för administratörsgodkännande.
2. Välj **Alla tjänster** högst upp på menyn för vänster navigering. **Azure Active Directory-tillägget** öppnas.
3. Skriv " Azure Active**Directory**" i sökrutan för filter och välj **Azure Active Directory-objektet.**
4. Klicka på Företagsprogram på **navigeringsmenyn**.
5. Under **Aktivitet**väljer du **Begäran om administratörssamtycke (förhandsversion)**.

   > [!NOTE]
   > Granskare ser bara administratörsbegäranden som har skapats efter att de har angetts som granskare.

1. Välj det program som begärs.
2. Granska information om begäran:  

   * Om du vill se vem som begär åtkomst och varför väljer du fliken **Begärd efter.**
   * Om du vill se vilka behörigheter som begärs av programmet väljer du **Granska behörigheter och medgivande**.

8. Utvärdera begäran och vidta lämpliga åtgärder:

   * **Godkänn begäran**. Om du vill godkänna en begäran beviljar du administratörens medgivande till programmet. När en begäran har godkänts meddelas alla begärande att de har beviljats åtkomst.  
   * **Neka begäran**. Om du vill neka en begäran måste du ange en motivering som ska ges till alla beställare. När en begäran har nekats meddelas alla begärande att de har nekats åtkomst till programmet. Om du nekar en begäran hindras inte användare från att begära administratörsgodkännande till appen igen i framtiden.  
   * **Blockera begäran**. Om du vill blockera en begäran måste du ange en motivering som ska ges till alla beställare. När en begäran har blockerats meddelas alla begärande att de har nekats åtkomst till programmet. Om du blockerar en begäran skapas ett huvudobjekt för tjänsten för programmet i klienten i inaktiverat tillstånd. Användare kan inte begära administratörsgodkännande till programmet i framtiden.
 
## <a name="email-notifications"></a>E-postmeddelanden
 
Om det är konfigurerat får alla granskare e-postmeddelanden när:

* En ny begäran har skapats
* En begäran har upphört att gälla
* En begäran närmar sig utgångsdatumet  
 
Beställare får e-postmeddelanden när:

* De lämnar in en ny begäran om tillgång
* Deras begäran har löpt ut
* Deras begäran har avslagits eller blockerats
* Deras begäran har godkänts
 
## <a name="audit-logs"></a>Granskningsloggar 
 
I tabellen nedan beskrivs de scenarier och granskningsvärden som är tillgängliga för arbetsflödet för administratörsgodkännande. 

> [!NOTE]
> Användarkontexten för granskningsaktören saknas för närvarande i alla scenarier. Detta är en känd begränsning i förhandsversionen.


|Scenario  |Revisionstjänst  |Granskningskategori  |Revisionsaktivitet  |Revisionsaktör  |Begränsningar för granskningslogg  |
|---------|---------|---------|---------|---------|---------|
|Administratör som aktiverar arbetsflödet för begäran om medgivande        |Åtkomstgranskningar           |UserManagement Användare           |Skapa mall för styrningsprincip          |Appkontext            |För närvarande kan du inte hitta användarkontexten            |
|Administratör inaktiverar arbetsflödet för begäran om samtycke       |Åtkomstgranskningar           |UserManagement Användare           |Ta bort mall för styrningsprincip          |Appkontext            |För närvarande kan du inte hitta användarkontexten           |
|Administratör som uppdaterar konfigurationerna för arbetsflöde för medgivande        |Åtkomstgranskningar           |UserManagement Användare           |Uppdatera principmall för styrning          |Appkontext            |För närvarande kan du inte hitta användarkontexten           |
|Slutanvändare skapar en begäran om administratörsgodkännande för en app       |Åtkomstgranskningar           |Princip         |Skapa begäran           |Appkontext            |För närvarande kan du inte hitta användarkontexten           |
|Granskare som godkänner en begäran om administratörsmedgivande       |Åtkomstgranskningar           |UserManagement Användare           |Godkänna alla begäranden i affärsflödet          |Appkontext            |För närvarande kan du inte hitta användarkontexten eller app-ID:t som har beviljats administratörsmedgivande.           |
|Granskare som nekar en begäran om administratörsgodkännande       |Åtkomstgranskningar           |UserManagement Användare           |Godkänna alla begäranden i affärsflödet          |Appkontext            | För närvarande kan du inte hitta användarkontexten för den aktör som nekade en begäran om administratörsgodkännande          |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR 

**Jag har aktiverat det här arbetsflödet, men när jag testar funktionen, varför kan jag inte se den nya uppmaningen "Godkännande krävs" som tillåter mig att begära åtkomst?**

När du har aktiverat funktionen kan det ta upp till 60 minuter för slutanvändare att se uppdateringen. Du kan kontrollera att konfigurationen har trätt i kraft på rätt sätt `https://graph.microsoft.com/beta/settings` genom att visa värdet **EnableAdminConsentRequests** i API:et.

**Varför kan jag inte se alla väntande begäranden som granskare?**

Granskare kan bara se administratörsbegäranden som har skapats efter att de har utsetts till granskare. Så om du nyligen har lagts till som granskare visas inga begäranden som har skapats före tilldelningen.

**Varför ser jag flera begäranden om samma program som granskare?**
  
Om en programutvecklare har konfigurerat sin app för att använda statiskt och dynamiskt medgivande för att begära åtkomst till slutanvändarens data visas två begäranden om administratörsgodkännande. En begäran representerar de statiska behörigheterna och den andra representerar de dynamiska behörigheterna.

**Kan jag som beställare kontrollera statusen för min begäran?**  

Nej, för nu kan beställare bara få uppdateringar via e-postmeddelanden.

**Som granskare, är det möjligt att godkänna ansökan, men inte för alla?**
 
Om du är orolig för att bevilja administratörsmedgivande och låta alla användare i klienten använda programmet rekommenderar vi att du nekar begäran. Bevilja sedan administratörsgodkännande manuellt genom att begränsa åtkomsten till programmet genom att kräva användartilldelning och tilldela användare eller grupper till programmet. Mer information finns i [Metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

## <a name="next-steps"></a>Nästa steg

Mer information om godkännande till program finns i [Azure Active Directory consent framework](../develop/consent-framework.md).

[Konfigurera hur slutanvändare samtycker till program](configure-user-consent.md)

[Bevilja administratör för hela klienten till ett program](grant-admin-consent.md)

[Behörigheter och medgivande på Microsofts identitetsplattform](../develop/active-directory-v2-scopes.md)

[Azure AD på StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
