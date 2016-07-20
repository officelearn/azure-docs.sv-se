1. I portalen går du till **Ny** och sedan **Nätverk**. Välj **Virtuell nätverksgateway** från listan.

    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. På bladet **Skapa virtuell nätverksgateway** namnger du din gateway i **Namn**-fältet. Det här är inte samma sak som att namnge ett gateway-undernät. Det är namnet på gateway-objektet.
 
3. Därefter väljer du det virtuella nätverk som du vill distribuera gatewayen till. Klicka på pilen för att öppna bladet **Välj ett virtuellt nätverk** och klicka sedan på VNet. För att VNet ska visas i listan, måste det redan ha ett giltigt gateway-undernät.

4. Välj en offentlig IP-adress. Klicka på pilen för att öppna **Välj offentlig IP-adress-bladet**. Klicka sedan på **Skapa ny**, för att öppna **Skapa offentlig IP-adress-bladet**. Ange ett namn för din offentliga IP-adress. Observera att det inte handlar om en IP-adress. IP-adressen tilldelas dynamiskt. Det handlar istället om namnet för IP-adressobjektet som adressen kommer att tilldelas. Spara ändringarna genom att klicka på **OK**.

5. För **Gateway-typ**, väljer du den gateway-typ som anges för din konfiguration.

6. För **VPN-typ**, väljer du den VPN-typ som anges för din konfiguration.

7. För **Prenumeration**, verifierar du att korrekt prenumeration har valts.

8. När det gäller **Resursgrupp**, avgörs resursgruppen av det virtuella nätverket som du väljer.

9. För **Plats**, kontrollera att den visar platsen där både din resursgrupp och VNet finns.

10. Du kan välja **Fäst vid instrumentpanelen** om du vill att din gateway ska visas på instrumentpanelen. Klicka på **Skapa** för att börja skapa gatewayen. Du ser "Distribuerar virtuell nätverksgateway"-ikonen på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är mycket som händer i bakgrunden. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

    
    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. När gatewayen är skapad, kan du se IP-adressen som har tilldelats den genom att se på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) för att se mer information.






<!--HONumber=Jun16_HO2-->


