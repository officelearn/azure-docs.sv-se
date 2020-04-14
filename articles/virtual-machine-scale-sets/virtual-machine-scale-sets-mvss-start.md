---
title: Lär dig mer om skalningsuppsättningar för virtuella datorer
description: Lär dig hur du skapar en grundläggande skalningsuppsättningsmall för Azure-skalningsuppsättningar för virtuella datorer genom flera enkla steg.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 9c6a30a5f08b33adfa515973962236516f34fbf3
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273400"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Lär dig mer om skalningsuppsättningar för virtuella datorer
[Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) är ett bra sätt att distribuera grupper av relaterade resurser. Den här självstudieserien visar hur du skapar en grundläggande skalningsuppsättningsmall och hur du ändrar den här mallen så att den passar olika scenarier. Alla exempel kommer från den här [GitHub-databasen](https://github.com/gatneil/mvss).

Den här mallen är avsedd att vara enkel. Mer fullständiga exempel på skalningsuppsättningsmallar finns i [Azure Quickstart Templates GitHub-databasen](https://github.com/Azure/azure-quickstart-templates) och söker efter mappar som innehåller strängen `vmss`.

Om du redan är bekant med att skapa mallar kan du gå vidare till avsnittet "Nästa steg" för att se hur du ändrar den här mallen.

## <a name="define-schema-and-contentversion"></a>Definiera $schema och contentVersion
Definiera först `$schema` `contentVersion` och i mallen. Elementet `$schema` definierar versionen av mallspråket och används för syntaxmarkering av Visual Studio och liknande valideringsfunktioner. Elementet `contentVersion` används inte av Azure. I stället hjälper det dig att hålla reda på mallversionen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definiera parametrar
Definiera sedan två `adminUsername` parametrar `adminPassword`och . Parametrar är värden som du anger vid tidpunkten för distributionen. Parametern `adminUsername` är `string` helt enkelt `adminPassword` en typ, men `securestring`eftersom det är en hemlighet, ge den typ . Senare skickas dessa parametrar till skalningsuppsättningskonfigurationen.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definiera variabler
Med Resource Manager-mallarna kan du också definiera variabler som ska användas senare i mallen. Exemplet använder inga variabler, så JSON-objektet är tomt.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definiera resurser
Nästa är resursavsnittet i mallen. Här definierar du vad du egentligen vill distribuera. Till `parameters` `variables` skillnad från och (som är JSON-objekt) `resources` är en JSON-lista med JSON-objekt.

```json
   "resources": [
```

Alla resurser `type` `name`kräver `apiVersion`, `location` , och egenskaper. Det här exemplets första resurs har typen [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), namn `myVnet`och apiVersion `2018-11-01`. (Information om hur du hittar den senaste API-versionen för en resurstyp finns i [mallreferensen för Azure Resource Manager](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Ange plats
Om du vill ange platsen för det virtuella nätverket använder du [en Resource Manager-mallfunktion](../azure-resource-manager/templates/template-functions.md). Denna funktion måste bifogas inom citationstecken och `"[<template-function>]"`hakparenteser så här: . Använd i `resourceGroup` så fall funktionen. Det tar inga argument och returnerar ett JSON-objekt med metadata om resursgruppen som distributionen distribueras till. Resursgruppen anges av användaren vid tidpunkten för distributionen. Det här värdet indexeras sedan till `.location` det här JSON-objektet med för att hämta platsen från JSON-objektet.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Ange egenskaper för virtuella nätverk
Varje Resource Manager-resursresurs har ett eget `properties` avsnitt för konfigurationer som är specifika för resursen. I det här fallet anger du att det virtuella nätverket `10.0.0.0/16`ska ha ett undernät med hjälp av det privata IP-adressintervallet . En skaluppsättning finns alltid i ett undernät. Det går inte att sträcka sig över undernäten.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Lägg till dependsOn-lista
Förutom de `type`obligatoriska `name`, `apiVersion`, `location` och egenskaper, kan `dependsOn` varje resurs ha en valfri lista med strängar. Den här listan anger vilka andra resurser från den här distributionen som måste slutföras innan den här resursen distribueras.

I det här fallet finns det bara ett element i listan, det virtuella nätverket från föregående exempel. Du anger det här beroendet eftersom skalningsuppsättningen måste finnas nätverket innan du skapar några virtuella datorer. På så sätt kan skalningsuppsättningen ge dessa virtuella datorer privata IP-adresser från IP-adressintervallet som tidigare angetts i nätverksegenskaperna. Formatet för varje sträng i listan `<type>/<name>`dependsOn är . Använd samma `type` `name` och används tidigare i definitionen av virtuella nätverksresurser.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Ange skalningsuppsättningsegenskaper
Skalningsuppsättningar har många egenskaper för att anpassa de virtuella datorerna i skalningsuppsättningen. En fullständig lista över dessa egenskaper finns i [mallreferensen](/azure/templates/microsoft.compute/virtualmachinescalesets). För den här självstudien anges endast ett fåtal vanliga egenskaper.
### <a name="supply-vm-size-and-capacity"></a>Ange vm-storlek och kapacitet
Skalningsuppsättningen måste veta vilken storlek på den virtuella datorn som ska skapas ("sku-namn") och hur många sådana virtuella datorer som ska skapas ("sku-kapacitet"). Information om vilka vm-storlekar som är tillgängliga finns i [dokumentationen för VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Välj typ av uppdateringar
Skalningsuppsättningen måste också veta hur du hanterar uppdateringar på skalningsuppsättningen. För närvarande finns det `Manual` `Rolling` tre `Automatic`alternativ, och . Mer information om skillnaderna mellan de två finns i dokumentationen om [hur du uppgraderar en skalningsuppsättning](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Välj VM-operativsystem
Skalningsuppsättningen måste veta vilket operativsystem som ska läggas på de virtuella datorerna. Här skapar du de virtuella datorerna med en helt lappad Ubuntu 16.04-LTS-bild.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Ange computerNamePrefix
Skalningsuppsättningen distribuerar flera virtuella datorer. Ange i stället för att `computerNamePrefix`ange varje vm-namn . Skalningsuppsättningen lägger till ett index i prefixet för `<computerNamePrefix>_<auto-generated-index>`varje virtuell dator, så VM-namn har formuläret .

I följande kodavsnitt använder du parametrarna från tidigare för att ange administratörens användarnamn och lösenord för alla virtuella datorer i skalningsuppsättningen. Den här `parameters` processen använder mallfunktionen. Den här funktionen tar in en sträng som anger vilken parameter som ska referera till och matar ut värdet för den parametern.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Ange VM-nätverkskonfiguration
Slutligen anger du nätverkskonfigurationen för de virtuella datorerna i skalningsuppsättningen. I det här fallet behöver du bara ange ID för det undernät som skapats tidigare. Detta talar om för skalningsuppsättningen att placera nätverksgränssnitten i det här undernätet.

Du kan hämta ID:t för det virtuella `resourceId` nätverket som innehåller undernätet med hjälp av mallfunktionen. Den här funktionen används i typen och namnet på en resurs och returnerar den fullständigt kvalificerade identifieraren för den resursen. Detta ID har formuläret:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Identifieraren för det virtuella nätverket är dock inte tillräckligt. Ange det specifika undernät som de virtuella skalningsuppsättningarna ska finnas i. För att göra detta, `/subnets/mySubnet` sammanfoga till ID för det virtuella nätverket. Resultatet är det fullständigt kvalificerade ID:t för undernätet. Gör detta sammanfogning `concat` med funktionen, som tar i en serie strängar och returnerar deras sammanfogning.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
