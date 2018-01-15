---
title: "Hantera åtkomst nödsituation administrativa konton i Azure AD | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder nödåtkomst konton för att hjälpa organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö."
services: active-directory
keywords: "Inte lägga till eller Redigera nyckelord utan samråd med din SEO-champ."
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 1545fb9a89794a74efbb855c4480040973c3308e
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Hantera åtkomst nödsituation administrativa konton i Azure AD 

För de flesta dagliga aktiviteter *global administratör* rättigheter inte krävs av dina användare. Användare ska inte tilldelas permanent till rollen, eftersom de av misstag kan utföra en uppgift som kräver högre behörighet än de ska ha. När användarna inte behöver fungera som en global administratör, bör de aktivera rolltilldelning med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management (PIM), på sitt eget konto eller ett annat administrativt konto.

Utöver användarnas tar på administrativ behörighet som krävs för sig själva, behöver du förhindra att av misstag utelåst från administrationen av Azure AD-klient eftersom du kan varken logga in eller aktivera en befintlig enskilda användares konto som ett administratören. Du kan minimera effekten av oavsiktligt bristande administrativ åtkomst genom att lagra två eller fler *konton för nödåtkomst* i din klient.

Konton för nödåtkomst hjälper organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö. Dessa konton är mycket Privilegierade och de inte är tilldelade till specifika personer. Konton för nödåtkomst är begränsade till nödsituation eller 'nödkonto' scenarier situationer där normala administrativa konton inte kan användas. Organisationer måste upprätthålla ett mål för att begränsa användningen av nödfall konton till endast den tid då det är nödvändigt.

En organisation kan behöva använda ett konto för nödåtkomst i följande situationer:

 - Användarkonton är federerad och federation är inte tillgänglig på grund av ett cell nätverksavbrott eller identitetsleverantör avbrott. Till exempel om identitet providern värden i din miljö har avbrutits kanske användare inte kan logga in när Azure AD omdirigeras till deras identitetsleverantör. 
 - Administratörer är registrerade via Azure Multi-Factor Authentication och deras enskilda enheter är inte tillgängliga. Användare kan inte slutföra Multi-Factor Authentication för att aktivera en roll. Till exempel hindrar nätverksavbrott cell dem från besvara samtal och ta emot textmeddelanden bara två autentiseringsmekanismer som de har registrerats för enheten. 
 - Personen med senaste global administrativ åtkomst lämnar organisationen. Azure AD gör att senaste *global administratör* kontot tas bort, men det hindrar inte kontot tas bort eller inaktiveras lokalt. Antingen situation kan göra att det går inte att återställa kontot organisationen.

## <a name="initial-configuration"></a>Inledande konfiguration

Skapa två eller flera konton för nödåtkomst. Bör vara endast molnbaserad konton som använder den \*. onmicrosoft.com-domän och som inte federerad eller som synkroniseras från en lokal miljö. 

Konton får inte vara kopplad till en enskild användare i organisationen. Organisationer behöver säkerställa att autentiseringsuppgifter för dessa konton hålls säker och kända endast till personer som har behörighet att använda dem. 

> [!NOTE]
> Lösenordet för ett konto för ett konto för nödåtkomst är vanligtvis indelade i två eller tre delar, skrivs på olika delar av dokumentet och lagras i säker, brandsäkert kassaskåp som är säkra, separata platser. 
>
> Se till att dina konton för nödåtkomst inte är kopplad till alla medarbetare angivna mobiltelefoner, maskinvara tokens som överförs med enskilda medarbetare eller andra medarbetare-specifika autentiseringsuppgifter. Den här försiktighetsåtgärden omfattar instanser där en enskild medarbetare kan inte nås när autentiseringsuppgifter krävs. 

### <a name="initial-configuration-with-permanent-assignments"></a>Inledande konfiguration med permanenta tilldelningar

Ett alternativ är att göra användarna permanenta medlemmar i den *global administratör* roll. Det här alternativet är lämpligt för organisationer som inte har Azure AD Premium P2-prenumerationer.

Om du vill minska risken för angrepp som härrör från en komprometterad lösenord rekommenderar Azure AD som du vill ha Multifaktorautentisering för alla enskilda användare. Den här gruppen ska innehålla administratörer och alla andra (till exempel finansiella polis) vars komprometterat konto skulle ha en betydande inverkan. 

