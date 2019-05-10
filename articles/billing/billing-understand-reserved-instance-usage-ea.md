---
title: Förstå användningen av Azure reservationer för Enterprise-avtal
description: Lär dig mer om att läsa din användning för att förstå hur Azure reservation för Enterprise-registreringen tillämpas.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: 8d85dd1c21f952261e838c01843e15dafcc0e931
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415760"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Hämta Enterprise-avtal reservation kostnader och användning

Reservation kostnader och användningsdata är tillgängliga för Enterprise Agreement-kunder i Azure-portalen och REST API: er. Den här artikeln hjälper dig att:

- Hämta data för köp av reservation
- Hämta reserverade under användningsdata
- Amortera reservation kostnader
- Återbetalning för reservation användning
- Beräkna reservation besparingar

Marketplace-avgifter konsolideras i användningsdata. Du kan visa avgifter för den första part användning, marketplace användning och inköp från en enskild datakälla.

## <a name="reservation-charges-in-azure-usage-data"></a>Reservation kostnader på Azure användningsdata

Data är uppdelad i två olika datauppsättningar: _Verklig kostnad_ och _Amorterad kostnad_. Hur dessa två datamängder skiljer sig:

**Verklig kostnad** -innehåller data för att stämma av med din månatliga faktura. Dessa data har reservation baskostnaderna. Den har noll EffectivePrice för användning som tog emot reservationsrabatten.

**Amorterad kostnad** -resursen EffectiveCost som får rabatten är proportionell kostnaden för den reserverade instansen. Datauppsättningen har också oanvända reserverade kostnader. Summan av reservationskostnad och oanvända reserverade innehåller dagliga amorterade kostnaden för reservationen.

Jämförelse av två datauppsättningar:

| Data | Faktisk kostnad datauppsättning | Amorterad kostnad datauppsättning |
| --- | --- | --- |
| Köp av reservation | Tillgängligt i den här vyn.<br>  Att hämta datafiltret på ChargeType = &quot;köp&quot;. <br> Referera till ReservationID eller ReservationName veta vilka reservation avgiften gäller för.  | Ej tillämpligt för den här vyn. <br> Köp kostnader tillhandahålls inte i amorterade data. |
| EffectivePrice | Värdet är noll för användning som får rabatten. | Värdet är per timme-beräknas kostnaden för reservation för användning med reservationsrabatten. |
| Oanvända reserverade (ger antalet timmar som de reservationen inte har använts under en dag och värdet av avfallet) | Ej tillämpligt i den här vyn. | Tillgängligt i den här vyn.<br> För att få dessa data kan filtrera på ChargeType = &quot;UnusedReservation&quot;.<br>  Referera till ReservationID eller ReservationName veta vilka reservation har varit underutnyttjade. Detta är hur mycket av reservationen har gått i för dagen.  |
| Enhetspris (pris på resursen från din prisdokument) | Tillgänglig | Tillgänglig |

Annan information som är tillgängliga i Azure-användningsdata har ändrats:

- Produkt- och information om mätnings - Azure ersätter inte ursprungligen förbrukade mätaren med ReservationId och ReservationName, som det gjorde tidigare.
- ReservationId och ReservationName – de är sina egna fält i datamängden. Tidigare såg det endast tillgänglig i AdditionalInfo.
- ProductOrderId - reservation order-ID läggs till som ett eget fält.
- ProductOrderName - den köpta reservationen produktnamn.
- Villkor - 12 månader eller 36 månader.
- RINormalizationRatio - tillgängliga under AdditionalInfo. Detta är förhållandet mellan där reservationen ska gälla i posten för användning. Om instansen storlek flexibilitet aktiveras på för din reservation, kan den tillämpas på andra storlekar. Värdet visas förhållandet att reservationen har tillämpats på för posten för användning.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Hämta Azure förbrukning och reservation användningsdata med hjälp av API

Du kan hämta data med hjälp av API: et eller ladda ned det från Azure-portalen.

