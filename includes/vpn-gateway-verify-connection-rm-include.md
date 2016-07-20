Du kan verifiera en VPN-anslutning i Azure-portalen genom att navigera till **Virtuella nätverksgatewayer** **>** ***klicka på ditt gatewaynamn*** **>** **Inställningar** **>** **Anslutningar**. Genom att markera namnet på anslutningen så kan du visa mer information om anslutningen. Anslutningen i exemplet nedan är inte ansluten och det strömmar ingen data genom den.


![Verifiera anslutning](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### Verifiera din anslutning med PowerShell

Det går också att verifiera att din anslutning lyckats med `Get-AzureRmVirtualNetworkGatewayConnection –Debug`. Du kan använda följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Välj A när du uppmanas, för att köra alla.

    Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Bläddra igenom för att se värdena efter att cmdleten har slutförts. I exemplet nedan visas anslutningsstatusen som *Ansluten* och du kan se ingående och utgående byte.

    Body:
    {
      "name": "localtovon",
      "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
    ltovon",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
        "virtualNetworkGateway1": {
          "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
    teways/vnetgw1"
        },
        "localNetworkGateway2": {
          "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
    ways/LocalSite"
        },
        "connectionType": "IPsec",
        "routingWeight": 10,
        "sharedKey": "abc123",
        "connectionStatus": "Connected",
        "ingressBytesTransferred": 33509044,
        "egressBytesTransferred": 4142431
      }


<!--HONumber=Jun16_HO2-->


