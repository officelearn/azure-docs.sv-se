### <a name="noconnection"></a>Hur man lägger till eller tar bort prefix – ingen gatewayanslutning

- **Om du vill lägga till** ytterligare adressprefix till en lokal nätverksgateway som du har skapat, men som inte har en gatewayanslutning än, använder du exemplet nedan. Se till att ändra värdena till dina egna.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Om du vill ta bort** ett adressprefix från en lokal nätverksgateway som inte har en VPN-anslutning, använder du exemplet nedan. Utelämna de prefix som du inte längre behöver. I det här exemplet behöver vi inte längre prefixet 20.0.0.0/24 (från föregående exempel) så vi kommer att uppdatera den lokala nätverksgatewayen och ta bort det prefixet.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Hur man lägger till eller tar bort prefix – existerande gatewayanslutning

Om du har skapat din gatewayanslutning och vill lägga till eller ta bort IP-adressprefixet som ingår i din lokala nätverksgateway, behöver du genomföra följande steg i turordning. Det kommer leda till en del avbrott för din VPN-anslutning. När du uppdaterar dina prefix, måste du först ta bort anslutningen, ändra prefix och sedan skapa en ny anslutning. Se till att ändra värdena till dina egna i exemplen nedan.

>[AZURE.IMPORTANT] Ta inte bort VPN-gatewayen. Om du gör det, måste du göra om alla stegen för att återskapa den, samt omkonfigurera din lokala router med den nya inställningarna.
 
1. Ta bort anslutningen.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Ändra IP-adressprefixen för din lokala nätverksgateway.

    Ställ in variabeln för LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Ändra prefixen.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Skapa anslutningen. I det här exemplet konfigurerar vi en IPSec-anslutningstyp. När du återskapar anslutningen kan du använda den anslutningstyp som har angetts för din konfiguration. Ytterligare anslutningar finns på sidan [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx).

    Ställ in variabeln för VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Skapa anslutningen. Observera att det här exemplet använder variabeln $local som du anger i föregående steg.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'


<!--HONumber=Sep16_HO3-->


