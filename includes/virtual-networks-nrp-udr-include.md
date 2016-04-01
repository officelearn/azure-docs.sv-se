## Routentabellen
Routentabelle enthalten Routen, die den Datenfluss in Ihrer Azure-Infrastruktur bestimmen. Sie können benutzerdefinierte Routen nutzen, um den gesamten Datenverkehr aus einem bestimmten Subnetz zu einem virtuellen Gerät zu leiten, z. B. einer Firewall oder einem System zum Erkennen von Netzwerkangriffen. Sie können Subnetzen eine Routentabelle zuordnen. 

Routentabellen haben die folgenden Eigenschaften:

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**routes**|Auflistung benutzerdefinierter Routen in der Routentabelle|finden Sie unter [benutzerdefinierte Routen](#User-defined-routes)|
|**Subnetze**|Auflistung von Subnetzen, für die die Routentabelle gilt|finden Sie unter [Subnetze](#Subnets)|


### Benutzerdefinierte Routen
Sie können benutzerdefinierte Routen erstellen, um anzugeben, wohin Datenverkehr basierend auf seiner Zieladresse gesendet werden soll. Sie können sich eine Route als Definition eines Standardgateways basierend auf der Zieladresse eines Netzwerkpakets vorstellen.

Benutzerdefinierte Routen haben die folgenden Eigenschaften: 

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**addressPrefix**|Adresspräfix oder vollständige IP-Adresse des Ziels|192.168.1.0/24, 192.168.1.101|
|**nextHopType**|Typ des Zielgeräts des Datenverkehrs|VirtualAppliance, VPN Gateway, Internet|
|**nextHopIpAddress**|IP-Adresse des nächsten Hops|192.168.1.4|


Beispielroutentabelle im JSON-Format:

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

### Zusätzliche Ressourcen

- Weitere Informationen zu erhalten [UDRs](virtual-networks-udr-overview.md).
- Lesen der [REST-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt502549.aspx) für Routentabellen.
- Lesen der [REST-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt502539.aspx) für Benutzer definiert Routen.

