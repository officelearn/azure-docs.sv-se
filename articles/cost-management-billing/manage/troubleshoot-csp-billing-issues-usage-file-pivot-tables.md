---
title: Felsöka problem med CSP-faktureringen med hjälp av pivottabeller baserade på användningsfiler
description: Den här artikeln beskriver hur du felsöker problem med CSP-faktureringen (Azure-molnlösningsleverantör) med hjälp av pivottabeller som skapats från dina CSV-användningsfiler.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026872"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Felsöka problem med CSP-faktureringen med hjälp av pivottabeller baserade på användningsfiler

Den här artikeln beskriver hur du felsöker problem med CSP-faktureringen (Azure-molnlösningsleverantör) med hjälp av pivottabeller från dina avstämningsfiler (användningsfiler) i Partnercenter. Azure-användningsfilerna innehåller all information om din användning och förbrukning i Azure. Med den här informationen blir det lättare att:

- Förstå hur Azure-reservationer används och tillämpas
- Stämma av information i Azure Cost Management med din faktura
- Felsöka kostnadstoppar
- Beräkna återbetalningsbelopp för servicenivåavtal

Informationen från användningsfilerna hjälper dig att förstå och diagnostisera användningsrelaterade problem. Användningsfiler genereras i kommaavgränsat format (CSV). Eftersom användningsfilerna kan vara stora CSV-filer är det lättare att ändra och visa dem som pivottabeller i ett kalkylbladsprogram som Excel. I exemplen i den här artikeln används Excel, men du kan använda valfritt kalkylbladsprogram.

