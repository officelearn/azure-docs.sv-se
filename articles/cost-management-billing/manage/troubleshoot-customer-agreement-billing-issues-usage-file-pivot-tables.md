---
title: Felsöka problem med MCA-faktureringen i Azure med hjälp av pivottabeller baserade på användningsfiler
description: Den här artikeln beskriver hur du felsöker problem med MCA-faktureringen (Microsoft-kundavtal) med hjälp av pivottabeller som skapats från dina CSV-användningsfiler.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 32c0779f4086574aeaf8d38ea675f80fbd2c1ec7
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132235"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>Felsöka problem med MCA-faktureringen med hjälp av pivottabeller baserade på användningsfiler

Den här artikeln beskriver hur du felsöker problem med MCA-faktureringen (Microsoft-kundavtal) med hjälp av pivottabeller baserade på dina användningsfiler. Azure-användningsfilerna innehåller all information om din användning och förbrukning i Azure. Med den här informationen blir det lättare att:

- Förstå hur Azure-reservationer används och tillämpas
- Stämma av information i Azure Cost Management med din faktura
- Felsöka kostnadstoppar
- Beräkna återbetalningsbelopp för servicenivåavtal

Informationen från användningsfilerna hjälper dig att förstå och diagnostisera användningsrelaterade problem. Användningsfiler genereras i kommaavgränsat format (CSV). Eftersom användningsfilerna kan vara stora CSV-filer är det lättare att ändra och visa dem som pivottabeller i ett kalkylbladsprogram som Excel. I exemplen i den här artikeln används Excel, men du kan använda valfritt kalkylbladsprogram.

Endast faktureringsprofilägare, deltagare, läsare eller fakturaansvariga har behörighet att ladda ned användningsfiler. Mer information finns i [Ladda ned användningsdata för ditt Microsoft-kundavtal](./download-azure-invoice-daily-usage-date.md#download-usage-for-your-microsoft-customer-agreement). 

## <a name="get-the-data-and-format-it"></a>Hämta data och formatera dem

Eftersom Azure-användningsfilerna har CSV-format måste du förbereda dina data för användning i Excel. Formatera data som en tabell genom att följa stegen nedan.

1. Ladda ned användningsfilen genom att följa anvisningarna i [Ladda ned användningsdata på Azure-portalen](./download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
1. Öppna filen i Excel.
1. I exemplet nedan ser du filen med oformaterade data.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. Markera det första fältet i tabellen, **invoiceID** (Faktura-ID).
1. Markera all information i tabellen genom att trycka på Ctrl+Skift+Nedpil följt av Ctrl+Skift+Högerpil.
1. Välj **Infoga** > **Tabell** på den översta menyn. Välj **Min tabell har rubriker** i rutan Skapa tabell och välj sedan **OK**.  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exempel som visar oformaterade data" :::
1. Välj **Infoga** > **Pivottabell** på den översta menyn och välj sedan **OK**. Nu skapas ett nytt blad i filen och pivottabellområdet till höger i bladet blir aktivt.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Området med pivottabellfält är ett ”dra och släpp”-område. Fortsätt till nästa avsnitt där du ska skapa pivottabellen.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Skapa en pivottabell som visar Azure-kostnader efter resurs

I det här avsnittet ska du skapa en pivottabell där du kan felsöka den övergripande Azure-användningen. Exempeltabellen hjälper dig att ta reda på vilken tjänst som använder flest resurser. Du kan också se vilka resurser som står för den högsta kostnaden och se hur en tjänst debiteras.

1. I området med pivottabellfält drar du **Meter Category** (Mätarkategori) och **Product** (Produkt) till avsnittet **Rader**. Placera **Product** (Produkt) nedanför **Meter Category** (Mätarkategori).  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Lägg sedan till kolumnen **costInBillingCurrenty** (Kostnad i faktureringsvaluta) i avsnittet **Värden**. Du kan också använda kolumnen **Quantity** (Kvantitet) i stället för att få information om förbrukningsenheter och transaktioner. Till exempel GB och timmar. Eller transaktioner i stället för kostnader i olika valutor, t.ex. USD, EUR och INR.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Nu har du en instrumentpanel som hjälper dig att undersöka den övergripande förbrukningen. Du kan filtrera fram en specifik tjänst med hjälp av filtreringsalternativen i pivottabellen.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Om du vill filtrera en andra nivå i en pivottabell, till exempel en resurs, väljer du ett objekt på den andra nivån i tabellen.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Dra kolumnen **ResourceID** (Resurs-ID) till området **Rader** under **Product** (Product) för att visa kostnaden för varje tjänst efter resurs.
1. Lägg till kolumnen **Date** (Datum) i området **Kolumner** för att visa den dagliga förbrukningen av produkten.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Visa och dölj månader med hjälp av **+** -symbolerna för kolumnen för respektive månad.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

Det är valfritt att lägga till båda kolumnerna **Cost** (Kostnad) och **Quantity** (Kvantitet) i området **Värden**. Om du gör det skapas två kolumner för varje dataavsnitt under varje månad och dag när kolumnen Date (Datum) finns i avsnittet Kolumner i pivottabellen.

Om du vill använda fler filter kan du lägga till InvoiceSection (Fakturaavsnitt), costCenter (Kostnadscenter), SubscriptionID (Prenumerations-ID), ResourceGroupName (Resursgruppens namn) eller Tags (Taggar) i filteravsnittet och välja önskat omfång.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Skapa en pivottabell som visar kostnaden för en specifik resurs

En enskild resurs kan medföra flera avgifter för olika tjänster. En virtuell dator kan till exempel medföra kostnader för beräkning, operativsystemlicensiering, bandbredd (dataöverföringar), RI-användning och lagring för ögonblicksbilder. Om du vill undersöka den övergripande användningen för specifika resurser följer du stegen nedan för att skapa en instrumentpanel som visar användningen baserat på dina användningsfiler.

1. På den högra menyn drar du **ResourceID** (Resurs-ID) till avsnittet **Filter** på pivottabellmenyn.
1. Välj den resurs som du vill visa kostnaden för. Sök efter ett resursnamn genom att skriva i **sökrutan**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. Lägg till **meterCategory** (Mätarkategori) och **Product** (Produkt) i området **Rader**. Placera **Product** (Produkt) nedanför **meterCategory** (Mätarkategori).  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Exempel som visar oformaterade data" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Lägg sedan till kolumnen **Extended Cost** (Utökad kostnad) i avsnittet **Värden**. Du kan också använda kolumnen Consumed Quantity (Använd kvantitet) i stället för att få information om förbrukningsenheter och transaktioner. Till exempel GB och timmar. Eller transaktioner i stället för kostnader i olika valutor, t.ex. USD, EUR och INR. Nu har du en instrumentpanel som visar alla tjänster som resursen förbrukar.
1. Lägg till kolumnen **Date** (Datum) i avsnittet **Kolumner**. Den dagliga förbrukningen visas.
1. Du kan visa och dölja med hjälp av **+** -ikonerna i kolumnen för varje månad.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Exempel som visar oformaterade data" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Nästa steg

- [Utforska och analysera kostnader med kostnadsanalys](../costs/quick-acm-cost-analysis.md).