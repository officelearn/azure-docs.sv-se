---
title: Få fakturerings ägande av Azure-prenumerationer
description: Lär dig hur du begär fakturerings ägande av Azure-prenumerationer från andra användare.
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
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019565"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Få fakturerings ägande av Azure-prenumerationer från andra konton

Du kanske vill ta över ägarskapet för Azure-prenumerationer om den befintliga fakturerings ägaren lämnar din organisation eller om du vill betala för prenumerationer via ditt fakturerings konto. Om du tar över ägarskapet överförs fakturerings ansvar för prenumerationer på ditt konto.

Den här artikeln gäller för ett fakturerings konto för ett Microsofts kund avtal. [Kontrol lera om du har åtkomst till ett Microsofts kund avtal](#check-for-access).

Om du vill begära fakturerings ägarskapet måste du vara en **faktura avsnitts ägare** eller en **faktura avsnitts deltagare**. Mer information finns i [avsnittet om faktura avsnitt roller](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Begär fakturerings ägarskap

1. Logga in på [Azure Portal](https://portal.azure.com) som en faktura avsnitts ägare eller deltagare för ett fakturerings konto för Microsofts kund avtal.

2. Sök efter **Cost Management + fakturering**.

   ![Skärm bild som visar Azure Portal Sök efter kostnads hantering + fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Välj **faktura avsnitt** från vänster sida. Beroende på åtkomst kan du behöva välja ett fakturerings konto eller en fakturerings profil. Välj **faktura avsnitt**på fakturerings kontot eller-profilen.
   
   ![Skärm bild som visar val av faktura avsnitt](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Välj en faktura i listan. När du tar över prenumerationens ägarskap kommer de att faktureras i avsnittet om faktura.

5. Välj **överför förfrågningar** från den nedre vänstra sidan och välj sedan **Lägg till**.
 
   ![Skärm bild som visar val av överförings förfrågningar](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Ange e-postadressen till den användare som du begär fakturerings ägande från. Användaren måste vara konto administratör på ett Microsoft Online Service-programs fakturerings konto eller en konto ägare på en Enterprise-avtal. Mer information finns i [Visa dina fakturerings konton i Azure Portal](billing-view-all-accounts.md). Välj **Skicka överförings förfrågan**.

   ![Skärm bild som visar sändning av en överförings förfrågan](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. Användaren får ett e-postmeddelande med instruktioner för att granska din överförings förfrågan.

   ![Skärm bild som visar e-postbegäran om att granska överföring](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. För att godkänna överförings förfrågan väljer användaren länken i e-postmeddelandet och följer instruktionerna.

    ![Skärm bild som visar e-postbegäran om att granska överföring](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Kontrol lera status för överförings förfrågan

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **Cost Management + fakturering**.

   ![Skärm bild som visar Azure Portal Sök efter kostnads hantering + fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Välj **faktura avsnitt** från vänster sida. Beroende på åtkomst kan du behöva välja ett fakturerings konto eller en fakturerings profil. Välj **faktura avsnitt**på fakturerings kontot eller-profilen.
   
   ![Skärm bild som visar val av faktura avsnitt](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Välj faktura avsnittet i listan som du skickade överföringsbegäran för.

5. Välj **överförings begär Anden** från den nedre vänstra sidan. Sidan överförings förfrågningar visar följande information:

    ![Skärm bild som visar en lista över överförings begär Anden](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Kolumn|Definition|
   |---------|---------|
   |Datum för begäran|Datumet då överföringsbegäran skickades|
   |Mottagare|E-postadressen för den användare som du skickade begäran om att överföra fakturerings ägarskapet|
   |Upphörandedatum|Datumet då begäran upphör att gälla|
   |Status|Status för överföringsbegäran|

    Överföringsbegäran kan ha en av följande status:

   |Status|Definition|
   |---------|---------|
   |Pågår|Användaren har inte accepterat överförings förfrågan|
   |Bearbetning|Användaren godkände överföringsbegäran. Fakturering för prenumerationer som användaren har valt överförs till faktura avsnittet|
   |Slutfört| Faktureringen för de prenumerationer som användaren har valt överförs till faktura avsnittet|
   |Avslutades med fel|Begäran slutfördes men faktureringen för vissa prenumerationer som användaren valt kunde inte överföras|
   |Har upphört att gälla|Användaren accepterade inte begäran på den aktuella tiden och den har gått ut|
   |Avbrutna|Någon med åtkomst till överföringsbegäran avbröt begäran|
   |Avböjt|Användaren avböjde överföringsbegäran|

7. Välj en överförings förfrågan för att visa information. Sidan överförings information visar följande information:
   
   ![Skärm bild som visar en lista över överförda prenumerationer](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Kolumn  |Definition|
   |---------|---------|
   |ID för överförings förfrågan|Unikt ID för överföringsbegäran. Om du skickar en support förfrågan kan du dela med dig av ditt ID med Azure-supporten för att påskynda din support förfrågan|
   |Överföring begärd|Datumet då överföringsbegäran skickades|
   |Överföring begärd av|E-postadressen för den användare som skickade överföringsbegäran|
   |Överförings förfrågan upphör att gälla| Datumet då överföringsbegäran upphör att gälla|
   |Mottagarens e-postadress|E-postadressen för den användare som du skickade begäran om att överföra fakturerings ägarskapet|
   |Överförings länken skickades till mottagaren|Den URL som skickades till användaren för att granska överförings förfrågan|

## <a name="supported-subscription-types"></a>Prenumerations typer som stöds

Du kan begära fakturerings ägande av de prenumerations typer som anges nedan.

- [Åtgärds paket](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure i Open-licensiering](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure-pass sponsring](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise dev/test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Kostnads fri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure sponsrat erbjudande](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise-prenumeranter (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise-prenumeranter (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*All kredit som är tillgänglig för prenumerationen är inte tillgänglig i det nya kontot efter överföringen.

\*\*Stöds endast för prenumerationer i konton som skapas vid registreringen på Azure-webbplatsen.


## <a name="additional-information"></a>Ytterligare information

I följande avsnitt finns mer information om hur du överför prenumerationer.

### <a name="no-service-downtime"></a>Inget avbrott i tjänsten

Azure-tjänster i prenumerationen håller på att köras utan avbrott. Vi överlåter bara fakturerings relationen för de Azure-prenumerationer som användaren väljer att överföra.

### <a name="disabled-subscriptions"></a>Inaktiverade prenumerationer

Inaktiverade prenumerationer kan inte överföras. Prenumerationer måste vara i aktivt tillstånd för att överföra sin fakturerings ägande.

### <a name="azure-resources-transfer"></a>Överföring av Azure-resurser

Alla resurser från prenumerationer som virtuella datorer, diskar och webbplatser överförs.

### <a name="azure-marketplace-products-transfer"></a>Överföring av Azure Marketplace-produkter

Azure Marketplace-produkter överförs tillsammans med deras respektive prenumerationer.

### <a name="azure-reservations-transfer"></a>Azure Reservations överföring

Azure Reservations flyttas inte automatiskt med prenumerationer. [Kontakta Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) -supporten för att flytta reservationer.

### <a name="access-to-azure-services"></a>Åtkomst till Azure-tjänster

Åtkomst för befintliga användare, grupper eller tjänst huvud namn som har tilldelats med (Azure RBAC (rollbaserad åtkomst kontroll)) [. /Role-based-Access-Control/overview.MD] påverkas inte under över gången.

### <a name="azure-support-plan"></a>Azure-supportplan

Azure-support överförs inte med prenumerationerna. Om användaren överför alla Azure-prenumerationer ber du dem att säga upp support avtalet.

### <a name="charges-for-transferred-subscription"></a>Avgifter för överförd prenumeration

Den ursprungliga fakturerings ägaren för prenumerationerna ansvarar för eventuella kostnader som rapporter ATS fram till den tidpunkt då överföringen slutfördes. Faktura avsnittet ansvarar för avgifter som rapporteras från tiden för överföringen. Det kan finnas vissa kostnader som ägde rum före överföringen men som rapporter ATS efteråt. De här avgifterna visas i avsnittet faktura.

### <a name="cancel-a-transfer-request"></a>Avbryt en överförings förfrågan

Du kan avbryta överförings förfrågan tills begäran har godkänts eller nekats. Om du vill avbryta överförings förfrågan går du till [sidan överförings information](#check-the-transfer-request-status) och väljer Avbryt längst ned på sidan.

### <a name="software-as-a-service-saas-transfer"></a>Överföring av program vara som en tjänst (SaaS)

SaaS-produkter överförs inte med prenumerationerna. Be användaren att [kontakta Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) -supporten för att överföra fakturerings ägandet för SaaS-produkter. Tillsammans med fakturerings ägarskapet kan användaren också överföra resurs ägarskapet. Med resurs ägarskap kan du utföra hanterings åtgärder som att ta bort och Visa information om produkten. Användaren måste vara en resurs ägare på SaaS-produkten för att överföra resurs ägarskapet.

## <a name="check-for-access"></a>Kontrol lera åtkomst
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- Fakturerings ägandet av Azure-prenumerationerna överförs till faktura avsnittet. Håll koll på avgifterna för dessa prenumerationer i [Azure Portal](https://portal.azure.com).
- Ge andra behörigheter för att visa och hantera fakturering för dessa prenumerationer. Mer information finns i [avsnittet om roller och uppgifter för faktura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
