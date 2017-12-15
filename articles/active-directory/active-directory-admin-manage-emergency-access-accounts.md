---
title: "Hantera nödåtkomst administrativa konton i Azure AD | Microsoft Docs"
description: "Beskriver hur du använder nödåtkomst konton för att hjälpa organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö."
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
ms.openlocfilehash: 75ea8e445c890e7af5ab14f4fc4c53cfb1af4568
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="managing-emergency-access-administrative-accounts-in-azure-ad"></a>Hantera nödåtkomst administrativa konton i Azure AD 

För de flesta dagliga aktiviteter behövs inte global administratörsroll rättigheter.  Användare ska inte tilldelas permanent till rollen som användaren kan oavsiktligt utföra en uppgift med högre behörighet än vad som krävs. När en användare behöver att fungera som en global administratör, bör de aktivera rolltilldelning med hjälp av Azure AD PIM, antingen på sitt eget konto eller ett annat administrativt konto.

Förutom att användare tar på administrativ behörighet som krävs för sig själva, måste kunder du förbereda så att de inte står i en situation där de kan oavsiktligt utestängas från administration av sina Azure AD-klient på grund av deras oförmåga att logga i eller aktivera en befintlig enskilda användares konto som administratör.  De kan minimera effekten av misstag bristande administrativ åtkomst genom att lagra i klientorganisationen två eller flera *konton för nödåtkomst*.

Konton för nödåtkomst hjälpa organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö. Dessa konton är mycket Privilegierade och har tilldelats inte till specifika personer. Konton för nödåtkomst är begränsade till nödfall 'nödkonto' scenarier där normala administrativa konton inte kan användas.  Organisationer måste kontrollera i syfte att kontrollera och minska användningen av nödfall konton till endast den tid som det är nödvändigt.

Scenarier där en organisation kan behöva göra användning av ett nödåtkomst konto:

 - Användarkonton är federerad och federation är inte tillgänglig på grund av ett nätverksavbrott eller ett avbrott i identitetsleverantören.  Till exempel om identitet providern värden i kundens miljö har avbrutits kanske sedan en användare inte kan logga in när Azure AD omdirigeras till deras identitetsleverantör. 
 - Administratörerna som har registrerats via MFA och alla enskilda enheter är inte tillgängliga.  Det är möjligt att användare inte kan fullständiga multifaktorautentisering att aktivera en roll om till exempel nätverksavbrott cell kan hindra användare från att besvara samtal eller ta emot meddelanden och som var den enda funktionen som de registrerats för enheten. 
 - Den senaste personen som hade global administrativ åtkomst lämnar organisationen.  Azure AD förhindrar senaste globala administratörskonto tas bort, men det hindrar inte kontot tas bort eller inaktiveras lokalt, vilket leder till en situation där organisationen är det går inte att återställa det kontot.

## <a name="initial-configuration"></a>Inledande konfiguration

Skapa två eller flera konton för nödåtkomst.  Endast molnbaserad konton med hjälp av bör vara den *. onmicrosoft.com-domän som inte är federerat eller synkroniseras från en lokal miljö.  

De får inte vara kopplad till en enskild användare i organisationen.  Organisationer behöver du autentiseringsuppgifterna för dessa konton stannar säker och kända endast till personer som har behörighet att använda dem. 

Obs: Normalt kontolösenord för ett konto för nödåtkomst är indelade i två eller tre delar, skrivs på olika delar av dokumentet och lagras i säker, brandsäkert kassaskåp som finns i säker olika platser. Se till att dina konton för nödåtkomst inte är kopplad till alla medarbetare angivna mobiltelefoner, maskinvara tokens som överförs med enskilda medarbetare eller andra autentiseringsuppgifter för specifika medarbetare, om den medarbetaren kan inte nås vid tidpunkten autentiseringsuppgifter krävs. 

### <a name="initial-configuration-with-permanent-assignments"></a>Inledande konfiguration med permanenta tilldelningar

Ett alternativ är att tilldela dessa användare som permanenta medlemmar i rollen Global administratör.  Det är lämpligt för organisationer som inte har Azure AD Premium P2-prenumerationer.

