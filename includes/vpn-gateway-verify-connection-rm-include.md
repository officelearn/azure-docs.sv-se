### Så här verifierar du din anslutning med PowerShell

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

### Så här verifierar du din anslutning med Azure-portalen

Du kan visa anslutningsstatus i Azure-portalen genom att navigera till anslutningen. Det finns flera sätt att göra detta på. Nedan är ett sätt att navigera till anslutningen.

1. I [Azure-portalen](http://portal.azure.com), navigera till **Virtuell nätverksgateway**. Klicka på ditt gatewaynamn.
2. I rutan, under **Inställningar**, klicka på **Anslutningar**. Du kan se status för varje anslutning.
3. Klicka på anslutningens namn för mer information om anslutningen. På sidan Essentials för anslutningen, observera **Anslutningsstatus**. Status kommer att vara Lyckades och Ansluten när en anslutning har upprättats. Du kan kontrollera de data som flödar genom att titta på **Data in** och **Data u**.

    I exemplet nedan, är **Anslutningsstatus** Inte ansluten. 

    ![Verifiera anslutning](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)

<!--HONumber=Sep16_HO3-->


