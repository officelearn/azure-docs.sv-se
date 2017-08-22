1. På portalen går du till det virtuella Resource Manager-nätverk som du vill skapa en virtuell nätverksgateway för.
2. I avsnittet **Inställningar** på sidan för det virtuella nätverket, klickar du på **Undernät** så att sidan Undernät öppnas.
3. På sidan **Undernät** klickar du på **+Gateway-undernät** längst upp så att sidan **Lägg till undernät** öppnas.

  ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/add-gw-subnet.png "Lägg till gatewayundernätet")
4. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. GatewaySubnet-värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera de automatiskt ifyllda värdena för **adressintervall** så att de motsvarar dina konfigurationskrav.

  ![Lägg till gatewayundernät](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/gwsubnetip.png "Lägg till gatewayundernät")
5. Klicka på **OK** längst ned på sidan för att skapa undernätet.