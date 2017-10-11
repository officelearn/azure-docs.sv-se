## <a name="route-tables"></a>Vägtabeller
Väg tabellen resurser innehåller vägar som används för att definiera hur trafiken flödar i Azure-infrastrukturen. Du kan använda användardefinierade vägar (UDR) för att skicka all trafik från ett visst undernät till en virtuell installation, till exempel en brandvägg eller intrångsidentifiering identifiering-system (ID). Du kan associera en routingtabell till undernät. 

Routningstabeller innehåller följande egenskaper.

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **vägar** |Samling användardefinierade vägar i routningstabellen |Se [användardefinierade vägar](#User-defined-routes) |
| **undernät** |Samling av undernät i vägtabellen tillämpas på |Se [undernät](#Subnets) |

### <a name="user-defined-routes"></a>Användardefinierade vägar
Du kan skapa udr: er för att ange där trafik ska skickas till, baserat på dess mål-adress. Du kan se en väg som standard-gateway-definition baserat på mål-adress för ett nätverkspaket.

Udr: er innehåller följande egenskaper. 

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **addressPrefix** |Adressprefixet eller fullständig IP-adress för mål |192.168.1.0/24, 192.168.1.101 |
| **nextHopType** |Typ av enhet som trafiken skickas till |VirtualAppliance, VPN-Gateway, Internet |
| **nextHopIpAddress** |IP-adressen för nästa hopp |192.168.1.4 |

Exempel routningstabellen i JSON-format:

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### <a name="additional-resources"></a>Ytterligare resurser
* Hämta mer information [udr: er](../articles/virtual-network/virtual-networks-udr-overview.md).
* Läs den [REST API-referensdokumentation](https://msdn.microsoft.com/library/azure/mt502549.aspx) för routningstabeller.
* Läs den [REST API-referensdokumentation](https://msdn.microsoft.com/library/azure/mt502539.aspx) definierats vägar (udr: er) för användaren.

