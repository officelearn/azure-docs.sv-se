---
title: Hantera åtkomst vid akutfall i Azure AD | Microsoft Docs
description: Den här artikeln beskriver hur du använder för åtkomst vid akutfall för att förhindra oavsiktligt att låsas ute från Azure Active Directory (Azure AD)-klienten.
services: active-directory
author: markwahl-msft
ms.author: curtand
ms.date: 12/21/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bd08f9414353eb6d458494d76654dae7262c524
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769207"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Hantera åtkomst vid akutfall i Azure AD

Det är viktigt att du förhindrar att av misstag låsas ute från din Azure Active Directory (Azure AD)-klient eftersom du inte kan logga in eller aktivera en befintlig enskilda användares konto som administratör. Du kan minimera effekten av oavsiktliga bristande administrativ åtkomst genom att skapa två eller flera *för åtkomst vid akutfall* i din klient.

För åtkomst vid akutfall är högt privilegierade och de tilldelas inte till enskilda individer. För åtkomst vid akutfall är begränsade till nödfall eller ”Bryt om” scenarier där normala administrativa konton inte kan användas. Organisationer måste ha ett mål för att begränsa nödfall Kontoanvändning till endast de tider som när det är absolut nödvändigt.

Den här artikeln innehåller riktlinjer för att hantera åtkomst vid akutfall i Azure AD.

## <a name="when-would-you-use-an-emergency-access-account"></a>När ska du använda ett konto för åtkomst vid akutfall?

En organisation kan behöva använda ett konto för åtkomst vid akutfall i följande situationer:

- Användarkonton är externa och federation är inte tillgänglig för tillfället på grund av ett cell nätverksavbrott eller en leverantör av användaridentitet avbrott. Om identitet providern värden i din miljö har slutat fungera, till exempel vara användare det går inte att logga in när Azure AD omdirigerar till sina identitetsprovider.
- Administratörerna har registrerats via Azure Multi-Factor Authentication och deras enskilda enheter är inte tillgänglig eller tjänsten är inte tillgänglig. Användare kanske inte att slutföra Multi-Factor Authentication för att aktivera en roll. Till exempel hindrar ett nätverksavbrott cell dem från besvara telefonsamtal eller ta emot textmeddelanden, bara två autentiseringsmekanismer som de har registrerats för sin enhet.
- Person med den senaste Global administratör för användaråtkomst har lämnat organisationen. Azure AD förhindrar det senaste kontot för Global administratör från att tas bort, men den förhindrar inte att kontot tas bort eller inaktiveras lokalt. Antingen situation kan se hur det går inte att återställa kontot.
- Oförutsedda omständigheter, till exempel en naturkatastrof nödfall, då en mobiltelefon eller till andra nätverk kan saknas. 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>Skapa två molnbaserade Nödfallsåtkomst konton

Skapa två eller flera konton för åtkomst vid akutfall. Dessa konton bör vara molnbaserad konton som använder den \*. onmicrosoft.com-domän och som inte federerad eller synkroniseras från en lokal miljö.

När du konfigurerar dessa konton, måste följande krav uppfyllas:

- För åtkomst vid akutfall ska inte associeras med en enskild användare i organisationen. Se till att dina konton inte är kopplade till alla medarbetare anger mobiltelefoner, maskinvara tokens som överförs med enskilda medarbetare eller andra anställda-specifika autentiseringsuppgifter. Den här försiktighetsåtgärden täcker instanser där en medarbetare kan inte nås när autentiseringsuppgifter krävs. Det är viktigt att säkerställa att alla registrerade enheter hålls på en känd, säker plats som har flera sätt att kommunicera med Azure AD.
- Den autentiseringsmetod som används för ett konto för åtkomst vid akutfall ska vara samma som används av dina andra administrativa konton, inklusive andra konton för åtkomst vid akutfall.  Till exempel om dina normala administratör logga in via den lokala MFA, skulle sedan Azure MFA vara en annan mekanism.  Men om Azure MFA är din primära del av autentisering för dina administrativa konton kan du överväga att en annan metod för dessa, till exempel med villkorlig åtkomst i en tredje parts MFA-provider.
- Enheten eller autentiseringsuppgifter måste inte upphör att gälla eller vara i omfånget för automatisk rensning på grund av bristande användning.  
- Du bör kontrollera Global administratör rolltilldelningen permanent för dina konton för åtkomst vid akutfall. 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Exkludera minst ett konto från telefon Multi-Factor authentication

För att minska risken för en attack som härrör från ett olämpligt lösenord rekommenderar Azure AD att multifaktorautentisering krävs för alla enskilda användare. Den här gruppen innehåller administratörer och alla andra (till exempel finansiella införlivande) vars komprometterat konto skulle ha en betydande inverkan.

