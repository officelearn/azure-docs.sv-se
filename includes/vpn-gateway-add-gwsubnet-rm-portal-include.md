1. I [portalen](http://portal.azure.com) går du till det virtuella Resource Manager-nätverket för vilket du vill skapa en virtuell nätverksgateway.
2. I avsnittet **Inställningar** i VNet-bladet klickar du på **Undernät** för att expandera bladet undernät.
3. På bladet **Undernät** klickar du på **+Gateway-undernät** för att öppna bladet **Lägg till undernät**. 
   
    ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet.png "Lägg till gatewayundernätet")
4. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera de automatiskt ifyllda värdena för **adressintervall** så att de motsvarar dina konfigurationskrav.

    ![Lägga till undernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Lägga till undernätet")
5. Klicka på **OK** längst ned på bladet för att skapa undernätet.

