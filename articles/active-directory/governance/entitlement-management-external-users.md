---
title: Styra åtkomsten för externa användare i Azure AD-rättighetshantering - Azure Active Directory
description: Lär dig mer om de inställningar du kan ange för att styra åtkomsten för externa användare i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128687"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Styra åtkomsten för externa användare i Azure AD-rättighetshantering

Azure AD-berättigandehantering använder [Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) för att samarbeta med personer utanför organisationen i en annan katalog. Med Azure AD B2B autentiserar externa användare till sin arbetskatalog, men har en representation i katalogen. Representationen i katalogen gör det möjligt för användaren att tilldelas åtkomst till dina resurser.

I den här artikeln beskrivs de inställningar du kan ange för att styra åtkomsten för externa användare.

## <a name="how-entitlement-management-can-help"></a>Hur rättighetshantering kan hjälpa

När du använder azure AD B2B-inbjudningsupplevelsen måste du redan känna till e-postadresserna till de externa gästanvändare som du vill ta med i resurskatalogen och arbeta med. [Azure AD B2B](../b2b/what-is-b2b.md) Detta fungerar bra när du arbetar på ett mindre eller kortsiktigt projekt och du redan känner alla deltagare, men det här är svårare att hantera om du har massor av användare som du vill arbeta med eller om deltagarna förändras med tiden.  Du kanske till exempel arbetar med en annan organisation och har en kontaktpunkt med den organisationen, men med tiden behöver ytterligare användare från den organisationen också åtkomst.

Med rättighetshantering kan du definiera en princip som tillåter användare från organisationer som du anger att självbegärana ett åtkomstpaket. Du kan ange om godkännande krävs och ett utgångsdatum för åtkomsten. Om godkännande krävs kan du också bjuda in en eller flera användare från den externa organisationen till din katalog och ange dem som godkännare - eftersom de sannolikt vet vilka externa användare från organisationen som behöver åtkomst. När du har konfigurerat åtkomstpaketet kan du skicka åtkomstpaketets länk till din kontaktperson (sponsor) i den externa organisationen. Kontakten kan dela med andra användare i den externa organisationen och de kan använda den här länken för att begära åtkomstpaketet. Användare från den organisationen som redan har bjudits in till din katalog kan också använda den länken.

När en begäran godkänns kommer rättighetshantering att ge användaren nödvändig åtkomst, vilket kan innefatta att bjuda in användaren om de inte redan finns i katalogen. Azure AD skapar automatiskt ett B2B-gästkonto för dem. Observera att en administratör tidigare kan ha begränsat vilka organisationer som är tillåtna för samarbete, genom att ange en [B2B-lista](../b2b/allow-deny-list.md) för att tillåta eller blockera inbjudningar till andra organisationer.  Om användaren inte tillåts av listan tillåt eller blockera, kommer de inte att bjudas in.

Eftersom du inte vill att den externa användarens åtkomst ska vara för evigt anger du ett utgångsdatum i principen, till exempel 180 dagar. Efter 180 dagar, om deras åtkomst inte utökas, kommer rättighetshantering att ta bort all åtkomst som är associerad med det åtkomstpaketet. Om användaren som har bjudits in via rättighetshantering inte har några andra åtkomstpakettilldelningar, blockeras gästkontot från att logga in i 30 dagar och tas sedan bort när de förlorar sin senaste tilldelning. Detta förhindrar spridning av onödiga konton. Som beskrivs i följande avsnitt kan dessa inställningar konfigureras.

## <a name="how-access-works-for-external-users"></a>Så här fungerar åtkomst för externa användare

Följande diagram och steg ger en översikt över hur externa användare beviljas åtkomst till ett åtkomstpaket.

