---
title: Hämta faktureringsägarskapet av Azure-prenumerationer | Microsoft Docs
description: Lär dig mer om att begära faktureringsägarskapet för Azure-prenumerationer från andra användare.
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
ms.openlocfilehash: 01d34ef722034ef5b56c842bab97a3415a02b0bb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341875"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Hämta faktureringsägarskapet av Azure-prenumerationer från andra användare

Du kanske vill bli ägare av Azure-prenumerationer om befintliga fakturering ägaren som lämnar organisationen, eller om du vill betala för prenumerationer via ditt faktureringskonto.

Du kan skicka en begäran om att överta ägarskapet för Azure-prenumerationer från befintliga ägare i andra fakturering konton. Bli ägare överför fakturering ansvarsområden prenumerationer till faktura-avsnittet.

Om du vill begära faktureringsägarskapet, måste du vara en **faktura avsnittet ägare** eller **faktura avsnittet deltagare**. Mer information finns i [faktura avsnittet rollaktiviteter](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Begär faktureringsägarskapet i Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Gå till avsnittet faktura. Beroende på din åtkomst kan du behöva välja en faktureringskonto eller en profil för fakturering. Fakturering konto eller profil, väljer du **faktura avsnitt** och sedan en faktura-avsnittet.
   <!-- Todo - Add a screenshot -->

4. Välj **att överföra** från längst ned till vänster sida.

5. Högst upp på sidan Välj **Lägg till**.

6. Ange e-postadressen för den användare som du ska få faktureringsägarskapet från. Användaren måste vara kontoadministratör på något faktureringskonto för Microsoft Online Service-Program eller en kontoinnehavare med ett Enterprise Agreement. Mer information finns i [visa din faktureringsinformation konton i Azure-portalen](billing-view-all-accounts.md).

   ![Skärmbild som visar att lägga till en ny begäran om att överföra](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Välj **skicka överföringsbegäran**.

8. Användaren får ett e-postmeddelande med anvisningar för att granska din överföringsbegäran.

   ![Skärmbild som visar granskning överföring e-post](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Om du vill godkänna överföringsbegäran användaren väljer länken i e-postmeddelandet och följer instruktionerna.

    ![Skärmbild som visar granskning överföring e-post](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Kontrollera status för din överföringsbegäran i Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning för kostnadshantering + fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Gå till avsnittet faktura. Beroende på din åtkomst kan du behöva välja en faktureringskonto eller en profil för fakturering. Fakturering konto eller profil, väljer du **faktura avsnitt** och sedan en faktura-avsnittet.
   <!-- Todo - Add a screenshot -->

4. Välj **att överföra** från längst ned till vänster sida.

5. Sidan överföring begäranden visas följande information:

    ![Skärmbild som visar listan över begäranden om zonöverföring](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Kolumn|Definition|
   |---------|---------|
   |Datum för begäran|Det datum då överföringsbegäran skickades|
   |Mottagare|E-postadressen för användaren som du har skickat förfrågan om att överföra faktureringsägarskapet|
   |Upphörandedatum|Det datum då begäran upphör att gälla|
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
   |Avböjt|Användaren avböjde överföringsbegäran|

6. Välj en begäran att visa information om att överföra. Sidan överföring visar följande information: <!-- Todo - Add a screenshot -->

   |Kolumn  |Definition|
   |---------|---------|
   |Begäran-ID för överföring|Unikt ID för din överföringsbegäran. Om du skickar en supportförfrågan, dela det ID: T med Azure-supporten påskynda din supportbegäran|
   |Överföring begärdes den|Det datum då överföringsbegäran skickades|
   |Överföring begärd av|E-postadressen för den användare som skickade överföringsbegäran|
   |Överföringsförfrågan förfaller| Dagen då överföringsbegäran förfaller|
   |Mottagarens e-postadress|E-postadressen för användaren som du har skickat förfrågan om att överföra faktureringsägarskapet|
   |Överföringslänken skickades till mottagare|Den url som har skickats till användaren att granska överföringsbegäran|

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

Åtkomst till Azure-resurser som har ställts in med Azure RBAC (rollbaserad åtkomstkontroll) påverkas inte under övergången.

### <a name="azure-support-plan"></a>Azure-supportplan

Support för Azure överföra inte med prenumerationer. Om användaren överför alla Azure-prenumerationer, be dem att säga upp sitt supportavtal.

### <a name="charges-for-transferred-subscription"></a>Avgifter för överförda prenumeration

Den ursprungliga fakturering ägaren av prenumerationerna som är ansvarig för eventuella kostnader som har rapporterats fram till att överföringen är klar. Din faktura-avsnittet är ansvarig för kostnaderna som rapporteras från tidpunkten för överföring och senare. Det kan finnas vissa kostnader som ägde rum före överföringen men rapporterades efteråt. Dessa kostnader visas på din faktura-avsnittet.

### <a name="supported-offers"></a>Stöds erbjudanden

Prenumerationen av alla typer eller betalning, förutom CSP erbjuder kan överföras.

### <a name="cancel-a-transfer-request"></a>Avbryta en begäran om att överföra

Du kan avbryta överföringsbegäran tills begäran godkänns eller nekas. Du avbryta överföringsbegäran, [gå till sidan överföring](#check-status-of-your-transfer-request-in-the-Azure-portal) och Välj Avbryt längst ned på sidan.

### <a name="software-as-a-service-saas-transfer"></a>Programvara som en tjänst (SaaS)-överföring

SaaS-produkter överförs inte med prenumerationer. Be användaren att [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att överföra faktureringsägarskapet för SaaS-produkter. Du kan också överföra ägarskapet tillsammans med faktureringsägarskapet. Resurs-ägare kan du utföra hanteringsåtgärder som tar bort och visar information om produkten. Användaren måste vara en resursägare på SaaS-produkt för att överföra äganderätten för resursen.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- Faktureringsägarskapet för Azure-prenumerationer överförs till faktura-avsnittet. Håll koll på kostnaderna för dessa prenumerationer i den [Azure-portalen](https://portal.azure.com).
- Ge andra användare behörighet att visa och hantera faktureringen för dessa prenumerationer. Mer information finns i [faktura avsnittet roller och uppgifter](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).