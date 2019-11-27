---
title: Vanliga kostnads analyser används i Azure Cost Management
description: Den här artikeln förklarar hur du kan få resultat för vanliga kostnads analys uppgifter i Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/16/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 01087f3e40e278490abb87d0ab3b7b6ab5052b6b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219494"
---
# <a name="common-cost-analysis-uses"></a>Vanliga kostnads analyser används

Azure Cost Management användare ofta vill ha svar på frågor som många andra ber. Den här artikeln vägleder dig genom att få resultat för vanliga kostnads analys uppgifter i Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Visa kostnads uppdelning efter Azure-tjänst

Genom att Visa kostnader med en Azure-tjänst kan du bättre förstå de delar av infrastrukturen som kostar mest. Beräknings kostnader för virtuella datorer kan till exempel vara små. Du kan behöva Periodisera avsevärda nätverks kostnader på grund av mängden information som sänds från de virtuella datorerna. Det är viktigt att förstå de primära kostnads driv rutinerna för dina Azure-tjänster så att du kan justera tjänst användningen efter behov.

1. I Azure Portal navigerar du till kostnads analys för ditt omfång. Exempel: **Cost Management + fakturerings** > **Cost Management** > **kostnads analys**.
1. Välj **kostnad per tjänst** och sedan gruppera efter **tjänst nivå**.
1. Ändra vyn till **tabell**.

![Kostnads uppdelning per Azure-tjänst](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Visa kostnads uppdelning per Azure-resurs

Dina tjänster har skapats med Azure-resurser. Att granska kostnader baserat på resurser kan hjälpa dig att snabbt identifiera dina primära kostnads bidrags givare. Om en tjänst har resurser som är för dyra bör du överväga att göra ändringar för att minska kostnaderna.

1. I Azure Portal navigerar du till kostnads analys för ditt omfång. Exempel: **Cost Management + fakturerings** > **Cost Management** > **kostnads analys**.
1. Välj **kostnad per resurs**.
1. Ändra vyn till **tabell**.

![Visa kostnads uppdelning per Azure-resurs](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Visa kostnads uppdelning per valda dimensioner

Med dimensionerna kan du organisera dina kostnader baserat på olika metadata-värden som visas i dina avgifter. Du kan till exempel gruppera dina kostnader efter plats.

1. I Azure Portal navigerar du till kostnads analys för ditt omfång. Exempel: **Cost Management + fakturerings** > **Cost Management** > **kostnads analys**.
1. Välj **Group by** -filtret.  
    ![välja en gruppera efter objekt](./media/cost-analysis-common-uses/group-by.png)
1. Alternativt kan du spara vyn för senare användning.
1. Klicka på ett cirkel diagram under grafen om du vill visa mer detaljerade data.  
    ![Visa kostnads uppdelning per valda dimensioner](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Visa kostnader per dag eller per månad

Att titta på dagliga och månatliga kostnader kan hjälpa dig att bättre förstå om det finns en tid på veckan eller året där kostnaderna är högre. Leder det till en motsvarande ökning av dina Azure-kostnader om du har mer kund trafik under en semester period? Är fredag en dyrare dag än måndag?

1. I Azure Portal navigerar du till kostnads analys för ditt omfång. Exempel: **Cost Management + fakturerings** > **Cost Management** > **kostnads analys**.
1. Ange **granularitet** till **varje månad** eller **varje dag**.

![Visa kostnader per dag](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Visa kostnader för en speciell tagg

Många Azure-användare använder taggar till sina resurser, till exempel ett kostnads ställe eller en utvecklings miljö (produktion och test) för att bättre kategorisera kostnader. Taggar visas som en dimension i kostnads analys. Du kan använda dimensionen för att få insikter om dina egna taggnings kategoriseringar.

1. I Azure Portal navigerar du till kostnads analys för ditt omfång. Exempel: **Cost Management + fakturerings** > **Cost Management** > **kostnads analys**.
1. Välj **Gruppera efter** för taggen.

![Visa kostnader för en speciell tagg](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Hämta din användnings information

Rapport filen med användnings information, i CSV-format, innehåller en uppdelning av alla avgifter som debiteras mot en faktura. Du kan använda rapporten för att jämföra den med och bättre förstå din faktura. Varje fakturerad avgift på fakturan motsvarar brutna kostnader i användnings rapporten.

1. I Azure Portal navigerar du till fliken **användning och avgifter** för ett fakturerings konto eller en prenumeration. Exempel: **Cost Management + fakturerings** > **fakturering** > **användning + kostnader**.
1. Välj det rad objekt som du vill ladda ned från och klicka sedan på nedladdnings symbolen.  
    ![Hämta användning och avgifter](./media/cost-analysis-common-uses/download1.png)
1.  Välj den användnings fil som ska laddas ned.  
    ![Välj en användnings fil som ska laddas ned](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Visa månatlig kostnads uppdelning per månad

Din EA-registrering påförs kostnader för hela organisationen. Att förstå hur kostnader påförs och faktureras över tid hjälper dig att engagera lämpliga intressenter för att säkerställa att kostnaderna hanteras ett ansvarsfullt sätt.

1. I Azure Portal navigerar du till **Cost Management + fakturerings** > **Översikt**.
1. Klicka på **nedbrytning** för den aktuella månaden och Visa ditt åtagande att bränna.  
    Översikt över ![EA-kostnader – Sammanfattning](./media/cost-analysis-common-uses/breakdown1.png)
1.  Klicka på fliken **användning och avgifter** och visa den föregående månadens analys under det valda tidsintervallet.  
    fliken ![användning och debitering](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Visa registrering månads kostnad per period

Använd en grafisk vy över din registrerings månads kostnad för att förstå kostnads trender och fakturerade belopp under en viss period.

1. I Azure Portal navigerar du till kostnads analys för ditt omfång. Exempel: **Cost Management + fakturerings** > **Cost Management** > **kostnads analys**.
1. Välj din registrering och ange registrerings villkor.
1. Ange granularitet till varje månad och ställ sedan in vyn till **kolumn (staplad)** .

Du kan gruppera efter och filtrera dina data för en mer detaljerad analys.

![Månatlig registrerings kostnad per period](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Visa ackumulerade kostnader för EA-registrering

Se de netto kostnader som ackumulerats med tiden för att förstå övergripande utgifter för din organisation under en viss period.

1. I Azure Portal navigerar du till kostnads analys för ditt omfång. Exempel: **Cost Management + fakturerings** > **Cost Management** > **kostnads analys**.
1. Välj din registrering och Visa dina aktuella ackumulerade kostnader.

![Kostnader för ackumulerad registrering](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Nästa steg
- Om du inte redan har slutfört den första snabb starten för Cost Management läser du den för att [börja analysera kostnaderna](quick-acm-cost-analysis.md).
- Läs [Cost Management-dokumentationen](index.yml).
