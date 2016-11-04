För att ändra IP-adress till gatewayen, använd `New-AzureRmVirtualNetworkGatewayConnection`-cmdlet. Så länge du håller namnet på den lokala nätverksgatewayen exakt på samma sätt som det befintliga namnet, skriver inställningarna över. För tillfället stöder inte cmdlet:en "Ange" ändring av IP-adress till gateway.

### <a name="gwipnoconnection"></a>Så här ändrar du IP-adress till gateway - ingen gatewayanslutning
Använd exemplet nedan för att uppdatera IP-adressen till gatewayen för din lokala nätverksgateway som ännu inte har en anslutning. Du kan också uppdatera adressprefix på samma gång. De inställningar du anger skriver över de befintliga inställningarna. Se till att använda det befintliga namnet på din lokala nätverksgateway. Om du inte gör detta skapar du en ny lokal nätverksgateway, och skriver inte över den befintliga.

Använd följande exempel och ersätt värdena för dina egna.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Så här ändrar du IP-adress till gateway - existerande gatewayanslutning
Om det redan finns en gatewayanslutning, måste du först ta bort anslutningen. Sedan kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Det kommer leda till en del avbrott för din VPN-anslutning.

> [!IMPORTANT]
> Ta inte bort VPN-gatewayen. Om du gör det måste du gå tillbaka genom stegen för att återskapa den, samt konfigurera om routern lokalt med IP-adressen som ska tilldelas till den nya gatewayen.
> 
> 

1. Ta bort anslutningen. Du hittar namnet på anslutningen genom att använda `Get-AzureRmVirtualNetworkGatewayConnection`-cmdlet:en.
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName
2. Ändra värdet för GatewayIpAddress. Du kan också, om det behövs, ändra dina adressprefix vid detta tillfälle. Observera att detta ersätter befintliga inställningar för lokal nätverksgateway. Använd det befintliga namnet på din lokala nätverksgateway när du ändrar så att inställningarna skriver över. Om du inte gör detta skapar du en ny lokal nätverksgateway, och ändrar inte den befintliga.
   
        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
3. Skapa anslutningen. I det här exemplet konfigurerar vi en IPSec-anslutningstyp. När du återskapar anslutningen kan du använda den anslutningstyp som har angetts för din konfiguration. Ytterligare anslutningar finns på sidan [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx).  För att få VirtualNetworkGateway-namnet kan du köra `Get-AzureRmVirtualNetworkGateway`-cmdlet:en.
   
    Ange variabler:
   
        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
   
    Skapa anslutningen:
   
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!--HONumber=Sep16_HO3-->


