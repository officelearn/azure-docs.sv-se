## <a name="scenario"></a>Scenario
Det här dokumentet använder för att illustrera hur du skapar NSG: er bättre scenariot nedan.

![VNet-scenario](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

I det här scenariot skapar du en NSG för varje undernät i den **TestVNet** virtuella nätverk som beskrivs nedan: 

* **NSG-klientdel**. Klientdelen NSG tillämpas på den *klientdel* undernät, och innehåller två regler:    
  * **RDP-regel**. Den här regeln tillåter RDP-trafik till den *klientdel* undernät.
  * **regel för Web**. Den här regeln ska tillåta HTTP-trafik till den *klientdel* undernät.
* **NSG-BackEnd**. NSG tillämpas på serverdelen i *BackEnd* undernät, och innehåller två regler:    
  * **SQL-regel**. Den här regeln kan SQL-trafik från den *klientdel* undernät.
  * **regel för Web**. Den här regeln nekar alla internet-bunden trafik från den *BackEnd* undernät.

Kombinationen av reglerna skapar ett DMZ-liknande scenario där backend-undernät kan endast ta emot inkommande trafik för SQL från klientdelens undernät, och inte har åtkomst till Internet, medan klientdelens undernät kan kommunicera med Internet och ta emot inkommande HTTP-begäranden endast.

