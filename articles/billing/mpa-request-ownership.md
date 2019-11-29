---
title: Få faktureringsägarskap för Azure-prenumerationer för Microsoft-partneravtal (MPA)
description: Lär dig hur du begär faktureringsägarskap för Azure-prenumerationer från andra användare.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: banders
ms.openlocfilehash: d68d95619b6aa6e8098c282977866c4bc21a357c
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561055"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Få faktureringsägarskap för Azure-prenumerationer till ditt MPA-konto

CSP:er kan ta över faktureringsägarskapet för Azure-prenumerationer från dina kunder med direkt Enterprise-avtal för att kunna tillhandahålla en samlad faktura för alla hanterade tjänster och all Azure-förbrukning.

Den här funktionen är bara tillgänglig för CSP-direktfaktureringspartner certifierade som [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp). Den omfattas av Microsofts reglering och policyer och kan behöva godkännas för vissa kunder.

Om du vill begära faktureringsägarskap måste du ha rollen som **global administratör** eller **administratörsrepresentant**. Läs mer i [Partner Center – Tilldela användarroller och behörigheter](https://docs.microsoft.com/partner-center/permissions-overview).

Den här artikeln gäller faktureringskonton för Microsoft-partneravtal. Dessa konton skapas för att molnlösningsleverantörer (CSP:er) ska kunna hantera faktureringen för sina kunder i den nya e-handelsmiljön. Den nya miljön är bara tillgänglig för partner som har minst en kund som har accepterat ett Microsoft-kundavtal (MCA) och har ett Azure-prenumerationsavtal. [Kontrollera om du har åtkomst till ett Microsoft-partneravtal](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Nödvändiga komponenter

1. Upprätta en [återförsäljarrelation](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer) med kunden. Kontrollera [översikten över godkända regioner för CSP](https://docs.microsoft.com/partner-center/regional-authorization-overview) för att säkerställa att både kundens och partnerns klientorganisation ligger i samma godkända regioner.  

2. [Bekräfta att kunden har godkänt Microsoft-kundavtalet](https://docs.microsoft.com/partner-center/confirm-customer-agreement).

3. Konfigurera kundens [Azure-prenumerationsavtal](https://docs.microsoft.com/partner-center/purchase-azure-plan). Om kunden köper av flera återförsäljare måste du konfigurera Azure-prenumerationsavtalet för varje kombination av kund och återförsäljare.

## <a name="request-billing-ownership"></a>Begära faktureringsägarskap

1. Logga in i [Azure-portalen](https://portal.azure.com) med CSP-administratörsrepresentantens inloggningsinformation.

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/mpa-request-ownership/search-cmb.png)

3. Välj **Kunder** till vänster och sedan en kund i listan.
   
   ![Skärmbild som visar val av kunder](./media/mpa-request-ownership/mpa-select-customers.png)        

4. Välj **Överföringsbegäranden** nere till vänster och välj sedan **Lägg till en ny begäran**.
 
   ![Skärmbild som visar val av överföringsbegäranden](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. Ange e-postadressen till användaren i kundorganisationen som ska godkänna överföringsbegäran. Användaren måste vara kontoägare för ett Enterprise-avtal. Välj **Skicka överföringsbegäran**.

   ![Skärmbild som visar sändning av en överföringsbegäran](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. Användaren får ett e-postmeddelande med instruktioner för att granska din överföringsförfrågan.

   ![Skärmbild som visar granskning av e-postmeddelande med överföringsbegäran](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. För att godkänna överföringsbegäran väljer användaren länken i e-postmeddelandet och följer anvisningarna.

    ![Skärmbild som visar granskning av e-postmeddelande med överföringsbegäran](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Kontrollera statusen för överföringsbegäran

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Välj **Kunder** till vänster.

   ![Skärmbild som visar val av kunder](./media/mpa-request-ownership/mpa-select-customers.png)

4. Välj kunden som du skickade överföringsbegäran till i listan.

5. Välj **Överföringsbegäranden** på den nedre vänstra sidan. På sidan Överföringsbegäranden visas följande information:

    ![Skärmbild som visar lista med överföringsbegäranden](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |Kolumn|Definition|
   |---------|---------|
   |Datum för begäran|Det datum då överföringsbegäran skickades|
   |Mottagare|E-postadressen för den användare till vilken du skickade begäran om att överföra faktureringsägarskapet|
   |Förfallodatum|Det datum då begäran upphör att gälla|
   |Status|Status för överföringsbegäran|

    Överföringsbegäran kan ha en av följande statusar:

   |Status|Definition|
   |---------|---------|
   |Pågår|Användaren har inte accepterat överföringsbegäran|
   |Bearbetar|Användaren har accepterat överföringsbegäran. Fakturering för de prenumerationer som användaren valde överförs till ditt konto|
   |Slutfört| Fakturering för de prenumerationer som användaren valde har överförts till ditt konto|
   |Slutförd med fel|Begäran slutfördes men faktureringen för vissa prenumerationer som användaren valde kunde inte överföras|
   |Upphörd|Användaren accepterade inte begäran i tid, och den upphörde|
   |Avbrutna|Någon med åtkomst till överföringsbegäran avbröt begäran|
   |Nekad|Användaren nekade överföringsbegäran|

6. Välj en överföringsbegäran för att visa information. På sidan för överföringsbegäran visas följande information: ![Skärmbild som visar en lista med överförda prenumerationer](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |Kolumn  |Definition|
   |---------|---------|
   |ID för överföringsbegäran|Unikt ID för din överföringsbegäran. Om du skickar en supportbegäran kan du uppge ID:t för Azure-supporten för att påskynda supportbegäran|
   |Överföringen begärdes den|Det datum då överföringsbegäran skickades|
   |Överföringen begärdes av|E-postadressen för den användare som skickade överföringsbegäran|
   |Överföringsbegäran upphör den| Det datum då överföringsbegäran upphör|
   |Mottagarens e-postadress|E-postadressen för den användare till vilken du skickade begäran om att överföra faktureringsägarskapet|
   |Överföringslänk som skickats till mottagaren|Den URL som skickades till användaren för granskning av överföringsbegäran|

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Du kan begära faktureringsägarskap för de prenumerationstyper som anges nedan.

- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\* 
- [Microsoft Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* Prenumerationen Enterprise DevTest faktureras enligt Betala per användning när den överförs, eventuella rabatter som erbjuds via Enterprise DevTest-erbjudandet och kundens Enterprise-avtal gäller inte för CSP-partnern.

## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om överföring av prenumerationer.

### <a name="no-service-downtime"></a>Inget avbrott i tjänsten

Azure-tjänster i prenumerationen fortsätter att köras utan avbrott. Vi överför endast faktureringsrelationen för de Azure-prenumerationer som användaren väljer att överföra.

### <a name="disabled-subscriptions"></a>Inaktiverade prenumerationer

Inaktiverade prenumerationer kan inte överföras. Prenumerationer måste vara i aktivt tillstånd för att deras faktureringsägarskap ska överföras.

### <a name="azure-resources-transfer"></a>Överföring av Azure-resurser

Alla resurser från prenumerationer såsom virtuella datorer, diskar och webbplatser överförs.

### <a name="azure-marketplace-products-transfer"></a>Överföring av Azure Marketplace-produkter

Azure Marketplace-produkter som är tillgängliga för prenumerationer som hanteras av molnlösningsleverantörer (CSP:er) överförs tillsammans med respektive prenumeration. Du kan inte överföra prenumerationer som omfattar Azure Marketplace-produkter som inte är aktiverade för CSP:er.

### <a name="azure-reservations-transfer"></a>Överföring av Azure-reservationer

Azure-reservationer flyttas inte automatiskt med prenumerationer. [Kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att flytta reservationer.

### <a name="access-to-azure-services"></a>Åtkomst till Azure-tjänster

Åtkomst för befintliga användare, grupper eller tjänsthuvudnamn som tilldelades med hjälp av [Azure RBAC (rollbaserad åtkomstkontroll)](../role-based-access-control/overview.md) påverkas inte under övergången. Partnern får inte ny RBAC-åtkomst till prenumerationerna.  

Partnern bör samarbeta med kunden om att få åtkomst till prenumerationer.  Partnern måste få antingen [AOBO-åtkomst (administratör på uppdrag av)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) eller [Azure Lighthouse-åtkomst](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider) till öppna supportärenden.

### <a name="azure-support-plan"></a>Azure-supportplan

Azure-support överförs inte med prenumerationerna. Om användaren överför alla Azure-prenumerationer ber du den att säga upp sin supportplan. Efter överföringen ansvarar CSP-partnern för supporten. Kunden bör samarbeta med CSP-partnern om eventuella supportärenden.  

### <a name="charges-for-transferred-subscription"></a>Avgifter för överförd prenumeration

Den ursprungliga faktureringsägaren för prenumerationerna ansvarar för eventuella avgifter som rapporterades fram till den tidpunkt då överföringen slutfördes. Du ansvarar för de avgifter som rapporteras från och med överföringen. Det kan finnas en vissa avgifter som ägde rum före överföringen men som rapporterades efteråt. De här avgifterna visas på din faktura.

### <a name="cancel-a-transfer-request"></a>Avbryta en överföringsbegäran

Du kan avbryta överföringsbegäran tills begäran har godkänts eller nekats. Om du vill avbryta överföringsbegäran går du till [sidan med överföringsinformation](#check-the-transfer-request-status) och väljer Avbryt längst ned på sidan.

### <a name="software-as-a-service-saas-transfer"></a>Överföring av SaaS (Software as a Service)

SaaS-produkter överförs inte med prenumerationerna. Be användaren att [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att överföra faktureringsägarskap för SaaS-produkter. Utöver faktureringsägarskapet kan användaren även överföra resursägarskapet. Med resursägarskap kan du utföra hanteringsåtgärder som att ta bort och visa information om produkten. Användaren måste vara resursägare för SaaS-produkten för att överföra resursägarskap.

### <a name="additional-approval-for-certain-customers"></a>Ytterligare godkännande för vissa kunder

För en del kunder kan det krävas ytterligare granskning av Microsoft i samband med överföringar, på grund av strukturen för kundens aktuella Enterprise-registrering. Partnern meddelas om sådana krav när inbjudan skickas till kunden. Partnern måste samarbeta med sin Partner Development Manager och kundens kontoteam för att slutföra den här granskningsprocessen.

### <a name="azure-subscription-directory"></a>Azure-prenumerationens katalog 
Katalogen för de Azure-prenumerationer som överförs måste matcha katalogen för kunden som valdes när CSP-relationen etablerades. 

Om katalogerna inte matchar går det inte att överföra prenumerationerna. Du måste antingen upprätta en ny CSP-återförsäljarrelation med kunden genom att välja katalogen för Azure-prenumerationerna eller ändra katalogen för Azure-prenumerationer så att den matchar katalogen i kundens CSP-relation. Du kan läsa mer i [Associera en befintlig prenumeration med din Azure AD-katalog](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Kontrollera åtkomsten till ett Microsoft-partneravtal
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- Faktureringsägarskapet för Azure-prenumerationerna överförs till dig. Håll koll på avgifterna för dessa prenumerationer i [Azure-portalen](https://portal.azure.com).

- Samarbeta med kunden om att få åtkomst till de överförda Azure-prenumerationerna. [Hantera åtkomst till Azure-resurser med hjälp av RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