Minst en av dina konton för åtkomst vid akutfall ska inte ha samma multifaktorautentisering mekanism som andra icke-nödfall-konton. Detta inkluderar lösningar från tredje part för multifaktorautentisering. Om du har en princip för villkorlig åtkomst som kräver [Multi-Factor authentication för varje administratör](../authentication/howto-mfa-userstates.md) för Azure AD och andra anslutna programvara som en tjänst (SaaS)-appar bör du exkludera för åtkomst vid akutfall från den här krav, och konfigurera en annan mekanism i stället. Dessutom bör du se till konton som inte har en princip för multifaktorautentisering per användare.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Undanta minst ett konto från principer för villkorlig åtkomst

Under en nödsituation vill du inte att en princip för att blockera potentiellt din åtkomst för att åtgärda ett problem. Minst ett åtkomstkonto för nödläge ska uteslutas från alla principer för villkorlig åtkomst. Om du har aktiverat en [baslinjeprincip](../conditional-access/baseline-protection.md), ska du undanta dina konton för åtkomst vid akutfall.

## <a name="additional-guidance-for-hybrid-customers"></a>Ytterligare vägledning för kunder med hybriddistributioner

Ytterligare en möjlighet för organisationer som använder AD DS och AD FS eller liknande identitetsprovider att federera Azure AD är att konfigurera ett åtkomstkonto för nödläge vars MFA-anspråk kan anges av den identitetsprovidern.  Åtkomstkonto för nödläge kan exempelvis vara backas upp av ett certifikat och nyckelpar, till exempel en lagras på ett smartkort.  När användaren autentiseras till AD kan AD FS ange ett anspråk till Azure AD som anger att användaren har genomgått MFA-kraven.  Även med den här metoden måste organisationer fortfarande ha molnbaserade för åtkomst vid akutfall om det inte går att upprätta federation. 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>Store-enheter och autentiseringsuppgifter på en säker plats

Organisationer behöver säkerställa att autentiseringsuppgifterna för åtkomst vid akutfall hålls säkra och kända endast till personer som har behörighet att använda dem för. Vissa kunder använda ett smartkort och andra använda lösenord. Ett lösenord för ett konto för åtkomst vid akutfall är vanligtvis indelade i två eller tre delar, skrivna på separata delar av dokumentet och lagras i säkra, brandsäkert kassaskåp som är säker, separata platser.

Om du använder lösenord, kontrollera att kontona har starka lösenord som inte går ut lösenordet. Vi rekommenderar att lösenorden är minst 16 tecken långt och slumpmässigt genereras.


## <a name="monitor-sign-in-and-audit-logs"></a>Övervaka inloggning och granskningsloggar

Övervaka den [Azure AD-inloggningen och granska loggar](../reports-monitoring/concept-sign-ins.md) för alla inloggningar och granska aktivitet från åtkomst vid akutfall. Normalt kan dessa konton bör inte logga in och bör inte göra ändringar, så att använda troligen kommer att vara avvikande och kräva säkerhetsundersökning.

## <a name="validate-accounts-at-regular-intervals"></a>Verifiera konton med jämna mellanrum

För att utbilda personalen kan använda för åtkomst vid akutfall och validera för åtkomst vid akutfall och gör följande minsta med jämna mellanrum:

- Kontrollera att säkerhetsövervakning personal är medveten om att konto-check-aktivitet pågår.
- Kontrollera att nödfall break glas processen att använda dessa konton är dokumenterade och aktuella.
- Se till att administratörer och de inom som kan behöva utföra dessa steg vid nödfall är tränade på processen.
- Uppdatera kontoinformation, i synnerhet eventuella lösenord för åtkomst vid akutfall-konton och verifiera sedan att åtkomst vid akutfall kan logga in och utföra administrativa uppgifter.
- Se till att användare inte har registrerat Multi-Factor Authentication eller lösenordsåterställning via självbetjäning (SSPR) till alla enskilda användares enhet eller personlig information. 
- Se till att enheten är tillgänglig för alla administratörer som kan behöva använda en nödsituation om konton som har registrerats för Multifaktorautentisering till en enhet för användning under inloggning eller roll-aktivering. Kontrollera också att enheten kan kommunicera via minst två nätverkssökvägar som inte delar ett gemensamt läge för fel. Exempelvis kan kan enheten kommunicera med internet via både en anläggning trådlöst nätverk och en cell-providernätverket.

De här stegen bör utföras med jämna mellanrum och viktiga ändringar:

- Minst var 90 dagar
- När det har skett en ändring i IT-personal, till exempel en ändring i jobbet, sig eller en ny nyanställda
- När Azure AD-prenumerationer i organisationen har ändrats

## <a name="next-steps"></a>Nästa steg

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](directory-admin-roles-secure.md)
- [Lägga till användare med hjälp av Azure AD](../fundamentals/add-users-azure-active-directory.md) och [tilldela den nya användaren till rollen som Global administratör](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Registrera dig för Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), om du inte har registrerat dig redan
- [Hur du kräver tvåstegsverifiering för en användare](../authentication/howto-mfa-userstates.md)
- [Konfigurera ytterligare skydd för globala administratörer i Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), om du använder Office 365
- [Starta en åtkomstgranskning av globala administratörer](../privileged-identity-management/pim-how-to-start-security-review.md) och [övergång befintliga globala administratörer av mer specifika administratörsroller](directory-assign-admin-roles.md)
