---
title: "Skicka komplexa värden mellan Azure-mallar | Microsoft Docs"
description: "Visar rekommenderade metoder för att dela tillstånd med Azure Resource Manager-mallar och länkade mallar med komplexa objekt."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fd2f5e2d-d56d-4e01-a57d-34f3eaead4a9
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: tomfitz
ms.openlocfilehash: e163f3c2e9a78b057dc2a7a42924c59d0aac3fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="share-state-to-and-from-azure-resource-manager-templates"></a>Tillstånd för filresurs till och från Azure Resource Manager-mallar
Det här avsnittet innehåller metodtips för att hantera och dela tillstånd i mallar. Parametrar och variabler som visas i det här avsnittet är exempel på vilken typ av objekt som du kan definiera lätt ordna dina distributionskrav. Du kan implementera objekten med egenskapsvärden som passar din miljö i dessa exempel.

Det här avsnittet är en del av en större vitbok. Om du vill läsa fullständig dokumentet, hämta [World klassen Resource Manager-mallar överväganden och beprövade metoder](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## <a name="provide-standard-configuration-settings"></a>Ange standard konfigurationsinställningar
I stället för att erbjuda en mall som ger totalt flexibilitet och oräkneliga variationer är vanliga mönstret att tillhandahålla ett urval av kända konfigurationer. Användare kan i praktiken välja standard t-shirt storlekar, till exempel sandbox små, medelstora och stora. Andra exempel på t-shirt storlekar är produkterbjudanden, till exempel community edition eller enterprise edition. I andra fall kan det vara belastningsspecifikt konfigurationer av en teknik – som kartan minska eller utan sql.

Du kan skapa variabler som innehåller datasamlingar, kallas ibland för ”egenskapsuppsättningar” och använda dessa data att resursdeklarationen i mallen med komplexa objekt. Den här metoden ger bra, kända konfigurationer i olika storlekar förkonfigurerade för kunder. Utan kända konfigurationer måste användare av mallen fastställa klustret storleken på egen hand factor plattform resurs begränsningar och beräkna för att identifiera den resulterande partitionering av lagringskonton och andra resurser (på grund av storlek och resursen begränsningar för kluster). Förutom att göra en bättre upplevelse för kunden, några kända konfigurationer är enklare att stödja och kan hjälpa dig att leverera en högre säkerhetsnivå för densitet.

I följande exempel visas hur du definierar variabler som innehåller komplexa objekt som representerar samlingar av data. Samlingarna Definiera värden som används för storlek på virtuell dator, nätverksinställningar, operativsysteminställningar och tillgänglighetsinställningarna.

```json
"variables": {
  "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
  "tshirtSizeSmall": {
    "vmSize": "Standard_A1",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
    "vmCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 1,
      "pool": "db",
      "map": [0,0],
      "jumpbox": 0
    }
  },
  "tshirtSizeMedium": {
    "vmSize": "Standard_A3",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
    "vmCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 2,
      "pool": "db",
      "map": [0,1],
      "jumpbox": 0
    }
  },
  "tshirtSizeLarge": {
    "vmSize": "Standard_A4",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
    "vmCount": 3,
    "slaveCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 2,
      "pool": "db",
      "map": [0,1,1],
      "jumpbox": 0
    }
  },
  "osSettings": {
    "scripts": [
      "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
    ],
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer",
      "sku": "14.04.2-LTS",
      "version": "latest"
    }
  },
  "networkSettings": {
    "vnetName": "[parameters('virtualNetworkName')]",
    "addressPrefix": "10.0.0.0/16",
    "subnets": {
      "dmz": {
        "name": "dmz",
        "prefix": "10.0.0.0/24",
        "vnet": "[parameters('virtualNetworkName')]"
      },
      "data": {
        "name": "data",
        "prefix": "10.0.1.0/24",
        "vnet": "[parameters('virtualNetworkName')]"
      }
    }
  },
  "availabilitySetSettings": {
    "name": "pgsqlAvailabilitySet",
    "fdCount": 3,
    "udCount": 5
  }
}
```

Observera att den **tshirtSize** variabeln sammanfogar t-shirt storlek som du angav via en parameter (**små**, **medel**, **stor**) i texten **tshirtSize**. Du kan använda den här variabeln för att hämta associerade komplexa objektvariabeln för att t-shirt storlek.

Sedan kan du referera dessa variabler senare i mallen. Möjligheten att referera till namngivna variabler och deras egenskaper förenklar syntaxen och gör det enkelt att förstå kontext. I följande exempel definieras en resurs för att distribuera med hjälp av objekten som visas tidigare för att ställa in värden. Till exempel VM-storlek anges genom att hämta värdet för `variables('tshirtSize').vmSize` när värdet för diskens storlek hämtas från `variables('tshirtSize').diskSize`. Dessutom URI för en länkad mall har angetts med värdet för `variables('tshirtSize').vmTemplate`.

```json
"name": "master-node",
"type": "Microsoft.Resources/deployments",
"apiVersion": "2015-01-01",
"dependsOn": [
    "[concat('Microsoft.Resources/deployments/', 'shared')]"
],
"properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[variables('tshirtSize').vmTemplate]",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "value": "[parameters('adminPassword')]"
      },
      "replicatorPassword": {
        "value": "[parameters('replicatorPassword')]"
      },
      "osSettings": {
        "value": "[variables('osSettings')]"
      },
      "subnet": {
        "value": "[variables('networkSettings').subnets.data]"
      },
      "commonSettings": {
        "value": {
          "region": "[parameters('region')]",
          "adminUsername": "[parameters('adminUsername')]",
          "namespace": "ms"
        }
      },
      "storageSettings": {
        "value":"[variables('tshirtSize').storage]"
      },
      "machineSettings": {
        "value": {
          "vmSize": "[variables('tshirtSize').vmSize]",
          "diskSize": "[variables('tshirtSize').diskSize]",
          "vmCount": 1,
          "availabilitySet": "[variables('availabilitySetSettings').name]"
        }
      },
      "masterIpAddress": {
        "value": "0"
      },
      "dbType": {
        "value": "MASTER"
      }
    }
  }
}
```

## <a name="pass-state-to-a-template"></a>Skicka status till en mall
Du kan dela tillstånd till en mall med parametrar som du anger direkt under distributionen.

I följande tabell visas vanliga parametrar i mallar.

| Namn | Värde | Beskrivning |
| --- | --- | --- |
| location |Sträng från en begränsad lista med Azure-regioner |Den plats där resurserna som har distribuerats. |
| storageAccountNamePrefix |Sträng |Unikt DNS-namn för det Lagringskonto där den Virtuella datorns diskar placeras |
| Domännamn |Sträng |Domännamnet för offentligt tillgänglig jumpbox VM i formatet: **{domainName}. { Location}.cloudapp.com** till exempel: **mydomainname.westus.cloudapp.azure.com** |
| adminUsername |Sträng |Användarnamn för de virtuella datorerna |
| adminPassword |Sträng |Lösenord för de virtuella datorerna |
| tshirtSize |Sträng från en begränsad lista över erbjuds t-shirt storlekar |Storleken på namngivna skala att etablera. Till exempel ”liten”, ”medel”, ”stor” |
| virtualNetworkName |Sträng |Namnet på det virtuella nätverk som du vill att klienten ska använda. |
| enableJumpbox |Sträng från en begränsad lista (aktiverat/inaktiverat) |Parameter som anger om du vill aktivera en jumpbox för miljön. Värden: ”aktiverad”, ”inaktiverad” |

Den **tshirtSize** parameter används i föregående avsnitt definieras som:

```json
"parameters": {
  "tshirtSize": {
    "type": "string",
    "defaultValue": "Small",
    "allowedValues": [
      "Small",
      "Medium",
      "Large"
    ],
    "metadata": {
      "Description": "T-shirt size of the MongoDB deployment"
    }
  }
}
```

## <a name="pass-state-to-linked-templates"></a>Skicka status till länkade mallar
När du ansluter till länkade mallar kan du ofta använder en blandning av statiska och genereras variabler.

### <a name="static-variables"></a>Statiska variabler
Statiska variabler används ofta för att ge grundläggande värden, till exempel URL: er som används i en mall.

I följande utdrag i mallen `templateBaseUrl` anger var roten för mallen i GitHub. Nästa rad skapar en ny variabel `sharedTemplateUrl` som sammanfogar bas-URL med kända namnet på mallen delade resurser. Under den raden är en variabel med komplexa objekt används för att lagra en t-shirt storlek, där den grundläggande Webbadressen sammanfogas till platsen för mall kända konfiguration och lagras i den `vmTemplate` egenskapen.

Fördelen med den här metoden är att om platsen mall ändras, behöver du bara ändra den statiska variabeln på ett ställe som passerar i hela länkade mallar.

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "tshirtSizeSmall": {
    "vmSize": "Standard_A1",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
    "vmCount": 2,
    "slaveCount": 1,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 1,
      "pool": "db",
      "map": [0,0],
      "jumpbox": 0
    }
  }
}
```

### <a name="generated-variables"></a>Genererade variabler
Förutom statiska variabler genereras flera variabler dynamiskt. Det här avsnittet beskrivs några av vanliga genererade variabler.

#### <a name="tshirtsize"></a>tshirtSize
Du är bekant med den här variabeln som genereras från exemplen ovan.

#### <a name="networksettings"></a>networkSettings
I en kapacitet, kapaciteten eller begränsade lösningsmall för slutpunkt till slutpunkt Skapa länkade mallar normalt resurser som finns på ett nätverk. Ett enkelt sätt är att använda ett komplext objekt för att lagra nätverksinställningar och skicka dem till länkade mallar.

Du kan se ett exempel på kommunikation nätverksinställningar nedan.

```json
"networkSettings": {
  "vnetName": "[parameters('virtualNetworkName')]",
  "addressPrefix": "10.0.0.0/16",
  "subnets": {
    "dmz": {
      "name": "dmz",
      "prefix": "10.0.0.0/24",
      "vnet": "[parameters('virtualNetworkName')]"
    },
    "data": {
      "name": "data",
      "prefix": "10.0.1.0/24",
      "vnet": "[parameters('virtualNetworkName')]"
    }
  }
}
```

#### <a name="availabilitysettings"></a>availabilitySettings
Resurser som skapades i länkade mallar placeras ofta i en tillgänglighetsuppsättning. I följande exempel anges tillgänglighetsuppsättningen och även feldomän och uppdateringsdomän räkna om du vill använda.

```json
"availabilitySetSettings": {
  "name": "pgsqlAvailabilitySet",
  "fdCount": 3,
  "udCount": 5
}
```

Om du behöver flera tillgänglighetsuppsättningar (till exempel en för överordnade noder) och en annan för datanoder, kan du använda ett namn som ett prefix kan ange flera tillgänglighetsuppsättningar eller följa modellen som visades tidigare för att skapa en variabel för en viss t-shirt storlek.

#### <a name="storagesettings"></a>storageSettings
Lagringsinformation om som ofta delas med länkade mallar. I exemplet nedan, en *storageSettings* objekt innehåller information om de storage-konto och behållare.

```json
"storageSettings": {
    "vhdStorageAccountName": "[parameters('storageAccountName')]",
    "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
    "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
}
```

#### <a name="ossettings"></a>osSettings
Länkade mallar kan behöva du skicka operativsysteminställningar till olika typer av noder i olika för kända konfigurationstyper. Ett komplext objekt är ett enkelt sätt att lagra och dela information om operativsystem så att det blir enklare att stödja flera operativsystem alternativ för distributionen.

I följande exempel visas ett objekt för *osSettings*:

```json
"osSettings": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "14.04.2-LTS",
    "version": "latest"
  }
}
```

#### <a name="machinesettings"></a>machineSettings
En genererad variabel *machineSettings* är ett komplext objekt som innehåller en blandning av kärnor variabler för att skapa en virtuell dator. Variablerna omfattar administratörsanvändarnamn och lösenord, ett prefix för VM-namn och en referens för avbildningen av operativsystemet.

```json
"machineSettings": {
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "machineNamePrefix": "mongodb-",
    "osImageReference": {
        "publisher": "[variables('osFamilySpec').imagePublisher]",
        "offer": "[variables('osFamilySpec').imageOffer]",
        "sku": "[variables('osFamilySpec').imageSKU]",
        "version": "latest"
    }
},
```

Observera att *osImageReference* hämtar värden från den *osSettings* variabel som definierats i mallen för huvudsakliga. Det innebär att du kan enkelt ändra operativsystemet för en virtuell dator, helt eller baserat på inställningen för mallen konsumenter.

#### <a name="vmscripts"></a>vmScripts
Den *vmScripts* objekt innehåller information om skript för att hämta och köra på en VM-instans, inklusive utanför och inom referenser. Utanför innehåller referenser infrastrukturen.
I referenser är installerad programvara installerad och konfiguration.

Du använder den *scriptsToDownload* egenskapen för att visa en lista över de skript som ska hämtas till den virtuella datorn. Det här objektet innehåller också referenser till kommandoradsargument för olika typer av åtgärder. Dessa åtgärder omfattar köra standardinstallationen för varje enskild nod, en installation som körs när alla noder har distribuerats och ytterligare skript som är specifika för en viss mall.

Det här exemplet är från en mall som används för att distribuera MongoDB, som kräver en avgörandet att ge hög tillgänglighet. Den *arbiterNodeInstallCommand* har lagts till *vmScripts* installera avgörandet.

Avsnittet variabler är där du hittar de variabler som definierar den specifika texten för att köra skriptet med rätt värden.

```json
"vmScripts": {
    "scriptsToDownload": [
        "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
        "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],
    "regularNodeInstallCommand": "[variables('installCommand')]",
    "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
    "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
},
```

## <a name="return-state-from-a-template"></a>Returnera tillstånd från en mall
Inte bara kan du överföra data till en mall kan du också dela data tillbaka till anropande mallen. I den **matar ut** avsnitt i en länkad mall kan du ange nyckel/värde-par som kan användas av källmallen.

I följande exempel visas hur du skickar den privata IP-adressen som genereras i en länkad mall.

```json
"outputs": {
    "masterip": {
        "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
        "type": "string"
     }
}
```

Du kan använda dessa data med följande syntax i huvudsakliga mallen:

    "[reference('master-node').outputs.masterip.value]"

Du kan använda det här uttrycket i avsnittet utdata eller avsnittet resurser huvudsakliga mallen. Du kan inte använda uttrycket i avsnittet variables eftersom den är beroende av runtime-tillståndet. Använd följande för att returnera värdet från den huvudsakliga mallen:

```json
"outputs": {
  "masterIpAddress": {
    "value": "[reference('master-node').outputs.masterip.value]",
    "type": "string"
  }
```

Ett exempel på hur du använder avsnittet utdata för en länkad mall för att returnera datadiskar för en virtuell dator finns [skapar flera datadiskar för en virtuell dator](resource-group-create-multiple.md).

## <a name="define-authentication-settings-for-virtual-machine"></a>Definiera autentiseringsinställningar för den virtuella datorn
Du kan använda samma mönster tidigare för konfigurationsinställningar för att ange inställningarna för autentisering för en virtuell dator. Du skapar en parameter för typ av autentisering.

```json
"parameters": {
  "authenticationType": {
    "allowedValues": [
      "password",
      "sshPublicKey"
    ],
    "defaultValue": "password",
    "metadata": {
      "description": "Authentication type"
    },
    "type": "string"
  }
}
```

Du lägger till variabler för de olika typerna av autentisering och en variabel för att lagra vilken typ som används för den här distributionen som baseras på värdet för parametern.

```json
"variables": {
  "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
  "osProfilepassword": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "notused",
    "computerName": "[parameters('vmName')]",
    "customData": "[base64(variables('customData'))]"
  },
  "osProfilesshPublicKey": {
    "adminUsername": "notused",
    "computerName": "[parameters('vmName')]",
    "customData": "[base64(variables('customData'))]",
    "linuxConfiguration": {
      "disablePasswordAuthentication": "true",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "[parameters('sshPublicKey')]",
            "path": "/home/notused/.ssh/authorized_keys"
          }
        ]
      }
    }
  }
}
```

När du definierar den virtuella datorn kan du ange den **osProfile** till variabeln som du skapade.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  ...
  "osProfile": "[variables('osProfile')]"
}
```

## <a name="next-steps"></a>Nästa steg
* Läs om avsnitt i mallen i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md)
* De funktioner som är tillgängliga i en mall finns [Azure Resource Manager Mallfunktioner](resource-group-template-functions.md)
