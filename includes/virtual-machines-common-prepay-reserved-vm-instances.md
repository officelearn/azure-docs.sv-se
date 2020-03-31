---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371646"
---
När du förbinder dig till en Azure-reserverad VM-instans kan du spara pengar. Reservationsrabatten tillämpas automatiskt på antalet virtuella datorer som körs som matchar reservationsomfånget och attributen. Du behöver inte tilldela en reservation till en virtuell dator för att få rabatter. Ett reserverat instansköp täcker endast beräkningsdelen av den virtuella datorns användning. För virtuella Windows-datorer delas användningsmätaren upp i två separata mätare. Det finns en beräkningsmätare, som är samma som Linux-mätaren, och en Windows IP-mätare. De avgifter som du ser när du gör köpet är endast för beräkningskostnaderna. Avgifter inkluderar inte Windows-programkostnader. Mer information om programvarukostnader finns i [Programvarukostnader som inte ingår i Azure Reserved VM Instances](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Fastställ rätt VM-storlek innan du köper

Innan du köper en reservation bör du bestämma storleken på den virtuella datorn som du behöver. Följande avsnitt hjälper dig att avgöra rätt vm-storlek.

### <a name="use-reservation-recommendations"></a>Använd reservationsrekommendationer

Du kan använda reservationsrekommendationer för att avgöra vilka reservationer du ska köpa.

- Inköpsrekommendationer och rekommenderad kvantitet visas när du köper en reserverad vm-instans i Azure-portalen.
- Azure Advisor innehåller köprekommendationer för enskilda prenumerationer.  
- Du kan använda API:erna för att få köprekommendationer för både delat scope och enstaka prenumerationsomfattning. Mer information finns i [API:er för reserverad instansför köprekommendation för företagskunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- För kunder i Enterprise Agreement (EA) och Microsoft Customer Agreement (MCA) är köprekommendationer för delade och enskilda prenumerationsomfattningar tillgängliga med [Azure Consumption Insights Power BI-innehållspaketet](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Tjänster som får rabatter för VM-reservation

Dina VM-reservationer kan gälla för VM-användning som skickas från flera tjänster – inte bara för dina VM-distributioner. Resurser som får reservationsrabatter varierar beroende på inställningen för flexibel instansstorlek.

#### <a name="instance-size-flexibility-setting"></a>Inställning för flexibel instansstorlek

Inställningen för flexibel instansstorlek avgör vilka tjänster som får rabatterna för reserverad instans.

Oavsett om inställningen är på eller av tillämpas reservationsrabatter automatiskt på matchande VM-användning när *ConsumedService* är `Microsoft.Compute`. Kontrollera därför dina användningsdata med avseende på värdet för *ConsumedService*. Några exempel är:

- Virtuella datorer
- Skalningsuppsättningar för virtuella datorer
- Containertjänst
- Azure Batch-distributioner (i användarprenumerationsläge)
- Azure Kubernetes Service (AKS)
- Service Fabric

När inställningen är på tillämpas reservationsrabatter automatiskt på matchande VM-användning när *ConsumedService* är något av följande:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Kontrollera värdet för *ConsumedService* i dina användningsdata för att fastställa huruvida användningen är berättigad till reservationsrabatter.

Mer information om flexibel instansstorlek finns i artikeln om [flexibel storlek för virtuella datorer med reserverade VM-instanser](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analysera din användningsinformation

Analysera din användningsinformation för att avgöra vilka reservationer du ska köpa. Användningsdata är tillgängliga i användningsfilen och API:erna. Använd dem tillsammans för att avgöra vilken reservation du ska köpa. Kontrollera om vm-instanser som har hög användning dagligen för att bestämma antalet reservationer som ska köpas. Undvik `Meter` underkategorin och `Product` fälten i användningsdata. De skiljer inte mellan vm-storlekar som använder premiumlagring. Om du använder dessa fält för att bestämma storleken på den virtuella datorn för reservationsköp kan du köpa fel storlek. Då får du inte den bokningsrabatt du förväntar dig. I stället, `AdditionalInfo` se fältet i din användningsfil eller användning API för att fastställa rätt VM-storlek.

Din användningsfil visar dina avgifter efter faktureringsperiod och daglig användning. Information om hur du hämtar användningsfilen finns i [Visa och ladda ned din Azure-användning och dina avgifter](../articles/cost-management-billing/understand/download-azure-daily-usage.md). Sedan, genom att använda användningsfilinformationen, kan du [bestämma vilken reservation som ska köpas](../articles/cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Köpbegränsningsöverväganden

Reserverade VM-instanser är tillgängliga för de flesta VM-storlekar med vissa undantag. Reservationsrabatter gäller inte för följande virtuella datorer:

- **VM-serien** - A-serien, Av2-serien eller G-serien.

- **Förhandsgranska eller befordra virtuella datorer** – Alla virtuella datorer eller storlekar som används i förhandsversionen eller använder kampanjmätare.

- **Moln** - Bokningar är inte tillgängliga för köp i Tyskland eller Kina regioner.

- **Otillräcklig kvot** - En reservation som är begränsad till en enda prenumeration måste ha vCPU-kvot tillgänglig i prenumerationen för den nya RI.Otillräcklig kvot - En reservation som är begränsad till en enda prenumeration måste ha vCPU-kvot tillgänglig i prenumerationen för den nya RI.Otillräcklig kvot - En reservation som är begränsad till en enda prenumeration måste ha vCPU-kvot tillgänglig i prenumerationen för Om målprenumerationen till exempel har en kvotgräns på 10 virtuella processorer för D-serien kan du inte köpa en reservation för 11 Standard_D1 instanser. Kvotkontrollen för reservationer inkluderar de virtuella datorer som redan har distribuerats i prenumerationen. Om prenumerationen till exempel har en kvot på 10 virtuella processorer för D-serien och har två standard_D1 instanser som distribueras, kan du köpa en reservation för 10 standard_D1 instanser i den här prenumerationen. Du kan [skapa begäran om ökning av offertförhöjning](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) för att lösa problemet.

- **Kapacitetsbegränsningar** – I sällsynta fall begränsar Azure inköp av nya reservationer för delmängd av VM-storlekar, på grund av låg kapacitet i en region.

## <a name="buy-a-reserved-vm-instance"></a>Köp en reserverad VM-instans

Du kan köpa en reserverad VM-instans i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Betala för reservationen [i förväg eller via månadsbetalningar](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).
Dessa krav gäller för att köpa en reserverad VM-instans:

- Du måste ha en ägarroll för minst en EA-prenumeration eller en prenumeration med en användningsbaserad betalning.
- För EA-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.
- För CSP-programmet (Cloud Solution Provider) kan endast administratörsagenter eller försäljningsagenter köpa reservationer.

Så här köper du en instans:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj Alla > **servicereservationer**. **All services**
1. Välj **Lägg till** om du vill köpa en ny reservation och klicka sedan på Virtuell **dator**.
1. Fyll i obligatoriska fält. Om du kör virtuella datorinstanser som matchar de attribut du väljer gäller det för att hämta reservationsrabatten. Det faktiska antalet virtuella datorinstanser som erhåller rabatten beror på omfattning och vald kvantitet.

Om du har ett EA-avtal kan du använda **alternativet Lägg till fler** för att snabbt lägga till ytterligare instanser. Alternativet är inte tillgängligt för andra prenumerationstyper.


| Field      | Beskrivning|
|------------|--------------|
|Prenumeration|Prenumerationen användes för att betala för bokningen. Betalningsmetoden för prenumerationen debiteras kostnaderna för bokningen. Prenumerationstypen måste vara ett företagsavtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft Kundavtal eller en individuell prenumeration med abonnemang som gäller (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P). Avgifterna dras av från det monetära åtagandesaldot, om tillgängligt, eller debiteras som överfall. För en prenumeration med användningsbaserad betalning debiteras avgifterna till kreditkorts- eller fakturabetalningsmetoden för prenumerationen.|    
|Omfång       |Reservationens omfattning kan omfatta en prenumeration eller flera prenumerationer (delat scope). Om du väljer: <ul><li>**Omfång för enskild resursgrupp** – tillämpar reservationsrabatten på de matchande resurserna enkom i den valda resursgruppen.</li><li>**Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.</li><li>**Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För EA-kunder är faktureringskontexten registreringen. För enskilda prenumerationer med Betala per användning-priser är faktureringsomfånget alla berättigade prenumerationer som skapats av kontoadministratören.</li></ul>|
|Region    |Den Azure-region som omfattas av reservationen.|    
|Storlek på virtuell dator     |Storleken på VM-instanserna.|
|Optimera för     |Flexibilitet för VM-instansstorlek är markerad som standard. Klicka på **Avancerade inställningar** om du vill ändra flexibilitetsvärdet för instansstorlek för att tillämpa reservationsrabatten på andra virtuella datorer i samma [storleksgrupp](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)för virtuella datorer . Om du prioriterar kapaciteten prioriteras datacenterkapaciteten för dina distributioner. Det ger ytterligare förtroende för din förmåga att starta VM-instanser när du behöver dem. Kapacitetsprioritet är endast tillgänglig när reservationsomfattningen är en enda prenumeration. |
|Period        |Ett år eller tre år.|
|Kvantitet    |Antalet instanser som köps i reservationen. Kvantiteten är antalet vm-instanser som körs som kan få faktureringsrabatten. Om du till exempel kör 10 Standard_D2 virtuella datorer i östra USA anger du kvantitet som 10 för att maximera förmånen för alla virtuella datorer som körs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Användningsdata och reservationsanvändning

Dina användningsdata har ett effektivt pris på noll för användning som täcks av reservationen. Du kan se vilken VM-instans som fick reservationsrabatten för varje reservation.

Mer information om hur reservationsrabatter visas i användningsdata finns i [Förstå Azure-reservationsanvändning för din Enterprise-registrering](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) om du är EA-kund. Om du har en enskild prenumeration läser du [Förstå Azure-reservationsanvändning för din prenumeration på användningsbaserad betalning](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Ändra en reservation efter köpet

Du kan göra följande typer av ändringar i en reservation efter köpet:

- Uppdatera reservationsomfång
- Instansstorleksflexibilitet (om tillämpligt)
- Ägarskap

Du kan också dela upp en reservation i mindre segment och sammanfoga redan delade reservationer. Ingen av ändringarna orsakar en ny affärstransaktion eller ändrar slutdatum för reservationen.

Du kan inte göra följande typer av ändringar efter köpet direkt:

- En befintlig reservation region
- SKU
- Kvantitet
- Varaktighet

Du kan dock *byta* en reservation om du vill göra ändringar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Du kan läsa mer om Azure-reservationer i följande artiklar:
    - [Vad är Azure-reservationer?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Hantera reservationer i Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Förstå hur reservationsrabatten tillämpas](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Förstå reservationsanvändning för en prenumeration med Betala per användning-priser](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Förstå reservationsanvändning för din Enterprise-registrering](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Windows-programvarukostnader ingår inte i bokningar](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)
