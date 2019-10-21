---
title: Visa och ladda ned information om användning och avgifter i Azure
description: Här beskrivs hur du laddar ned eller visar din dagliga användning och dina avgifter i Azure.
keywords: billing usage,usage charges, usage download, view usage, azure invoice,azure usage
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e7d1947b2194c04bb5269887b73e2f4fa13df6e7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375749"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visa och ladda ned information om din användning och dina avgifter i Azure

Du kan ladda ned en daglig uppdelning av din Azure-användning och dina kostnader från Azure-portalen. Det är endast vissa roller som har behörighet att hämta Azure-användningsinformation, bland annat kontoadministratören och företagsadministratören. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Om du har ett Microsoft-kundavtal (MCA) måste du vara ägare, deltagare, läsare eller fakturaansvarig för fakturaprofilen för att kunna visa användning och avgifter för Azure.  Om du har ett Microsoft-partneravtal (MPA) är det bara rollerna global administratör och administratörsrepresentant i partnerorganisationen som kan visa och ladda ned användning och avgifter för Azure. [Kontrollera typen av faktureringskonto i Azure-portalen](#check-your-billing-account-type).

## <a name="download-usage-from-the-azure-portal-csv"></a>Ladda ned användningsinformation från Azure-portalen (.csv)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.

    ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil.
1. Välj **Fakturor** under **Fakturering** i den vänstra menyn.
1. Leta rätt på raden i rutnätet för faktureringsperioden som motsvarar den användning du vill ladda ned.
1. Klicka på nedladdningsikonen eller ellipsen (`...`) till höger.
1. Välj **Hämta användning och avgifter för Azure** från nedladdningsmenyn.

## <a name="download-usage-for-ea-customers"></a>Ladda ned användningsdata för EA-kunder

Om du vill visa och ladda ned användningsdata som EA-kund måste du vara Enterprise-administratör, kontoägare eller avdelningsadministratör med principen för att visa avgifter aktiverad.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering + fakturering*.

    ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. Välj **Användning och avgifter**.
1. Välj **Ladda ned** för den månad du vill ladda ned.

## <a name="download-usage-for-pending-charges"></a>Ladda ned användning för väntande avgifter

Om du har ett Microsoft-kundavtal kan du ladda ned månadens användning fram till dagens datum för den aktuella faktureringsperioden. De här användningsavgifterna har ännu inte fakturerats.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter *Kostnadshantering + fakturering*.
3. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
4. I området **Översikt** finns nedladdningslänkarna nedanför avgifterna hittills under månaden.
5. Välj **Användning och avgifter för Azure**.

    ![Skärmbild som visar nedladdning från Översikt](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-billing-account-type"></a>Kontrollera typen av faktureringskonto
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om fakturor och användningsavgifter finns i:

- [Förstå termerna i den detaljerade informationen om Microsoft Azure-användning](billing-understand-your-usage.md)
- [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md)
- [Visa och ladda ned din Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och ladda ned din organisations Azure-priser](billing-ea-pricing.md)

Om du har ett Microsoft-kundavtal kan du läsa:

- [Förstå termerna i den detaljerade Azure-användningen för ditt Microsoft-kundavtal](billing-mca-understand-your-usage.md)
- [Förstå avgifterna på fakturan för ditt Microsoft-kundavtal](billing-mca-understand-your-bill.md)
- [Visa och ladda ned din Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och ladda ned skattedokument för ditt Microsoft-kundavtal](billing-mca-download-tax-document.md)
- [Visa och ladda ned din organisations Azure-priser](billing-ea-pricing.md)