Du anropar den [användning information API](/rest/api/consumption/usagedetails/list) med API-versionen &quot;2019-04-01-preview&quot; att hämta nya data. Mer information om terminologi finns [Användarvillkor](billing-understand-your-usage.md). Anroparen måste vara en företagsadministratör för enterprise-avtal med det [EA-portalen](https://ea.azure.com). Skrivskyddad Företagsadministratörer kan också hämta data.

Data är inte tillgänglig i [Reporting API: er för företagskunder - användningsinformation](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Här är en exempel-anrop till API:

```
https://consumption.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Mer information om {enrollmentId} och {billingPeriodId}, finns det [användningsinformation – lista](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API-artikel.

Med hjälp av informationen i följande tabell om mått och filter kan du lösa vanliga problem för reservation.

| **Typ av API-data** | Åtgärd för API-anrop |
| --- | --- |
| **Alla avgifter (förbrukning och inköp)** | Ersätt {mått} med ActualCost |
| **Användning som har fått reservationsrabatten** | Ersätt {mått} med ActualCost<br>Ersätt {filter} med: properties/reservationId%20ne%20 |
| **Användning som inte fick reservationsrabatten** | Ersätt {mått} med ActualCost<br>Ersätt {filter} med: properties/reservationId%20eq%20 |
| **Amorterade avgifter (förbrukning och inköp)** | Ersätt {mått} med AmortizedCost |
| **Oanvända reserverade rapport** | Ersätt {mått} med AmortizedCost<br>Ersätt {filter} med: properties/ChargeType%20eq%20'UnusedReservation' |
| **Köp av reservation** | Ersätt {mått} med ActualCostReplace {filter} med: properties/ChargeType%20eq%20'Purchase'  |
| **Återbetalningar** | Ersätt {mått} med ActualCost<br>Ersätt {filter} med: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Hämta CSV-fil för användning med nya data

Om du är en EA-administratör kan hämta du den CSV-fil som innehåller nya användningsdata från Azure-portalen. Dessa data är inte tillgängligt från den [EA-portalen](https://ea.azure.com).

I Azure-portalen går du till [kostnadshantering + fakturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Välj faktureringskontot.
2. Klicka på **användning och kostnader**.
3. Klicka på **Hämta**.  
![Exempel som visar var du hämtar datafilen för CSV-användning i Azure portal](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. I **ladda ned användning och kostnader** under **användning information Version 2** väljer **alla avgifter (förbrukning och inköp)** och klicka sedan på Hämta. Upprepa detta för **Amorterad avgifter (förbrukning och inköp)**.

CSV-filer som hämtas innehåller faktiska kostnader och amorterade.

## <a name="common-cost-and-usage-tasks"></a>Vanliga uppgifter för kostnader och användning

I följande avsnitt är vanliga uppgifter som de flesta använder för att visa sina reservation kostnader och användning.

### <a name="get-reservation-purchase-costs"></a>Få reservation köp kostnader

Kostnader för köp av reservation är tillgängliga i verklig kostnad-data. Filtrera efter _ChargeType = köp_. Referera till ProductOrderID att avgöra vilka reservationsbeställning inköpet.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Hämta underutnyttjade Reservationskvantitet och kostnader

Hämta data för Amorterad kostnad och filtrera efter _ChargeType_ _= UnusedReservation_. Du får den dagliga oanvända Reservationskvantitet och kostnaden. Du kan filtrera data för reservation eller reservation ordning med hjälp av _ReservationId_ och _ProductOrderId_ respektive fält. Om en reservation har 100% används, innehåller posten en mängd 0.

### <a name="amortize-reservation-costs"></a>Amortera reservation kostnader

Hämta data för Amorterad kostnad och filter för en reservation ordning med hjälp av _ProductOrderID_ att hämta daglig amorterade kostnader för en reservation.

### <a name="chargeback-for-a-reservation"></a>Återbetalning för en reservation

Du kan återbetalning reservation användning till andra organisationer efter prenumeration, resursgrupp eller taggar. Amorterad kostnadsdata innehåller värdet av en reservation användning på följande datatyper:

- Resurser (t.ex en virtuell dator)
- Resursgrupp
- Taggar
- Prenumeration

### <a name="get-the-blended-rate-for-chargeback"></a>Hämta blandat frekvensen för återbetalning

Hämta data amorterade kostnader och sammanställa den totala kostnaden för att fastställa den blandat hastigheten. Du kan använda antingen MeterName eller ServiceType information från AdditionalInfo JSON-data för virtuella datorer. Dela upp den totala kostnaden med det antal som används för att hämta den blandat hastigheten.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Granska optimala reservation Använd till exempel storleken flexibilitet

Flera kvantitet med den _RINormalizationRatio_, från AdditionalInfo. Resultatet anger hur många timmar reservation användning har tillämpats på posten för användning.

### <a name="determine-reservation-savings"></a>Fastställa reservation besparingar

Hämta data för Amortized kostnader och filtrera data för en reserverad instans. Sedan:

1. Få uppskattade kostnader för betala per användning. Multiplicera det _enhetspris_ värde med _kvantitet_ värden för att hämta beräknade användningsbaserad kostnader om reservationsrabatten inte gäller för användningen.
2. Få reservation-kostnader. Summan i _kostnaden_ värden för att hämta värdet av vad du har betalat för den reserverade instansen. Den innehåller de använda och oanvända kostnaderna för reservationen.
3. Subtrahera reservation kostnader från beräknade användningsbaserad kostnader att få uppskattade besparingar.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>Köp av reservation och amorteringar i Azure kostnadsanalys

Kostnad för reserverad instans är tillgänglig i [förhandsgranskningsläge för Azure cost analysis](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis). Som standard är datavyn kostnaden för faktisk kostnad. Du kan växla till amorterad kostnad. Här är ett exempel.

![Exempel som visar var du väljer amorterad kostnad i kostnadsanalys](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Använda filter för att se dina kostnader för reserverade eller laddning specificeras. Gruppera efter namn på reservation att se kostnaderna uppdelade efter reservationer.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)
