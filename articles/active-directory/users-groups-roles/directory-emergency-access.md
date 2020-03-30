---
title: Hantera administratörskonton för hantering av fjärråtkomst – Azure AD | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder konton för nödåtkomst för att förhindra att du oavsiktligt blir utelåst från din Azure Active Directory-organisation (Azure AD).
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ab7e0603f63fb395832b0da887916dc032c3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028130"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Hantera konton för nödåtkomst i Azure AD

Det är viktigt att du förhindrar att bli utelåst från din Azure Active Directory-organisation (Azure AD) eftersom du inte kan logga in eller aktivera en annan användares konto som administratör. Du kan minska effekten av oavsiktlig brist på administrativ åtkomst genom att skapa två eller flera *konton för nödåtkomst* i organisationen.

Konton för nödåtkomst är mycket privilegierade och de tilldelas inte specifika personer. Konton för nödåtkomst är begränsade till kris- eller "krossa glas"-scenarier där normala administrativa konton inte kan användas. Vi rekommenderar att du har som mål att begränsa användningen av nödkonto till endast de tider då det är absolut nödvändigt.

Den här artikeln innehåller riktlinjer för hantering av konton för nödåtkomst i Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Varför använda ett nödåtkomstkonto

En organisation kan behöva använda ett nödåtkomstkonto i följande situationer:

- Användarkontona är federerade och federationen är för närvarande inte tillgänglig på grund av en cell-nätverksbrytning eller ett identitetsprovideravbrott. Om till exempel identitetsprovidervärden i din miljö har gått ner kanske användare inte kan logga in när Azure AD omdirigerar till sin identitetsprovider.
- Administratörerna registreras via Azure Multi-Factor Authentication och alla deras enskilda enheter är inte tillgängliga eller tjänsten är inte tillgänglig. Användare kanske inte kan slutföra multifaktorautentisering för att aktivera en roll. Ett avbrott i mobilnätverket hindrar dem till exempel från att svara på telefonsamtal eller ta emot textmeddelanden, de enda två autentiseringsmekanismer som de har registrerat för sin enhet.
- Personen med den senaste globala administratörsåtkomsten har lämnat organisationen. Azure AD förhindrar att det senaste globala administratörskontot tas bort, men det hindrar inte att kontot tas bort eller inaktiveras lokalt. Båda situationerna kan göra att organisationen inte kan återställa kontot.
- Oförutsedda omständigheter som en naturkatastrof nödsituation, under vilken en mobiltelefon eller andra nät kan vara otillgänglig. 

## <a name="create-emergency-access-accounts"></a>Skapa konton för nödåtkomst

Skapa två eller flera konton för nödåtkomst. Dessa konton bör vara molnkonton \*som använder domänen .onmicrosoft.com och som inte är federerade eller synkroniserade från en lokal miljö.

När du konfigurerar dessa konton måste följande krav vara uppfyllda:

- Konton för nödåtkomst bör inte associeras med någon enskild användare i organisationen. Kontrollera att dina konton inte är anslutna till några medarbetarbaserade mobiltelefoner, maskinvarutoken som reser med enskilda anställda eller andra medarbetarespecifika autentiseringsuppgifter. Den här försiktighetsåtgärden omfattar fall där en enskild medarbetare inte kan nås när autentiseringsuppgifterna behövs. Det är viktigt att se till att alla registrerade enheter hålls på en känd, säker plats som har flera sätt att kommunicera med Azure AD.
- Autentiseringsmekanismen som används för ett nödåtkomstkonto bör skilja sig från den som används av dina andra administrativa konton, inklusive andra konton för nödåtkomst.  Om din vanliga administratörsinbildning till exempel är via lokala MFA, skulle Azure MFA vara en annan mekanism.  Men om Azure MFA är din primära del av autentisering för dina administrativa konton, sedan överväga en annan metod för dessa, till exempel använda villkorlig åtkomst med en tredje part MFA-leverantör.
- Enheten eller autentiseringsuppgifterna får inte upphöra att gälla eller omfattas av automatisk rensning på grund av bristande användning.  
- Du bör göra den globala administratörsrolltilldelningen permanent för dina konton för nödåtkomst. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Exkludera minst ett konto från telefonbaserad multifaktorautentisering

För att minska risken för en attack till följd av ett komprometterat lösenord rekommenderar Azure AD att du behöver multifaktorautentisering för alla enskilda användare. Denna grupp omfattar administratörer och alla andra (till exempel finansiella tjänstemän) vars komprometterade konto skulle ha en betydande inverkan.

