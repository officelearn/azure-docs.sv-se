### <a name="to-verify-your-connection-by-using-powershell"></a>Så här verifierar du din anslutning med PowerShell
Du kan kontrollera att anslutningen har utförts genom att använda `Get-AzureRmVirtualNetworkGatewayConnection`-cmdlet:en, med eller utan `-Debug`. 

1. Använd följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Välj A om du uppmanas, för att köra Alla. Exemplet `-Name` refererar till namnet på den anslutning som du skapade och vill testa.
   
        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
2. Visa värdena när cmdlet:en har slutförts. I exemplet nedan visas anslutningsstatusen som Ansluten och du kan se ingående och utgående byte.
   
        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>Så här verifierar du din anslutning med Azure-portalen
Du kan visa anslutningsstatus i Azure-portalen genom att navigera till anslutningen. Det finns flera sätt att göra detta på. Följande steg visar ett sätt att navigera till din anslutning och verifiera.

1. I [Azure Portal](http://portal.azure.com), klickar du på **Alla resurser** och går till din virtuella nätverksgateway .
2. På bladet för den virtuella nätverksgatewayen, klickar du på **Anslutningar**. Du kan se status för varje anslutning.
3. Klicka på namnet på den anslutning du vill verifiera för att öppna **Essentials**. I Essentials, kan du visa mer information om din anslutning. **Statusen** är Lyckades och Ansluten, när du har skapat en lyckad anslutning.
   
    ![Verifiera anslutning](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)



<!--HONumber=Nov16_HO2-->


