# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>Förskottsbetala virtuella datorer med reserverade VM-instanser

Betala i förhand för virtuella datorer och spara pengar med den reserverade virtuella instanser. Mer information finns i [reserverade virtuella Datorinstanser erbjudande](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Du kan köpa reserverade virtuella Datorinstanserna den [Azure-portalen](https://portal.azure.com). Att köpa en reserverad virtuell dator-instans:
-   Du måste ha en ägarrollen för minst en Enterprise- eller prenumeration med användningsbaserad betalning.
-   Enterprise-prenumerationer, reservation inköp måste vara aktiverade i den [EA portal](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>Köp en reserverad virtuell dator-instans
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **fler tjänster** > **reservationer**.
3. Välj **Lägg till** att köpa en ny reservation.
4. Fyll i de obligatoriska fälten. Körande VM instanser som matchar de markerade attributen som uppfyller kraven för att få rabatt för reservation. Det faktiska antalet VM-instanser som får rabatten är beroende av omfång och kvantitet som väljs.

    | Fält      | Beskrivning|
    |:------------|:--------------|
    |Namn        |Namnet på denna reservation.| 
    |Prenumeration|Den prenumeration som används för att betala för reservationen. Betalningsmetod på prenumerationen debiteras direktkostnader reservationens. Prenumerationstypen måste vara ett enterprise-avtal (erbjuder många: MS-AZR - 0017P) eller betala per användning (erbjuder många: MS-AZR - 0003P). Avgifterna är dras av från det registrering summa saldo eller debiteras som överförbrukning för en enterprise-prenumeration. Betala per användning prenumeration debiteras avgifterna till betalningsmetod kreditkort eller en faktura för prenumerationen.|    
    |Omfång       |Den reservationen omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>Enda prenumeration - reservation rabatt tillämpas på virtuella datorer i den här prenumerationen. </li><li>Delad - tillämpas reservation rabatt på virtuella datorer som körs i alla prenumerationer inom din faktureringsadress kontext. Företagskunder kan delade scope är registreringen och innefattar alla prenumerationer (med undantag för utveckling och testning prenumerationer) i registreringen. Betala per användning kunder är delade scope alla betala per användning prenumerationer som skapats av kontoadministratören.</li></ul>|
    |Plats    |Azure-regionen som omfattas av reservationen.|    
    |VM-storlek     |Storleken på VM-instanser.|
    |Period        |Ett år eller tre år.|
    |Antal    |Antalet instanser som köps i reservationen. Antalet är antalet VM-instanser som kan hämta faktureringsinformation rabatten som körs. Till exempel om du kör 10 Standard_D2 virtuella datorer i östra USA, skulle du ange antalet som 10 för att maximera förmån för alla datorer som körs. |
5. Du kan visa kostnaden för reservation när du väljer **beräkna kostnaden**.

    ![Skärmbild innan du skickar reservation köp](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Välj **inköp**.
7. Välj **visa denna Reservation** att se status för köpet.

    ![Skärmbild innan du skickar reservation köp](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps-after-buying-a-reservation"></a>Nästa steg efter att köpa en reservation
Reservation rabatten används automatiskt antalet virtuella datorer som matchar reservation scope och attribut som körs. Du kan uppdatera omfånget för reservation via [Azure-portalen](https://portal.azure.com), PowerShell, CLI eller via API: et. 

Information om hur du hanterar en reservation finns [hantera Azure reserverade virtuella Datorinstanser](https://go.microsoft.com/fwlink/?linkid=861613).

