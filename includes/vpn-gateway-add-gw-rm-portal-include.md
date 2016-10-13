1. Gå till **Nytt** i portalen. Ange ”Virtuell nätverksgateway” i sökfältet. Hitta **Virtuell nätverksgateway** bland sökresultaten och klicka på det. Detta öppnar bladet **Skapa virtuell nätverksgateway**.
2. Klicka på **Skapa** längst ner på bladet **Virtuell nätverksgateway**. Bladet **Skapa virtuell nätverksgateway** öppnas. Fyll i värden för din virtuella nätverksgateway.

    ![Skapa bladfält för virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/createvnetgw300.png "Create virtual network gateway blade fields")

3. **Namn**: namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.

4. **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 

5. **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.

6. **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt.

7. **Plats**: Justera fältet **Plats** så att det anger platsen där ditt virtuella nätverk befinner sig.
 
8. Välj i vilket virtuellt nätverk du vill lägga till denna gateway. Klicka på **Virtuella nätverk** för att öppna bladet **Välj ett virtuellt nätverk**. Välj VNet. Om du inte ser ditt VNet, kontrollera att fältet **plats** anger regionen där det virtuella nätverket befinner sig.

9. Välj en offentlig IP-adress. Klicka på **Offentlig IP-adress** för att öppna bladet **Välj offentlig IP-adress**. Klicka på **+Skapa ny**, för att öppna bladet **Skapa offentlig IP-adress**. Ange ett namn för din offentliga IP-adress. Det här bladet skapar ett objekt för en offentlig IP-adress som en offentlig IP-adress tilldelas till dynamiskt.<br>Klicka på **OK** att spara dina ändringar i det här bladet.

10. **Prenumeration**: Kontrollera att rätt prenumerationen har valts.

11. **Resursgrupp**: den här inställningen avgörs av vilket virtuella nätverk du väljer. 

12. Justera inte **platsen** när du har angett ovanstående inställningar.

13. Verifiera inställningarna. Du kan välja **Fäst vid instrumentpanelen** längst ner på bladet om du vill att din gateway ska visas på instrumentpanelen.

14. Klicka på **Skapa** för att börja skapa gatewayen. Inställningarna kommer att valideras och du ser ikonen "Distribuera virtuell nätverksgateway" på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

    ![Distribuera en virtuell nätverks-gateway](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Deploying Virtual network gateway")

11. När gatewayen är skapad, kan du se IP-adressen som har tilldelats den genom att se på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.





<!--HONumber=Oct16_HO1-->


