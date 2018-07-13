---
title: Hantera nödfall-åtkomst till administratörskonton i Azure AD | Microsoft Docs
description: Den här artikeln beskriver hur du använder för åtkomst vid akutfall som hjälper organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö.
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 4f3772abc1cdbd3b35b8b1f16e7a47c0f1a17783
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38595662"
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Hantera nödfall-åtkomst till administratörskonton i Azure AD 

För de flesta dagliga aktiviteter, *global administratör* rights inte behövs av användarna. Användare bör inte tilldelas permanent till rollen, eftersom de av misstag kan utföra en uppgift som kräver högre behörighet än de ska ha. När användare inte behöver agera som global administratör, bör de aktiverar rolltilldelningen med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management (PIM), på sitt eget konto eller ett alternativt administrativt konto.

Förutom användarnas tar på administrativ behörighet för själva, måste du förhindra att av misstag låsas ute från administrationen av Azure AD-klient eftersom du kan varken logga in eller aktivera en befintlig enskilda användares konto som ett administratör. Du kan minimera effekten av oavsiktliga bristande administrativ åtkomst genom att lagra två eller flera *för åtkomst vid akutfall* i din klient.

För åtkomst vid akutfall kan hjälpa organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö. Dessa konton är mycket Privilegierade och de tilldelas inte till enskilda individer. För åtkomst vid akutfall är begränsade till nödfall eller ”Bryt om” scenarion situationer där normala administrativa konton inte kan användas. Organisationer måste ha ett mål för att begränsa nödfall Kontoanvändning till endast den tid då det är nödvändigt.

En organisation kan behöva använda ett konto för åtkomst vid akutfall i följande situationer:

 - Användarkonton är externa och federation är inte tillgänglig för tillfället på grund av ett cell nätverksavbrott eller en leverantör av användaridentitet avbrott. Om identitet providern värden i din miljö har slutat fungera, till exempel vara användare det går inte att logga in när Azure AD omdirigerar till sina identitetsprovider. 
 - Administratörerna har registrerats via Azure Multi-Factor Authentication och deras enskilda enheter är inte tillgänglig. Användare kanske inte att slutföra Multi-Factor Authentication för att aktivera en roll. Till exempel hindrar ett nätverksavbrott cell dem från besvara telefonsamtal eller ta emot textmeddelanden, bara två autentiseringsmekanismer som de har registrerats för sin enhet. 
 - Person med senaste global administrativ åtkomst har lämnat organisationen. Azure AD förhindrar sist *global administratör* kontot tas bort, men den förhindrar inte att kontot tas bort eller inaktiveras lokalt. Antingen situation kan se hur det går inte att återställa kontot.

## <a name="initial-configuration"></a>Inledande konfiguration

Skapa två eller flera konton för åtkomst vid akutfall. Bör vara molnbaserad konton som använder den \*. onmicrosoft.com-domän och som inte federerad eller synkroniseras från en lokal miljö. 

Kontona får inte vara kopplad till en enskild användare i organisationen. Organisationer behöver säkerställa att autentiseringsuppgifterna för dessa konton hålls säkra och kända endast till personer som har behörighet att använda dem för. 

> [!NOTE]
> Lösenordet för ett konto för ett konto för åtkomst vid akutfall är vanligtvis indelade i två eller tre delar, skrivna på separata delar av dokumentet och lagras i säkra, brandsäkert kassaskåp som är säker, separata platser. 
>
> Se till att dina konton för åtkomst vid akutfall inte är anslutna med alla medarbetare anger mobiltelefoner, maskinvara tokens som överförs med enskilda medarbetare eller andra anställda-specifika autentiseringsuppgifter. Den här försiktighetsåtgärden täcker instanser där en medarbetare kan inte nås när autentiseringsuppgifter krävs. 

### <a name="initial-configuration-with-permanent-assignments"></a>Inledande konfiguration med permanent tilldelningar

Ett alternativ är att göra användare permanenta medlemmar i den *global administratör* roll. Det här alternativet skulle vara lämplig för organisationer som inte har Azure AD Premium P2-prenumerationer.

För att minska risken för en attack som härrör från ett olämpligt lösenord rekommenderar Azure AD att Multifaktorautentisering krävs för alla enskilda användare. Den här gruppen ska inkludera administratörer och alla andra (till exempel finansiella införlivande) vars komprometterat konto skulle ha en betydande inverkan. 

