---
title: "Resurs-providern: översikt | Microsoft Docs"
description: "Lär dig mer om den nya Nätverksresursprovidern i Azure Resource Manager"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 79bf09da-4809-45cb-8d21-705616ef24dc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 2428c707ddeed281fddd1e57bc5574603f0b9b1c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="network-resource-provider"></a>Provider för nätverksresurser
Behovet av en gäller i dagens företag lyckade är möjligheten att bygga och hantera anpassade program i stor skala nätverk på ett flexibelt, flexibel, säker och repeterbara sätt. Azure Resource Manager kan du skapa sådana program som en enda samling resursgrupper. Dessa resurser hanteras via olika resursproviders under Resource Manager.

Azure Resource Manager förlitar sig på olika resursproviders att ge åtkomst till resurser. Det finns tre huvudsakliga resursproviders: nätverk, lagring och beräkning. Det här dokumentet beskriver egenskaper och fördelarna med Nätverksresursprovidern, inklusive:

* **Metadata** – du kan lägga till information till resurser med hjälp av taggar. Dessa taggar kan användas för att spåra resursutnyttjande alla resursgrupper och prenumerationer.
* **Större kontroll över nätverket** - nätverksresurser är löst sammanfogade och du kan kontrollera dem på ett bättre sätt. Det innebär att du har mer flexibilitet för att hantera nätverksresurser.
* **Snabbare configuration** -eftersom nätverksresurser är löst sammansatta, kan du skapa och samordna nätverksresurser parallellt. Detta har kraftigt minska konfiguration.
* **Rollbaserad åtkomstkontroll** -RBAC ger standardroller med specifika säkerhetsomfattning, förutom att tillåta skapandet av anpassade roller för säker hantering.
* **Enklare hantering och distribution** – det är enklare att distribuera och hantera program eftersom du kan skapa en hel programstack som en enda samling resurser i en resursgrupp. Och snabbare att distribuera, eftersom du kan distribuera genom att bara ange en mall för JSON-nyttolast.
* **Snabb anpassning** -du kan använda mallar deklarativ typ för att aktivera repeterbara och snabb anpassning av distributioner.
* **Anpassning av repeterbara** -du kan använda mallar deklarativ typ för att aktivera repeterbara och snabb anpassning av distributioner.
* **Hanteringsgränssnitt** -du kan använda någon av följande gränssnitt för att hantera dina resurser:
  * REST-baserad API
  * PowerShell
  * .NET SDK
  * SDK för Node.JS
  * Java SDK
  * Azure CLI
  * Preview-portalen
  * Språk för Resource Manager-mallen

## <a name="network-resources"></a>Nätverksresurser
Du kan nu hantera nätverksresurser oberoende av varandra, i stället för att de hanteras via en enda beräknings-resurs (en virtuell dator). Detta innebär en högre grad av flexibilitet och smidighet i skriver en komplex och stor skala infrastruktur i en resursgrupp.

Nedan visas en översikt över en exempeldistribution med ett program med flera nivåer. Varje resurs som du ser, till exempel nätverkskort, offentliga IP-adresser och virtuella datorer kan hanteras oberoende av varandra.

![Resursmodell för nätverk](./media/resource-groups-networking/Figure2.png)

Alla resurser innehåller en gemensam uppsättning egenskaper och deras enskilda egenskapsuppsättning. Gemensamma egenskaper är:

| Egenskap | Beskrivning | Exempelvärden |
| --- | --- | --- |
| **Namn** |Unikt resursnamn. Varje resurs har sin egen namngivningsbegränsningar. |PIP01 VM01, NIC01 |
| **Plats** |Azure-region där resursen finns |westus eastus |
| **ID** |Unik URI baserad identifiering |/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP |

Du kan kontrollera enskilda egenskaper för resurserna i avsnitten nedan.

