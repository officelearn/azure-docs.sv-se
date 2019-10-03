---
title: Konfigurera ditt faktureringskonto för ett Microsoft-kundavtal – Azure
description: Lär dig hur du konfigurerar ditt faktureringskonto för ett Microsoft-kundavtal.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 9cdda62f0178a18897e3bc6c8cec6cfa943d3b1b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709515"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Konfigurera ditt faktureringskonto för ett Microsoft-kundavtal

Om din Enterprise-avtalsregistrering har upphört att gälla eller snart upphör kan du teckna ett Microsoft-kundavtal för att förnya registreringen. I den här artikeln beskrivs ändringarna i din befintliga fakturering efter konfigurationen, och du får hjälp med konfigurationen av ditt nya faktureringskonto. Förnyelsen omfattar följande steg:

1. Godkänna det nya Microsoft-kundavtalet. Arbeta med din Microsoft-fältrepresentant för att förstå detaljerna och godkänna det nya avtalet.
2. Konfigurera det nya faktureringskonto som skapas för det nya Microsoft-kundavtalet.

För att konfigurera faktureringskontot måste du överföra faktureringen av Azure-prenumerationer från din Enterprise-avtalsregistrering till det nya kontot. Konfigurationen påverkar inte Azure-tjänster som körs i dina prenumerationer. Den ändrar dock ditt sätt att hantera faktureringen för dina prenumerationer.

