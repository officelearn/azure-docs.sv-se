### <a name="noconnection"></a>Ändra IP-adressprefix för nätverksgateway – ingen gatewayanslutning

Om du inte har någon gatewayanslutning och du vill lägga till eller ta bort IP-adressprefix använder du samma kommando som du använder för att skapa den lokala nätverksgatewayen, [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_create). Du kan också använda det här kommandot för att uppdatera gatewayens IP-adress för VPN-enheten. Använd det befintliga namnet på din lokala nätverksgateway när du ska skriva över befintliga inställningar. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

Varje gång du gör en ändring måste hela listan med prefix specificeras, inte bara de prefix du vill ändra. Ange endast de prefix du vill behålla. I det här fallet 10.0.0.0/24 och 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>Ändra IP-adressprefix för nätverksgateway – existerande gatewayanslutning

Om du har en gatewayanslutning och vill lägga till eller ta bort IP-adressprefix kan du uppdatera prefixen med [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update). Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressprefixen behöver du inte ta bort VPN-gatewayen.

Varje gång du gör en ändring måste hela listan med prefix specificeras, inte bara de prefix du vill ändra. I det här exemplet är 10.0.0.0/24 och 20.0.0.0/24 redan med. Vi lägger till prefixen 30.0.0.0/24 och 40.0.0.0/24 och anger alla 4 prefixen vid uppdateringen.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
