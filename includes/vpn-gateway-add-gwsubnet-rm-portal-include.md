1. I portalen, går du till det virtuella nätverket som du vill ansluta en gateway till.

2. I avsnittet **Inställningar** i VNet-bladet klickar du på **Undernät** för att expandera bladet undernät.

3. På bladet **Undernät** klickar du på **+Gateway-undernätet** längst upp. Då öppnas bladet **Lägg till undernät**. **Namn** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet.

    ![Lägg till gateway-undernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet250.png)

4. Du kan ändra CIDR-block för adressintervallet om det behövs. Kontrollera de specifika kraven för konfigurationen för att bekräfta det rekommenderade CIDR-blocket.

5. Klicka på **OK** längst ned på bladet för att skapa undernätet.






<!--HONumber=sep16_HO1-->