Men om organisationen inte har delade enheter kanske Multifaktorautentisering inte möjligt för dessa konton för nödåtkomst. Om du konfigurerar en princip för villkorlig åtkomst att kräva [Multi-Factor Authentication-registrering för varje admin](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) för Azure AD och andra anslutet programvara som en tjänst (SaaS)-appar du kan behöva konfigurera en princip undantag för att utesluta konton för nödåtkomst från det här kravet.

### <a name="initial-configuration-with-approvals"></a>Inledande konfiguration med godkännanden

Ett annat alternativ är att konfigurera dina användare som kvalificerade och godkännare att aktivera den *global administratör* roll. Det här alternativet kräver din organisation har Azure AD Premium P2-prenumerationer. Kräver också ett alternativ för Multifaktorautentisering som är lämpliga för delade bland flera personer och nätverksmiljön. Dessa krav är eftersom aktivering av den *global administratör* rollen kräver att användare tidigare har utfört Multifaktorautentisering. Mer information finns i [kräva Multifaktorautentisering i Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Vi rekommenderar inte använda Multifaktorautentisering som är associerad med personliga enheter för konton för nödåtkomst. I nödfall faktiska kanske inte den person som behöver åtkomst till en Multi-Factor Authentication-registrerad enhet som också har den personliga enheten. 

Överväg också att hotbilden i grunden. Exempelvis kan ett oförutsett omständigheter, till exempel en naturkatastrof nödfall uppstå under vilka en mobiltelefon eller andra nätverk kan vara otillgänglig. Det är viktigt att se till att alla registrerade enheter hålls i en känd, säker plats som har flera sätt att kommunicera med Azure AD.

## <a name="ongoing-monitoring"></a>Kontinuerlig övervakning

Övervaka den [Azure AD-inloggning och granska loggar](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) för alla inloggningar och granska aktivitet från nödsituation åtkomstkonton. Normalt bör inte logga in dessa konton och bör inte göra ändringar, så att användning av dem kommer troligen att vara avvikande och kräver säkerhet undersökning.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Kontokontroll verifiering måste ske med jämna mellanrum

Utför följande steg som minimum för att verifiera kontot:
- Efter 90 dagar.
- När det har en ändring i IT-avdelning, till exempel en ändring i jobbet, en avvikelse eller en ny hyra.
- När Azure AD-prenumerationer i organisationen har ändrats.

För att träna anställda att använda konton för nödåtkomst, gör du följande:

* Kontrollera att säkerhetsövervakning personal är medveten om att konto-check-aktivitet pågår.
* Kontrollera att molnet användarkonton kan logga in och aktivera sina roller och att användare som kan behöva utföra dessa steg under en nödsituation tränas på processen.
* Se till att de inte har registrerat Multifaktorautentisering eller lösenordsåterställning via självbetjäning (SSPR) till en enskild användare enhet eller personlig information. 
* Se till att enheten är tillgänglig för alla administratörer som kan behöva använda en nödsituation om konton som har registrerats för Multifaktorautentisering till en enhet för användning under aktiveringen av rollen. Kontrollera också att enheten är registrerad med minst två mekanismer som inte delar en gemensam felläget. Enheten kan till exempel kommunicera till internet via både en anläggning trådlöst nätverk och en cell providern nätverk.
* Uppdatera autentiseringsuppgifterna för kontot.

## <a name="next-steps"></a>Nästa steg
- [Lägga till en molnbaserad användare](add-users-azure-active-directory.md) och [tilldelar den nya användaren rollen global administratör](active-directory-users-assign-role-azure-portal.md).
- [Registrera dig för Azure Active Directory Premium](active-directory-get-started-premium.md), om du inte har registrerat dig redan.
- [Kräver Azure Multi-Factor Authentication för enskilda användare som utsetts till administratörer](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Konfigurera ytterligare skydd för globala administratörer i Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), om du använder Office 365.
- [Utföra en åtkomst-granskning av globala administratörer](active-directory-privileged-identity-management-how-to-start-security-review.md) och [övergång befintliga globala administratörer av mer specifika administratörsroller](active-directory-assign-admin-roles-azure-portal.md).


