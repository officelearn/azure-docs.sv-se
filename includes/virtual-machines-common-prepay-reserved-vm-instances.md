---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/13/2019
ms.openlocfilehash: d9b9aae8bea323e5aac74a2e317b82d4cb43568f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118894"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser

Förskottsbetala för virtuella datorer och spara pengar med Azure reserverade virtuella datorn (VM)-instanser. Mer information finns i [Azure Reserved VM Instances erbjudande](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Du kan köpa en reserverad VM-instans den [Azure-portalen](https://portal.azure.com). Att köpa en instans:

- Du måste vara i en ägarrollen för minst en Enterprise eller användningsbaserad betalning.
- För Enterprise-prenumerationer, **lägga till reserverade instanser** måste aktiveras i den [EA-portalen](https://ea.azure.com). Eller, om den här inställningen har inaktiverats kan du måste vara en EA-administratör för prenumerationen.
- Efter programmet Cloud Solution Provider (CSP) köpa bara admin-agenter eller försäljning agents reservationer.

Reservationsrabatten tillämpas automatiskt på antalet virtuella datorer som matchar reservationsomfånget och attribut som körs. Du kan uppdatera reservationens via omfång [Azure-portalen](https://portal.azure.com), PowerShell, CLI, eller via API: et.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Fastställa rätt VM-storleken innan du köper

Innan du köper en reservation, bör du fastställa storleken på den virtuella datorn som du behöver. I följande avsnitt hjälper dig att fastställa rätt VM-storlek.

### <a name="use-reservation-recommendations"></a>Använd rekommendationer för reservation

Du kan använda reservationen rekommendationer för att avgöra de ska du köpa reservationer.

- Rekommendationer för köp och rekommenderade kvantitet är visar när du köper en reserverad VM-instans i Azure-portalen.
- Azure Advisor tillhandahåller rekommendationer för köp för enskilda prenumerationer.  
- Du kan använda API: erna för att hämta rekommendationer för köp för både delade scope och scope för enskild prenumeration. Mer information finns i [reserverad instans köp rekommendation API: er för företagskunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- För EA-kunder kan köpa rekommendationer för delade och enstaka prenumeration scope är tillgängliga med den [Azure Consumption Insights Power BI-Innehållspaketet](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="classic-vms-and-cloud-services"></a>Klassiska virtuella datorer och molntjänster

Reserverade instanser av virtuella datorer gäller för både klassiska virtuella datorer och molntjänster när instansen storlek flexibilitet aktiveras automatiskt. Det finns inte någon särskild SKU: er för klassiska virtuella datorer eller molntjänster. Samma VM SKU: er gäller för dem.

Exempelvis kan du konvertera ditt klassiska virtuella datorer eller molntjänster till Azure Resource Manager-baserade virtuella datorer. I det här exemplet tillämpas reservationsrabatten automatiskt på matchande virtuella datorer. Det finns inget behov av att *exchange* en befintlig reserverad instans – den tillämpas automatiskt.

### <a name="analyze-your-usage-information"></a>Analysera din användningsinformation
Du ska analysera dina användningsinformation för att bestämma vilka ska du köpa reservationer.

Användningsdata är tillgängliga i användningsfil och API: er. Använda dem tillsammans för att avgöra vilka reservation om du vill köpa. Du bör kolla för VM-instanser som har hög användning på daglig basis att avgöra hur många reservationer att köpa.

Undvik den `Meter` underkategori och `Product` fält i användningsdata. De skilja inte mellan VM-storlekar som använder premium storage. Om du använder de här fälten för att fastställa VM-storleken för reservationsköp, kan du köpa fel storlek. Sedan kommer du inte reservationsrabatten som du förväntar dig. I stället referera till den `AdditionalInfo` i din användningsfil eller användning API för att fastställa rätt VM-storlek.

### <a name="purchase-restriction-considerations"></a>Köp begränsning överväganden

Reserverade VM-instanser är tillgängliga för de flesta storlekar för Virtuella datorer med vissa undantag. Reservation rabatter gäller inte för följande virtuella datorer:

- **VM-serie** -A-serien, Av2-serien och G-serien.

- **Virtuella datorer i en förhandsversion** -alla VM-serien och storlekar som är en förhandsversion.

- **Moln** -reservationer är inte tillgängligt att köpa i Tyskland eller Kina regioner.

- **Otillräcklig kvot** – en reservation som är begränsad till en enda prenumeration måste ha vCPU-kvoten som är tillgängliga i prenumerationen för den nya RI. Till exempel om målprenumerationen har en kvot högst 10 virtuella processorer för D-serien, köpa sedan du inte en reservation för den 11 Standard_D1 instanser. Kvoten ingår för reservationer de virtuella datorerna som redan har distribuerats i prenumerationen. Till exempel om prenumerationen har en kvot på 10 virtuella processorer för D-serien och har två standard_D1 instanser som distribuerats kan köpa du en reservation för den 10 standard_D1 instanser i den här prenumerationen. Du kan [citationstecken skapa begäran om ökning](../articles/azure-supportability/resource-manager-core-quotas-request.md) att lösa problemet.

- **Kapacitet begränsningar** – i sällsynta fall kan Azure-gränser inköp av ny reservationer för delmängd av VM-storlekar, på grund av låg kapacitet i en region.

## <a name="buy-a-reserved-vm-instance"></a>Köp en reserverad VM-instans

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj **Lägg till** att köpa en ny reservation.
4. Fyll i fälten som krävs. Köra VM-instanser som matchar de attribut som du väljer kvalificera sig för att få rabatten. Det faktiska antalet dina VM-instanser som får rabatten är beroende av omfång och kvantitet som väljs.

    | Fält      | Beskrivning|
    |------------|--------------|
    |Name        |Namnet på den här reservationen.|
    |Prenumeration|Den prenumeration som används för att betala för reservationen. Betalningsmetoden för prenumerationen debiteras startavgifter för reservationen. Prenumerationstypen måste vara ett enterprise-avtal (erbjuder siffror: MS-AZR-0017P eller MS-AZR - 0148 P) eller betala per användning (erbjuder siffror: MS-AZR-0003P eller MS-AZR - 0023 P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.|    
    |Scope       |Den reservationsomfånget kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>Enstaka prenumeration - reservationsrabatten tillämpas på virtuella datorer i den här prenumerationen. </li><li>Delad – tillämpas reservationsrabatten på virtuella datorer som körs i alla prenumerationer i din faktureringskontexten. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.</li></ul>|
    |Region    |Azure-regionen som omfattas av reservationen.|    
    |Storlek på virtuell dator     |Storleken på de Virtuella datorinstanserna.|
    |Optimera för     |Flexibilitet för Virtuella datorinstanser storlek tillämpas reservationsrabatten på andra virtuella datorer i samma [VM storleksgruppen](https://aka.ms/RIVMGroups). Prioritet för datacenterkapacitet prioriterar datacentrets kapacitet för dina distributioner. Det ger ytterligare förtroende i din möjlighet att starta VM-instanser när så behövs. Kapacitetsprioritet är bara tillgänglig när reservationsomfånget är enstaka prenumeration. |
    |Period        |Ett eller tre år.|
    |Kvantitet    |Antalet instanser som köpts i reservationen. Antalet är antalet VM-instanser som kan få fakturering rabatten som körs. Till exempel om du kör 10 Standard_D2 virtuella datorer i östra USA, skulle du ange antalet som 10 om du vill maximera förmån för alla datorer som körs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Ändra en reservation efter köpet

Du kan göra följande typer av ändringar till en reservation efter köpet:

- Uppdatera reservationsomfånget
- Flexibilitet för datorinstanser storleken (om tillämpligt)
- Ägarskap

Du kan också dela upp en reservation i mindre segment och sammanfoga redan dela reservationer. Inga ändringar som orsakar en ny affärstransaktion eller ändra slutdatum för reservationen.

Du kan inte ändra följande typer av ändringar efter köpet direkt:

- En befintlig reservation region
- SKU
- Kvantitet
- Varaktighet

Du kan dock *exchange* en reservation om du vill göra ändringar.

## <a name="cancellations-and-exchanges"></a>Avbokningar och utbyten

Om du vill avbryta din reservation kan det medföra en avgift på 12 % för uppsägning i förtid. Återbetalningar baseras på det lägsta priset – antingen ditt inköpspris eller det aktuella priset för reservationen. Återbetalningar begränsas till 50 000 USD per år. Din återbetalning består av det återstående proportionellt beräknade saldot minus uppsägningsavgiften på 12 %. Om du vill begära ett avbrott, går du till reservationen i Azure-portalen och välj **återbetala** att skapa en supportbegäran.

Om du behöver ändra din Reserved VM Instances-reservation till en annan region, storleksgrupp eller period kan du byta till en annan reservation med samma eller högre värde. Startdatumet för den nya reservationens överförs inte från den utbytte reservationen. 1 eller 3 år termen startar från när du skapar den nya reservationen. Om du vill begära en exchange, gå till reservationen i Azure-portalen och välj **Exchange** att skapa en supportbegäran.

Läs mer om hur du exchange- eller återbetalning reservationer [Reservation utbyte och återbetalningar](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Läs hur du hanterar en reservation i [hantera Azure-reservationer](../articles/billing/billing-manage-reserved-vm-instance.md).
- Om du vill veta mer om Azure reservationer, finns i följande artiklar:
    - [Vad är Azure reservationer?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Hantera reservationer i Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Förstå hur reservationsrabatten tillämpas](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Förstå användningen av reserverade för din Enterprise-registrering](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Kostnader för Windows-programvara ingår inte i reservationer](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
