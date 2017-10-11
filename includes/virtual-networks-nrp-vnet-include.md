## <a name="virtual-network"></a>Virtual Network
Virtuella nätverk (VNET) och undernät resurser hjälpa dig att definiera en säkerhetsgräns för arbetsbelastningar som körs i Azure. Ett virtuellt nätverk kännetecknas av en samling adressutrymmen, definierad som CIDR-block. 

> [!NOTE]
> Nätverksadministratörer är bekanta med CIDR-notering. Om du inte är bekant med CIDR, [mer information om den](http://whatismyipaddress.com/cidr).
> 
> 

![Virtuella nätverk med flera undernät](./media/resource-groups-networking/Figure4.png)

Vnet innehåller följande egenskaper.

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **addressSpace** |Samling av adressprefix som utgör VNet i CIDR-notation |192.168.0.0/16 |
| **undernät** |Samling av undernät som utgör VNet |Se [undernät](#Subnets) nedan. |
| **IP-adress** |IP-adress som tilldelats objekt. Det här är en skrivskyddad egenskap. |104.42.233.77 |

### <a name="subnets"></a>Undernät
Ett undernät är en underordnad resurs i ett VNet och hjälper dig att definiera segmenten i adressutrymmen i CIDR-block med IP-adressprefix. Nätverkskorten kan läggas till i undernät och anslutna till virtuella datorer kan man har anslutning för olika arbetsbelastningar.

Undernät innehåller följande egenskaper. 

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **addressPrefix** |Enkel adressprefixet som utgör undernät i CIDR-notation |192.168.1.0/24 |
| **networkSecurityGroup** |NSG tillämpas för undernätet |Se [NSG: er](#Network-Security-Group) |
| **Migreringstillståndet** |Routningstabellen tillämpad på undernätet |Se [UDR](#Route-table) |
| **ipConfigurations** |Samling av IP-configruation objekt som används av nätverkskort som är anslutna till undernätet |Se [UDR](#Route-table) |

Exempel VNet i JSON-format:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Ytterligare resurser
* Hämta mer information [VNet](../articles/virtual-network/virtual-networks-overview.md).
* Läs den [REST API-referensdokumentation](https://msdn.microsoft.com/library/azure/mt163650.aspx) för Vnet.
* Läs den [REST API-referensdokumentation](https://msdn.microsoft.com/library/azure/mt163618.aspx) för undernät.

