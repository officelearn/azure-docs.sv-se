---
title: DPS IP-anslutningsfilter för Azure IoT | Microsoft-dokument
description: Så här använder du IP-filtrering för att blockera anslutningar från specifika IP-adresser till din Azure IoT DPS-instans. Du kan blockera anslutningar från enskilda eller intervall av IP-adresser.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284920"
---
# <a name="use-ip-filters"></a>Använda IP-filter

Säkerhet är en viktig aspekt av alla IoT-lösningar. Ibland måste du uttryckligen ange IP-adresser från vilka enheter kan ansluta som en del av din säkerhetskonfiguration. *Med IP-filterfunktionen* för en DPS -etableringstjänst för Azure IoT Hub Device Provisioning Service (DPS) kan du konfigurera regler för att avvisa eller acceptera trafik från specifika IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användningsfall där det är användbart att blockera anslutningar till en DPS-slutpunkt från vissa IP-adresser:

* Din DPS bör endast ta emot trafik från ett angivet intervall av IP-adresser och avvisa allt annat. Du använder till exempel din DPS med [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) för att skapa privata anslutningar mellan en DPS och dina enheter.

* Du måste avvisa trafik från IP-adresser som har identifierats som misstänkta av DPS-administratören.

## <a name="how-filter-rules-are-applied"></a>Så här tillämpas filterregler

IP-filterreglerna tillämpas på DPS-instansnivå. Därför gäller IP-filterreglerna för alla anslutningar från enheter och backend-appar som använder protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en avvisning av IP-regel i DPS-instansen får en obehörig 401-statuskod och beskrivning. Svarsmeddelandet nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställning

Som standard är **RUTNÄTET FÖR IP-filter** i portalen för DPS tomt. Den här standardinställningen innebär att ditt DPS accepterar anslutningar från valfri IP-adress. Den här standardinställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0/0.

![Standardinställningar för IoT DPS-IP-filter](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägga till eller redigera en IP-filterregel

Om du vill lägga till en IP-filterregel väljer du **+ Lägg till IP-filterregel**.

![Lägga till en IP-filterregel i en IoT-DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

När du har valt **Lägg till IP-filterregel**fyller du i fälten.

![När du har valt Lägg till en IP-filterregel](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Ange ett **namn** för IP-filterregeln. Detta måste vara en unik, skiftlägesokänslig, alfanumerisk sträng upp till 128 tecken lång. Endast ASCII 7-bitars alfanumeriska tecken plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` accepteras.

* Ange en enda IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation 192.168.100.0/22 representerar till exempel 1024 IPv4-adresser från 192.168.100.0 till 192.168.103.255.

* Välj **Tillåt** eller **Blockera** som **åtgärd** för IP-filterregeln.

När du har fyllt i fälten väljer du **Spara** för att spara regeln. Du ser en avisering som meddelar dig att uppdateringen pågår.

![Meddelande om hur du sparar en IP-filterregel](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Alternativet **Lägg till** är inaktiverat när du når maximalt 10 IP-filterregler.

Om du vill redigera en befintlig regel markerar du de data du vill ändra, gör ändringen och väljer sedan **Spara** för att spara redigeringen.

> [!NOTE]
> Att avvisa IP-adresser kan förhindra att andra Azure-tjänster interagerar med DPS-instansen.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en IP-filterregel

Om du vill ta bort en IP-filterregel markerar du papperskorgen på den raden och väljer sedan **Spara**. Regeln tas bort och ändringen sparas.

![Ta bort en IP-filterregel för IoT-DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Uppdatera IP-filterregler i kod

Du kan hämta och ändra DPS IP-filtret med hjälp av Azure Resource Provider's REST-slutpunkt. Se `properties.ipFilterRules` i [metoden createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Uppdatering av DPS IP-filterregler stöds för närvarande inte med Azure CLI eller Azure PowerShell, men kan utföras med Azure Resource Manager-mallar. Se [Azure Resource Manager-mallar](../azure-resource-manager/templates/overview.md) för vägledning om hur du använder Resource Manager-mallar. Mallexemplen som följer visar hur du skapar, redigerar och tar bort DPS IP-filterregler.

### <a name="add-an-ip-filter-rule"></a>Lägga till en IP-filterregel

I följande mallexempel skapas en ny IP-filterregel med namnet "AllowAll" som accepterar all trafik.

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

Uppdatera IP-filterregelattributen för mallen baserat på dina krav.

| Attribut                | Beskrivning |
| ------------------------ | ----------- |
| **FilterNamn**           | Ange ett namn för IP-filterregeln. Detta måste vara en unik, skiftlägesokänslig, alfanumerisk sträng upp till 128 tecken lång. Endast ascii 7-bitars alfanumeriska tecken plus {'-,,',\''", '/', '.', '+', '%', '_', '*', '?', '!', '!', '", ')', ',', '=', '@', ';','''}'} godtas. |
| **Åtgärd**               | Godkända värden **accepteras** eller **Avvisa** som åtgärd för IP-filterregeln. |
| **ipMask (ipMask)**               | Ange en enda IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation 192.168.100.0/22 representerar till exempel 1024 IPv4-adresser från 192.168.100.0 till 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Uppdatera en IP-filterregel

I följande mallexempel uppdateras IP-filterregeln "AllowAll", som tidigare visats, för att avvisa all trafik.

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

### <a name="delete-an-ip-filter-rule"></a>Ta bort en IP-filterregel

I följande mallexempel tas alla IP-filterregler för DPS-instansen bort.

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



## <a name="ip-filter-rule-evaluation"></a>Utvärdering av IP-filterregel

IP-filterregler tillämpas i ordning och den första regeln som matchar IP-adressen avgör åtgärden acceptera eller avvisa.

Om du till exempel vill acceptera adresser i intervallet 192.168.100.0/22 och avvisa allt annat, bör den första regeln i rutnätet acceptera adressintervallet 192.168.100.0/22. Nästa regel bör avvisa alla adresser med hjälp av intervallet 0.0.0.0/0.

Du kan ändra ordningen på IP-filterreglerna i rutnätet genom att klicka på de tre lodräta punkterna i början av en rad och använda dra och släpp.

Om du vill spara den nya IP-filterregelordningen klickar du på **Spara**.

![Ändra ordningen på dina DPS IP-filterregler](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utforskar hanteringen av DPS finns i:

* [Förstå IoT DPS IP-adresser](iot-dps-understand-ip-address.md)
* [Konfigurera DPS med Azure CLI](how-to-manage-dps-with-cli.md)
* [Kontrollera åtkomsten till DPS](how-to-control-access.md)