- I stället för [EA-portalen](https://ea.azure.com) hanterar du Azure-tjänster och -fakturering i [Azure-portalen](https://portal.azure.com).
- Du får en månatlig, digital faktura för dina avgifter. Du kan visa och analysera fakturan på sidan för Azure-kostnadshantering och fakturering.
- I stället för avdelningar och konton i din Enterprise-avtalsregistrering använder du faktureringsstrukturen och omfången från det nya kontot för att hantera och organisera din fakturering.

Innan du påbörjar konfigurationen rekommenderar vi att du gör följande:

- **Förstå ditt nya faktureringskonto**
  - Ditt nya konto förenklar faktureringen för din organisation. [Få en snabb översikt över ditt nya faktureringskonto](billing-mca-overview.md)
- **Kontrollera din åtkomst för att slutföra konfigurationen**
  - Endast användare med vissa administrativa behörigheter kan slutföra konfigurationen. Kontrollera om du har [nödvändig åtkomst för att slutföra konfigurationen](#access-required-to-complete-the-setup).
- **Förstå ändringar i din faktureringshierarki**
  - Det nya faktureringskontot är organiserat på ett annat sätt än din Enterprise-avtalsregistrering. [Förstå ändringar i din faktureringshierarki i det nya kontot](#understand-changes-to-your-billing-hierarchy).
- **Förstå ändringar i din faktureringsadministratörs åtkomst**
  - Administratörer från din Enterprise-avtalsregistrering får åtkomst till faktureringsomfången i det nya kontot.[Förstå ändringar av deras åtkomst](#changes-to-billing-administrator-access).
- **Visa Enterprise-avtalsfunktioner som har ersatts av det nya kontot**
  - Visa funktioner i Enterprise-avtalsregistrering som ersätts av funktioner i det nya kontot.
- **Visa svar på de vanligaste frågorna**
  - Visa [ytterligare information](#additional-information) om du vill veta mer om konfigurationen.

## <a name="access-required-to-complete-the-setup"></a>Åtkomst för att slutföra konfigurationen

För att kunna slutföra konfigurationen behöver du följande åtkomst:

- Ägare till den faktureringsprofil som skapades när Microsoft-kundavtalet signerades. Mer information om faktureringsprofiler finns i [förstå faktureringsprofiler](billing-mca-overview.md#billing-profiles).

- Företagsadministratör för den registrering som förnyas.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Om du inte är företagsadministratör för registreringen

Du kan be företagsadministratörerna för registreringen att slutföra konfigurationen av ditt faktureringskonto.

1. Logga in på Azure-portalen med hjälp av länken i det e-postmeddelande som skickades till dig när du signerade Microsoft-kundavtalet.

2. Om någon annan i din organisation signerade avtalet eller om du inte har e-postmeddelandet loggar du in via följande länk. Ersätt **enrollmentNumber** med registreringsnumret för ditt Enterprise-avtal som förnyades.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Välj de företagsadministratörer som du vill skicka begäran till.

   ![Skärmbild som visar inbjudan till företagsadministratörer](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Välj **Skicka begäran**.

   Administratörerna får ett e-postmeddelande med instruktioner för att slutföra konfigurationen.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Om du inte är ägare till faktureringsprofilen

Den användare i din organisation som signerade Microsoft-kundavtalet läggs till som ägare i faktureringsprofilen. Be användaren att lägga till dig som ägare så att du kan slutföra konfigurationen.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Förstå ändringar i din faktureringshierarki

Ditt nya faktureringskonto förenklar faktureringen för din organisation och ger dig förbättrade funktioner för fakturering och kostnadshantering. I följande diagram förklaras hur faktureringen organiseras på det nya faktureringskontot.

![Bild på ea-mca-post-transition-hierarchy](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. Du använder faktureringskontot för att hantera faktureringen för ditt Microsoft-kundavtal. Mer information om faktureringskonto finns i [förstå faktureringskonto](billing-mca-overview.md#your-billing-account).
2. Du använder faktureringsprofilen för att hantera faktureringen för din organisation på ett liknande sätt som för din Enterprise-avtalsregistrering. Företagsadministratörer blir ägare till faktureringsprofilen. Mer information om faktureringsprofiler finns i [förstå faktureringsprofiler](billing-mca-overview.md#billing-profiles).
3. Du kan använda ett fakturaavsnitt för att organisera dina kostnader utifrån dina behov, ungefär som avdelningar i din Enterprise-avtalsregistrering. Avdelning blir fakturaavsnitt, och avdelningsadministratörer blir ägare av respektive fakturaavsnitt. Mer information om fakturaavsnitt finns i [förstå fakturaavsnitt](billing-mca-overview.md#invoice-sections).
4. De konton som skapades i ditt Enterprise-avtal stöds inte på det nya faktureringskontot. Kontots prenumerationer tillhör respektive fakturaavsnitt för deras avdelning. Kontoägare kan skapa och hantera prenumerationer för sina fakturaavsnitt.

## <a name="changes-to-billing-administrator-access"></a>Ändringar av faktureringsadministratörers åtkomst

Beroende på åtkomsten kan faktureringsadministratörer i din Enterprise-avtalsregistrering få åtkomst till faktureringsomfången på det nya kontot. I följande tabell förklaras ändringen av åtkomst under konfigurationen:

| Befintlig roll | Roll efter övergång |
| --- | --- |
| **Företagsadministratör (Skrivskyddad = Nej)** | **– Faktureringsprofilsägare** </br> Hantera allt i faktureringsprofilen </br> - **Fakturaavsnittsägare i alla fakturaavsnitt** </br> Hantera allt i fakturaavsnitten |
| **Företagsadministratör (Skrivskyddad = Ja)** | **– Faktureringsprofilsläsare** </br> – Skrivskyddad vy över allt på faktureringskontot</br>**– Fakturaavsnittsläsare i alla fakturaavsnitt**</br> – Skrivskyddad vy över allt i fakturaavsnittet|
| **Avdelningsadministratör (Skrivskyddad = Nej)** |**– Fakturaavsnittsägare i det fakturaavsnitt som skapats för respektive avdelning** </br>Hantera allt i fakturaavsnittet|
| **Avdelningsadministratör (Skrivskyddad = Ja)**|**– Fakturaavsnittsläsare i det fakturaavsnitt som skapats för respektive avdelning**</br> Skrivskyddad vy över allt i fakturaavsnittet|
| **Kontoägare** | **– Azure-prenumerationsskapare i det fakturaavsnitt som skapats för respektive avdelning** </br>  Skapa Azure-prenumerationer för sitt fakturaavsnitt|

En Azure Active Directory-klientorganisation väljs för det nya faktureringskontot vid signering av Microsoft-kundavtalet. Om en klientorganisation inte finns för din organisation skapas en ny klientorganisation. Klientorganisationen representerar din organisation i Azure Active Directory. Globala klientorganisationsadministratörer i din organisation använder klientorganisationen för att hantera åtkomst till program och data i din organisation.

Ditt nya konto stöder endast användare från den klientorganisation som valdes vid signeringen av Microsoft-kundavtalet. Om användare med administrativ behörighet i ditt Enterprise-avtal är en del av klientorganisationen får de åtkomst till det nya faktureringskontot under konfigurationen. Om de inte är en del av klientorganisationen kommer de inte att kunna komma åt det nya faktureringskontot såvida du inte bjuder in dem.

När du bjuder in användare läggs de till i klientorganisationen som gästanvändare och får åtkomst till faktureringskontot. För att gäster ska kunna bjudas in måste gäståtkomst vara aktiverad för klientorganisationen. Mer information finns i [kontrollera gäståtkomst i Azure Active Directory](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Om gäståtkomsten är avstängd kontaktar du de globala administratörerna för din klientorganisation för att aktivera den. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Visa ersatta funktioner

Följande Enterprise-avtalsfunktioner ersätts med nya funktioner i faktureringskontot för ett Microsoft-kundavtal.

### <a name="enterprise-agreement-accounts"></a>Enterprise-avtalskonton

De konton som skapades i din Enterprise-avtalsregistrering stöds inte på det nya faktureringskontot. Kontots prenumerationer tillhör det fakturaavsnitt som skapats för deras respektive avdelning. Kontoägare blir Azure-prenumerationsskapare och kan skapa och hantera prenumerationer för sina fakturaavsnitt.

### <a name="notification-contacts"></a>Meddelandekontakter

Meddelandekontakter får e-postmeddelanden om Azure Enterprise-avtalet. De stöds inte på det nya faktureringskontot. E-postmeddelanden om Azure-krediter och fakturor skickas till användare som har åtkomst till faktureringsprofiler i ditt faktureringskonto.

### <a name="spending-quotas"></a>Utgiftskvoter

Utgiftskvoter som angavs för avdelningar i din Enterprise-avtalsregistrering ersätts med budgetar i det nya faktureringskontot. En budget skapas för varje utgiftskvot som angetts för avdelningar i din registrering. Mer information om budgetar finns i [skapa och hantera Azure-budgetar](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Kostnadsställen

Kostnadsställen som angavs för Azure-prenumerationerna i din Enterprise-avtalsregistrering följer med i det nya faktureringskontot. Dock stöds inte kostnadsställen för avdelningar och Enterprise-avtalskonton.

## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om hur du konfigurerar ditt faktureringskonto.

### <a name="no-service-downtime"></a>Inget avbrott i tjänsten

Azure-tjänster i din prenumeration fortsätter att köras utan avbrott. Vi flyttar bara faktureringsrelationen för dina Azure-prenumerationer. Befintliga resurser, resursgrupper och hanteringsgrupper påverkas inte.

### <a name="user-access-to-azure-resources"></a>Användaråtkomst till Azure-resurser

Åtkomst till Azure-resurser som angavs med hjälp av Azure RBAC (rollbaserad åtkomstkontroll) påverkas inte under övergången.

### <a name="azure-reservations"></a>Azure-reservationer

Azure-reservationer i din Enterprise-avtalsregistrering flyttas till ditt nya faktureringskonto. Vid övergången sker inga ändringar av reservationsrabatter som tillämpas på dina prenumerationer.

### <a name="azure-marketplace-products"></a>Azure Marketplace-produkter

Alla Azure Marketplace-produkter i din Enterprise-avtalsregistrering flyttas tillsammans med prenumerationerna. Det kommer inte att ske några ändringar i tjänståtkomsten för Marketplace-produkterna under övergången.

### <a name="support-plan"></a>Supportplan

Supportförmåner överförs inte som en del av övergången. Köp en ny supportplan för att få förmåner för Azure-prenumerationer i ditt nya faktureringskonto.

### <a name="past-charges-and-balance"></a>Tidigare avgifter och saldo

Avgifts- och kreditsaldo före övergången kan visas i din Enterprise-avtalsregistrering via Azure-portalen. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>När ska konfigurationen slutföras?

Slutför konfigurationen av ditt faktureringskonto innan din Enterprise-avtalsregistrering upphör att gälla. Om registreringen upphör fortsätter tjänsterna i dina Azure-prenumerationer att köras utan avbrott. Dock debiteras du standardpriser för tjänsterna.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Ändringar i Enterprise-avtalsregistreringen efter konfigurationen

Azure-prenumerationer som skapas för Enterprise-avtalsregistreringen efter övergången kan flyttas manuellt till det nya faktureringskontot. Mer information finns i [få faktureringsägarskap för Azure-prenumerationer från andra användare](billing-mca-request-billing-ownership.md). Om du vill flytta Azure-reservationer som köps efter övergången [kontaktar du Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Du kan även ge användare åtkomst till faktureringskontot efter övergången. Mer information finns i [hantera faktureringsroller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Ångra övergången

Övergången kan inte ångras. När faktureringen av dina Azure-prenumerationer flyttas till det nya faktureringskontot kan du inte gå tillbaka till din Enterprise-avtalsregistrering.

### <a name="closing-your-browser-during-setup"></a>Stänga webbläsaren under konfigurationen

Innan du klickar på **Starta övergången** kan du stänga webbläsaren. Du kan gå tillbaka till konfigurationen via den länk som du fick i e-postmeddelandet och starta övergången. Om du stänger webbläsaren efter att övergången har startats fortsätter övergången att köras. Gå tillbaka till sidan för övergångsstatus om du vill se senaste status för övergången. Du får ett e-postmeddelande när övergången är klar.

## <a name="complete-the-setup-in-the-azure-portal"></a>Slutföra konfigurationen i Azure-portalen

För att slutföra konfigurationen behöver du tillgång till både det nya faktureringskontot och Enterprise-avtalsregistreringen. Mer information finns i [åtkomst krävs för att slutföra konfigurationen av ditt faktureringskonto](#access-required-to-complete-the-setup).

1. Logga in på Azure-portalen med hjälp av länken i det e-postmeddelande som skickades till dig när du signerade Microsoft-kundavtalet.

2. Om någon annan i din organisation signerade avtalet eller om du inte har e-postmeddelandet loggar du in via följande länk. Ersätt **enrollmentNumber** med registreringsnumret för ditt Enterprise-avtal som förnyades.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Välj **Starta övergången** i det sista steget i konfigurationen. När du har valt att starta övergången:

    ![Skärmbild som visar konfigurationsguiden](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - En faktureringshierarki som motsvarar din Enterprise-avtalshierarki skapas i det nya faktureringskontot. Mer information finns i [förstå ändringar i din faktureringshierarki](#understand-changes-to-your-billing-hierarchy).
    - Administratörer från din Enterprise-avtalsregistrering får åtkomst till det nya faktureringskontot så att de fortsätter att hantera faktureringen för din organisation.
    - Faktureringen för dina Azure-prenumerationer överförs till det nya kontot. **Dina Azure-tjänster påverkas inte under övergången. De fortsätter att köras utan avbrott**.
    - Om du har Azure-reservationer flyttas de till ditt nya faktureringskonto med samma rabatt och period. Din reservationsrabatt fortsätter att gälla under övergången.

4. Du kan övervaka status för övergången på sidan **Övergångsstatus**.

   ![Skärmbild som visar övergångsstatus](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-set-up"></a>Verifiera konfiguration av faktureringskonto

 Kontrollera följande för att se till att ditt nya faktureringskonto är korrekt konfigurerat:

### <a name="azure-subscriptions"></a>Azure-prenumerationer

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Välj faktureringsprofilen. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan faktureringsprofilen.

4. Välj **Azure-prenumerationer** på vänster sida.

   ![Skärmbild som visar en lista över prenumerationer](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Azure-prenumerationer som överförs från din Enterprise-avtalsregistrering till det nya faktureringskontot visas på sidan Azure-prenumerationer. Om du tror att någon prenumeration saknas kan du överföra faktureringen för prenumerationen manuellt i Azure-portalen. Mer information finns i [få faktureringsägarskap för Azure-prenumerationer från andra användare](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure-reservationer

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Välj ett fakturaavsnitt. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil.  Från faktureringskontot eller faktureringsprofilen väljer du **Fakturaavsnitt** och sedan ett fakturaavsnitt.

    ![Skärmbild som visar listan över fakturaavsnitt efter övergången](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Välj **Alla produkter** på vänster sida.

5. Sök efter **Reserverade**.

    ![Skärmbild som visar listan över prenumerationer efter övergången](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Azure-reservationer som överförs från din Enterprise-avtalsregistrering till det nya faktureringskontot visas på sidan Alla produkter. Upprepa stegen för alla fakturaavsnitt för att kontrollera att alla Azure-reservationer flyttas från din Enterprise-avtalsregistrering. Om du tror att någon Azure-reservation saknas [kontaktar du Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att flytta reservationen till det nya faktureringskontot.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Åtkomst för företagsadministratörer i faktureringsprofilen

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Välj den faktureringsprofil som skapats för din registrering. Beroende på din åtkomst kan du behöva välja ett faktureringskonto.  Från faktureringskontot väljer du **Faktureringsprofiler** och sedan faktureringsprofilen.

4. Välj **Åtkomstkontroll (IAM)** på vänster sida.

   ![Skärmbild som visar åtkomst för företagsadministratörer efter övergången](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Företagsadministratörer anges som faktureringsprofilägare medan företagsadministratörer med skrivskyddad behörighet anges som faktureringsprofilläsare. Om du tror att åtkomsten för någon företagsadministratör saknas kan du ge den åtkomst i Azure-portalen. Mer information finns i [hantera faktureringsroller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Åtkomst för företagsadministratörer, avdelningsadministratörer och kontoägare i fakturaavsnitt

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Välj ett fakturaavsnitt. Fakturaavsnitten har samma namn som sina respektive avdelningar i Enterprise-avtalsregistreringar. Beroende på din åtkomst kan du behöva välja en faktureringsprofil eller ett faktureringskonto. Från faktureringsprofilen eller faktureringskontot väljer du **Fakturaavsnitt** och sedan ett fakturaavsnitt.

   ![Skärmbild som visar listan över fakturaavsnitt efter övergången](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Välj **Åtkomstkontroll (IAM)** på vänster sida.

    ![Skärmbild som visar åtkomst för avdelningsadministratörer och kontoadministratörer efter övergång](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Företagsadministratörer och avdelningsadministratörer anges som fakturaavsnittsägare eller fakturaavsnittsläsare medan kontoägare på avdelningen anges som Azure-prenumerationsskapare. Upprepa steget för alla fakturaavsnitt för att kontrollera åtkomsten för alla avdelningar i din Enterprise-avtalsregistrering. Kontoägare som inte tillhörde någon avdelning får behörighet i ett fakturaavsnitt som heter **Default invoice section** (Standardfakturaavsnitt). Om du tror att åtkomsten för någon administratör saknas kan du ge den åtkomst i Azure-portalen. Mer information finns i [hantera faktureringsroller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Komma igång med ditt nya faktureringskonto](billing-mca-overview.md)

- [Slutföra Enterprise-avtalsuppgifter i ditt faktureringskonto för ett Microsoft-kundavtal](billing-mca-enterprise-operations.md)

- [Hantera åtkomst till ditt faktureringskonto](billing-understand-mca-roles.md)
