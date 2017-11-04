## <a name="nic"></a>NÄTVERKSKORT
En nätverksresurs interface card (NIC) ger nätverksanslutning till ett befintligt undernät i ett VNet-resursen. Även om du kan skapa ett nätverkskort som ett fristående objekt, måste du koppla den till ett annat objekt att faktiskt tillhandahålla anslutningar. Ett nätverkskort kan användas för att ansluta en virtuell dator till ett undernät, en offentlig IP-adress eller en belastningsutjämnare.  

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **virtualMachine** |Virtuella nätverkskortet är associerad med. |/subscriptions/{GUID}/../microsoft.Compute/virtualMachines/vm1 |
| **MAC-adress** |MAC-adress för nätverkskortet |ett värde mellan 4 och 30 |
| **networkSecurityGroup** |NSG som kopplats till nätverkskortet |/subscriptions/{GUID}/../microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |DNS-inställningarna för nätverkskortet |Se [PIP](#Public-IP-address) |

Ett nätverkskort eller NIC, representerar ett nätverksgränssnitt som kan kopplas till en virtuell dator (VM). En virtuell dator kan ha en eller flera nätverkskort.

![NIC på en enda virtuell dator](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>IP-konfigurationer
Är ett underordnat objekt med namnet **ipConfigurations** som innehåller följande egenskaper:

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **undernät** |NIC-undernätet har onnected till. |/subscriptions/{GUID}/../microsoft.Network/virtualNetworks/myvnet1/Subnets/mysub1 |
| **privateIPAddress** |IP-adress för nätverkskortet i undernätet |10.0.0.8 |
| **privateIPAllocationMethod** |IP-allokeringsmetod |Dynamiska eller statiska |
| **enableIPForwarding** |Om nätverkskortet kan användas för Routning |True eller false |
| **primär** |Om nätverkskortet är det primära nätverkskortet för den virtuella datorn |True eller false |
| **publicIPAddress** |PIP som är kopplade till nätverkskortet |Se [DNS-inställningar](#DNS-settings) |
| **loadBalancerBackendAddressPools** |Säkerhetskopiera end-adresspooler nätverkskortet som är associerad med | |
| **loadBalancerInboundNatRules** |Inkommande belastningen belastningsutjämnaren NAT-regler som nätverkskortet är kopplad till | |

Exempel offentliga IP-adressen i JSON-format:

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>Ytterligare resurser
* Läs den [REST API-referensdokumentation](https://msdn.microsoft.com/library/azure/mt163579.aspx) för nätverkskort.

