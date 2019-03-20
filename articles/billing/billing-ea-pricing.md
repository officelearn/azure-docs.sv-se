---
title: Visa och hämta din organisations Azure priser | Microsoft Docs
description: Lär dig mer om att visa och hämta priser eller beräkna dina kostnader med priser för din organisation.
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d4c64433089f14d845cea8be9adf0ef13675cfd1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892480"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Visa och hämta din organisations Azure-prissättning

Azure-kunder med ett Enterprise Agreement (EA) eller [Microsoft kundavtal](#check-your-access-to-a-microsoft-customer-agreement) kan visa och hämta sina priser från Azure-portalen. Om du har ett Microsoft-kundavtal Se Visa och ladda ned priserna för ditt Microsoft-kundavtal.

## <a name="view-and-download-ea-pricing"></a>Visa och hämta EA-priser

Beroende på vilka principer som angetts för din organisation av företagsadministratören ger endast vissa administrativa roller åtkomst till din organisations EA prisinformation. Mer information finns i [förstå Azure Enterprise-avtal administrativa roller i Azure](billing-understand-ea-roles.md).

1. Som en Enterprise-administratör kan logga in på [Azure-portalen](https://portal.azure.com/).
1. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Välj under faktureringskonto, **användning och kostnader**.

   ![Skärmbild som visar användning och avgifter under fakturering](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Välj ![skärmbild som visar Azure portal-sökning](./media/billing-ea-pricing/download-icon.png) **hämta** för månaden.
1. Under **Prisdokument**väljer **ladda ned csv**.

   ![Skärmbild som visar prisdokumentet ladda ned csv-knappen](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>Visa och ladda ned priserna för ditt Microsoft-kundavtal

Du måste vara den fakturering profilens ägare, deltagare, läsare eller faktura manager att visa och hämta priser. Läs mer om fakturering administratörsroller för Microsoft Customer avtal i [fakturering profil roller och uppgifter](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Ladda ned prisdokument för den aktuella faktureringsperioden

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök på **Cost Management + fakturering**.
1. Välj en profil för fakturering. Du kan behöva välja ett faktureringskonto först beroende på din åtkomst.
1. I den **översikt** bladet hitta nedladdningslänkarna under månaden-till-date-avgifter.
1. Välj **Azure prisdokument**.
![Skärmbild som visar nedladdning från översikt](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Ladda ned prisdokument för fakturerade kostnader

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök på **Cost Management + fakturering**.
1. Välj en profil för fakturering. Du kan behöva välja ett faktureringskonto först beroende på din åtkomst.
1. Välj **fakturor**.
1. Hitta raden motsvarar prisdokument som du vill hämta fakturans i rutnätet för fakturan.
1. Klicka på ellipsknappen (`...`) i slutet av raden.
![Skärmbild som visar de tre punkterna valt](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Om du vill se priserna för tjänster i den valda fakturan väljer **faktura prisdokument**.
1. Om du vill se priser för alla Azure-tjänster för den angivna faktureringsperioden väljer **Azure prisdokument**.

![Skärmbild som visar snabbmenyn med prisdokument](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Beräkna kostnader med Azures priskalkylator

Du kan också använda organisationens priser uppskatta kostnader med Azures priskalkylator.

1. Gå till den [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator).
1. Längst upp till höger, Välj **logga in**.
1. Under **program och erbjudandet** > **licensieringsprogrammet**väljer **Enterprise Agreement (EA)**.
1. Under **program och erbjudandet** > **valt avtal**väljer **inga valda**.

    ![Skärmbild som visar prisdokumentet ladda ned csv-knappen](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Välj vilken organisation.
1. Välj **Använd**.
1. Sök och Lägg till produkter i beräkningen.
1. Beräknad priserna baseras på priserna för den organisation som du har valt.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Kontrollera din åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Nästa steg

Om du är EA-kund, se:

- [Hantera åtkomst till din EA-faktureringsinformation för Azure](billing-manage-access.md)
- [Visa och ladda ned Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och hämta dina Microsoft Azure-användning och kostnader](billing-download-azure-daily-usage.md)
- [Förstå fakturan för Enterprise Agreement-kunder](billing-understand-your-bill-ea.md)

Om du har ett Microsoft-kundavtal, se:

- [Förstå villkoren i ditt prisdokument för ett Microsoft-kundavtal](billing-mca-understand-pricesheet.md)
- [Visa och ladda ned Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och hämta dina Microsoft Azure-användning och kostnader](billing-download-azure-daily-usage.md)
- [Visa och hämta skattedokument för din faktureringsinformation profil](billing-mca-download-tax-document.md)
- [Förstå debiteringar på din faktureringsinformation profil faktura](billing-mca-understand-your-bill.md)
