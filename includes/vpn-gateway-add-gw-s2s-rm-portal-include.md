1. Till vänster på portalen klickar du på **+** och skriver ”Virtuell nätverksgateway” i sökrutan. I **Resultat** letar du upp och klickar på **Virtuell nätverksgateway**.
2. Klicka på **”Skapa”** längst ned på bladet ”Virtuell nätverksgateway”. Detta öppnar bladet **Skapa virtuell nätverksgateway**.

    ![Fält på bladet Skapa virtuell nätverksgateway](./media/vpn-gateway-add-gw-s2s-rm-portal-include/vnet_gw.png "Ny gateway")

3. På bladet **Skapa virtuell nätverksgateway** anger du värdena för din virtuella nätverksgateway.

  - **Namn**: namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
  - **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 
  - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
  - **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.
  - **Plats**: Du kan behöva rulla för att se Plats. Justera fältet **Plats** så att det pekar på platsen för det virtuella nätverket. Om platsen inte pekar på regionen där det virtuella nätverket finns visas inte det virtuella nätverket i listrutan ”Välj ett virtuellt nätverk” i nästa steg.
  - **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen i. Klicka på **Virtuella nätverk** för att öppna bladet ”Välj ett virtuellt nätverk”. Välj VNet. Om du inte ser ditt VNet, kontrollera att fältet Plats anger regionen där det virtuella nätverket befinner sig.
  - **Offentlig IP-adress**: Bladet ”Skapa offentlig IP-adress” skapar ett offentligt IP-adressobjekt. Den offentliga IP-adressen tilldelas dynamiskt när en VPN-gateway har skapats. VPN-gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

    - Klicka först på **Offentlig IP-adress** för att öppna bladet ”Välj offentlig IP-adress”. Klicka sedan på **+ skapa ny** för att öppna bladet ”Skapa offentlig IP-adress”.
    - Ange sedan ett **Namn** för din offentliga IP-adress och klicka sedan på **OK** längst ned på det här bladet för att spara ändringarna.

      ![Skapa offentlig IP](./media/vpn-gateway-add-gw-s2s-rm-portal-include/pip.png "Skapa offentlig IP")

4. Verifiera inställningarna. Du kan välja **Fäst vid instrumentpanelen** längst ner på bladet om du vill att din gateway ska visas på instrumentpanelen. 
5. Klicka på **Skapa** för att börja skapa VPN-gatewayen. Inställningarna kommer att valideras och du ser ikonen "Distribuera virtuell nätverksgateway" på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

När gatewayen har skapats kan du se IP-adressen som har tilldelats till den genom att visa det virtuella nätverket på portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.