1. Till vänster på portalen klickar du på **+** och skriver ”Virtuell nätverksgateway” i sökrutan. I **Resultat** letar du upp och klickar på **Virtuell nätverksgateway**. Klicka på **Skapa** längst ned på bladet **Virtuell nätverksgateway**. Detta öppnar bladet **Skapa virtuell nätverksgateway**.
2. På bladet **Skapa virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway.

    ![Fält på bladet Skapa virtuell nätverksgateway](./media/vpn-gateway-add-gw-s2s-rm-portal-include/newgw.png "Ny gateway")
3. **Namn**: namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
4. **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 
5. **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
6. **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt.
7. **Plats**: Du kan behöva rulla för att se Plats. Justera fältet **Plats** så att det pekar på platsen för det virtuella nätverket. Om platsen inte pekar på regionen där det virtuella nätverket finns visas inte det virtuella nätverket i listrutan ”Välj ett virtuellt nätverk” i nästa steg.
8. **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen i. Klicka på **Virtuella nätverk** för att öppna bladet **Välj ett virtuellt nätverk**. Välj VNet. Om du inte ser ditt VNet, kontrollera att fältet **plats** anger regionen där det virtuella nätverket befinner sig.
9. **Offentlig IP-adress**: Det här bladet skapar ett objekt för en offentlig IP-adress som en offentlig IP-adress tilldelas till dynamiskt. Klicka på **Offentlig IP-adress** för att öppna bladet **Välj offentlig IP-adress**. Klicka på **+Skapa ny**, för att öppna bladet **Skapa offentlig IP-adress**. Ange ett namn för din offentliga IP-adress. Klicka på **OK** att spara dina ändringar i det här bladet.

    ![Skapa offentlig IP](./media/vpn-gateway-add-gw-s2s-rm-portal-include/createpip.png "Skapa offentlig IP")
10. **Prenumeration**: Kontrollera att rätt prenumerationen har valts.
11. **Resursgrupp**: den här inställningen avgörs av vilket virtuella nätverk du väljer. 
12. Justera inte **platsen** när du har angett ovanstående inställningar.
13. Verifiera inställningarna. Du kan välja **Fäst vid instrumentpanelen** längst ner på bladet om du vill att din gateway ska visas på instrumentpanelen.
14. Klicka på **Skapa** för att börja skapa gatewayen. Inställningarna kommer att valideras och du ser ikonen "Distribuera virtuell nätverksgateway" på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.
    
    ![Skapa gateway](./media/vpn-gateway-add-gw-s2s-rm-portal-include/creategw.png "Skapa gateway")
15. När gatewayen har skapats kan du se IP-adressen som har tilldelats till den genom att visa det virtuella nätverket på portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.