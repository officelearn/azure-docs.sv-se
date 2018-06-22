---
ms.topic: include
ms.openlocfilehash: 32fdc23aded78ca491fbde2ef5c76247c56d0c4c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313752"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Förskottsbetala virtuella datorer med Azure reserverade VM-instanser

Betala i förhand för virtuella datorer och spara pengar med Azure reserverade virtuella datorn (VM)-instanser. Mer information finns i [Azure reserverade instanser erbjudande](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Du kan köpa Azure reserverade instanser den [Azure-portalen](https://portal.azure.com). Att köpa en reserverad instans:
-   Du måste ha en ägarrollen för minst en Enterprise- eller prenumeration med användningsbaserad betalning.
-   Enterprise-prenumerationer, reserverade inköp måste vara aktiverade i den [EA portal](https://ea.azure.com).
-   Endast admin agenter eller försäljning agenter kan köpa reserverade instanserna för Cloud Solution Providers (CSP) program.

[!IMPORTANT]
Du måste använda en av de metoder som beskrivs nedan för att identifiera den korrekt VM-storlek för ett reservation inköp.

## <a name="determine-the-right-vm-size-before-purchase"></a>Bestämma rätt storlek för virtuell dator innan du köper
1. Finns i AdditionalInfo-fältet i din användning av filen eller användning API för att bestämma rätt VM-storlek för ett reservation inköp. Använd inte värdena från underkategori för mätaren eller produkten fält eftersom de här fälten inte skiljer och icke versioner av en virtuell dator.
2. Du kan också få korrekt information om Virtuellt storlek med hjälp av Powershell, Azure Resource Manager eller från VM information i Azure-portalen.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Köp en reserverad virtuell dator-instans
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj **Lägg till** att köpa en ny reserverad instans.
4. Fyll i de obligatoriska fälten. Körande VM instanser som matchar de markerade attributen som uppfyller kraven för att få rabatt för reserverade. Det faktiska antalet VM-instanser som får rabatten är beroende av omfång och kvantitet som väljs.

    | Fält      | Beskrivning|
    |:------------|:--------------|
    |Namn        |Namnet på den här reserverade instansen.| 
    |Prenumeration|Den prenumeration som används för att betala för den reserverade instansen. Betalningsmetod på prenumerationen debiteras direktkostnader reserverade instansen. Prenumerationstypen måste vara ett enterprise-avtal (erbjuder många: MS-AZR - 0017P) eller betala per användning (erbjuder många: MS-AZR - 0003P). Avgifterna är dras av från det registrering summa saldo eller debiteras som överförbrukning för en enterprise-prenumeration. Betala per användning prenumeration debiteras avgifterna till betalningsmetod kreditkort eller en faktura för prenumerationen.|    
    |Omfång       |Den reserverade instansen scope kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>Enda prenumeration - instans med det reserverade rabatt tillämpas på virtuella datorer i den här prenumerationen. </li><li>Delad - tillämpas reserverade rabatt på virtuella datorer som körs i alla prenumerationer inom din faktureringsadress kontext. Företagskunder kan delade scope är registreringen och innefattar alla prenumerationer (med undantag för utveckling och testning prenumerationer) i registreringen. Betala per användning kunder är delade scope alla betala per användning prenumerationer som skapats av kontoadministratören.</li></ul>|
    |Plats    |Azure-regionen som motsvarar den reserverade instansen.|    
    |Storlek på virtuell dator     |Storleken på VM-instanser.|
    |Period        |Ett år eller tre år.|
    |Antal    |Antalet instanser som köps inom reserverade instansen. Antalet är antalet VM-instanser som kan hämta faktureringsinformation rabatten som körs. Till exempel om du kör 10 Standard_D2 virtuella datorer i östra USA, skulle du ange antalet som 10 för att maximera förmån för alla datorer som körs. |
5. Du kan visa kostnaden för den reserverade instansen när du väljer **beräkna kostnaden**.

    ![Skärmbild innan du skickar reserverade köpet](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Välj **inköp**.
7. Välj **visa denna Reservation** att se status för köpet.

    ![Skärmbild efter att ha skickat reserverade köpet](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Nästa steg 
Reserverade rabatten används automatiskt antalet virtuella datorer som matchar reserverade scope och attribut som körs. Du kan uppdatera omfånget för den reserverade instans via [Azure-portalen](https://portal.azure.com), PowerShell, CLI eller via API: et. 

Information om hur du hanterar en reserverad instans finns [hantera Azure reserverade instanser](../articles/billing/billing-manage-reserved-vm-instance.md).

Mer information om Azure reserverade instanser finns i följande artiklar:

- [Spara pengar på virtuella datorer med reserverade instanser](../articles/billing/billing-save-compute-costs-reservations.md)
- [Hantera Azure reserverade instanser](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Förstå hur de reserverade rabatterna används](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för din prenumeration med användningsbaserad betalning](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Förstå reserverade användning för Enterprise-registrering](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Windows programvarukostnader ingår inte i reserverade instanser](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Reserverade instanser i programmet för Partner Center Cloud Solution Providers (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.