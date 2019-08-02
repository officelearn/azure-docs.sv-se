---
title: Förstå användning av Azure-reservationer för Enterprise-avtal
description: Lär dig hur du läser din användning för att förstå hur Azure-reservationen för företags registreringen tillämpas.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598093"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Få Enterprise-avtal reservations kostnader och användning

Reservations kostnader och användnings data är tillgängliga för Enterprise-avtal kunder i Azure Portal och REST-API: er. Den här artikeln hjälper dig att:

- Hämta reservations inköps data
- Vet vilken prenumeration, resurs grupp eller resurs som använts för reservationen
- Åter betalning för reservations användning
- Beräkna reservations besparingar
- Hämta reservationer under användnings data
- Amortera reservations kostnader

Marketplace-kostnader samlas in i användnings data. Du kan visa avgifter för första parts användning, Marketplace-användning och köp från en enda data källa.

## <a name="reservation-charges-in-azure-usage-data"></a>Reservations avgifter i Azures användnings data

Data delas upp i två separata data uppsättningar: _Verklig kostnad_ och _periodiserad kostnad_. Hur de här två data uppsättningarna skiljer sig:

**Faktisk kostnad** – tillhandahåller data för att stämma av med din månads faktura. Dessa data har reservations inköps kostnader och reservations program information. Med dessa data kan du se vilken prenumeration eller resurs grupp eller resurs som har tagit emot reservations rabatten på en viss dag. EffectivePrice för den användning som tar emot reservations rabatten är noll.

Beräknad **kostnad** – den här data uppsättningen liknar den faktiska kostnads data uppsättningen, förutom att-EffectivePrice för användning som erhåller reservations rabatt är den beräknade kostnaden för reservationen (i stället för noll). Detta hjälper dig att känna till det monetära värdet av reservations förbrukningen för en prenumeration, en resurs grupp eller en resurs och kan hjälpa dig att debitera dig för reservations användningen internt. Data uppsättningen har även oanvända reservations timmar. Data uppsättningen saknar reservations inköps poster. 

Jämförelse av två data uppsättningar:

| Data | Faktisk kostnads data uppsättning | Data uppsättning för periodiserad kostnad |
| --- | --- | --- |
| Reservations köp | Tillgängligt i den här vyn.<br><br>  För att hämta det här data filtret på &quot;ChargeType&quot;= Purchase. <br><br> Se ReservationID eller ReservationName för att veta vilken reservation som avgiften avser.  | Inte tillämpligt för den här vyn. <br><br> Inköps kostnader tillhandahålls inte i periodiserade data. |
| EffectivePrice | Värdet är noll för användning som hämtar reservations rabatt. | Värdet är per timmes beräknade kostnad för reservationen för användning med reservations rabatten. |
| Oanvänd reservation (visar antalet timmar som reservationen inte användes under en dag och det monetära värdet för avfallet) | Inte tillämpligt i den här vyn. | Tillgängligt i den här vyn.<br><br> Filtrera på ChargeType = &quot;UnusedReservation&quot;för att hämta dessa data.<br><br>  Se ReservationID eller ReservationName för att veta vilken reservation som har underutnyttjats. Detta är hur mycket av reservationen som har avvisats för dagen.  |
| Enhets pris (pris för resursen från pris dokumentet) | Tillgängligt | Tillgängligt |

Annan information som är tillgänglig i Azures användnings data har ändrats:

- Produkt-och mätar information – Azure ersätter inte den ursprungligen förbrukade mätaren med ReservationId och ReservationName, precis som tidigare.
- ReservationId och ReservationName – de är egna fält i data. Tidigare användes den bara för att vara tillgänglig under AdditionalInfo.
- ProductOrderId – ID för reservations order, tillagt som ett eget fält.
- ProductOrderName – produkt namnet för den köpta reservationen.
- Term – 12 månader eller 36 månader.
- RINormalizationRatio – tillgänglig under AdditionalInfo. Detta är förhållandet där reservationen tillämpas på användnings posten. Om flexibiliteten för instans storleken är aktive rad för din reservation kan den gälla andra storlekar. Värdet visar förhållandet som reservationen tillämpades på för användnings posten.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Hämta användnings data för Azure-användning och reservationer med API

Du kan hämta data med hjälp av API: et eller ladda ned det från Azure Portal.

