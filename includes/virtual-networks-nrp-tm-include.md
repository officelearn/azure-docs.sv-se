## <a name="traffic-manager-profile"></a>Traffic Manager-profilen
Traffic manager och dess underordnade endpoint-resursen kan du aktivera DNS-routning till slutpunkter i Azure och utanför Azure. Sådana trafikfördelning styrs av routningsmetoder för principen. Traffic manager kan också endpoint hälsotillstånd som ska övervakas och trafik som distribueras på rätt sätt baserat på hälsotillståndet för en slutpunkt. 

| Egenskap | Beskrivning |
| --- | --- |
| **trafficRoutingMethod** |möjliga värden är *prestanda*, *viktat*, och *prioritet* |
| **Det går** |FQDN för profilen |
| **Protokoll** |övervakning, möjliga värden är *HTTP* och *HTTPS* |
| **Port** |övervakning av port |
| **Sökväg** |övervakningssökvägen |
| **Slutpunkter** |behållare för slutpunkt-resurser |

### <a name="endpoint"></a>Slutpunkt
En slutpunkt är en underordnad resurs för en Traffic Manager-profil. Representerar en tjänst eller webbslutpunkten som trafiken fördelas baserat på de konfigurerade principinställningarna i resursen Traffic Manager-profilen. 

| Egenskap | Beskrivning |
| --- | --- |
| **Typ** |typ av slutpunkten möjliga värden är *Azure slutpunkt*, *extern slutpunkt*, och *kapslade slutpunkt* |
| **targetResourceId** |offentliga IP-adressen för en tjänst eller web slutpunkt. Detta kan vara en Azure eller extern slutpunkt. |
| **Vikt** |slutpunkten vikten som används vid hantering av nätverkstrafik. |
| **Prioritet** |prioriteten för slutpunkten, används för att definiera Redundansåtgärden |

Exempel på Traffic Manager i Json-format: 

        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }


## <a name="additional-resources"></a>Ytterligare resurser
Läs [REST API-dokumentation för Traffic Manager](https://msdn.microsoft.com/library/azure/mt163664.aspx) för mer information.

