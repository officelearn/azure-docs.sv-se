---
title: Hämta faktureringsägarskapet av Azure-prenumerationer
description: Lär dig mer om att begära faktureringsägarskapet för Azure-prenumerationer från andra användare.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4c9a8c866a157757121e6a9dd07a0a8559937c5e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490857"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Hämta faktureringsägarskapet av Azure-prenumerationer från andra konton

Du kanske vill ta över ägarskapet av Azure-prenumerationer om befintliga fakturering ägaren som lämnar organisationen, eller om du vill betala för prenumerationer via ditt faktureringskonto. Bli ägare överför fakturering ansvarsområden prenumerationer till ditt konto.

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-for-access).

Om du vill begära faktureringsägarskapet, måste du vara en **faktura avsnittet ägare** eller **faktura avsnittet deltagare**. Mer information finns i [faktura avsnittet rollaktiviteter](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Faktureringsägarskapet för begäran

1. Logga in på den [Azure-portalen](http://portal.azure.com) faktura avsnittet ägare eller deltagare för något faktureringskonto för Microsoft kundavtal.

2. Sök efter **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Välj **faktura avsnitt** till vänster. Beroende på din åtkomst kan du behöva välja en faktureringskonto eller en profil för fakturering. Fakturering konto eller profil, väljer du **faktura avsnitt**.
   
   ![Skärmbild som visar att välja faktura avsnitt](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Välj en faktura-avsnitt i listan. När du överta ägarskapet för prenumerationer, kommer att faktureras till det här avsnittet för fakturan.

5. Välj **att överföra** längst ned till vänster sida och välj sedan **Lägg till**.
 
   ![Skärmbild som visar att välja begäranden om zonöverföring](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Ange e-postadressen för den användare som du ska få faktureringsägarskapet från. Användaren måste vara administratör-konto på något faktureringskonto för Microsoft Online Service-Program eller en kontoinnehavare med ett Enterprise Agreement. Mer information finns i [visa din faktureringsinformation konton i Azure-portalen](billing-view-all-accounts.md). Välj **skicka överföringsbegäran**.

   ![Skärmbild som visar att skicka en begäran om att överföra](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. Användaren får ett e-postmeddelande med anvisningar för att granska din överföringsbegäran.

   ![Skärmbild som visar granskning överföring e-post](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Om du vill godkänna överföringsbegäran användaren väljer länken i e-postmeddelandet och följer instruktionerna.

    ![Skärmbild som visar granskning överföring e-post](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Kontrollera status för begäran om överföring

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Välj **faktura avsnitt** till vänster. Beroende på din åtkomst kan du behöva välja en faktureringskonto eller en profil för fakturering. Fakturering konto eller profil, väljer du **faktura avsnitt**.
   
   ![Skärmbild som visar att välja faktura avsnitt](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Välj avsnittet faktura i listan som du skickade överföringsbegäran.

5. Välj **att överföra** från längst ned till vänster sida. Sidan överföring begäranden visas följande information:

    ![Skärmbild som visar listan över begäranden om zonöverföring](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Kolumn|Definition|
   |---------|---------|
   |Datum för begäran|Det datum då överföringsbegäran skickades|
   |Mottagaren|E-postadressen för användaren som du har skickat förfrågan om att överföra faktureringsägarskapet|
   |Förfallodatum|Det datum då begäran upphör att gälla|
   |Status|Status för begäran om att överföra|

    Överföringsbegäran kan ha något av följande statusar:

   |Status|Definition|
   |---------|---------|
   |Pågår|Användaren har inte accepterat överföringsbegäran|
   |Bearbetar|Användaren godkända överföringsbegäran. Fakturering för prenumerationer som användaren har valt komma överförs till faktura-avsnitt|
   |Slutfört| Faktureringen för prenumerationer som användaren har valt överförs till faktura-avsnitt|
   |Slutfördes med fel|Begäran har slutförts men faktureringen för vissa prenumerationer som användaren har valt kunde inte överföras|
   |Har upphört att gälla|Användaren kunde inte godkänna begäran i tid och den har upphört att gälla|
   |Avbrutna|Användare med åtkomst till överföringsbegäran har avbrutit begäran|
   |Avvisade|Användaren avböjde överföringsbegäran|

7. Välj en begäran att visa information om att överföra. Sidan överföring visar följande information:
   
   ![Skärmbild som visar listan över överförda prenumerationer](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Kolumn  |Definition|
   |---------|---------|
   |Begäran-ID för överföring|Unikt ID för din överföringsbegäran. Om du skickar en supportförfrågan, dela det ID: T med Azure-supporten påskynda din supportbegäran|
   |Överföring begärdes den|Det datum då överföringsbegäran skickades|
   |Överföring begärd av|E-postadressen för den användare som skickade överföringsbegäran|
   |Överföringsförfrågan förfaller| Dagen då överföringsbegäran förfaller|
   |Mottagarens e-postadress|E-postadressen för användaren som du har skickat förfrågan om att överföra faktureringsägarskapet|
   |Överföringslänken skickades till mottagare|Den url som har skickats till användaren att granska överföringsbegäran|

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Du kan begära faktureringsägarskapet för prenumerationstyper som anges nedan.

- [Action pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure i Open-licensiering](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure-Pass sponsring](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure-Sponsringserbjudande](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Prenumeranter på Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Prenumeranter på Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Krediter som är tillgängliga i prenumerationen kan inte tillgängliga i det nya kontot efter överföringen.

\*\* Stöds endast för prenumerationer i konton som skapas under registreringen på Azure-webbplatsen.


## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om att överföra prenumerationer.

### <a name="no-service-downtime"></a>Inga driftstopp för tjänsten

Azure-tjänster i prenumerationen att köra utan avbrott. Vi bara övergång faktureringsrelation för Azure-prenumerationer som användaren väljer för att överföra.

### <a name="disabled-subscriptions"></a>Inaktiverade prenumerationer

Inaktiverade prenumerationer kan inte överföras. Prenumerationer måste vara i aktivt läge för att överföra sina faktureringsägarskapet.

### <a name="azure-resources-transfer"></a>Överföra Azure-resurser

Alla resurser från prenumerationer som virtuella datorer, diskar och webbplatser överföring.

### <a name="azure-marketplace-products-transfer"></a>Överföring av Azure Marketplace-produkter

Azure Marketplace-produkter överföra tillsammans med deras respektive prenumerationer.

### <a name="azure-reservations-transfer"></a>Azure reservationer överföring

Azure reservationer flyttar inte automatiskt med prenumerationer. [Kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att flytta reservationer.

### <a name="access-to-azure-services"></a>Åtkomst till Azure-tjänster

Åtkomst för befintliga användare, grupper eller tjänstens huvudnamn som tilldelades med (Azure RBAC (rollbaserad åtkomstkontroll)) [... /role-based-Access-Control/Overview.MD] påverkas inte under övergången.

### <a name="azure-support-plan"></a>Azure-supportplan

Support för Azure överföra inte med prenumerationer. Om användaren överför alla Azure-prenumerationer, be dem att säga upp sitt supportavtal.

### <a name="charges-for-transferred-subscription"></a>Avgifter för överförda prenumeration

Den ursprungliga fakturering ägaren av prenumerationerna som är ansvarig för eventuella kostnader som har rapporterats fram till att överföringen är klar. Din faktura-avsnittet är ansvarig för kostnaderna som rapporteras från tidpunkten för överföring och senare. Det kan finnas vissa kostnader som ägde rum före överföringen men rapporterades efteråt. Dessa kostnader visas på din faktura-avsnittet.

### <a name="cancel-a-transfer-request"></a>Avbryta en begäran om att överföra

Du kan avbryta överföringsbegäran tills begäran godkänns eller nekas. Om du vill avbryta överföringsbegäran, går du till den [överföring informationssidan](#check-the-transfer-request-status) och Välj Avbryt längst ned på sidan.

### <a name="software-as-a-service-saas-transfer"></a>Programvara som en tjänst (SaaS)-överföring

SaaS-produkter överförs inte med prenumerationer. Be användaren att [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att överföra faktureringsägarskapet för SaaS-produkter. Du kan också överföra ägarskapet tillsammans med faktureringsägarskapet. Resurs-ägare kan du utföra hanteringsåtgärder som tar bort och visar information om produkten. Användaren måste vara en resursägare på SaaS-produkt för att överföra äganderätten för resursen.

## <a name="check-for-access"></a>Kontrollera åtkomst
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- Faktureringsägarskapet för Azure-prenumerationer överförs till faktura-avsnittet. Håll koll på kostnaderna för dessa prenumerationer i den [Azure-portalen](https://portal.azure.com).
- Ge andra användare behörighet att visa och hantera faktureringen för dessa prenumerationer. Mer information finns i [faktura avsnittet roller och uppgifter](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