Men om organisationen inte har delade enheter, kanske Multi-Factor Authentication inte möjligt för dessa konton för åtkomst vid akutfall. Om du konfigurerar en princip för villkorlig åtkomst som kräver [Multi-Factor Authentication-registrering för varje administratör](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) för Azure AD och andra anslutet programvara som en tjänst (SaaS)-appar, du kan behöva konfigurera princip undantag ska undantas för åtkomst vid akutfall från det här kravet.

### <a name="initial-configuration-with-approvals"></a>Inledande konfiguration med godkännanden

Ett annat alternativ är att konfigurera dina användare som berättigade och godkännare att aktivera den *global administratör* roll. Det här alternativet kräver din organisation har Azure AD Premium P2-prenumerationer. Det också kräver ett Multi-Factor Authentication-alternativ som passar för delad användning av flera personer och nätverksmiljön. Dessa krav är eftersom aktivering av den *global administratör* rollen kräver att användarna har tidigare utfört Multi-Factor Authentication. Mer information finns i [så här kräver du Multifaktorautentisering i Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Vi rekommenderar inte att använda Multifaktorautentisering som är associerat med personliga enheter för åtkomst vid akutfall. I nödfall faktiska kanske inte den person som behöver åtkomst till en Multi-Factor Authentication-registrerad enhet som har den personliga enheten. 

Överväg också att hotlandskapet. Till exempel kan en oförutsedda omständigheter, till exempel en naturkatastrof vid akutfall uppstå, då en mobiltelefon eller till andra nätverk kan saknas. Det är viktigt att säkerställa att alla registrerade enheter hålls på en känd, säker plats som har flera sätt att kommunicera med Azure AD.

## <a name="ongoing-monitoring"></a>Pågående övervakning

Övervaka den [Azure AD-inloggningen och granska loggar](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) för alla inloggningar och granska aktivitet från nödfall-access-konton. Normalt kan dessa konton bör inte logga in och bör inte göra ändringar, så att använda troligen kommer att vara avvikande och kräva säkerhetsundersökning.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Kontokontroll verifiering måste ske med jämna mellanrum

Om du vill verifiera kontot, gör du följande minimikrav:
- Efter 90 dagar.
- När det har skett en ändring i IT-personal, till exempel en ändring i jobbet, sig eller en ny anställa.
- När Azure AD-prenumerationer i organisationen har ändrats.

För att utbilda personalen kan använda för åtkomst vid akutfall och gör följande:

* Kontrollera att säkerhetsövervakning personal är medveten om att konto-check-aktivitet pågår.
* Verifiera att cloud-användarkonton kan logga in och aktivera sina roller och att användare som kan behöva utföra dessa steg vid nödfall är tränade på processen.
* Se till att de inte har registrerat Multi-Factor Authentication eller lösenordsåterställning via självbetjäning (SSPR) till alla enskilda användares enhet eller personlig information. 
* Om konton som har registrerats för Multifaktorautentisering till en enhet för användning under rollaktivering, se till att enheten är tillgänglig för alla administratörer som kan behöva använda vid nödfall. Kontrollera också att enheten är registrerad med minst två mekanismer som inte delar ett gemensamt läge för fel. Exempelvis kan kan enheten kommunicera med internet via både en anläggning trådlöst nätverk och en cell-providernätverket.
* Uppdatera autentiseringsuppgifterna för kontot.

## <a name="next-steps"></a>Nästa steg
- [Lägga till en molnbaserad användare](../fundamentals/add-users-azure-active-directory.md) och [tilldela den nya användaren till rollen som global administratör](../fundamentals/active-directory-users-assign-role-azure-portal.md).
- [Registrera dig för Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md), om du inte har registrerat dig redan.
- [Kräv Azure Multi-Factor Authentication för enskilda användare som utsetts till administratörer](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Konfigurera ytterligare skydd för globala administratörer i Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), om du använder Office 365.
- [Utför en åtkomstgranskning av globala administratörer](../privileged-identity-management/pim-how-to-start-security-review.md) och [övergång befintliga globala administratörer av mer specifika administratörsroller](directory-assign-admin-roles.md).


