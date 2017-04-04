1. På portalen går du till det virtuella Resource Manager-nätverk som du vill skapa en virtuell nätverksgateway för.
2. I avsnittet **Inställningar** i VNet-bladet klickar du på **Undernät** för att expandera bladet undernät.
3. På bladet **Undernät** klickar du på **+Gateway-undernätet** längst upp. Då öppnas bladet **Lägg till undernät**. 
   
    ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsubnet.png "Lägg till gatewayundernätet")
4. **Namn** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera de automatiskt ifyllda värdena för **adressintervall** så att de motsvarar dina konfigurationskrav.

    ![Lägga till undernätet](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/gwsubnet.png "Lägga till undernätet")
5. Klicka på **OK** längst ned på bladet för att skapa undernätet.