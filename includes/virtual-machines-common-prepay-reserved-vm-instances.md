---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-07-2018
ms.openlocfilehash: 19a153a5cdc9d5f878494984313baebd12dbcbb5
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631171"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser

Förskottsbetala för virtuella datorer och spara pengar med Azure reserverade virtuella datorn (VM)-instanser. Mer information finns i [Azure Reserved VM Instances erbjudande](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Du kan köpa en reserverad VM-instans den [Azure-portalen](https://portal.azure.com). Att köpa en instans:

- Du måste vara i en ägarrollen för minst en Enterprise eller användningsbaserad betalning.
- Köp av reservation måste aktiveras för Enterprise-prenumerationer för i den [EA-portalen](https://ea.azure.com).
- Endast admin-agenter eller försäljning agenter kan köpa reservationer för Cloud Solution Provider (CSP)-programmet.

## <a name="determine-the-right-vm-size-before-purchase"></a>Fastställa rätt VM-storlek före köp

Fälten underkategori för mätning och produkt i användningsdata skilja inte mellan VM-storlekar som använder premium storage från VM-storlekar som inte använder premium storage, med dessa fält för att avgöra den virtuella datorn storleken för reservationsköp kan leda till felaktig reservationen köpa och ger dig rabatter för reserverade. Använd någon av metoderna nedan för att fastställa rätt VM-storlek för reservationsköp.

- Referera till fältet AdditionalInfo i din användningsfil eller användning API för att fastställa rätt VM-storleken för en reservationsköp. Använd inte värdena från fälten underkategori för mätning eller produkten, eftersom de här fälten inte skiljer på datorer och icke-S versioner av en virtuell dator.
- Du kan också hämta korrekt information för VM-storlek med hjälp av Powershell, Azure Resource Manager eller från virtuell dator som beskrivs i Azure-portalen.

Reserverade VM-instanser är tillgängliga för de flesta storlekar för Virtuella datorer med vissa undantag:

- Virtuella datorer i en förhandsversion: alla VM-serien och storlekar som är en förhandsversion är inte tillgängliga för reservationsköp.
- Moln: reservationer är inte tillgängligt att köpa i regionerna som Azure US Government, Tyskland eller Kina.
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
    |Plats    |Azure-regionen som omfattas av reservationen.|    
    |Storlek på virtuell dator     |Storleken på de Virtuella datorinstanserna.|
    |Optimera för     |Flexibilitet för Virtuella datorinstanser storlek tillämpas reservationsrabatten på andra virtuella datorer i samma [VM storleksgruppen](https://aka.ms/RIVMGroups). Prioritet för datacenterkapacitet reserverar datacenterkapacitet för dina distributioner. Det ger ytterligare förtroende i din möjlighet att starta VM-instanser när så behövs. Kapacitetsprioritet är bara tillgänglig när reservationsomfånget är enstaka prenumeration. |
    |Period        |Ett eller tre år.|
    |Antal    |Antalet instanser som köpts i reservationen. Antalet är antalet VM-instanser som kan få fakturering rabatten som körs. Till exempel om du kör 10 Standard_D2 virtuella datorer i östra USA, skulle du ange antalet som 10 om du vill maximera förmån för alla datorer som körs. |
5. Du kan visa kostnaden för reservationen när du väljer **beräkna kostnaden**.

    ![Skärmbild innan du skickar reservationsköp](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Välj **Köp**.
7. Välj **visa den här reservationen** att se status för ditt köp.

    ![Skärmbild efter att ha skickat reservationsköp](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

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
