---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/11/2019
ms.openlocfilehash: 766856438b22661b961bfbadc0b63376031622f6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850797"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Förskotts betalning för Virtual Machines med Azure Reserved VM Instances (RI)

Förskottsbetal för virtuella datorer och spara pengar med reserverade instanser för virtuella Azure-datorer (VM). Mer information finns i [Azure Reserved VM instances erbjudande](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Du kan köpa en reserverad virtuell dator instans i [Azure Portal](https://portal.azure.com). Så här köper du en instans:

- Du måste vara ägare av rollen för minst en företags prenumeration eller en prenumeration med en kostnad enligt principen betala per användning.
- För företags prenumerationer måste **Lägg till reserverade instanser** vara aktiverade i [EA-portalen](https://ea.azure.com). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
- För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa reservationer.

Reservations rabatten tillämpas automatiskt på antalet virtuella datorer som körs och som matchar reservations omfånget och attributen. Du kan uppdatera omfånget för reservationen genom [Azure Portal](https://portal.azure.com), POWERSHELL, CLI eller via API: et.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Fastställ rätt VM-storlek innan du köper

Innan du köper en reservation bör du bestämma storleken på den virtuella dator som du behöver. I följande avsnitt får du hjälp att avgöra rätt VM-storlek.

### <a name="use-reservation-recommendations"></a>Använd reservations rekommendationer

Du kan använda reservations rekommendationer för att avgöra vilka reservationer du bör köpa.

- Köp rekommendationer och Rekommenderad kvantitet visas när du köper en reserverad VM-instans i Azure Portal.
- Azure Advisor tillhandahåller inköps rekommendationer för enskilda prenumerationer.  
- Du kan använda API: erna för att få inköps rekommendationer för både delad omfattning och enskild prenumerations omfattning. Mer information finns i [reserverade instanser köp rekommendations-API: er för företags kunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- För EA-kunder är inköps rekommendationer för delade och enskilda prenumerations omfattningar tillgängliga med [Azure Consumption Insights Power BI innehålls paketet](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Tjänster som hämtar rabatter för VM-reservationer

Dina VM-reservationer kan gälla VM-användning som genereras från flera tjänster – inte bara för dina VM-distributioner. Resurser som erhåller reservations rabatter varierar beroende på storleks inställningen flexibilitet för instans storlek.

#### <a name="instance-size-flexibility-setting"></a>Inställning av flexibilitet för instans storlek

Inställningen för flexibilitet för instans storlek avgör vilka tjänster som hämtar de reserverade instans rabatterna.

Oavsett om inställningen är på eller av, gäller reservations rabatter automatiskt för matchande VM-användning när *ConsumedService* är `Microsoft.Compute`. Kontrol lera därför användnings data för *ConsumedService* -värdet. Några exempel är:

- Virtuella datorer
- Skalningsuppsättningar för virtuella datorer
- Container tjänst
- Azure Batch distributioner (i användar prenumerations läge)
- Azure Kubernetes Service (AKS)
- Service Fabric

När inställningen är på tillämpas reservations rabatter automatiskt på matchande VM-användning när *ConsumedService* är något av följande:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft. Kusto

Kontrol lera värdet *ConsumedService* i dina användnings data för att avgöra om användningen är berättigad till reservations rabatter.

Mer information om flexibilitet för instans storlek finns i [storleks flexibilitet för virtuella datorer med reserverade VM-instanser](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analysera din användnings information
Analysera användnings informationen för att avgöra vilka reservationer du bör köpa.

Användnings data är tillgängliga i användnings filen och API: erna. Använd dem tillsammans för att avgöra vilken reservation som ska köpas. Sök efter virtuella dator instanser som har hög användning dagligen för att fastställa antalet reservationer som ska köpas.

Undvik under kategori och `Product` fält i användnings data. `Meter` De skiljer inte mellan VM-storlekar som använder Premium Storage. Om du använder dessa fält för att fastställa storleken på den virtuella datorn för reservations köp kan du köpa fel storlek. Sedan får du inte den reservations rabatt som du förväntar dig. Läs i stället in i `AdditionalInfo` fältet i användnings filen eller användnings-API: et för att fastställa rätt storlek på den virtuella datorn.

### <a name="purchase-restriction-considerations"></a>Överväganden vid köp av begränsningar

Reserverade VM-instanser är tillgängliga för de flesta VM-storlekar med vissa undantag. Reservations rabatter gäller inte för följande virtuella datorer:

- **VM-serien** – A-serien, AV2-serien eller G-serien.

- **Virtuella datorer i för hands version** – alla VM-serier eller storlekar som är i för hands version.

- **Moln** – reservationer är inte tillgängliga för köp i Tyskland eller regioner i Kina.

- **Otillräcklig kvot** – en reservation som är begränsad till en enskild prenumeration måste ha vCPU-kvot tillgänglig i prenumerationen för den nya RI. Om mål prenumerationen till exempel har en kvot gräns på 10 virtuella processorer för D-serien kan du inte köpa en reservation för 11 Standard_D1-instanser. Kvot kontrollen för reservationer omfattar de virtuella datorer som redan har distribuerats i prenumerationen. Om prenumerationen till exempel har en kvot på 10 virtuella processorer för D-serien och har två standard_D1-instanser distribuerade kan du köpa en reservation för 10 standard_D1-instanser i den här prenumerationen. Du kan [skapa en begäran om offert ökning](../articles/azure-supportability/resource-manager-core-quotas-request.md) för att lösa problemet.

- **Kapacitets begränsningar** – i sällsynta fall begränsar Azure inköpet av nya reservationer för en delmängd av VM-storlekar, på grund av låg kapacitet i en region.

## <a name="buy-a-reserved-vm-instance"></a>Köp en reserverad VM-instans

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **alla tjänster** > **reservationer**.
1. Välj **Lägg till** för att köpa en ny reservation och klicka sedan på **virtuell dator**.
1. Ange obligatoriska fält. Kör virtuella dator instanser som matchar de attribut du väljer kvalificera för att hämta reservations rabatten. Det faktiska antalet virtuella dator instanser som erhåller rabatten beror på omfattning och vald kvantitet.

| Fält      | Beskrivning|
|------------|--------------|
|Subscription|Prenumerationen som används för att betala för reservationen. Betalnings metoden för prenumerationen debiteras för reservationens start kostnader. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller en enskild prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. För en prenumeration med priser enligt principen betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.|    
|Omfång       |Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>**Definitions område för enskild resurs grupp** – tillämpar reservations rabatten på de matchande resurserna enbart i den valda resurs gruppen.</li><li>**Enda prenumerations omfång** – tillämpar reservations rabatten på de matchande resurserna i den valda prenumerationen.</li><li>**Delat omfång** – tillämpar reservations rabatten på matchande resurser i berättigade prenumerationer som finns i fakturerings kontexten. För Enterprise-avtal kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med priser enligt principen betala per användning är fakturerings omfånget alla berättigade prenumerationer som skapats av konto administratören.</li></ul>|
|Region    |Den Azure-region som omfattas av reservationen.|    
|Storlek på virtuell dator     |Storleken på de virtuella dator instanserna.|
|Optimera för     |Flexibilitet för storlek på virtuell dator instans är markerad som standard. Klicka på **Avancerade inställningar** för att ändra storlek på flexibiliteten för instans storlek för att tillämpa reservations rabatten på andra virtuella datorer i samma [storleks grupp för virtuell dator](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Kapacitets prioritet prioriterar data centrets kapacitet för dina distributioner. Det ger ytterligare förtroende för möjligheten att starta de virtuella dator instanserna när du behöver dem. Kapacitets prioritet är bara tillgängligt när reservations omfånget är en enskild prenumeration. |
|Term        |Ett år eller tre år.|
|Kvantitet    |Antalet instanser som köps i reservationen. Antalet är antalet aktiva VM-instanser som kan hämta fakturerings rabatten. Om du till exempel kör 10 virtuella Standard_D2-datorer i USA, kan du ange kvantitet som 10 för att maximera fördelarna med alla virtuella datorer som körs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Ändra en reservation efter köpet

Du kan göra följande typer av ändringar i en reservation efter köpet:

- Uppdatera reservations omfång
- Flexibilitet för instans storlek (om tillämpligt)
- Kap

Du kan också dela upp en reservation i mindre segment och slå samman redan delade reservationer. Ingen av ändringarna orsakar en ny kommersiell transaktion eller ändrar slut datumet för reservationen.

Du kan inte göra följande typer av ändringar efter köpet, direkt:

- En befintlig reservations region
- SKU
- Kvantitet
- Duration

Du kan dock *byta* ut en reservation om du vill göra ändringar.

## <a name="cancellations-and-exchanges"></a>Avbokningar och byten

Om du vill avbryta din reservation kan det medföra en avgift på 12 % för uppsägning i förtid. Återbetalningar baseras på det lägsta priset – antingen ditt inköpspris eller det aktuella priset för reservationen. Återbetalningar begränsas till 50 000 USD per år. Din återbetalning består av det återstående proportionellt beräknade saldot minus uppsägningsavgiften på 12 %. Om du vill begära en annullering går du till reservationen i Azure Portal och väljer **åter betalning** för att skapa en supportbegäran.

Om du behöver ändra reservationen för reserverade VM-instanser till en annan region, storleks grupp för virtuell dator eller en period, kan du byta ut den. Exchange måste vara för en annan reservation som är lika med eller högre. Startdatumet för den nya reservationens överförs inte från den utbytte reservationen. En eller tre års perioden börjar från när du skapar den nya reservationen. Om du vill begära ett utbyte går du till reservationen i Azure Portal och väljer **Exchange** för att skapa en support förfrågan.

Mer information om hur du utbyter eller återbetalar reservationer finns i [reservations utbyte och åter betalningar](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [hantera Azure reservations](../articles/billing/billing-manage-reserved-vm-instance.md).
- Mer information om Azure Reservations finns i följande artiklar:
    - [Vad är Azure Reservations?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Hantera reservationer i Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Förstå hur reservations rabatten tillämpas](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Förstå reservations användningen för en prenumeration med priser för betala per användning](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Förstå reservations användningen för företags registreringen](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Windows-programkostnader som inte ingår i reservationer](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations i programmet Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
