---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: 9bed0a47c6c13b3c209f593509b71aa697c6eb4a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901728"
---
När du genomför en virtuell Azure-instans kan du spara pengar. Reservations rabatten tillämpas automatiskt på antalet virtuella datorer som körs och som matchar reservations omfånget och attributen. Du behöver inte tilldela en reservation till en virtuell dator för att få rabatterna. En reserverad instans köps bara täcker beräknings delen av din virtuella dator användning. Användnings mätaren delas upp i två separata meter för virtuella Windows-datorer. Det finns en beräknings mätare, som är samma som Linux-mätaren och en Windows IP-mätare. De kostnader som du ser när du gör köpet är bara för beräknings kostnader. Avgifter omfattar inte kostnader för Windows-programvara. Mer information om program varu kostnader finns i [program varu kostnader som inte ingår i Azure Reserved VM instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Fastställ rätt VM-storlek innan du köper

Innan du köper en reservation bör du bestämma storleken på den virtuella dator som du behöver. I följande avsnitt får du hjälp att avgöra rätt VM-storlek.

### <a name="use-reservation-recommendations"></a>Använd reservations rekommendationer

Du kan använda reservations rekommendationer för att avgöra vilka reservationer du bör köpa.

- Köp rekommendationer och Rekommenderad kvantitet visas när du köper en reserverad VM-instans i Azure Portal.
- Azure Advisor tillhandahåller inköps rekommendationer för enskilda prenumerationer.  
- Du kan använda API: erna för att få inköps rekommendationer för både delad omfattning och enskild prenumerations omfattning. Mer information finns i [reserverade instanser köp rekommendations-API: er för företags kunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- För Enterprise-avtal (EA) och Microsoft Customer Agreement (MCA) är inköps rekommendationer för delade och enkla prenumerations omfattningar tillgängliga med [Azure Consumption Insights Power BI innehålls paketet](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Tjänster som får rabatter för VM-reservation

Dina VM-reservationer kan gälla för VM-användning som skickas från flera tjänster – inte bara för dina VM-distributioner. Resurser som får reservationsrabatter varierar beroende på inställningen för flexibel instansstorlek.

#### <a name="instance-size-flexibility-setting"></a>Inställning för flexibel instansstorlek

Inställningen för flexibel instansstorlek avgör vilka tjänster som får rabatterna för reserverad instans.

Oavsett om inställningen är på eller av tillämpas reservationsrabatter automatiskt på matchande VM-användning när *ConsumedService* är `Microsoft.Compute`. Kontrollera därför dina användningsdata med avseende på värdet för *ConsumedService*. Några exempel är:

- Virtuella maskiner
- VM-skalningsuppsättningar
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

### <a name="analyze-your-usage-information"></a>Analysera din användnings information
Analysera användnings informationen för att avgöra vilka reservationer du bör köpa.

Användnings data är tillgängliga i användnings filen och API: erna. Använd dem tillsammans för att avgöra vilken reservation som ska köpas. Sök efter virtuella dator instanser som har hög användning dagligen för att fastställa antalet reservationer som ska köpas.

Undvik `Meter` under kategori och `Product` fält i användnings data. De skiljer inte mellan VM-storlekar som använder Premium Storage. Om du använder dessa fält för att fastställa storleken på den virtuella datorn för reservations köp kan du köpa fel storlek. Sedan får du inte den reservations rabatt som du förväntar dig. Läs i stället `AdditionalInfo`-fältet i användnings filen eller användnings-API: et för att fastställa rätt storlek på den virtuella datorn.

### <a name="purchase-restriction-considerations"></a>Överväganden vid köp av begränsningar

Reserverade VM-instanser är tillgängliga för de flesta VM-storlekar med vissa undantag. Reservations rabatter gäller inte för följande virtuella datorer:

- **VM-serien** – A-serien, AV2-serien eller G-serien.

- **Förhandsgranska eller kampanj-VM** – alla VM-serier eller storlekar som finns i för hands versionen eller använder kampanj mätare.

- **Moln** – reservationer är inte tillgängliga för köp i Tyskland eller regioner i Kina.

- **Otillräcklig kvot** – en reservation som är begränsad till en enskild prenumeration måste ha vCPU-kvot tillgänglig i prenumerationen för den nya RI. Om mål prenumerationen till exempel har en kvot gräns på 10 virtuella processorer för D-serien kan du inte köpa en reservation för 11 Standard_D1 instanser. Kvot kontrollen för reservationer omfattar de virtuella datorer som redan har distribuerats i prenumerationen. Om prenumerationen till exempel har en kvot på 10 virtuella processorer för D-serien och har två standard_D1-instanser distribuerade, kan du köpa en reservation för 10 standard_D1 instanser i den här prenumerationen. Du kan [skapa en begäran om offert ökning](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) för att lösa problemet.

- **Kapacitets begränsningar** – i sällsynta fall begränsar Azure inköpet av nya reservationer för en delmängd av VM-storlekar, på grund av låg kapacitet i en region.

## <a name="buy-a-reserved-vm-instance"></a>Köp en reserverad VM-instans

Du kan köpa en reserverad virtuell dator instans i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Betala för reservationen [i förväg eller via månadsbetalningar](../articles/billing/billing-monthly-payments-reservations.md).
Dessa krav gäller för att köpa en reserverad VM-instans:

- Du måste ha ägar rollen för minst en EA-prenumeration eller en prenumeration med en taxa enligt principen betala per användning.
- För EA-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör för prenumerationen.
- För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa reservationer.

Så här köper du en instans:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** > **Reservationer**.
1. Välj **Lägg till** för att köpa en ny reservation och klicka sedan på **virtuell dator**.
1. Fyll i obligatoriska fält. Om du kör virtuella datorinstanser som matchar de attribut du väljer gäller det för att hämta reservationsrabatten. Det faktiska antalet virtuella datorinstanser som erhåller rabatten beror på omfattning och vald kvantitet.

Om du har ett EA-avtal kan du använda **alternativet Lägg till fler** för att snabbt lägga till fler instanser. Alternativet är inte tillgängligt för andra prenumerations typer.


| Field      | Beskrivning|
|------------|--------------|
|Prenumeration|Prenumerationen som används för att betala för reservationen. Betalnings metoden för prenumerationen debiteras kostnaderna för reservationen. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft-kundavtal eller en enskild prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). Avgifterna dras av från betalnings åtagande beloppet, om det är tillgängligt eller debiteras som överanvändning. För en prenumeration med priser enligt principen betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.|    
|Omfång       |Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>**Omfång för enskild resursgrupp** – tillämpar reservationsrabatten på de matchande resurserna enkom i den valda resursgruppen.</li><li>**Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.</li><li>**Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För EA-kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med Betala per användning-priser är faktureringsomfånget alla berättigade prenumerationer som skapats av kontoadministratören.</li></ul>|
|Region    |Den Azure-region som omfattas av reservationen.|    
|VM-storlek     |Storleken på de virtuella dator instanserna.|
|Optimera för     |Flexibilitet för storlek på virtuell dator instans är markerad som standard. Klicka på **Avancerade inställningar** för att ändra storlek på flexibiliteten för instans storlek för att tillämpa reservations rabatten på andra virtuella datorer i samma [storleks grupp för virtuell dator](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Om du prioriterar kapaciteten prioriteras datacenterkapaciteten för dina distributioner. Det ger ytterligare förtroende för möjligheten att starta de virtuella dator instanserna när du behöver dem. Kapacitets prioritet är bara tillgängligt när reservations omfånget är en enskild prenumeration. |
|Period        |Ett år eller tre år.|
|Kvantitet    |Antalet instanser som köps i reservationen. Antalet är antalet aktiva VM-instanser som kan hämta fakturerings rabatten. Om du till exempel kör 10 Standard_D2 virtuella datorer i USA, kan du ange kvantitet som 10 för att maximera fördelarna med alla virtuella datorer som körs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Användnings data och reservations användning

Dina användningsdata har ett effektivt pris på noll för användning som täcks av reservationen. Du kan se vilken VM-instans som har tagit emot reservations rabatten för varje reservation.

Mer information om hur reservations rabatter visas i användnings data finns i [förstå Azure reservation-användning för företagets registrering](../articles/billing/billing-understand-reserved-instance-usage-ea.md) om du är en EA-kund. Om du har en enskild prenumeration kan du läsa mer i avsnittet om [hur du betalar per användning för din prenumeration enligt principen betala per användning](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Ändra en reservation efter köpet

Du kan göra följande typer av ändringar i en reservation efter köpet:

- Uppdatera reservationsomfång
- Flexibilitet för instans storlek (om tillämpligt)
- Äganderätt

Du kan också dela upp en reservation i mindre segment och slå samman redan delade reservationer. Ingen av ändringarna orsakar en ny kommersiell transaktion eller ändrar slut datumet för reservationen.

Du kan inte göra följande typer av ändringar efter köpet, direkt:

- En befintlig reservations region
- SKU
- Kvantitet
- Längd

Du kan dock *byta* ut en reservation om du vill göra ändringar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](../articles/billing/billing-manage-reserved-vm-instance.md).
- Du kan läsa mer om Azure-reservationer i följande artiklar:
    - [Vad är Azure-reservationer?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Hantera reservationer i Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Förstå hur reservationsrabatten tillämpas](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Förstå reservationsanvändning för en prenumeration med Betala per användning-priser](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Förstå reservationsanvändning för din Enterprise-registrering](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Kostnader för Windows-programvara ingår inte i reservationer](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)
