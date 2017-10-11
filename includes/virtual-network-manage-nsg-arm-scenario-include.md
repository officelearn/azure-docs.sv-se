## <a name="sample-scenario"></a>Exempelscenario
Den här artikeln används för att illustrera hur du hanterar NSG: er bättre scenariot nedan.

![VNet-scenario](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

I det här scenariot skapar du en NSG för varje undernät i den **TestVNet** virtuella nätverk som beskrivs nedan: 

* **NSG-klientdel**. Klientdelen NSG tillämpas på den *klientdel* undernät, och innehåller två regler:    
  * **RDP-regel**. Den här regeln tillåter RDP-trafik till den *klientdel* undernät.
  * **regel för Web**. Den här regeln ska tillåta HTTP-trafik till den *klientdel* undernät.
* **NSG-BackEnd**. NSG tillämpas på serverdelen i *BackEnd* undernät, och innehåller två regler:    
  * **SQL-regel**. Den här regeln kan SQL-trafik från den *klientdel* undernät.
  * **regel för Web**. Den här regeln nekar alla internet-bunden trafik från den *BackEnd* undernät.

Kombinationen av reglerna skapar ett DMZ-liknande scenario där backend-undernät kan bara att ta emot inkommande trafik för SQL-trafik från klientdelens undernät, och inte har åtkomst till Internet, medan klientdelens undernät kan kommunicera med Internet och ta emot inkommande HTTP-begäranden endast.

Om du vill distribuera det scenario som beskrivs ovan, Följ [länken](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), klickar du på **till Azure**, ersätta Standardparametervärden om det behövs och följ instruktionerna i portalen. I exemplet anvisningarna nedan mallen som användes för att distribuera en resursgruppnamn **RG NSG**. 