Du anropar [API:](/rest/api/consumption/usagedetails/list) et för användnings &quot;information med API&quot; -version 2019-04-01 – för hands version för att hämta nya data. Mer information om terminologi finns i [användnings villkor](billing-understand-your-usage.md). Anroparen måste vara företags administratör för Enterprise-avtalet med hjälp av [EA-portalen](https://ea.azure.com). Skrivskyddade företags administratörer kan också hämta data.

Data är inte tillgängliga i rapporterings- [API: er för företags kunder – användnings information](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Här är ett exempel på ett anrop till API: et:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Mer information om {enrollmentId} och {billingPeriodId} finns i artikeln [om användnings Detaljer – Visa](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API.

Information i följande tabell om mått och filter kan hjälpa dig att lösa vanliga reservations problem.

| **Typ av API-data** | API-anrops åtgärd |
| --- | --- |
| **Alla avgifter (användning och inköp)** | Ersätt {Metric} med ActualCost |
| **Användning som hämtade reservations rabatt** | Ersätt {Metric} med ActualCost<br><br>Ersätt {filter} med: Properties/reservationId% 20NE% 20 |
| **Användning som inte fick reservations rabatt** | Ersätt {Metric} med ActualCost<br><br>Ersätt {filter} med: Properties/reservationId% 20eq% 20 |
| **Periodiserade avgifter (användning och inköp)** | Ersätt {Metric} med AmortizedCost |
| **Oanvänd reservations rapport** | Ersätt {Metric} med AmortizedCost<br><br>Ersätt {filter} med: Properties/ChargeType% 20eq% 20 ' UnusedReservation ' |
| **Reservations köp** | Ersätt {Metric} med ActualCost<br><br>Ersätt {filter} med: Properties/ChargeType% 20eq% 20 "Purchase"  |
| **Åter betalningar** | Ersätt {Metric} med ActualCost<br><br>Ersätt {filter} med: Properties/ChargeType% 20eq% 20 ' återbetala ' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Hämta användnings filen CSV med nya data

Om du är EA-administratör kan du ladda ned den CSV-fil som innehåller nya användnings data från Azure Portal. Dessa data är inte tillgängliga från [EA-portalen](https://ea.azure.com).

I Azure Portal går du till [kostnads hantering + fakturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Välj fakturerings konto.
2. Klicka på **förbrukning + debitering**.
3. Klicka på **Hämta**.  
![Exempel som visar var du kan hämta CSV-filen med användnings data i Azure Portal](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. I **Hämta användning och kostnader** under **användnings information version 2** väljer du **alla avgifter (användning och inköp)** och klickar sedan på Ladda ned. Upprepa för **periodiserade avgifter (användning och inköp)** .

CSV-filerna som du hämtar innehåller faktiska kostnader och periodiserade kostnader.

## <a name="common-cost-and-usage-tasks"></a>Vanliga kostnader och användnings aktiviteter

Följande avsnitt är vanliga uppgifter som de flesta använder för att visa sina reservations kostnader och användnings data.

### <a name="get-reservation-purchase-costs"></a>Hämta kostnader för reservations inköp

Kostnader för reservations inköp är tillgängliga i faktiska kostnads data. Filter för _ChargeType = Purchase_. Se ProductOrderID för att avgöra vilken reservations ordning inköpet avser.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Hämta kvantitet och kostnader för underutnyttjad reservation

Hämta periodiserade kostnads data och filter för _ChargeType_ _= UnusedReservation_. Du får den dagliga outnyttjade reservations mängden och kostnaden. Du kan filtrera data för en reservations-eller reservations order med fälten _ReservationId_ respektive _ProductOrderId_ . Om en reservation var 100% använder posten en kvantitet på 0.

### <a name="amortize-reservation-costs"></a>Amortera reservations kostnader

Hämta periodiserade kostnads data och filtrera för en reservations order med _ProductOrderID_ för att få dagliga periodiserade kostnader för en reservation.

### <a name="chargeback-for-a-reservation"></a>Åter betalning för en reservation

Du kan återanvända reservationen för andra organisationer efter prenumeration, resurs grupper eller taggar. Periodiserade kostnads data ger ett penning värde för en reservations användning på följande data typer:

- Resurser (till exempel en virtuell dator)
- Resource group
- Tags
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>Hämta det blandade priset för åter betalning

För att fastställa den blandade frekvensen hämtar du de periodiserade kostnaderna och sammanställer den totala kostnaden. För virtuella datorer kan du använda antingen MeterName-eller ServiceType-information från AdditionalInfo JSON-data. Dividera den totala kostnaden med den kvantitet som används för att hämta det blandade priset.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Granska optimal reservations användning för storleks flexibilitet för instans

Flera kvantiteter med _RINormalizationRatio_, från AdditionalInfo. Resultaten visar hur många timmars användning av reservationer som tillämpats på användnings posten.

### <a name="determine-reservation-savings"></a>Fastställ reservations besparingar

Hämta data för de periodiserade kostnaderna och filtrera data för en reserverad instans. Dra

1. Få en uppskattad kostnad enligt principen betala per användning. Multiplicera värdet för _enhets pris_ med _kvantitet_ för att få en uppskattad kostnad enligt principen betala per användning, om reservations rabatten inte gäller för användningen.
2. Hämta reservations kostnaderna. Summera _kostnads_ värden för att få det monetära värdet för det du betalade för den reserverade instansen. Den innehåller reservationens använda och oanvända kostnader.
3. Dra tillbaka reservations kostnader från uppskattade kostnader för betala per användning för att få de uppskattade besparingarna.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Köp av reservationer och amorteringar i kostnads analys

Reservations kostnader är tillgängliga vid [kostnads analys](https://aka.ms/costanalysis). Som standard visar kostnads analys den **faktiska kostnaden**, vilket innebär hur kostnader visas på din faktura. Om du vill visa reservations köp som är uppdelat och associerat med de resurser som använde förmånen byter du till **periodiserad kostnad**:

![Exempel som visar var du väljer periodiserad kostnad i kostnads analys](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Gruppera efter avgifts typ för att se en uppdelning av användning, inköp och åter betalningar. eller efter reservation för en uppdelning av reservationer och kostnader på begäran. Kom ihåg att de enda reservations kostnader som du ser när du tittar på den faktiska kostnaden är inköp, men kostnader allokeras till de enskilda resurserna som använde benfit när du tittar på en periodiserad kostnad. Du ser också en ny **UnusedReservation** avgifts typ när du tittar på en periodiserad kostnad.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Reservations finns i följande artiklar:

- [Vad är Azure Reservations?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå hur reservations rabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå reservations användningen för din prenumeration enligt principen betala per användning](billing-understand-reserved-instance-usage.md)
- [Windows-programkostnader som inte ingår i reservationer](billing-reserved-instance-windows-software-costs.md)