Minst ett av dina konton för nödåtkomst bör dock inte ha samma multifaktorautentiseringsmekanism som dina andra konton som inte är i nödsituationer. Detta inkluderar lösningar för multifaktorautentisering från tredje part. Om du har en princip för villkorlig åtkomst som kräver [multifaktorautentisering för varje administratör](../authentication/howto-mfa-userstates.md) för Azure AD och andra anslutna program som en tjänst (SaaS) appar, bör du utesluta konton för nödåtkomst från det här kravet och konfigurera en annan mekanism i stället. Dessutom bör du se till att kontona inte har en multifaktorautentiseringsprincip per användare.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Exkludera minst ett konto från principer för villkorlig åtkomst

Under en nödsituation vill du inte att en princip ska blockera åtkomsten för att åtgärda ett problem. Minst ett konton för nödåtkomst bör uteslutas från alla principer för villkorlig åtkomst. Om du har aktiverat en [originalprincip](../conditional-access/baseline-protection.md)bör du utesluta dina konton för nödåtkomst.

## <a name="federation-guidance"></a>Federationvägledning

Ett ytterligare alternativ för organisationer som använder AD Domain Services och ADFS eller liknande identitetsprovider för att federera till Azure AD är att konfigurera ett nödåtkomstkonto vars MFA-anspråk kan tillhandahållas av den identitetsprovidern.  Kontot för nödåtkomst kan till exempel backas upp av ett certifikat och nyckelpar, till exempel ett som lagras på ett smartkort.  När den användaren autentiseras till AD kan ADFS tillhandahålla ett anspråk till Azure AD som anger att användaren har uppfyllt MFA-kraven.  Även med den här metoden måste organisationer fortfarande ha molnbaserade konton för nödåtkomst om federationen inte kan upprättas. 

## <a name="store-account-credentials-safely"></a>Lagra kontouppgifter på ett säkert sätt

Organisationer måste se till att autentiseringsuppgifterna för konton för nödåtkomst skyddas och endast är kända för personer som har behörighet att använda dem. Vissa kunder använder ett smartkort och andra använder lösenord. Ett lösenord för ett nödåtkomstkonto är vanligtvis uppdelat i två eller tre delar, skrivet på separata papperslappar, och lagras i säkra, brandsäkra kassaskåp som finns på säkra, separata platser.

Om du använder lösenord kontrollerar du att kontona har starka lösenord som inte upphör att gälla för lösenordet. Helst bör lösenorden vara minst 16 tecken långa och slumpmässigt genererade.

## <a name="monitor-sign-in-and-audit-logs"></a>Övervaka inloggnings- och granskningsloggar

Organisationer bör övervaka inloggnings- och granskningsloggaktivitet från nödsituationskontona och utlösa meddelanden till andra administratörer. När du övervakar aktiviteten på konton med krossat glas kan du verifiera att dessa konton endast används för testning eller faktiska nödsituationer. Du kan använda Azure Log Analytics för att övervaka inloggningsloggarna och utlösa e-post- och SMS-aviseringar till administratörer när glaskonton loggar in.

### <a name="prerequisites"></a>Krav

1. [Skicka Azure AD-inloggningsloggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) till Azure Monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Hämta objekt-ID:er för konton med brytglas

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto tilldelat till rollen Användaradministratör.
1. Välj Azure Active**Directory-användare** **Azure Active Directory** > .
1. Sök efter glasbrytkontot och välj användarens namn.
1. Kopiera och spara attributet Object ID så att du kan använda det senare.
1. Upprepa tidigare steg för andra break-glass konto.

### <a name="create-an-alert-rule"></a>Skapa en varningsregel

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto tilldelat till rollen Övervakningsdeltagare i Azure Monitor.
1. Välj **Alla tjänster**", skriv "logganalys" i Sök och välj sedan Log **Analytics-arbetsytor**.
1. Välj en arbetsyta.
1. Välj Aviseringar ny **varningsregel** > **New alert rule**på arbetsytan .
    1. Kontrollera att prenumerationen är den prenumeration som du vill associera aviseringsregeln med under **Resurs.**
    1. Under **Villkor**väljer du **Lägg till**.
    1. Välj **Anpassad loggsökning** under **Signalnamn**.
    1. Under **Sökfråga**anger du följande fråga genom att infoga objekt-ID:erna för de två kontona för brytglas.
        > [!NOTE]
        > För varje ytterligare break glass-konto som du vill inkludera lägger du till ett annat "eller UserId == "ObjectGuid" i frågan.

        ![Lägga till objekt-ID:erna för kontona för brytglas i en varningsregel](./media/directory-emergency-access/query-image1.png)

    1. Under **Varningslogik**anger du följande:

        - Baserat på: Antal resultat
        - Operatör: Större än
        - Tröskelvärde: 0

    1. Under **Utvärderad baserat på**väljer du Period **(i minuter)** för hur länge du vill att frågan ska köras och **frekvensen (i minuter)** för hur ofta du vill att frågan ska köras. Frekvensen ska vara mindre än eller lika med perioden.

        ![varningslogik](./media/directory-emergency-access/alert-image2.png)

    1. Välj **Done** (Klar). Du kan nu visa den beräknade månadskostnaden för den här aviseringen.
