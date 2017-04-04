1. Leta upp din virtuella nätverksgateway.
2. Klicka på **Anslutningar**. Klicka på **+Lägg till** överst på bladet Anslutningar för att öppna bladet **Lägg till anslutning**.
   
    ![Skapa en plats-till-plats-anslutning](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection.png)
3. Ge din anslutning ett **Namn** på **Lägg till anslutning**-bladet. 
4. För **Anslutningstyp**, väljer du **Plats-till-plats(IPSec)**.
5. För **Virtuell nätverksgateway** är värdet låst eftersom du ansluter från den här gatewayen.
6. För **Lokala nätverksgateway**, klickar du på **Välj en lokal nätverksgateway** och väljer den lokala nätverksgateway som du vill använda. 
7. För **Delad nyckel** måste värdet matcha det värde som du använder för din lokala VPN-enhet. I det här exemplet använder vi ”abc123”, men du kan (och bör) använda ett mer komplext värde. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.
8. De återstående värdena för **Prenumeration**, **Resursgrupp** och **Plats** är låsta.
9. Klicka på **OK** för att skapa din anslutning. Du kommer att se *Skapar anslutningen* blinka fram på skärmen.
10. När anslutningen är klar visas den på bladet **Anslutningar** för den virtuella nätverksgatewayen.
    
    ![Skapa en plats-till-plats-anslutning](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connectionstatus450.png)

