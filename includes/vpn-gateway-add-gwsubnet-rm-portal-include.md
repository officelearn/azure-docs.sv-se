1. I [portalen](http://portal.azure.com) går du till det virtuella Resource Manager-nätverket för vilket du vill skapa en virtuell nätverksgateway.
2. I avsnittet **Inställningar** på sidan för det virtuella nätverket, klickar du på **Undernät** så att sidan Undernät öppnas.
3. På sidan**Undernät** klickar du på **+Gateway-undernät** så att sidan **Lägg till undernät** öppnas.

  ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet.png "Lägg till gatewayundernätet")
4. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera de automatiskt ifyllda värdena för **Adressintervall** så att de matchar konfigurationskraven, och klicka sedan på **OK** längst ned på sidan för att skapa undernätet.

  ![Lägga till undernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Lägga till undernätet")