## Virtuelles Netzwerk
Mit virtuellen Netzwerken (VNET) und Subnetzressourcen können Sie eine Sicherheitsbegrenzung für in Azure ausgeführte Workloads definieren. Ein VNet ist durch eine Auflistung von Adressräumen gekennzeichnet, die als CIDR-Blöcke bezeichnet werden. 

>[AZURE.NOTE] Netzwerkadministratoren sind mit CIDR-Notation vertraut. Wenn Sie nicht mit CIDR-WERTS vertraut sind [erfahren Sie mehr über diese](http://whatismyipaddress.com/cidr).

![VNet mit mehreren Subnetzen](./media/resource-groups-networking/Figure4.png)

VNets umfassen die folgenden Eigenschaften:

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**addressSpace**|Auflistung der Adresspräfixe, aus denen das VNet besteht, in CIDR-Notation|192.168.0.0/16|
|**Subnetze**|Auflistung von Subnetzen, aus denen das VNet besteht|finden Sie unter [Subnetze](#Subnets) unten.|
|**ipAddress**|Die dem Objekt zugewiesene IP-Adresse. Dies ist eine schreibgeschützte Eigenschaft.|104.42.233.77|

### Subnetze
Ein Subnetz ist eine untergeordnete Ressource eines VNet und hilft, die Segmente von Adressräumen innerhalb eines CIDR-Blocks mithilfe von IP-Adressenpräfixen zu definieren. NICs können zu Subnetzen hinzugefügt und mit virtuellen Computern verbunden werden, sodass sie Konnektivität für verschiedene Workloads bereitstellen.

Subnetze umfassen die folgenden Eigenschaften: 

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**addressPrefix**|Einzelnes Adresspräfix für das Subnetz in CIDR-Notation|192.168.1.0/24|
|**networkSecurityGroup**|Auf das Subnetz angewendete NSG|finden Sie unter [NSGs](#Network-Security-Group)|
|**routeTable**|Auf das Subnetz angewendete Routentabelle|finden Sie unter [UDR](#Route-table)|
|**ipConfigurations**|Auflistung von IP-Konfigurationsobjekten, die von mit dem Subnetz verbundenen NICs verwendet werden|finden Sie unter [UDR](#Route-table)|


Beispiel-VNet im JSON-Format:

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

### Zusätzliche Ressourcen

- Weitere Informationen zu erhalten [VNet](virtual-networks-overview.md).
- Lesen der [REST-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt163650.aspx) für VNets.
- Lesen der [REST-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt163618.aspx) Subnetze.

