---
title: Filter för Azure IoT DPS IP-anslutning | Microsoft Docs
description: Hur du använder IP-filtrering för att blockera anslutningar från vissa IP-adresser till Azure IoT DPS-instansen. Du kan blockera anslutningar från enskilda eller intervall med IP-adresser.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284920"
---
# <a name="use-ip-filters"></a>Använda IP-filter

Säkerhet är en viktig aspekt av alla IoT-lösningar. Ibland måste du uttryckligen ange de IP-adresser som enheter kan ansluta till som en del av din säkerhets konfiguration. Med funktionen *IP-filter* för en Azure-IoT Hub Device PROVISIONING service (DPS) kan du konfigurera regler för att avvisa eller acceptera trafik från vissa IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användnings fall där det är praktiskt att blockera anslutningar till en DPS-slutpunkt från vissa IP-adresser:

* DPS bör endast ta emot trafik från ett visst intervall med IP-adresser och förkasta allt annat. Du använder till exempel din DPS med [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) för att skapa privata anslutningar mellan en DPS och dina enheter.

* Du måste avvisa trafik från IP-adresser som har identifierats som misstänkt av DPS-administratören.

## <a name="how-filter-rules-are-applied"></a>Hur filterregler tillämpas

IP-filter regler tillämpas på nivån för DPS-instansen. Därför gäller IP-filter reglerna för alla anslutningar från enheter och backend-appar med valfritt protokoll som stöds.

Alla anslutnings försök från en IP-adress som matchar en IP-regel som avvisar i din DPS får en otillåten 401 status kod och beskrivning. Svars meddelandet nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard är **IP-filter** rutnätet i portalen för DPS tomt. Den här standardinställningen innebär att DPS accepterar anslutningar från alla IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

![Standardinställningar för IP-filter för IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägga till eller redigera en regel för IP-filter

Om du vill lägga till en regel för IP-filter väljer du **+ Lägg till IP-filterlista**.

![Lägga till en filter regel för IP-filter i IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

När du har valt **Lägg till IP-filterlista**fyller du i fälten.

![När du har valt Lägg till en filter regel för IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Ange ett **namn** för IP-filterlistan. Detta måste vara en unik, SKIFT läges okänslig, alfanumerisk sträng på upp till 128 tecken. Endast ASCII 7-bitars alfanumeriska tecken plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` accepteras.

* Ange en enskild IPv4-adress eller ett block med IP-adresser i CIDR-format. I CIDR-notation 192.168.100.0/22 representerar till exempel IPv4-adresserna 1024 från 192.168.100.0 till 192.168.103.255.

* Välj **Tillåt** eller **blockera** som **åtgärd** för IP-filterlistan.

När du har fyllt i fälten väljer du **Spara** för att spara regeln. En avisering visas som meddelar dig att uppdateringen pågår.

![Meddelande om att spara en regel för IP-filter](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Alternativet **Lägg till** är inaktiverat när du når Max gränsen på 10 IP-filter.

Om du vill redigera en befintlig regel väljer du de data som du vill ändra, gör ändringen och väljer **Spara** för att spara din redigering.

> [!NOTE]
> Om IP-adresser avvisas kan andra Azure-tjänster inte interagera med DPS-instansen.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en regel för IP-filter

Om du vill ta bort en IP-filterlista väljer du pappers korgs ikonen på raden och väljer sedan **Spara**. Regeln tas bort och ändringen sparas.

![Ta bort en IoT DPS IP filter-regel](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Uppdatera IP filter regler i kod

Du kan hämta och ändra ditt DPS IP-filter med hjälp av Azure Resource providers REST-slutpunkt. Se `properties.ipFilterRules` i [createorupdate-metoden](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Att uppdatera regler för DPS-IP-filter stöds för närvarande inte med Azure CLI eller Azure PowerShell, men du kan göra det med Azure Resource Manager mallar. Se [Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md) för att få hjälp med att använda Resource Manager-mallar. Mall exemplen nedan visar hur du skapar, redigerar och tar bort DPS IP-filter regler.

### <a name="add-an-ip-filter-rule"></a>Lägg till en filter regel för IP

I följande mall skapas en ny filter regel med namnet "AllowAll" som godkänner all trafik.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Uppdatera attributen för IP-filterlistan i mallen baserat på dina krav.

| Attribut                | Beskrivning |
| ------------------------ | ----------- |
| **Namn**           | Ange ett namn för IP-filterlistan. Detta måste vara en unik, SKIFT läges okänslig, alfanumerisk sträng på upp till 128 tecken. Endast ASCII 7-bitars alfanumeriska tecken plus {"-", ":", "/", "\'", "+", "%", "_", "#", "*", "?", '! ', ' (', ') ', ', ' = ', ' @ ', '; ', ' ' '} har accepterats. |
| **Åtgärd**               | Godkända värden **accepterar** eller **avvisar** som åtgärd för IP-filterlistan. |
| **ipMask**               | Ange en enskild IPv4-adress eller ett block med IP-adresser i CIDR-format. I CIDR-notation 192.168.100.0/22 representerar till exempel IPv4-adresserna 1024 från 192.168.100.0 till 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Uppdatera en filter regel för IP

I följande mall-exempel uppdateras IP-filterlistan med namnet "AllowAll", som visas tidigare, för att avvisa all trafik.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Ta bort en regel för IP-filter

I följande mall-exempel raderas alla IP-filterlistor för DPS-instansen.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>IP-filter rule utvärdering

IP-filter regler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden acceptera eller avvisa.

Om du till exempel vill ta emot adresser i intervallet 192.168.100.0/22 och avvisa allt annat ska den första regeln i rutnätet acceptera adress intervallet 192.168.100.0/22. Nästa regel ska avvisa alla adresser med hjälp av adressintervallet 0.0.0.0/0.

Du kan ändra ordningen på dina IP filter-regler i rutnätet genom att klicka på de tre lodräta punkterna i början av en rad och använda dra och släpp.

Klicka på **Spara**om du vill spara din nya regel ordning för IP-filter.

![Ändra ordningen på dina DPS IP filter-regler](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Nästa steg

Mer information om att hantera DPS finns i:

* [Förstå IP-adresser för IoT DPS](iot-dps-understand-ip-address.md)
* [Konfigurera DPS med hjälp av Azure CLI](how-to-manage-dps-with-cli.md)
* [Kontrol lera åtkomsten till DPS](how-to-control-access.md)
