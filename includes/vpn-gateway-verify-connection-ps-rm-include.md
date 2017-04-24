Du kan kontrollera att anslutningen har upprättats med cmdleten **Get-AzureRmVirtualNetworkGatewayConnection**, med eller utan flaggan **–Debug**. 

1. Använd följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Välj A om du uppmanas, för att köra Alla. I exemplet avser **-Name** namnet på den anslutning du skapade och vill testa.

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. Visa värdena när cmdlet:en har slutförts. I exemplet nedan visas anslutningsstatusen som Ansluten och du kan se ingående och utgående byte.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```
  