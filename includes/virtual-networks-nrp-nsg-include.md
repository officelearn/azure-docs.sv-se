## <a name="network-security-group"></a>Nätverkssäkerhetsgrupp
NSG-resurs kan skapa säkerhetsgräns för arbetsbelastningar, genom att implementera Tillåt och neka regler. Dessa regler kan tillämpas på en virtuell dator, ett nätverkskort eller ett undernät.

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **undernät** |Lista över undernät-ID NSG: N används. |/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-XXXXXXXXXXXX/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/Subnets/FrontEnd |
| **securityRules** |Lista över säkerhetsregler som utgör NSG: N |Se [säkerhetsregeln](#Security-rule) nedan |
| **defaultSecurityRules** |Lista över säkerhet standardregler finns i varje NSG |Se [standard säkerhetsregler](#Default-security-rules) nedan |

* **Säkerhetsregeln** -en NSG kan ha flera säkerhetsregler som definierats. Varje regel kan tillåta eller neka olika typer av trafik.

### <a name="security-rule"></a>Säkerhetsregeln
En säkerhetsregel är en underordnad resurs som tillhör en NSG som innehåller egenskaperna nedan.

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **Beskrivning** |Beskrivning av regeln |Tillåt inkommande trafik för alla virtuella datorer i undernät X |
| **protokollet** |Protokoll att matcha för regeln |TCP, UDP eller * |
| **sourcePortRange** |Källportintervall att matcha för regeln |80, 100-200, * |
| **destinationPortRange** |Målportintervall att matcha för regeln |80, 100-200, * |
| **sourceAddressPrefix** |Källadress-prefix för att matcha för regeln |10.10.10.1 10.10.10.0/24 VirtualNetwork |
| **destinationAddressPrefix** |Måladress-prefix att matcha för regeln |10.10.10.1 10.10.10.0/24 VirtualNetwork |
| **riktning** |Trafikriktning att matcha för regeln |inkommande eller utgående |
| **prioritet** |Prioritet för regeln. Reglerna kontrolleras i prioritsordning, när en regel testas inga fler regler för matchning. |10, 100, 65000 |
| **åtkomst** |Typ av åtkomst som ska tillämpas om regeln matchar |tillåt eller neka |

Exempel NSG i JSON-format:

    {
        "name": "NSG-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkSecurityGroups",
        "location": "westus",
        "tags": {
            "displayName": "NSG - Front End"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "securityRules": [
                {
                    "name": "rdp-rule",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "description": "Allow RDP",
                        "protocol": "Tcp",
                        "sourcePortRange": "*",
                        "destinationPortRange": "3389",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "access": "Allow",
                        "priority": 100,
                        "direction": "Inbound"
                    }
                }
            ],
            "defaultSecurityRules": [
                { [...],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                }
            ]
        }
    }

### <a name="default-security-rules"></a>Standardsäkerhetsregler

Standard säkerhetsregler ha samma egenskaper som är tillgängliga i säkerhetsregler. De finns för att ge grundläggande anslutning mellan resurser som har NSG: er som tillämpas på. Kontrollera att du vet vilken [standard säkerhetsregler](../articles/virtual-network/virtual-networks-nsg.md#default-rules) finns.

### <a name="additional-resources"></a>Ytterligare resurser
* Hämta mer information [NSG: er](../articles/virtual-network/virtual-networks-nsg.md).
* Läs den [REST API-referensdokumentation](https://msdn.microsoft.com/library/azure/mt163615.aspx) för NSG: er.
* Läs den [REST API-referensdokumentation](https://msdn.microsoft.com/library/azure/mt163580.aspx) för säkerhetsregler.
