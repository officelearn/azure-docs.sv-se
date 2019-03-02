---
title: Konfigurera ditt faktureringskonto för en Microsoft-kundavtal – Azure | Microsoft Docs
description: Lär dig hur du ställer in ditt faktureringskonto för en Microsoft-kundavtal.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 0bb5b118b3d1bc7b89354bb23305eed15a96985d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249241"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Konfigurera ditt faktureringskonto för en Microsoft-kundavtal

Om din organisation har undertecknat ett avtal med Microsoft kunden att förnya ditt Enterprise Agreement-registrering, måste du konfigurera ditt faktureringskonto. Ditt nya faktureringskonto ger förbättrad fakturering och kostnaden funktioner via en ny smidig och enhetlig hanteringsmiljö:  

- Hantera din organisations Azure-tjänster och fakturering, inklusive fakturor, i Azure portal

- Organisera kostnader på fakturan utifrån dina behov för enkel spårning och kostnadsallokering

- Granska och analysera din månatliga, digitala faktura i sidan för Azure kostnadshantering + fakturering

När du ställer in faktureringskontot övergång att faktureringen för dina befintliga Azure-prenumerationer till det nya kontot. Hur du hanterar fakturering för dina prenumerationer i det nya kontot skiljer sig från hur du hanterar i ditt Enterprise Agreement-registrering. Vi rekommenderar att du gör följande innan du påbörjar installationen:

