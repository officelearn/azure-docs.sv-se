---
ms.topic: include
ms.openlocfilehash: 8e710bebf979b60f61552593ae550e95a8340d2b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34307574"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Förskottsbetala virtuella datorer med Azure reserverade VM-instanser

Betala i förhand för virtuella datorer och spara pengar med Azure reserverade virtuella datorn (VM)-instanser. Mer information finns i [Azure reserverade instanser erbjudande](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Du kan köpa Azure reserverade instanser den [Azure-portalen](https://portal.azure.com). Att köpa en reserverad instans:
-   Du måste ha en ägarrollen för minst en Enterprise- eller prenumeration med användningsbaserad betalning.
-   Enterprise-prenumerationer, reserverade inköp måste vara aktiverade i den [EA portal](https://ea.azure.com).
-   Endast admin agenter eller försäljning agenter kan köpa reserverade instanserna för Cloud Solution Providers (CSP) program.

## <a name="buy-a-reserved-instance"></a>Köp en reserverad instans
1. Logga in på [Azure-portalen](https://portal.azure.com).
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