---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09-05-2018
ms.openlocfilehash: 065ac0855fc47b23b434287f9f4406bd641f01ae
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454584"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser

Förskottsbetala för virtuella datorer och spara pengar med Azure reserverade virtuella datorn (VM)-instanser. Mer information finns i [Azure Reserved VM Instances erbjudande](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Du kan köpa en reserverad VM-instans den [Azure-portalen](https://portal.azure.com). Att köpa en instans:

- Du måste vara i en ägarrollen för minst en Enterprise eller användningsbaserad betalning.
- Köp av reservation måste aktiveras för Enterprise-prenumerationer för i den [EA-portalen](https://ea.azure.com).
- Efter programmet Cloud Solution Provider (CSP) köpa bara admin-agenter eller försäljning agents reservationer.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Fastställa rätt VM-storleken innan du köper

Fälten underkategori för mätning och produkt i användningsdata skilja inte mellan VM-storlekar som använder premium storage från virtuella datorer inte stöds. Om du använder de här fälten för att fastställa VM-storleken för reservationen, du kan köpa fel storlek och kommer inte få rabatten du förväntar dig. Använd någon av följande metoder för att fastställa rätt VM-storlek när du köper reservationen:

- Referera till fältet AdditionalInfo i din användningsfil eller användning API för att fastställa rätt VM-storlek. Använd inte värdena från fälten underkategori för mätning eller produkten. De här fälten skilja inte mellan och icke versioner av en virtuell dator.
- Hämta korrekt information för VM-storlek med hjälp av Powershell, Azure Resource Manager eller information i Azure portal från den virtuella datorn.

Reserverade VM-instanser är tillgängliga för de flesta storlekar för Virtuella datorer med vissa undantag:

- Rabatten gäller inte för följande virtuella datorer:
  - Klassiska virtuella datorer och molntjänster
  - Begränsade vCPU-storlekar
  - VM-serie: A-serien, Av2-serien och G-serien
  - Virtuella datorer i en förhandsversion: alla VM-serien och storlekar som är i förhandsversion
- Moln: Reservationer är inte tillgängligt att köpa i regionerna som Azure US Government, Tyskland eller Kina.
- Otillräcklig kvot: en reservation som är begränsad till en enda prenumeration måste ha vCPU-kvoten som är tillgängliga i prenumerationen för den nya RI. Till exempel om målprenumerationen har en kvot högst 10 virtuella processorer för D-serien, köpa sedan du inte en reservation för den 11 Standard_D1 instanser. Kvoten ingår för reservationer de virtuella datorerna som redan har distribuerats i prenumerationen. Till exempel om prenumerationen har en kvot på 10 virtuella processorer för D-serien och har två standard_D1 instanser som distribuerats kan köpa du en reservation för den 10 standard_D1 instanser i den här prenumerationen. 
- Begränsningar för kapacitet: I sällsynta fall kan Azure-begränsningar inköp av ny reservationer för delmängd av VM-storlekar, på grund av låg kapacitet i en region.

## <a name="buy-a-reserved-vm-instance"></a>Köp en reserverad VM-instans

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj **Lägg till** att köpa en ny reservation.
4. Fyll i fälten som krävs. Köra VM-instanser som matchar de attribut som du väljer kvalificera sig för att få rabatten. Det faktiska antalet dina VM-instanser som får rabatten är beroende av omfång och kvantitet som väljs.

    | Fält      | Beskrivning|
    |:------------|:--------------|
    |Namn        |Namnet på den här reservationen.| 
    |Prenumeration|Den prenumeration som används för att betala för reservationen. Betalningsmetoden för prenumerationen debiteras startavgifter för reservationen. Prenumerationstypen måste vara ett företagsavtal (erbjudandets nummer: MS-AZR-0017P) eller Betala per användning (erbjudandets nummer: MS-AZR-0003P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.|    
    |Omfång       |Den reservationsomfånget kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>Enstaka prenumeration - reservationsrabatten tillämpas på virtuella datorer i den här prenumerationen. </li><li>Delad – tillämpas reservationsrabatten på virtuella datorer som körs i alla prenumerationer i din faktureringskontexten. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer (utom prenumerationer för utveckling och testning) i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.</li></ul>|
    |Region    |Azure-regionen som omfattas av reservationen.|    
    |Storlek på virtuell dator     |Storleken på de Virtuella datorinstanserna.|
    |Optimera för     |Flexibilitet för Virtuella datorinstanser storlek tillämpas reservationsrabatten på andra virtuella datorer i samma [VM storleksgruppen](https://aka.ms/RIVMGroups). Prioritet för datacenterkapacitet prioriterar datacentrets kapacitet för dina distributioner. Det ger ytterligare förtroende i din möjlighet att starta VM-instanser när så behövs. Kapacitetsprioritet är bara tillgänglig när reservationsomfånget är enstaka prenumeration. |
    |Period        |Ett eller tre år.|
    |Antal    |Antalet instanser som köpts i reservationen. Antalet är antalet VM-instanser som kan få fakturering rabatten som körs. Till exempel om du kör 10 Standard_D2 virtuella datorer i östra USA, skulle du ange antalet som 10 om du vill maximera förmån för alla datorer som körs. |
5. Du kan visa kostnaden för reservationen när du väljer **beräkna kostnaden**.

    ![Skärmbild innan du skickar reservationsköp](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Välj **Köp**.
7. Välj **visa den här reservationen** att se status för ditt köp.

    ![Skärmbild efter att ha skickat reservationsköp](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="cancellations-and-exchanges"></a>Avbokningar och utbyten

Om du vill avbryta din reservation kan finnas det 12% uppsägningsavgift. Återbetalningar baseras på lägsta priset för din inköpspriset eller aktuella priset för reservationen. Återbetalningar är begränsade till 50 000 per år. Återbetalning felmeddelandet är det återstående proportionella saldot minus 12% uppsägningsavgift. Om du vill begära ett avbrott, går du till reservationen i Azure-portalen och välj **återbetala** att skapa en supportbegäran.

Om du vill ändra din reservation för reserverade VM-instanser till en annan region, VM-storleksgruppen eller termen kan byta du den för en annan reservation som är av samma eller högre värde. Termen startdatumet för den nya reservationen sprids inte från sker ett certifikatutbyte reservationen. 1 eller 3 år termen startar från när du skapar den nya reservationen. Om du vill begära en exchange, gå till reservationen i Azure-portalen och välj **Exchange** att skapa en supportbegäran.

## <a name="next-steps"></a>Nästa steg

Reservationsrabatten tillämpas automatiskt på antalet virtuella datorer som matchar reservationsomfånget och attribut som körs. Du kan uppdatera reservationens via omfång [Azure-portalen](https://portal.azure.com), PowerShell, CLI, eller via API: et.

Läs hur du hanterar en reservation i [hantera Azure-reservationer](../articles/billing/billing-manage-reserved-vm-instance.md).

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Hantera reservationer i Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