- **Förstå ändringar i hierarkin fakturering**
    - Du nya fakturering konto ordnas annorlunda än ditt Enterprise Agreement-registrering. Mer information finns i [förstå ändringar i hierarkin fakturering](#understand-changes-to-your-billing-hierarchy).
- **Förstå ändringar i din faktureringsadministratörer åtkomst**
    - Administratörer från din Enterprise Agreement-registrering får åtkomst till faktureringen Omfattningarna i det nya kontot. Mer information finns i [förstå ändringar i din faktureringsadministratörer åtkomst](#understand-changes-to-your-billing-administrators-access).
- **Granska Enterprise Agreement-funktioner som ersätts med det nya kontot**
    - Visa funktioner för Enterprise Agreement-registrering som ersätts med uppgraderade funktioner i det nya kontot. [Granska funktioner som har ersatts av det nya faktureringskontot](#review-features-replaced-by-the-new-billing-account).
- **Visa svar på vanliga frågor**
    - Visa ytterligare information för mer information om installationen. Gå till [ytterligare information](#additional-information).

## <a name="set-up-your-billing-account-in-the-azure-portal"></a>Konfigurera ditt faktureringskonto i Azure portal

Du behöver åtkomst till både det nya faktureringskontot och Enterprise Agreement-registrering för att slutföra installationen. Mer information finns i [åtkomst krävs för att slutföra konfigurationen av ditt faktureringskonto](#access-required-to-complete-the-setup).

1. Logga in på Azure-portalen med hjälp av länken i e-postmeddelandet som skickades till dig när du registrerade kundavtal Microsoft.

2. Om någon annan i organisationen har registrerat avtalet eller så har du inte e-postmeddelandet, logga in med följande länk. Ersätt **enrollmentNumber** med registreringsnummer i ditt Enterprise Agreement som har förnyats.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Välj **starta övergången** i det sista steget av installationen. När du har valt start övergången:

    ![Skärmbild som visar installationsguiden](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - En fakturering hierarki som motsvarar din Enterprise Agreement-hierarki har skapats i det nya faktureringskontot. Mer information finns i [förstå ändringar i hierarkin fakturering](#understand-changes-to-your-billing-hierarchy).
    - Administratörer från din Enterprise Agreement-registrering får åtkomst till det nya faktureringskontot så att de fortsätter att hantera faktureringen för din organisation.
    - Faktureringen för dina Azure-prenumerationer är överföras till det nya kontot. **Det inte någon inverkan på din Azure-tjänster under denna övergång. De kommer att köra utan något avbrott**.
    - Om du har Azure reservationer kan flyttas de till nya kontot med samma rabatt och termen. Din reservationsrabatten fortsätter att tillämpas under övergången.

4. Du kan övervaka statusen för övergången på den **övergång status** sidan.

   ![Skärmbild som visar status för övergång](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="access-required-to-complete-the-setup"></a>Åtkomst som krävs för att slutföra installationen

För att slutföra installationen måste behöver du följande åtkomstbehörighet:

- Ägare av fakturering profilen som skapades när Microsoft kundavtal var signerad. Läs mer om fakturering profiler i [förstå fakturering profiler](billing-mca-overview.md#understand-billing-profiles).

- Företagsadministratör i registreringen som förnyas.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Om du inte är en företagsadministratör i registreringen

Du kan begära Företagsadministratörer för registreringen att slutföra installationen av ditt faktureringskonto.

1. Logga in på Azure-portalen med hjälp av länken i e-postmeddelandet som skickades till dig när du registrerade kundavtal Microsoft.

2. Om någon annan i organisationen har registrerat avtalet eller så har du inte e-postmeddelandet, logga in med följande länk. Ersätt **enrollmentNumber** med registreringsnummer i ditt enterprise agreement som har förnyats.

   (https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber)

3. Välj Företagsadministratörer som du vill skicka din begäran.

   ![Skärmbild som visar bjuda in Företagsadministratörerna](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Välj **sändningsbegäranden**.

   Administratörer får ett e-postmeddelande med anvisningar för att slutföra installationen.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Om du inte är ägare av fakturering profilen

Användare i din organisation, vem som har signerat kundavtal Microsoft har lagts till som ägare på fakturering profilen. Be användaren att lägga till dig som ägare så att du kan slutföra installationen.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Förstå ändringar i din faktureringshierarki

Ditt nya faktureringskonto förenklar faktureringen för din organisation samtidigt som det ger dig förbättrad fakturering och kostnad hanteringsfunktioner. Diagrammet nedan förklaras hur fakturering är ordnade i det nya faktureringskontot.

![Bild av ea-mca-post-övergång-hierarki](./media/billing-mca-setup-account/mca-post-transition-hierarchy.svg)

1. Du kan använda faktureringskontot för att hantera faktureringen för ditt Microsoft-kundavtal. Läs mer om faktureringskonto i [förstå faktureringskonto](billing-mca-overview.md#understand-billing-account).
2. Du kan använda fakturering profilen för att hantera fakturering för din organisation, som ditt Enterprise Agreement-registrering. Företagsadministratörer bli ägare av fakturering profilen. Läs mer om fakturering profiler i [förstå fakturering profiler](billing-mca-overview.md#understand-billing-profiles).
3. Du kan använda en faktura-avsnittet för att organisera dina kostnader utifrån dina behov, liknar avdelningar i ditt Enterprise Agreement-registrering. Avdelning blir faktura avsnitt och avdelning administratörer bli ägare av avsnitten respektive faktura. Läs mer om fakturan avsnitt i [förstå fakturan avsnitt](billing-mca-overview.md#understand-invoice-sections).
4. De konton som har skapats i ditt Enterprise Agreement stöds inte i det nya faktureringskontot. Kontots prenumerationer som hör till avsnittet respektive faktura för sina fakultet. Kontoinnehavare kan skapa och hantera prenumerationer för sin faktura-avsnitt.

## <a name="understand-changes-to-your-billing-administrators-access"></a>Förstå ändringar i din faktureringsadministratörer åtkomst

Beroende på deras åtkomst får faktureringsadministratörer på din Enterprise Agreement-registrering åtkomst till faktureringen omfång på det nya kontot. Diagrammet nedan beskriver ändringen i access under övergången:

![Bild av ea-mca-post-övergång-åtkomst](./media/billing-mca-setup-account/mca-post-transition-access.png)

| Befintlig roll | Efter övergången roll  |
|---------|---------|
|Företagsadministratör (Skrivskyddad = Nej)     |-Ägare för fakturering profilen för registrering </br> – Ägare på alla faktura-avsnitt |
|Företagsadministratör | -Läsare för fakturering profilen för registrering </br> – Läsare på alla faktura-avsnitt |
|Avdelning administratör (Läs bara = Nej)    |– Ägare i avsnittet faktura skapas för deras respektive avdelning      |
|Avdelningsadministratör     | – Läsare i avsnittet faktura skapas för deras respektive avdelning      |
|Kontoägare     | -Skapare azure-prenumeration i avsnittet faktura skapas för deras respektive avdelning |

En Azure Active Directory-klient har valts för det nya faktureringskontot under signeringen kundavtal Microsoft. Om en klient inte finns för din organisation, skapas en ny klient. Klienten representerar organisationen i Azure Active Directory. Klientorganisations globala administratörer i din organisation använda klienten för att hantera åtkomst till program och data i din organisation.

Ditt nya konto har endast stöd för användare från den klient som valdes under signeringen kundavtal Microsoft. Om användare med administrativ behörighet i ditt Enterprise Agreement är en del av klienten, får de åtkomst till det nya faktureringskontot under övergången. Om de inte är en del av klienten, kan de inte åtkomst till det nya faktureringskontot såvida inte du bjuda in dem.

När du bjuder in användare läggs till klienten som gästanvändare och få åtkomst till faktureringskontot. Gäståtkomst måste aktiveras för klienten för att bjuda in användare. Mer information finns i [styra gäståtkomst i Azure Active Directory](https://docs.microsoft.com/en-us/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Kontakta globala administratörer för din klient för att aktivera dem om gäståtkomst är avstängd. <!-- Todo - How can they find their global administrator -->

## <a name="review-features-replaced-by-the-new-billing-account"></a>Granska funktioner som har ersatts av det nya faktureringskontot

Följande Företagsavtal funktioner ersätts med nya funktioner i faktureringskontot för en Microsoft-kundavtal.

### <a name="enterprise-agreement-accounts"></a>Enterprise Agreement-konton

De konton som har skapats i ditt Enterprise Agreement-registrering stöds inte i det nya faktureringskontot. Kontots prenumerationer hör till avsnittet faktura skapas för deras respektive avdelning. Kontoinnehavare kan blir skapare av Azure-prenumeration och skapa och hantera prenumerationer för sin faktura-avsnitt.

### <a name="notification-contacts"></a>Meddelande-kontakter

Meddelande kontakter skickas e-postmeddelanden om Azure Enterprise-avtal. De stöds inte i det nya faktureringskontot. E-postmeddelanden om Azure-krediter och fakturor skickas till användare som har åtkomst till faktureringen profiler i ditt faktureringskonto.

### <a name="spending-quotas"></a>Utgiftsgränser

Utgiftsgränsen kvoter som ställts in för avdelningar i ditt Enterprise Agreement-registrering har ersatts med budgetar i det nya faktureringskontot. En budget skapas för varje utgiftskvoten på avdelningar i din registrering. Läs mer på budgetar [skapa och hantera Azure budgetar](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Kostnadsställen

Det kostnadsställe som har ställts in på Azure-prenumerationer i ditt Enterprise Agreement-registrering överförs i det nya faktureringskontot. Dock stöds kostnadsställen för avdelningar och Enterprise Agreement-konton inte.

## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om hur du konfigurerar ditt faktureringskonto.

### <a name="no-service-downtime"></a>Inga driftstopp för tjänsten

Azure-tjänster i din prenumeration att köra utan avbrott. Vi bara övergång faktureringsrelation för dina Azure-prenumerationer. Det kommer inte att en påverkan på befintliga resurser, resursgrupper eller hanteringsgrupper.

### <a name="user-access-to-azure-resources"></a>Användaråtkomst till Azure-resurser

Åtkomst till Azure-resurser som har ställts in med Azure RBAC (rollbaserad åtkomstkontroll) påverkas inte under övergången.

### <a name="azure-reservations"></a>Azure-reservationer

Alla Azure-reservationer i ditt Enterprise Agreement-registrering har flyttats till ditt nya faktureringskonto. Under övergången, kommer inte det att ändringar av reservationen rabatterna som tillämpas på dina prenumerationer.

### <a name="azure-marketplace-products"></a>Azure Marketplace-produkter

Alla Azure Marketplace-produkter i ditt Enterprise agreement-registrering flyttas tillsammans med prenumerationerna. Det finns inte ändringar av tjänståtkomst till Marketplace-produkter under övergången.

### <a name="support-plan"></a>Supportplan

Supportförmåner överförs inte som en del av övergången. Köpa ett nytt supportavtal dig fördelar för Azure-prenumerationer i ditt nya faktureringskonto.

### <a name="past-charges-and-balance"></a>Senaste avgifter och saldo

Avgifter och krediter saldo innan övergång kan ses i ditt Enterprise Agreement-registrering via Azure portal. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>När inställningen för att slutföra?

Slutför installationen av ditt faktureringskonto innan ditt Enterprise Agreement-registrering upphör att gälla. Om din registrering upphör att gälla kommer tjänsterna i Azure-prenumerationerna fortfarande hålla på körs utan avbrott. Men debiteras du återförsäljarpriset för tjänsterna.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Ändringar av Enterprise Agreement-registrering efter installationen

Azure-prenumerationer som skapas för Enterprise Agreement-registrering efter övergången manuellt kan flyttas till det nya faktureringskontot. Mer information finns i [hämta faktureringsägarskapet av Azure-prenumerationer från andra användare](billing-mca-request-billing-ownership.md). Att flytta Azure-reservationer som köps efter övergången, [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Du kan även ge användare åtkomst till faktureringskontot efter övergången. Mer information finns i [hantera fakturering roller i Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Återställa övergången

Övergången kan inte återställas. När att faktureringen för dina Azure-prenumerationer till det nya faktureringskontot supportprocessen, kan inte du ångra den tillbaka till ditt Enterprise Agreement-registrering.

### <a name="closing-your-browser-during-setup"></a>Stänga webbläsaren under installationen

Innan du klickar på **starta övergången**, kan du stänga webbläsaren. Du kan gå tillbaka till installationen med hjälp av länken du fick i e-postmeddelandet och börja övergången. Om du stänger webbläsaren när övergången har startat fortsätter din övergång att köras på. Gå tillbaka till sidan övergången status för att övervaka den senaste statusen för din övergång. Du får ett e-postmeddelande när övergången är klar.

## <a name="validate-the-billing-account-is-set-up-properly"></a>Verifiera faktureringen konto är korrekt konfigurerad

 Verifiera följande för att säkerställa att ditt nya faktureringskonto är korrekt konfigurerad:

### <a name="azure-subscriptions"></a>Azure-prenumerationer

1. Logga in på [Azure Portal](http://portal.azure.com).

2. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Välj profilen som fakturering. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Faktureringskonto, Välj **fakturering profiler** och sedan fakturering profilen.

4. Välj **Azure-prenumerationer** till vänster.

   ![Skärmbild som visar listan över prenumerationer](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Alla Azure-prenumerationer som överförs från din Enterprise Agreement-registrering till det nya faktureringskontot visas på sidan för Azure-prenumerationer. Om du tror att någon prenumeration saknas, övergång faktureringen för prenumerationen manuellt i Azure-portalen. Mer information finns i [hämta faktureringsägarskapet av Azure-prenumerationer från andra användare](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure-reservationer

1. Logga in på [Azure Portal](http://portal.azure.com).

2. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Välj en faktura-avsnitt. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en profil för fakturering.  Faktureringskonto eller fakturering profil, väljer du **faktura avsnitt** och sedan en faktura-avsnittet.

    ![Skärmbild som visar listan över faktura avsnittet efter övergången](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Välj **alla produkter** till vänster.

5. Sök på **reserverade**.

    ![Skärmbild som visar listan över prenumerationer efter övergången](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Alla Azure-reservationer som flyttas från din Enterprise Agreement-registrering till det nya faktureringskontot visas på sidan alla produkter. Upprepa stegen för alla faktura avsnitt att verifiera att alla Azure-reservationer har flyttats från din Enterprise Agreement-registrering. Om du tror att någon Azure-Reservation saknas [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att flytta reservationen till det nya faktureringskontot.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Åtkomst till företagets administratörer på fakturering profilen

1. Logga in på [Azure Portal](http://portal.azure.com).

2. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Välj fakturering profilen som du skapade för din registrering. Beroende på din åtkomst kan du behöva välja ett faktureringskonto.  Faktureringskonto, Välj **fakturering profiler** och sedan fakturering profilen.

4. Välj **åtkomstkontroll (IAM)** till vänster.

   ![Skärmbild som visar åtkomst till enterprise administratörer efter övergången](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Företagsadministratörer listas som fakturering profil ägare när företaget administratörer med läsbehörighet listas som fakturering profil läsare. Om åtkomsten för alla Företagsadministratörer saknas kan ge du dem åtkomst i Azure-portalen. Mer information finns i [hantera fakturering roller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Åtkomst till Företagsadministratörer, avdelning administratörer och kontoinnehavare på faktura-avsnitt

1. Logga in på [Azure Portal](http://portal.azure.com).

2. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Välj en faktura-avsnitt. Faktura avsnitt har samma namn som deras respektive avdelningar i Enterprise-avtal registreringar. Beroende på din åtkomst kan du behöva välja en profil för fakturering eller något faktureringskonto. Fakturering profilen eller faktureringskontot väljer **faktura avsnitt** och välj sedan en faktura-avsnittet.

   ![Skärmbild som visar listan över faktura avsnittet efter övergången](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Välj **åtkomstkontroll (IAM)** till vänster.

    ![Skärmbild som visar åtkomst för avdelning och administratörer åtkomst till efter övergången-kontot](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Enterprise administratörer eller avdelning listas som faktura avsnittet ägare eller faktura avsnittet läsare kontoinnehavare i avdelningen finns listade som skapare av Azure-prenumeration. Upprepa steget för alla faktura avsnitt att kontrollera åtkomst för alla avdelningar i ditt Enterprise Agreement-registrering. Kontoinnehavare som inte var en del av en avdelning som får behörighet på en faktura-avsnittet med namnet **faktura standardavsnittet**. Om åtkomsten för alla administratörer saknas kan ge du dem åtkomst i Azure-portalen. Mer information finns i [hantera fakturering roller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Kom igång med ditt nya faktureringskonto](billing-mca-overview.md)

- [Lär dig hur du utför vanliga EA-åtgärder i din nya faktureringskonto](billing-mca-enterprise-operations.md)