[!INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[!INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[!INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[!INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[!INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[!INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[!INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[!INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[!INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[!INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Hanteringsgränssnitt
Du kan hantera dina Azure-nätverk resurser med olika gränssnitt. I det här dokumentet fokuserar vi på dra av dessa gränssnitt: REST-API och mallar.

### <a name="rest-api"></a>REST API
Som tidigare nämnts kan nätverksresurser hanteras via en mängd olika gränssnitt, inklusive REST API, .NET SDK, SDK för Node.JS, Java SDK, PowerShell, CLI, Azure-portalen och mallar.

Rest-API som stämmer överens med specifikationen för HTTP 1.1-protokollet. Den allmänna strukturen URI för API: et visas nedan:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Och parametrar i klammerparenteser representerar följande element:

* **prenumerations-id** -id för din Azure-prenumeration.
* **resurs-providernamnrymden** -namnrymd för den leverantör som används. Värdet för nätverksresursprovidern *Microsoft.Network*.
* **regionsnamnet** -namnet för Azure-region

Följande HTTP-metoder stöds när du anropar REST-API:

* **PLACERA** – används för att skapa en resurs som tillhör en viss typ, ändra en resursegenskap eller ändra en association mellan resurser.
* **Hämta** – används för att hämta information för en etablerad resurs.
* **Ta bort** – används för att ta bort en befintlig resurs.

Både begäran och svar uppfylla en JSON-nyttolast-format. Mer information finns i [Azure Resource Manager API: er](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="resource-manager-template-language"></a>Språk för Resource Manager-mallen
Du kan också använda en deklarativ programmering stil att bygga och hantera nätverksresurser med hjälp av hanteraren för filserverresurser mallen språk utöver att hantera resurser imperatively (via API: er eller SDK).

En exempel-representation av en mall som har angetts under –

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

Mallen är i första hand en JSON-beskrivning av resurserna och instans värdena matas in via parametrar. Exemplet nedan kan användas för att skapa ett virtuellt nätverk med 2 undernät.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Du har möjlighet att ange parametervärden manuellt när du använder en mall eller du kan använda en parameterfil. Exemplet nedan visar möjliga uppsättning parametervärden som ska användas med hjälp av mallen ovan:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


De huvudsakliga fördelarna med hjälp av mallar är:

* Du kan skapa en komplicerad infrastruktur i en resursgrupp i en deklarativ stil. Dirigering av skapar resurser, inklusive beroende management hanteras av Resource Manager.
* Infrastrukturen kan skapas på repeterbara sätt över olika regioner, och inom en region genom att ändra parametrar.
* Stilen deklarativ leder till kortare tid i Skapa mallar och lansera infrastrukturen.

Exempelmallar, se [Azure snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates).

Mer information om språk för Resource Manager-mallen finns [språk för Azure Resource Manager-mallen](../azure-resource-manager/resource-group-authoring-templates.md).

Mallen exempel använder virtuella nätverk och nätverksresurser på undernätet. Det finns andra nätverksresurser som du kan använda enligt nedan:

### <a name="using-a-template"></a>Använda en mall
Du kan distribuera tjänster till Azure från en mall med hjälp av PowerShell AzureCLI, eller genom att utföra en Klicka om du vill distribuera från GitHub. Utför följande steg för att distribuera tjänster från en mall i GitHub:

1. Öppna filen template3 från GitHub. Exempelvis öppna [virtuella nätverk med två undernät](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Klicka på **till Azure**, och sedan logga in Azure-portalen med dina autentiseringsuppgifter.
3. Kontrollera mallen och klicka sedan på **spara**.
4. Klicka på **redigera parametrar** och välja en plats, till exempel *västra USA*, för vnet och undernät.
5. Om det behövs ändrar den **ADDRESSPREFIX** och **SUBNETPREFIX** parametrar och klicka sedan på **OK**.
6. Klicka på **välja en resursgrupp** och klicka sedan på den resursgrupp som du vill lägga till vnet och undernät med. Du kan också skapa en ny resursgrupp genom att klicka på **eller skapa nya**.
7. Klicka på **Skapa**. Lägg märke till panelen Visa **etablering malldistribution**. När distributionen är klar visas en skärm som liknar en nedan.

![Exempel malldistribution](./media/resource-groups-networking/Figure6.png)

## <a name="next-steps"></a>Nästa steg
[Språk för Azure Resource Manager-mallen](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure-nätverk – vanliga mallar](https://github.com/Azure/azure-quickstart-templates)

[Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md)

[Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)

