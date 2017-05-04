Använd cmdleten New-AzureRmVirtualNetworkGatewayConnection om du vill ändra IP-adressen för gatewayen. För tillfället går det inte att ändra IP-adressen för gatewayen med cmdlet:en Set.

### <a name="gwipnoconnection"></a>Ändra IP-adress för gateway – ingen gatewayanslutning
Använd exemplet nedan för att ändra IP-adressen till gatewayen för din lokala nätverksgateway som ännu inte har en anslutning. Du kan också ändra adressprefixen på samma gång. Se till att använda det befintliga namnet på din lokala nätverksgateway. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

Använd följande exempel och ersätt värdena med dina egna:

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>Ändra IP-adress till gateway – existerande gatewayanslutning
Om det redan finns en gatewayanslutning, måste du först ta bort anslutningen. När anslutningen har tagits bort kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Du kan också ändra adressprefixen på samma gång. Det medför en del avbrott för din VPN-anslutning.

> [!IMPORTANT]
> Ta inte bort VPN-gatewayen. Om du gör det måste du gå tillbaka och utföra stegen för att skapa den på nytt. Dessutom måste du uppdatera din lokala VPN-enhet med den nya IP-adressen för VPN-gatewayen.
> 
> 

1. Ta bort anslutningen. Du kan ta reda på namnet på anslutningen med hjälp av cmdleten Get-AzureRmVirtualNetworkGatewayConnection.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Ändra värdet för GatewayIpAddress. Du kan också ändra adressprefixen på samma gång. Se till att använda det befintliga namnet på din lokala nätverksgateway. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Skapa anslutningen. I det här exemplet konfigurerar vi en IPsec-anslutningstyp. När du återskapar anslutningen kan du använda den anslutningstyp som har angetts för din konfiguration. Ytterligare anslutningar finns på sidan [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx).  För att få VirtualNetworkGateway-namnet kan du köra cmdlet:en Get-AzureRmVirtualNetworkGateway.
   
    Ange variablerna.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Skapa anslutningen.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```