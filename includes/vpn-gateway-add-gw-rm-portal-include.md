1. I portalen, gå till **Nytt** > **Nätverk** > **Virtuell nätverksgateway**. Då öppnas bladet **Skapa virtuell nätverksgateway**.

    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. På bladet **Skapa virtuell nätverksgateway** namnger du din gateway i **Namn**-fältet. Det här är inte samma sak som att namnge ett gateway-undernät. Detta är namnet på det gateway-objekt som du skapar.

3. Justera fältet **Plats** så att det pekar på platsen för det virtuella nätverket. Om du inte gör det visas inte listan VNet ditt virtuella nätverk.
 
4. Välj sedan det virtuella nätverket dit du vill lägga till denna gateway. Klicka på **Virtuella nätverk** för att öppna bladet **Välj ett virtuellt nätverk**. Välj VNet. För att VNet ska visas i listan, måste det redan ha ett giltigt gateway-undernät.

5. Välj en offentlig IP-adress. Klicka på **Offentlig IP-adress** för att öppna bladet **Välj offentlig IP-adress**. Klicka på **+Skapa ny**, för att öppna bladet **Skapa offentlig IP-adress**. Ange ett namn för din offentliga IP-adress. Detta skapar ett offentligt IP-adress-objekt dit en offentlig IP-adress dynamiskt ska tilldelas. <br>Spara ändringarna genom att klicka på **OK**.

5. För **Gateway-typ**, väljer du den gateway-typ som anges för din konfiguration.

6. För **VPN-typ**, väljer du den VPN-typ som anges för din konfiguration.

7. För **Prenumeration**, verifierar du att korrekt prenumeration har valts.

8. **Resursgrupp** avgörs av det virtuella nätverket som du väljer. 

9. Justera inte **Plats** efter att du har angett inställningarna ovan. 

10. Ditt blad kommer nu att se ut ungefär som på bilden i steg 1. Kontrollera att inställningarna matchar inställningarna för din egen konfiguration. Du kan välja **Fäst vid instrumentpanelen** längst ner på bladet om du vill att din gateway ska visas på instrumentpanelen.

11. Klicka på **Skapa** för att börja skapa gatewayen. Inställningarna kommer att valideras och du ser ikonen "Distribuera virtuell nätverksgateway" på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. När gatewayen är skapad, kan du se IP-adressen som har tilldelats den genom att se på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) för att se mer information.






<!--HONumber=sep16_HO1-->