1. Välj en åtgärdsgrupp med användare som ska meddelas av aviseringen. Om du vill skapa en läser du [Skapa en åtgärdsgrupp](#create-an-action-group).
1. Om du vill anpassa e-postmeddelandet som skickas till medlemmarna i åtgärdsgruppen väljer du åtgärder under **Anpassa åtgärder**.
1. Under **Varningsinformation**anger du namnet på varningsregeln och lägger till en valfri beskrivning.
1. Ange **allvarlighetsgraden** för händelsen. Vi rekommenderar att du ställer in den på **Critical (Sev 0)**.
1. Under **Aktivera regel när den skapas**lämnar du den inställd som **ja**.
1. Om du vill inaktivera aviseringar en stund markerar du kryssrutan **Undertryck varningar** och anger väntetiden innan du varnar igen och väljer sedan **Spara**.
1. Klicka på **Skapa aviseringsregel**.

### <a name="create-an-action-group"></a>Skapa en åtgärdsgrupp

1. Välj **Skapa en åtgärdsgrupp**.

    ![skapa en åtgärdsgrupp för meddelandeåtgärder](./media/directory-emergency-access/action-group-image3.png)

1. Ange åtgärdsgruppsnamnet och ett kort namn.
1. Verifiera prenumerations- och resursgruppen.
1. Under åtgärdstyp väljer du **E-post/SMS/Push/Voice**.
1. Ange ett åtgärdsnamn som **Meddela global administratör**.
1. Välj **åtgärdstyp** som **e-post/SMS/Push/Voice**.
1. Välj **Redigera information** om du vill välja de meddelandemetoder som du vill konfigurera och ange den erforderliga kontaktinformationen och välj sedan **Ok** för att spara informationen.
1. Lägg till ytterligare åtgärder som du vill utlösa.
1. Välj **OK**.

## <a name="validate-accounts-regularly"></a>Validera konton regelbundet

När du utbildar anställda att använda konton för nödåtkomst och validerar konton för nödåtkomst, gör du minst följande steg med jämna mellanrum:

- Se till att säkerhetsövervakningspersonalen är medveten om att kontokontrollaktiviteten pågår.
- Se till att nödkrossglasprocessen för att använda dessa konton är dokumenterad och aktuell.
- Se till att administratörer och säkerhetstjänstemän som kan behöva utföra dessa steg under en nödsituation tränas på processen.
- Uppdatera kontoautentiseringsuppgifterna, särskilt eventuella lösenord, för dina konton för nödåtkomst och verifiera sedan att konton för nödåtkomst kan logga in och utföra administrativa uppgifter.
- Kontrollera att användarna inte har registrerat Multifaktorautentisering eller självbetjäningslösenordsåterställning (SSPR) till någon enskild användares enhet eller personliga uppgifter. 
- Om kontona är registrerade för multifaktorautentisering på en enhet, för användning under inloggning eller rollaktivering, se till att enheten är tillgänglig för alla administratörer som kan behöva använda den under en nödsituation. Kontrollera också att enheten kan kommunicera via minst två nätverkssökvägar som inte delar ett gemensamt felläge. Enheten kan till exempel kommunicera till internet via både en anläggnings trådlösa nätverk och ett mobilleverantörsnätverk.

Dessa steg bör utföras med jämna mellanrum och för viktiga ändringar:

- Minst var 90:e dag
- När det nyligen har skett en förändring av IT-personal, till exempel ett jobbbyte, en avresa eller en nyanställd
- När Azure AD-prenumerationerna i organisationen har ändrats

## <a name="next-steps"></a>Nästa steg

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](directory-admin-roles-secure.md)
- [Lägga till användare som använder Azure AD](../fundamentals/add-users-azure-active-directory.md) och tilldela den nya användaren till rollen Global [Administratör](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Registrera dig för Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), om du inte redan har registrerat dig
- [Så här kräver du tvåstegsverifiering för en användare](../authentication/howto-mfa-userstates.md)
- [Konfigurera ytterligare skydd för globala administratörer i Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts)om du använder Office 365
- [Starta en åtkomstgranskning av globala administratörer](../privileged-identity-management/pim-how-to-start-security-review.md) och [överföra befintliga globala administratörer till mer specifika administratörsroller](directory-assign-admin-roles.md)
