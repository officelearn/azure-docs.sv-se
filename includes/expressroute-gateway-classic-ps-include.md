Du måste skapa ett VNet och ett gateway-undernät, innan du arbetar med följande uppgifter. Se artikeln [konfigurera ett virtuellt nätverk med den klassiska portalen](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) för mer information.   

## <a name="add-a-gateway"></a>Lägga till en gateway
Använd kommandot nedan för att skapa en gateway. Se till att ersätta alla värden för dina egna.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Kontrollera gatewayen som har skapats
Använd kommandot nedan för att kontrollera att gatewayen har skapats. Detta kommando hämtar också gateway-ID som du behöver för andra åtgärder.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway
Det finns ett antal [Gateway-SKU: er](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra Gateway-SKU när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance gateway. Om du vill ändra din gateway till en gateway för UltraPerformance först ta bort den befintliga ExpressRoute-gatewayen och sedan skapa en ny UltraPerformance gateway. Om du vill konvertera din gateway från en UltraPerformance gateway tar du bort UltraPerformance gateway och sedan skapa en ny gateway. 
> 
> 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Ta bort en gateway
Använd kommandot nedan för att ta bort en gateway

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>