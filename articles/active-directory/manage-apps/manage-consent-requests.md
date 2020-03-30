---
title: Hantera samtycke till program och utvärdera samtyckesbegäranden - Azure AD
description: Läs om hur du hanterar medgivandebegäranden när användarens medgivande är inaktiverat eller begränsat och hur du utvärderar en begäran om administratörsgodkännande för klientinnehåll till ett program.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367846"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Hantera samtycke till ansökningar och utvärdera samtyckesförfrågningar

Microsoft rekommenderar att slutanvändarens samtycke till program [inaktiveras.](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) Detta centraliserar beslutsprocessen med organisationens säkerhets- och identitetsadministratörsteam.

När slutanvändarens medgivande har inaktiverats eller begränsats finns det flera viktiga överväganden för att säkerställa att din organisation förblir säker samtidigt som affärskritiska program kan användas. Dessa steg är avgörande för att minimera påverkan på organisationens supportteam och IT-administratörer, samtidigt som användningen av ohanterada konton i program från tredje part förhindras.

## <a name="process-changes-and-education"></a>Processförändringar och utbildning

 1. Överväg att aktivera [arbetsflödet för administratörsgodkännande (förhandsversion)](configure-admin-consent-workflow.md) så att användare kan begära administratörsgodkännande direkt från medgivandeskärmen.

 2. Se till att alla administratörer förstår [behörigheterna och samtyckesramverket,](../develop/consent-framework.md)hur [samtyckesprompten](../develop/application-consent-experience.md) fungerar och hur du [utvärderar en begäran om administratörsmedgivande för hela klienten](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Granska organisationens befintliga processer för att användare ska begära administratörsgodkännande för ett program och gör uppdateringar om det behövs. Om processer ändras:
    * Uppdatera relevant dokumentation, övervakning, automatisering och så vidare.
    * Kommunicera processändringar till alla berörda användare, utvecklare, supportteam och IT-administratörer.

## <a name="auditing-and-monitoring"></a>Granskning och övervakning

1. [Granska appar och beviljade behörigheter](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) i din organisation för att säkerställa att inga omotiverade eller misstänkta program tidigare har beviljats åtkomst till data.

2. Granska [Identifiera och åtgärda bidrag för olagligt samtycke i Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) för ytterligare bästa praxis och skydd mot misstänkta program som begär OAuth-medgivande.

3. Om din organisation har rätt licens:

    * Använd ytterligare [granskningsfunktioner för OAuth-program i Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth).
    * Använd [Azure Monitor-arbetsböcker för att övervaka behörigheter och samtyckesrelaterad](../reports-monitoring/howto-use-azure-monitor-workbooks.md) aktivitet. *Arbetsboken Consent Insights* ger en vy över appar efter antal misslyckade begäranden om medgivande. Detta kan vara användbart för att prioritera program för administratörer att granska och besluta om att ge dem administratörsmedgivande.

### <a name="additional-considerations-for-reducing-friction"></a>Ytterligare överväganden för att minska friktionen

För att minimera påverkan på betrodda, affärskritiska program som redan används bör du proaktivt bevilja administratörsgodkännande till program som har ett stort antal bidrag från användarens medgivande:

1. Gör en inventering av de appar som redan har lagts till i organisationen med hög användning, baserat på inloggningsloggar eller samtyckesbidragsaktivitet. Ett [PowerShell-skript](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) kan användas för att snabbt och enkelt upptäcka program med ett stort antal bidrag från användarens medgivande.

2. Utvärdera de bästa programmen som ännu inte har beviljats administratörsgodkännande.

   > [!IMPORTANT]
   > Utvärdera noggrant ett program innan du beviljar administratörsmedgivande för hela klienten, även om många användare i organisationen redan har samtyckt till det själva.

3. För varje program som godkänns beviljar du administratörsgodkännande för hela klienten med någon av de metoder som beskrivs nedan.

4. Överväg [att begränsa användaråtkomsten](configure-user-consent.md)för varje godkänd ansökan .

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Utvärdera en begäran om administratörsmedgivande för klienten

Att bevilja administratörsgodkännande för hela klienten är en känslig åtgärd.  Behörigheter beviljas för hela organisationens räkning och kan innehålla behörigheter för att försöka med mycket privilegierade åtgärder. Till exempel rollhantering, fullständig åtkomst till alla postlådor eller alla webbplatser och fullständig personifiering av användare.

Innan du beviljar administratörsmedgivande för hela klienten måste du se till att du litar på programmet och programutgivaren, för den åtkomstnivå du beviljar. Om du inte är säker på att du förstår vem som styr programmet och varför programmet begär behörigheterna *ska du inte ge medgivande*.

Följande lista innehåller några rekommendationer att tänka på när du utvärderar en begäran om att bevilja administratörsgodkännande.

* **Förstå [behörighets- och samtyckesramverket i Microsofts identitetsplattform.](../develop/consent-framework.md)**

* **Förstå skillnaden mellan [delegerade behörigheter och programbehörigheter](../develop/v2-permissions-and-consent.md#permission-types).**

   Programbehörigheter gör det möjligt för programmet att komma åt data för hela organisationen, utan någon användarinteraktion. Delegerade behörigheter gör att programmet kan agera på uppdrag av en användare som någon gång har loggat in i programmet.

* **Förstå vilka behörigheter som begärs.**

   De behörigheter som begärs av programmet visas i [samtyckesprompten](../develop/application-consent-experience.md). Om du expanderar behörighetstiteln visas behörighetens beskrivning. Beskrivningen för programbehörigheter slutar i allmänhet i "utan en inloggad användare". Beskrivningen för delegerade behörigheter slutar vanligtvis med "för den inloggade användarens räkning". Behörigheter för Microsoft Graph API beskrivs i [Microsoft Graph Permissions Reference]- se dokumentationen för andra API:er för att förstå de behörigheter de exponerar.

   Om du inte förstår att ett tillstånd begärs ska *du inte ge samtycke*.

* **Förstå vilket program som begär behörigheter och vem som har publicerat programmet.**

   Var försiktig med skadliga program som försöker se ut som andra program.

   Om du tvivlar på en ansökans eller dess utgivares legitimitet ska *du inte ge sitt samtycke.* I stället söka ytterligare bekräftelse (till exempel direkt från programutgivaren).

* **Kontrollera att de begärda behörigheterna är anpassade till de funktioner du förväntar dig av programmet.**

   Ett program som erbjuder SharePoint-webbplatshantering kan till exempel kräva delegerad åtkomst för att läsa alla webbplatssamlingar, men behöver inte nödvändigtvis fullständig åtkomst till alla postlådor eller fullständig identitetsstöld i katalogen.

   Om du misstänker att programmet begär fler behörigheter än den behöver ska *du inte ge medgivande*. Kontakta programutgivaren för att få mer information.

## <a name="granting-consent-as-an-administrator"></a>Bevilja samtycke som administratör

### <a name="granting-tenant-wide-admin-consent"></a>Bevilja administratörsgodkännande för hela klienten

Se [Bevilja administratörsgodkännande för hela klienten till ett program](grant-admin-consent.md) för steg-för-steg-instruktioner för att bevilja administratörsmedgivande för hela klienten från Azure-portalen, med hjälp av Azure AD PowerShell eller från själva mediordnadsprompten.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Bevilja samtycke för en viss användares räkning

I stället för att bevilja samtycke för hela organisationen kan en administratör också använda [Api:et för Microsft Graph](https://docs.microsoft.com/graph/use-the-api) för att bevilja medgivande till delegerade behörigheter för en enskild användares räkning. Mer information finns i [Hämta åtkomst för en användare](https://docs.microsoft.com/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Begränsa användarnas åtkomst till program

Användarnas åtkomst till program kan fortfarande begränsas även när administratörsgodkännande för hela klienten har beviljats. Mer information om hur du kräver användartilldelning till ett program finns i [metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

Mer information om hur du hanterar ytterligare komplexa scenarier finns i [använda Azure AD för hantering av programåtkomst](what-is-access-management.md).

## <a name="next-steps"></a>Nästa steg

[Fem steg för att skydda din identitetsinfrastruktur](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Konfigurera arbetsflödet för administratörsgodkännande](configure-admin-consent-workflow.md)

[Konfigurera hur slutanvändare samtycker till program](configure-user-consent.md)

[Behörigheter och medgivande på Microsofts identitetsplattform](../develop/active-directory-v2-scopes.md)

[Azure AD på StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)