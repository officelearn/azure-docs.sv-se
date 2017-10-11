## <a name="scenario"></a>Scenario
Det här dokumentet använder för att illustrera hur du skapar udr: er bättre scenariot nedan.

![BESKRIVNING AV AVBILDNING](./media/virtual-network-create-udr-scenario-include/figure1.png)

I det här scenariot skapar du en UDR för den *Front end undernät* och en annan UDR för den *tillbaka slutet undernät* , enligt beskrivningen nedan: 

* **UDR klientdel**. Klientdelen UDR ska kopplas till den *klientdel* undernät, och innehåller en väg:    
  * **RouteToBackend**. Den här vägen skickar all trafik till backend-undernät som den **FW1** virtuella datorn.
* **UDR BackEnd**. Serverdelen UDR ska kopplas till den *BackEnd* undernät, och innehåller en väg:    
  * **RouteToFrontend**. Den här vägen skickar all trafik frontend-undernät som den **FW1** virtuella datorn.

Kombinationen av dessa vägar säkerställer att all trafik från ett undernät till en annan kommer att dirigeras till den **FW1** virtuell dator som används som en virtuell installation. Du måste också aktivera IP-vidarebefordring för den virtuella datorn, så det kan ta emot trafik till andra virtuella datorer.