![Diagram som visar livscykeln för externa användare](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Du [lägger till en ansluten organisation](entitlement-management-organization.md) för Azure AD-katalogen eller domänen som du vill samarbeta med.

1. Du skapar ett åtkomstpaket i katalogen som innehåller en princip [för användare som inte finns i katalogen](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Du skickar en [My Access-portallänk](entitlement-management-access-package-settings.md) till din kontakt på den externa organisationen som de kan dela med sina användare för att begära åtkomstpaketet.

1. En extern användare (**Requestor A** i det här exemplet) använder länken Min Åtkomstportal för att [begära åtkomst](entitlement-management-request-access.md) till åtkomstpaketet. Hur användaren loggar in beror på autentiseringstypen för katalogen eller domänen som definierats i den anslutna organisationen.

1. En godkännare [godkänner begäran](entitlement-management-request-approve.md) (eller begäran godkänns automatiskt).

1. Begäran går in i [leveranstillståndet](entitlement-management-process.md).

1. Med hjälp av B2B-inb-inbjudningsprocessen skapas ett gästanvändarkonto i katalogen **(Requestor A (Gäst)** i det här exemplet). Om en [tillåt-lista eller en listan med nekad behörighet](../b2b/allow-deny-list.md) har definierats tillämpas listinställningen.

1. Gästanvändaren tilldelas åtkomst till alla resurser i åtkomstpaketet. Det kan ta lite tid innan ändringar görs i Azure AD och andra Microsoft Online-tjänster eller anslutna SaaS-program. Mer information finns i [När ändringar tillämpas](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Den externa användaren får ett e-postmeddelande som anger att deras åtkomst [har levererats](entitlement-management-process.md).

1. För att komma åt resurserna kan den externa användaren antingen klicka på länken i e-postmeddelandet eller försöka komma åt någon av katalogresurserna direkt för att slutföra inbjudningsprocessen.

1. Beroende på principinställningarna upphör åtkomstpakettilldelningen för den externa användaren att gälla allteftersom tiden går ut och den externa användarens åtkomst tas bort.

1. Beroende på livscykeln för externa användarinställningar blockeras den externa användaren från att logga in när den externa användaren inte längre har några åtkomstpakettilldelningar och gästanvändarkontot tas bort från katalogen.

## <a name="settings-for-external-users"></a>Inställningar för externa användare

För att säkerställa att personer utanför organisationen kan begära åtkomstpaket och få åtkomst till resurserna i dessa åtkomstpaket finns det vissa inställningar som du bör kontrollera är korrekt konfigurerade.

### <a name="enable-catalog-for-external-users"></a>Aktivera katalog för externa användare

- När du skapar en [ny katalog](entitlement-management-catalog-create.md)är det som standard möjligt för externa användare att begära åtkomstpaket i katalogen. Kontrollera att **Aktiverad för externa användare** är inställd på **Ja**.

    ![Redigera kataloginställningar](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Konfigurera inställningarna för externt samarbete i Azure AD B2B

- Att låta andra gäster bjuda in andra gäster till din katalog innebär att gäst inbjudningar kan ske utanför rätt förvaltning. Vi rekommenderar att du ställer **in gäster kan bjuda in** till **Nej** för att endast tillåta korrekt styrda inbjudningar.
- Om du använder listan B2B-tillåt måste du se till att alla domäner som du vill samarbeta med med hjälp av rättighetshantering läggs till i listan. Om du använder listan B2B-nekande måste du också se till att alla domäner som du vill samarbeta med inte läggs till i listan.
- Om du skapar en princip för rättighetshantering för **alla användare** (alla anslutna organisationer + nya externa användare) har alla B2B-inställningar för tillåta eller neka list som du har företräde. Se därför till att inkludera de domäner som du tänker inkludera i den här principen i listan över tillåt om du använder en och utesluta dem från listan neka om du använder en listan över neka.
- Om du vill skapa en princip för rättighetshantering som innehåller **Alla användare** (Alla anslutna organisationer + alla nya externa användare) måste du först aktivera e-postautentisering med engångslösenord för katalogen. Mer information finns i [E-postautentisering (förhandsgranskning) via e-post](../b2b/one-time-passcode.md#opting-in-to-the-preview).
- Mer information om azure AD B2B-inställningar för externt samarbete finns i [Aktivera B2B externt samarbete och hantera vem som kan bjuda in gäster](../b2b/delegate-invitations.md).

    ![Inställningar för externt samarbete i Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Granska principerna för villkorlig åtkomst

- Se till att utesluta gäster från alla principer för villkorlig åtkomst som nya gästanvändare inte kan uppfylla eftersom det kommer att hindra dem från att logga in på din katalog. Gäster har till exempel sannolikt inte en registrerad enhet, inte befinner sig på en känd plats och vill inte registrera om för MFA (Multi factor Authentication), så om du lägger till dessa krav i en policy för villkorlig åtkomst blockeras gäster från att använda berättigandet Management. Mer information finns [i Vad är villkoren i Azure Active Directory Villkorlig åtkomst?](../conditional-access/concept-conditional-access-conditions.md).

    ![Azure AD-principprincipen utesluter inställningar](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Granska dina externa delningsinställningar för SharePoint Online

- Om du vill inkludera SharePoint Online-webbplatser i dina åtkomstpaket för externa användare kontrollerar du att inställningen för extern delning på organisationsnivå är inställd på **Vem som helst** (användare behöver inte logga in) eller Nya och befintliga **gäster** (gäster måste logga in eller ange en verifieringskod). Mer information finns i [Aktivera eller inaktivera extern delning](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Om du vill begränsa extern delning utanför rättighetshanteringen kan du ställa in inställningen för extern delning till **befintliga gäster**. Då kan endast nya användare som bjuds in via rättighetshantering få tillgång till dessa webbplatser. Mer information finns i [Aktivera eller inaktivera extern delning](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Kontrollera att inställningarna på webbplatsnivå aktiverar gäståtkomst (samma alternativval som tidigare i listan). Mer information finns i [Aktivera eller inaktivera extern delning för en webbplats](https://docs.microsoft.com/sharepoint/change-external-sharing-site).

### <a name="review-your-office-365-group-sharing-settings"></a>Granska inställningarna för delning av Office 365-grupp

- Om du vill inkludera Office 365-grupper i dina åtkomstpaket för externa användare kontrollerar du att **Låta användare lägga till nya gäster i organisationen** är inställt på **På** för att ge gäståtkomst. Mer information finns i [Hantera gäståtkomst till Office 365-grupper](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access).

- Om du vill att externa användare ska kunna komma åt SharePoint Online-webbplatsen och resurser som är kopplade till en Office 365-grupp kontrollerar du att du aktiverar extern SharePoint Online-delning. Mer information finns i [Aktivera eller inaktivera extern delning](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Information om hur du anger gästprincipen för Office 365-grupper på katalognivå i PowerShell finns i [Exempel: Konfigurera gästprincip för grupper på katalognivå](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Granska inställningarna för Teams-delning

- Om du vill inkludera Teams i dina åtkomstpaket för externa användare kontrollerar du att **Tillåt gäståtkomst i Microsoft Teams** är inställt på **På** för att ge gäståtkomst. Mer information finns [i Konfigurera gäståtkomst i administrationscentret för Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Hantera livscykeln för externa användare

Du kan välja vad som händer när en extern användare, som har bjudits in till din katalog via en begäran om åtkomstpaket som godkänns, inte längre har några åtkomstpakettilldelningar. Detta kan inträffa om användaren avstår från alla sina åtkomstpakettilldelningar eller om deras senaste åtkomstpakettilldelning upphör att gälla. När en extern användare inte längre har några åtkomstpakettilldelningar blockeras de som standard från att logga in på katalogen. Efter 30 dagar tas deras gästanvändarkonto bort från din katalog.

**Viktig roll:** Global administratör eller användaradministratör

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Inställningar**i avsnittet **Rättighetshantering** i den vänstra menyn .

1. Klicka på **Redigera**.

    ![Inställningar för att hantera livscykeln för externa användare](./media/entitlement-management-external-users/settings-external-users.png)

1. I avsnittet **Hantera livscykeln** för externa användare väljer du de olika inställningarna för externa användare.

1. När en extern användare förlorar sin senaste tilldelning till alla åtkomstpaket anger du att **blockera extern användare från att logga in på den här katalogen** till **Ja**.

    > [!NOTE]
    > Om en användare blockeras från att logga in på den här katalogen kan användaren inte begära åtkomstpaketet igen eller begära ytterligare åtkomst i den här katalogen. Konfigurera inte blockering av dem från att logga in om de senare behöver begära åtkomst till andra åtkomstpaket.

1. När en extern användare förlorar sin senaste tilldelning till alla åtkomstpaket anger du **Ta bort extern användare** till **Ja**om du vill ta bort deras gästanvändarkonto i den här katalogen .

    > [!NOTE]
    > Rättighetshantering tar bara bort konton som bjudits in via rättighetshantering. Observera också att en användare kommer att blockeras från att logga in och tas bort från den här katalogen även om användaren har lagts till resurser i den här katalogen som inte har åtkomst till pakettilldelningar. Om gästen var närvarande i den här katalogen innan han fick åtkomstpakettilldelningar, kommer de att finnas kvar. Men om gästen har bjudits in via en tilldelning av åtkomstpaket och efter att ha blivit inbjuden också tilldelats en OneDrive för företag- eller SharePoint Online-webbplats, tas de fortfarande bort.

1. Om du vill ta bort gästanvändarkontot i den här katalogen kan du ange antalet dagar innan det tas bort. Om du vill ta bort gästanvändarkontot så fort de förlorar sin senaste tilldelning till alla åtkomstpaket anger du **Antal dagar innan du tar bort externa användare från den här katalogen** till **0**.

1. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

- [Lägga till en ansluten organisation](entitlement-management-organization.md)
- [För användare som inte finns i katalogen](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Felsöka](entitlement-management-troubleshoot.md)
