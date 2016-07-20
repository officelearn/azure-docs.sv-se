### Lägg till eller ta bort prefix om du inte har skapat någon VPN-gatewayanslutning än

- **Om du vill lägga till** ytterligare adressprefix till en lokal nätverksgateway som du har skapat, men som inte har en VPN-gatewayanslutning än, använder du exemplet nedan.

        $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Om du vill ta bort** ett adressprefix från en lokal nätverksgateway som inte har en VPN-anslutning, använder du exemplet nedan. Utelämna de prefix som du inte längre behöver. I det här exemplet behöver vi inte längre prefixet 20.0.0.0/24 (från föregående exempel) så vi kommer att uppdatera den lokala nätverksgatewayen och ta bort det prefixet.

        $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Lägg till eller ta bort prefix om redan har skapat en VPN-gatewayanslutning

Om du har skapat din VPN-anslutning och vill lägga till eller ta bort IP-adressprefixet som ingår i din lokala nätverksgateway, behöver du genomföra följande steg i turordning. Det kommer leda till en del avbrott för din VPN-anslutning.

>[AZURE.IMPORTANT] Ta inte bort VPN-gatewayen. Om du gör det, måste du göra om alla stegen för att återskapa den, samt omkonfigurera din lokala router med den nya inställningarna.
 
1. Ta bort IPsec-anslutningen. 
2. Ändra prefixen för din lokala nätverksgateway. 
3. Skapa en ny IPsec-anslutning. 

Du kan använda följande exempel som riktlinjer.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

    Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
    Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
    
    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=Jun16_HO2-->


