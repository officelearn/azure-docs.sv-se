## <a name="azure-dns"></a>Azure DNS
Azure DNS är värdtjänsten för DNS-domäner som tillhandahåller namnmatchning med hjälp av Microsoft Azure-infrastrukturen.

| Egenskap | Beskrivning | Exempelvärde |
| --- | --- | --- |
| **DNSzones** |Information om zonen till DNS-värdposter i en viss domän |/ subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com ” |

### <a name="dns-record-sets"></a>DNS-postuppsättningar
DNS-zoner ha ett underordnat objekt med namnet postuppsättningen. Postuppsättningar är en samling värdposter efter typ för en DNS-zon. Posttyper är A, AAAA, CNAME, MX, NS, SOA, SRV och TXT.

| Egenskap | Beskrivning | Exempelvärde |
| --- | --- | --- |
| A |IPv4-posttyp |/subscriptions/{GUID}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA |IPv6-typ |/subscriptions/{GUID}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME |kanoniskt namn posttyp <sup>1</sup> |/subscriptions/{GUID}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX |e-post |/subscriptions/{GUID}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/Mail |
| NS |namn på server-posttyp |/subscriptions/{GUID}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA |Början av myndigheten posttyp <sup>2</sup> |/subscriptions/{GUID}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV |typ av tjänst |/subscriptions/{GUID}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> kan bara ett värde per uppsättningen av poster.

<sup>2</sup> tillåter endast en posttyp SOA per DNS-zon. 

Exempel på DNS-zonen i Json-format:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "newZoneName": {
          "type": "String",
          "metadata": {
              "description": "The name of the DNS zone to be created."
          }
        },
        "newRecordName": {
          "type": "String",
          "defaultValue": "www",
          "metadata": {
              "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
          }
        }
      },
      "resources": 
      [
        {
          "type": "microsoft.network/dnszones",
          "name": "[parameters('newZoneName')]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": {
          }
        },
        {
          "type": "microsoft.network/dnszones/a",
          "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": 
          {
            "TTL": 3600,
            "ARecords": 
            [
                {
                    "ipv4Address": "1.2.3.4"
                },
                {
                    "ipv4Address": "1.2.3.5"
                }
            ]
          },
          "dependsOn": [
            "[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
          ]
        }
          ]
    }

## <a name="additional-resources"></a>Ytterligare resurser
Läs den [REST API-dokumentation för DNS-zoner ](https://msdn.microsoft.com/library/azure/mt130626.aspx) för mer information.

Läs den [REST API-dokumentation för DNS-postuppsättningar](https://msdn.microsoft.com/library/azure/mt130627.aspx) för mer information.

