---
title: Visa och ladda ned information om användning och avgifter i Azure
description: Lär dig hur du laddar ned och visar daglig användning och avgifter, och visar fler tillgängliga resurser.
keywords: billing usage, usage charges, usage download, view usage, azure invoice, azure usage
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 141da3dfab9996110952ec266733271582e66ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439199"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visa och ladda ned information om din användning och dina avgifter i Azure

Du kan ladda ned en daglig uppdelning av din Azure-användning och dina kostnader från Azure-portalen. Det är endast vissa roller som har behörighet att hämta Azure-användningsinformation, bland annat kontoadministratören och företagsadministratören. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](../manage/manage-billing-access.md).

Om du har ett Microsoft-kundavtal (MCA) måste du vara ägare, deltagare, läsare eller fakturaansvarig för fakturaprofilen för att kunna visa användning och avgifter för Azure.  Om du har ett Microsoft-partneravtal (MPA) är det bara rollerna global administratör och administratörsrepresentant i partnerorganisationen som kan visa och ladda ned användning och avgifter för Azure. [Kontrollera typen av faktureringskonto i Azure-portalen](#check-your-billing-account-type).

Beroende på vilken typ av prenumeration du har varierar alternativen för att ladda ned användnings- och kostnadsinformation.

## <a name="download-usage-from-the-azure-portal-csv"></a>Ladda ned användningsinformation från Azure-portalen (.csv)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.  
    ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering.](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil.
1. Välj **Fakturor** under **Fakturering** i den vänstra menyn.
1. Leta rätt på raden i rutnätet för faktureringsperioden som motsvarar den användning du vill ladda ned.
1. Välj **nedladdningsikonen** eller ellipsen (`...`) till höger.  
  ![Skärmbild som visar sidan Fakturor i Kostnadshantering + fakturering med nedladdningsalternativ.](./media/download-azure-daily-usage/download-usage-others.png)  
1. Nedladdningsfönstret öppnas till höger. Välj **Ladda ned** i avsnittet **Användningsinformation**.  

## <a name="download-usage-for-ea-customers"></a>Ladda ned användningsdata för EA-kunder

Om du vill visa och ladda ned användningsdata som EA-kund måste du vara Enterprise-administratör, kontoägare eller avdelningsadministratör med principen för att visa avgifter aktiverad.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.  
    ![Skärmbild som visar en sökning i Azure-portalen.](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Välj **Användning och avgifter**.
1. Välj **Ladda ned** för den månad du vill ladda ned.  
    ![Skärmbild som visar sidan Fakturor i Kostnadshantering + fakturering för EA-kunder.](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-pending-charges"></a>Ladda ned användning för väntande avgifter

Om du har ett Microsoft-kundavtal kan du ladda ned månadens användning fram till dagens datum för den aktuella faktureringsperioden. De här användningsavgifterna har ännu inte fakturerats.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter *Kostnadshantering + fakturering*.
3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
4. I området **Översikt** finns nedladdningslänkarna under de senaste ändringarna.
5. Välj **Ladda ned användning och prissättning**.  
    :::image type="content" source="./media/download-azure-daily-usage/open-usage01.png" alt-text="Skärmbild som visar nedladdning från Översikt" lightbox="./media/download-azure-daily-usage/open-usage01.png" :::

## <a name="check-your-billing-account-type"></a>Kontrollera typen av faktureringskonto
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om fakturor och användningsavgifter finns i:

- [Förstå termerna i den detaljerade informationen om Microsoft Azure-användning](understand-usage.md)
- [Förstå fakturan för Microsoft Azure](review-individual-bill.md)
- [Visa och ladda ned din Microsoft Azure-faktura](download-azure-invoice.md)
- [Visa och ladda ned din organisations Azure-priser](../manage/ea-pricing.md)

Om du har ett Microsoft-kundavtal kan du läsa:

- [Förstå termerna i den detaljerade Azure-användningen för ditt Microsoft-kundavtal](mca-understand-your-usage.md)
- [Förstå avgifterna på fakturan för ditt Microsoft-kundavtal](review-customer-agreement-bill.md)
- [Visa och ladda ned din Microsoft Azure-faktura](download-azure-invoice.md)
- [Visa och ladda ned skattedokument för ditt Microsoft-kundavtal](mca-download-tax-document.md)
- [Visa och ladda ned din organisations Azure-priser](../manage/ea-pricing.md)
