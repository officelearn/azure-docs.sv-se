1. På vänstra sidan i portalen klickar du på **+** och skriver "virtuell nätverksgateway" i sökningen. Hitta **Virtuell nätverksgateway** bland sökresultaten och klicka på det. På sidan **Virtuell nätverksgateway** klickar du på **Skapa** längst ned på sidan för att öppna sidan **Skapa en virtuell nätverksgateway**.
2. På sidan **Skapa en virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway.

  ![Fält på sidan Skapa en virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Fält på sidan Skapa en virtuell nätverksgateway")
3. På den **Skapa virtuell nätverksgateway** anger värden för din virtuella nätverksgateway.

  - **Namn**: namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
  - **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 
  - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
  - **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.
  - **Plats**: Du kan behöva rulla för att se Plats. Justera fältet **Plats** så att det pekar på platsen för det virtuella nätverket. Om platsen inte pekar på den region där det virtuella nätverket finns när du väljer ett virtuellt nätverk i nästa steg, visas den inte i den nedrullningsbara listan.
  - **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen i. Klicka på **för virtuella nätverk** att öppna sidan, Välj ett virtuellt nätverk ”. Välj VNet. Om du inte ser ditt VNet, kontrollera att fältet Plats anger regionen där det virtuella nätverket befinner sig.
  - **Adressintervall för gatewayundernät**: visas bara den här inställningen om du inte tidigare har skapat ett gateway-undernät för det virtuella nätverket. Den här inställningen visas inte om du tidigare har skapat ett giltigt gatewayundernät.
  - **Första IP-konfiguration**: sidan 'Välja offentliga IP-adressen' skapar ett objekt med offentliga IP-adress som hämtar kopplad till VPN-gateway. Den offentliga IP-adressen tilldelas dynamiskt till det här objektet när VPN-gateway har skapats. VPN-gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

    - Klicka först på **skapa gateway IP-konfiguration** att öppna sidan ”Välj offentlig IP-adress, och klicka sedan på **+ Skapa nytt** att öppna sidan” Skapa offentlig IP-adress ”.
    - Ange sedan en **namn** för din offentliga IP-adress. Lämna SKU som **grundläggande** om det inte finns en särskild anledning att ändra till något annat, klicka på **OK** längst ned på den här sidan om du vill spara ändringarna.

      ![Skapa offentlig IP](./media/vpn-gateway-add-gw-s2s-rm-portal-include/gwip.png "Skapa offentlig IP")

4. Verifiera inställningarna. Du kan välja **fäst på instrumentpanelen** längst ned på sidan om du vill att din gateway ska visas på instrumentpanelen. 
5. Klicka på **Skapa** för att börja skapa VPN-gatewayen. Inställningarna verifieras och du ser ”distribuerar virtuell nätverksgateway” panelen på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

När gatewayen har skapats kan du se IP-adressen som har tilldelats till den genom att visa det virtuella nätverket på portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.