Azure AD rekommenderar att du kräver Multi-Factor authentication (MFA) för alla enskilda användare, administratörer och andra användare som skulle ha betydande påverkan om sitt konto komprometteras (t.ex. ekonomiska polis). Detta minskar risken för angrepp på grund av en komprometterad lösenord. Men om organisationen inte har delade enheter kanske sedan MFA inte möjligt för dessa konton för nödåtkomst.  Om du konfigurerar en princip för villkorlig åtkomst att kräva [MFA-registrering för varje admin](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) för Azure AD och andra anslutet SaaS-appar kan du behöva konfigurera undantag för principen för att undanta nödåtkomst konton från den här kravet.

### <a name="initial-configuration-with-approvals"></a>Inledande konfiguration med godkännanden

Ett annat alternativ är att konfigurera de användare som är berättigade och godkännare för att aktivera rollen Global administratör.  Detta kräver att organisationen får Azure AD Premium P2-prenumerationer, samt ett MFA-alternativ som är lämpliga för delade bland flera personer och nätverksmiljön.  Det beror på att aktivering av rollen Global administratör kräver att användaren har tidigare har utfört MFA.  Mer information finns i [kräva MFA i Azure AD Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Med hjälp av MFA som är associerade med personliga enheter rekommenderas inte för konton för nödåtkomst, eftersom i en verklig nödfall en person som måste ha inte kanske åtkomst till en MFA-registrerade enheter som håller den personliga enheten.  Överväg också att hotbild är om på grund av oförutsedda omständigheter mobiltelefon eller andra nätverk är tillgängliga under en naturkatastrof nödfall.  Det är därför viktigt att se till att alla registrerade enheter hålls i en känd, säker plats som har flera sätt att kommunicera med Azure AD.

## <a name="ongoing-monitoring"></a>Kontinuerlig övervakning

Övervaka den [Azure AD logga in och granskningsloggar](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins) för alla inloggningar och granska aktivitet från konton för nödåtkomst.  Normalt bör inte logga in dessa konton och bör inte göra ändringar, så att användning av dem kommer troligen att vara avvikande och kräver säkerhet undersökning.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Kontot du verifiering måste ske med jämna mellanrum

Utför följande steg på minst:
 - Efter 90 dagar
 - När det har en ändring i IT-personal – ett jobb ändra, avvikelse eller nya uthyrning
 - När Azure AD-prenumerationer i organisationen har ändrats

För att säkerställa tränas personal i hur du använder konton för nödåtkomst:

1.  Kontrollera övervakning säkerhetspersonal är medveten om att aktiviteten konto kontroll pågår.
2.  Kontrollera att molnet användarkonton kan logga in och aktivera sina roller och att användare som kan behöva utföra stegen vid en nödsituation tränas på processen.
3.  Se till att de inte har registrerats för MFA eller SSPR till en enskild användare enhet eller personlig information.  
4. Om konton som har registrerats för MFA till en enhet som ska användas under rollaktivering, se till att enheten är tillgänglig för alla administratörer som kan behöva använda en nödsituation.  Kontrollera också att enheten är registrerad med minst två mekanismer som inte delar en gemensam felläget (t.ex. enheten kan kommunicera till Internet både via en anläggning trådlöst nätverk samt via ett nätverk för cellen provider).
5.  Uppdatera autentiseringsuppgifterna.

## <a name="next-steps"></a>Nästa steg
- [Lägga till en molnbaserad användare](add-users-azure-active-directory.md) och [tilldelar den nya användaren rollen global administratör](active-directory-users-assign-role-azure-portal.md)
- [Registrera dig för Azure Active Directory Premium](active-directory-get-started-premium.md), om du inte redan gjort
- [Kräv Azure MFA för enskilda användare som utsetts till administratörer](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)
- [Konfigurera ytterligare skydd för globala administratörer i Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), om du använder Office 365
- [Utföra en åtkomst-granskning av globala administratörer](active-directory-privileged-identity-management-how-to-start-security-review.md) och [övergång befintliga globala administratörer av mer specifika administratörsroller](active-directory-assign-admin-roles-azure-portal.md)

