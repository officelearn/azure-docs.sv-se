---
title: ta med fil
description: ta med fil
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026737"
---
## <a name="transform-data-before-using-large-usage-files"></a>Transformera data innan du använder stora användningsfiler

Ibland är användnings- eller avstämningsfilen för stor för att kunna öppnas. Eller så kanske du bara behöver en del av informationen för att felsöka ett problem. Till exempel kanske du bara behöver information om en viss resurs, eller bara vill ha förbrukningen för särskilda tjänster eller resursgrupper. Innan du skapar en pivottabell kan du sammanfatta data genom att transformera dem.

1. Öppna en tom arbetsbok i Excel.
1. På den övre menyn väljer du **Data** > **Från text/CSV**, väljer din användningsfil och sedan **Importera**.
1. Välj **Transformera data** längst ned i fönstret. En sammanfattning av alla data visas i ett nytt fönster.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Exempel som visar sammanfattade data" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Om du har ett Microsoft-kundavtal hoppar du över det här steget och fortsätter till nästa steg eftersom de första raderna i MCA-användningsfiler vanligtvis innehåller kolumnrubriker. Förbered data genom att skapa tabellen. Ta bort de översta raderna och lämna bara rubrikerna. Välj **Ta bort rader** > **Ta bort de översta raderna**.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Exempel som visar sammanfattade data" :::
1. I fönstret Ta bort de översta raderna anger du hur många rader längst upp som ska tas bort. För EA är det vanligtvis 2, och för CSP är det oftast 1. Välj **OK**.
1. Välj **Använd första raden som rubriker**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Exempel som visar sammanfattade data" :::
    
    Tabellvyn visar kolumnrubriker överst.
1. Lägg sedan till ett filter. Filtrera genom att använda urvalspilarna till höger om varje kolumnrubrik. Använd till exempel filter för prenumerations-ID, tjänstnamn (mätarkategori), instans-ID och resursgrupp. Du kan använda flera filter i samma dokument. Vi rekommenderar att du använder flera filter för att minska dokumentstorleken och underlätta arbetet längre fram.
1. När du har tillämpat filtren väljer du **Stäng och läs in**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Exempel som visar sammanfattade data" :::

Filen blir inläst och visar en tabell med filtrerade användningsdata. Nu kan du skapa en ny pivottabell och felsöka användningsproblem.