Endast faktureringsadministratörer och globala administratörer har behörighet att ladda ned avstämningsfiler. Mer information finns i [Läsa radobjekt från avstämningsfiler i Partnercenter](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Hämta data och formatera dem

Eftersom Azure-användningsfilerna har CSV-format måste du förbereda dina data för användning i Excel. Formatera dina data som en tabell genom att följa stegen nedan.

1. Ladda ned användningsfilen genom att följa anvisningarna i [Hitta din faktura](/partner-center/read-your-bill#find-your-bill).
1. Öppna filen i Excel.
1. I exemplet nedan ser du filen med oformaterade data.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Markera det första fältet i tabellen, **PartnerID** (Partner-ID).
1. Markera all information i tabellen genom att trycka på Ctrl+Skift+Nedpil följt av Ctrl+Skift+Högerpil.
1. Välj **Infoga** > **Tabell** på den översta menyn. Välj **Min tabell har rubriker** i rutan Skapa tabell och välj sedan **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exempel som visar oformaterade data i Excel" :::
1. Välj **Infoga** > **Pivottabell** på den översta menyn och välj sedan **OK**. Nu skapas ett nytt blad i filen och pivottabellområdet till höger i bladet blir aktivt.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Området med pivottabellfält är ett ”dra och släpp”-område. Fortsätt till nästa avsnitt där du ska skapa pivottabellen.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Skapa en pivottabell som visar Azure-kostnader efter resurs

I det här avsnittet ska du skapa en pivottabell där du kan felsöka den övergripande Azure-användningen. Exempeltabellen hjälper dig att ta reda på vilken tjänst som använder flest resurser. Du kan också se vilka resurser som står för den högsta kostnaden och se hur en tjänst debiteras.

1. Dra **Service Name** (Tjänstnamn) och **Resource** (Resurs) i området med pivottabellfält till området **Rader**. Placera **Resource** (Resurs) nedanför **Service Name** (Tjänstnamn).  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Placera sedan **Post-Tax Total** (Totalt efter skatt) i området **Värden**. Du kan också använda kolumnen Consumed Quantity (Använd kvantitet) i stället för att få information om förbrukningsenheter och transaktioner. Till exempel GB och timmar. Eller transaktioner i stället för kostnader i olika valutor, t.ex. USD, EUR och INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Nu har du en instrumentpanel som hjälper dig att undersöka den övergripande förbrukningen. Du kan filtrera fram en specifik tjänst med hjälp av filtreringsalternativen i pivottabellen.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Om du vill filtrera en andra nivå i en pivottabell, till exempel en resurs, väljer du ett objekt på den andra nivån i tabellen.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Om du vill tillämpa fler filter kan du lägga till **SubscriptionID** (Prenumerations-ID) och **Customer Company Name** (Kundens företagsnamn) i **filterområdet** och välja önskat omfång.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Skapa en pivottabell som visar Azure-användning efter datum

I det här avsnittet ska du skapa en pivottabell där du kan felsöka den övergripande Azure-användningen efter använd kvantitet och datum. Det kan vara bra att identifiera faktureringstoppar efter datum och tjänst. Du kan också se vilka resurser som står för den högsta kostnaden och se hur en tjänst debiteras.

Din avstämningsfil innehåller två tabeller: en längst upp (huvudtabellen) och en till längst ned i dokumentet. Mycket av informationen är samma i båda tabellerna, men den andra tabellen innehåller ingen pris- eller kostnadsinformation. Den innehåller däremot användningsdatum och använd kvantitet.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Följ samma steg som i avsnittet [Hämta data och formatera dem](#get-the-data-and-format-it) för att skapa en Excel-tabell med informationen längst ned i avstämningsfilen.
1. När tabellen är klar och du har ett blad med en pivottabell förbereder du instrumentpanelen genom att följa samma steg som i avsnittet create-pivot-table-to-view-azure-costs-by-resources. I stället för att använda Post-Tax Total (Totalt efter skatt) placerar du **Consumed quantity** (Använd kvantitet) i området **Värden**.
1. Lägg till **användningsdatum** i avsnittet Kolumner. Pivottabellen bör se ut som i följande exempel.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Nu har du en instrumentpanel som visar användningen per datum. Du kan utöka varje månad genom att välja **+** -symbolen.

På instrumentpanelen visas den förbrukade kvantiteten i enheter som GB, timmar och överföringar.

Om du vill visa priset per dag kan du lägga till **Resource GUID** (Resurs-GUID) i området **Rader**. I den övre tabellen lägger du till enhetspriset (**ListPrice**) för resursen. Multiplicera **ListPrice** (Listpris) med **Consumed quantity** (Använd kvantitet) för att beräkna dina avgifter före skatt. Beloppen bör matcha varandra.

Den del resurser (tjänster) har prisskalor som baseras på använd kvantitet. Till exempel har vissa resurser ett högre pris för de första 100 GB som förbrukas, och ett lägre pris för ytterligare GB. Ha prisskalorna i åtanke när du beräknar kostnader manuellt.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Skapa en pivottabell som visar kostnaden för en specifik resurs

En enskild resurs kan medföra flera avgifter för olika tjänster. En virtuell dator kan till exempel medföra kostnader för beräkning, operativsystemlicensiering, bandbredd (dataöverföringar), RI-användning och lagring av ögonblicksbilder. Om du vill undersöka den övergripande användningen för specifika resurser följer du stegen nedan för att skapa en instrumentpanel som visar användningen baserat på dina användningsfiler.

Avstämningsfiler innehåller inte resursspecifik information. Därför använder du den sammanställda, eller aggregerade, användningsfilen. Kontakta [Azures faktureringssupport](https://go.microsoft.com/fwlink/?linkid=2083458) och be om en sammanställd användningsfil för din prenumeration. Sammanställda filer genereras på prenumerationsnivå. I exemplet nedan ser du filen med oformaterade data.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

Tabellen innehåller följande kolumner:

- **UsageStart** (Startdatum för användning) och **UsageEnd** (Slutdatum för användning) – Datum för varje radobjekt (varje användningsenhet). Till exempel varje dag.
- **MeteredResourceID** (ID för avläst resurs) – I Azure motsvarar det mätar-ID:t.
- **Properties** (Egenskaper) – Innehåller instans-ID:t (resursnamn) med annan information, till exempel plats.
- **Quantity** (Kvantitet) – Använd kvantitet i avstämningsfilen.

1. Markera det första fältet i tabellen, **PartnerID** (Partner-ID).  
1. Markera all information i tabellen genom att trycka på Ctrl+Skift+Nedpil följt av Ctrl+Skift+Högerpil.
1. Välj **Infoga** > **Tabell** på den översta menyn. Välj **Min tabell har rubriker** i rutan Skapa tabell och välj sedan **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exempel som visar oformaterade data i Excel" :::
1. Välj **Infoga** > **Pivottabell** på den översta menyn och välj sedan **OK**. Nu skapas ett nytt blad i filen och pivottabellområdet till höger i bladet blir aktivt.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Lägg sedan till **MeteredResourceID** (ID för avläst resurs) i området **Rader** och **Quantity** (Kvantitet) i **Värden**. Resultaten visar den övergripande användningsinformationen. Om du vill visa ytterligare information placerar du **UsageEndDateTime** (Slutdatum för användning) i området **Kolumner**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Exempel som visar oformaterade data i Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Om du vill visa en översiktsrapport lägger du till **Properties** (Egenskaper) i **Rader** under **MeteredResourceID** (ID för avläst resurs). Den visar en komplett instrumentpanel för din användning.
1. Om du vill filtrera efter en specifik resurs lägger du till **Properties** (Egenskaper) i området **Filter** och väljer önskad användning. Du kan använda Sök för att hitta ett resursnamn.
    Om du vill visa kostnaden för resursen letar du reda på den totala använda kvantiteten och multiplicerar värdet med listpriset. Listpriset är specifikt för varje resurs-GUID (MeteredResourceID). Om en resurs förbrukar flera MeteredResourceID:n skriver du ned det totala värdet för varje ID.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure Cost Management för partner](../costs/get-started-partners.md).