---
ms.topic: include
ms.openlocfilehash: c30dbe25252a18e1edaed5ec81d86cc1dd976250
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39514108"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser

Förskottsbetala för virtuella datorer och spara pengar med Azure reserverade virtuella datorn (VM)-instanser. Mer information finns i [Azure Reserved VM Instances erbjudande](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Du kan köpa reserverade instanser för Azure den [Azure-portalen](https://portal.azure.com). Du köper en reserverad instans:
-   Du måste vara i en ägarrollen för minst en Enterprise eller användningsbaserad betalning.
-   För Enterprise-prenumerationer, inköp av reserverad instans måste aktiveras i den [EA-portalen](https://ea.azure.com).
-   Endast administratören agenter eller försäljning agenter kan köpa reserverade instanser för Cloud Solution Provider (CSP) program.

## <a name="determine-the-right-vm-size-before-purchase"></a>Fastställa rätt VM-storlek före köp
Fälten underkategori för mätning och produkt i användningsdata skilja inte mellan VM-storlekar som använder premium storage från VM-storlekar som inte använder premium storage, med dessa fält för att avgöra den virtuella datorn storleken för reservationsköp kan leda till felaktig reservationen köpa och ger dig rabatter för reserverade. Använd någon av metoderna nedan för att fastställa rätt VM-storlek för reservationsköp.
- Referera till AdditionalInfo > ServiceType fält i din användningsfil eller användningsdata API. Det här fältet får rätt VM-storleken när du distribuerar virtuella datorer som kan använda premium storage.
- Du kan också hämta korrekt information för VM-storlek med hjälp av Powershell, Azure Resource Manager eller från virtuell dator som beskrivs i Azure-portalen.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Köp en reserverad virtuell datorinstans
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj **Lägg till** att köpa en ny reserverad instans.
4. Fyll i fälten som krävs. Köra VM-instanser som matchar de attribut som du väljer kvalificera sig för att få rabatt för reserverad instans. Det faktiska antalet dina VM-instanser som får rabatten är beroende av omfång och kvantitet som väljs.

    | Fält      | Beskrivning|
    |:------------|:--------------|
    |Namn        |Namnet på den här reserverade instanser.| 
    |Prenumeration|Den prenumeration som används för att betala för den reserverade instansen. Betalningsmetoden för prenumerationen debiteras för startavgifterna för den reserverade instansen. Prenumerationstypen måste vara ett företagsavtal (erbjudandets nummer: MS-AZR-0017P) eller Betala per användning (erbjudandets nummer: MS-AZR-0003P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.|    
    |Omfång       |Den reserverade instansens omfattning kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>Enstaka prenumeration – den reserverade instansen rabatt tillämpas på virtuella datorer i den här prenumerationen. </li><li>Delad – tillämpas rabatten för reserverade instanser på virtuella datorer som körs i alla prenumerationer i din faktureringskontexten. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer (utom prenumerationer för utveckling och testning) i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.</li></ul>|
    |Plats    |Azure-regionen som omfattas av den reserverade instansen.|    
    |Storlek på virtuell dator     |Storleken på de Virtuella datorinstanserna.|
    |Optimera för     |Flexibilitet för Virtuella datorinstanser storlek tillämpas reservationsrabatten på andra virtuella datorer i samma [VM storleksgruppen](https://aka.ms/RIVMGroups). Prioritet för datacenterkapacitet reserverar datacenterkapacitet för dina distributioner. Det ger ytterligare förtroende i din möjlighet att starta VM-instanser när så behövs. Kapacitetsprioritet är bara tillgänglig när reservationsomfånget är enstaka prenumeration. |
    |Period        |Ett eller tre år.|
    |Antal    |Antalet instanser som håller på att köpas inom den reserverade instansen. Antalet är antalet VM-instanser som kan få fakturering rabatten som körs. Till exempel om du kör 10 Standard_D2 virtuella datorer i östra USA, skulle du ange antalet som 10 om du vill maximera förmån för alla datorer som körs. |
5. Du kan visa kostnaden för den reserverade instansen när du väljer **beräkna kostnaden**.

    ![Skärmbild innan du skickar köp av reserverad instans](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Välj **Köp**.
7. Välj **visa den här reservationen** att se status för ditt köp.

    ![Skärmbild efter att ha skickat köp av reserverad instans](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Nästa steg 
Rabatten för reserverade instanser tillämpas automatiskt på antalet virtuella datorer som matchar den reserverade instansens omfattning och attribut som körs. Du kan uppdatera omfånget för den reserverade instansen via [Azure-portalen](https://portal.azure.com), PowerShell, CLI eller via API: et. 

Läs hur du hanterar en reserverad instans i [hantera reserverade instanser i Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Mer information om reserverade instanser för Azure finns i följande artiklar:

- [Vad är Azure Reserved VM Instances?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Hantera reserverade instanser i Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Förstå tillämpningen av rabatter för Reserved Instances](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reserverade instanser för din Betala per användning-prenumeration](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade instanser för Enterprise-registrering](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reserverade instanser](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Reserverade instanser i ett molnlösningsleverantörprogram för partnercenter (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
