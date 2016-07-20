1. Leta upp din lokala virtuella nätverksgateway och klicka på **Alla inställningar** för att öppna **Inställningar**-bladet.

2. På **Inställningar**-bladet, klickar du på **Anslutningar** och sedan på **Lägg till** överst på bladet för att öppna **Lägg till anslutning**-bladet.

    ![Skapa en plats-till-plats-anslutning](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. Ge din anslutning ett **Namn** på **Lägg till anslutning**-bladet. 

4. För **Anslutningstyp**, väljer du **Plats-till-plats(IPSec)**.

5. För **Virtuell nätverksgateway** är värdet låst eftersom du ansluter från den här gatewayen.

6. För **Lokala nätverksgateway**, klickar du på **Välj en lokal nätverksgateway** och väljer den lokala nätverksgateway som du vill använda. 

7. För **Delad nyckel**, måste värdet matcha det värde som du använder för din lokala VPN-enhet. Om VPN-enheten på ditt lokala nätverk inte ger en delad nyckel, kan du hitta på en och ange den här och på din lokala enhet. Det viktiga är att de båda matchar.

8. De återstående värdena för **Prenumeration**, **Resursgrupp** och **Plats** är låsta.

9. Klicka på **OK** för att skapa din anslutning. Du kommer att se *Skapar anslutningen* blinka fram på skärmen.

10. När anslutningen är skapad, kommer du att se den på **Anslutningar**-bladet för din Gateway.

    ![Skapa en plats-till-plats-anslutning](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)




<!--HONumber=Jun16_HO2-->


