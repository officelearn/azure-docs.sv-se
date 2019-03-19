---
title: Lär dig mer om skalningsuppsättningsmallar för virtuell dator | Microsoft Docs
description: Lär dig att skapa en minsta lönsamma skalningsuppsättningsmall för VM-skalningsuppsättningar
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: d4a3dd6ae390fd48a8085cca33063a6bb74bd96c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008417"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Lär dig mer om skalningsuppsättningsmallar för virtuell dator
[Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) är ett bra sätt att distribuera grupper av relaterade resurser. Den här självstudien visar hur du skapar en minsta lönsamma skalningsuppsättningsmall och hur du ändrar den här mallen för att passa olika scenarier. Alla exempel kommer från detta [GitHub-lagringsplatsen](https://github.com/gatneil/mvss). 

Den här mallen är avsedd att vara enkel. Mer komplett exempel på skala skalningsuppsättningsmallarna, finns i den [Azure Quickstart-mallar GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates) och söker efter mappar som innehåller strängen `vmss`.

Om du redan är bekant med att skapa mallar kan du gå vidare till avsnittet ”nästa steg” för att se hur du ändrar den här mallen.

## <a name="review-the-template"></a>Granska mallen

Använd GitHub för att granska den minsta lönsamma skalningsuppsättningsmall, [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

I den här självstudien ska vi Granska Versionens (`git diff master minimum-viable-scale-set`) att skapa den minsta lönsamma skalan ange mall bit för bit.

## <a name="define-schema-and-contentversion"></a>Definiera $schema och mallegenskapen
Definiera först `$schema` och `contentVersion` i mallen. Den `$schema` elementet definierar versionen av Mallspråket och används för Visual Studio syntaxmarkering och liknande funktioner för verifiering. Den `contentVersion` elementet används inte av Azure. I stället det hjälper dig att hålla reda på versionen av principmallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Definiera parametrar
Definiera två parametrar, `adminUsername` och `adminPassword`. Parametrarna är värden som du anger vid tidpunkten för distributionen. Den `adminUsername` parametern är helt enkelt en `string` typ, men eftersom `adminPassword` är en hemlig, ge den skriver `securestring`. Senare, är dessa parametrar skickades till konfigurationen för skalningsuppsättningen.

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
Resource Manager-mallar kan du definiera variabler som ska användas senare i mallen. Exemplet använder inte några variabler, så att JSON-objekt är tom.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definiera resurser
Nästa är resursavsnittet i mallen. Här kan definiera du vad du faktiskt vill distribuera. Till skillnad från `parameters` och `variables` (som är JSON-objekt), `resources` är en JSON-lista över JSON-objekt.

```json
   "resources": [
```

Alla resurser kräver `type`, `name`, `apiVersion`, och `location` egenskaper. Det här exemplet första resursen har typen [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks)och namnet `myVnet`, och apiVersion `2016-03-30`. (Du hittar den senaste API-versionen för en resurstyp i [mallreferensen för Azure Resource Manager](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Ange plats
Ange platsen för det virtuella nätverket genom att använda en [Resource Manager mallfunktionen](../azure-resource-manager/resource-group-template-functions.md). Den här funktionen måste stå inom citattecken och hakparenteser så här: `"[<template-function>]"`. I det här fallet den `resourceGroup` funktion. Den tar in utan argument och returnerar ett JSON-objekt med metadata om den här distributionen distribueras till resursgruppen. Resursgruppen har angetts av användaren vid tidpunkten för distributionen. Det här värdet sedan har indexerats i det här JSON-objekt med `.location` att hämta platsen från JSON-objekt.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Ange egenskaper för virtuellt nätverk
Varje Resource Manager-resurs har sin egen `properties` för konfigurationer som är specifika för resursen. I det här fallet anger att det virtuella nätverket ska ha ett undernät med hjälp av privata IP-adressintervallet `10.0.0.0/16`. En skalningsuppsättning finns alltid i ett undernät. Det kan inte omfatta undernät.

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
Förutom de nödvändiga `type`, `name`, `apiVersion`, och `location` egenskaperna för varje resurs kan ha en valfri `dependsOn` lista med strängar. Den här listan anger vilken andra resurser från den här distributionen måste avslutas innan du distribuerar den här resursen.

I det här fallet finns bara ett element i listan med det virtuella nätverket från föregående exempel. Du kan ange det här beroendet eftersom skalningsuppsättningen behov nätverk finnas innan du skapar virtuella datorer. På så sätt kan skalningsuppsättningen kan ge dessa privata IP-adresser för virtuella datorer från det IP-adressintervall som tidigare angetts i nätverksegenskaperna för. Formatet för varje sträng i listan över dependsOn är `<type>/<name>`. Använd samma `type` och `name` använde tidigare i resursdefinitionen virtuellt nätverk.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Ange egenskaper för set
Skalningsuppsättningar har många egenskaper för att anpassa de virtuella datorerna i skalningsuppsättningen. En fullständig lista över de här egenskaperna finns i den [mallreferensen](/azure/templates/microsoft.compute/virtualmachinescalesets). Några vanliga egenskaper anges i den här självstudien.
### <a name="supply-vm-size-and-capacity"></a>Ange storlek och kapacitet
Skalningsuppsättningen måste veta vilka storleken på virtuell dator för att skapa (”sku namn”) och hur många sådana virtuella datorer för att skapa (”sku-kapacitet”). Om du vill se vilka VM-storlekar som är tillgängliga finns i den [VM-storlekar dokumentation](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Välj typ av uppdateringar
Skalningsuppsättning också behöver veta hur du hanterar uppdateringar på skalningsuppsättningen. Det finns två alternativ `Manual` och `Automatic`. Mer information om skillnaderna mellan två finns i dokumentationen på [så här uppgraderar du en skalningsuppsättning](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Välj VM-operativsystem
Skalningsuppsättningen måste du ange vilket operativsystem som ska placeras på de virtuella datorerna. Här kan du skapa de virtuella datorerna med en fullständigt uppdaterad Ubuntu 16.04 LTS-avbildning.

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
Skalningsuppsättningen distribuerar flera virtuella datorer. I stället för att ange namn på varje virtuell dator kan du ange `computerNamePrefix`. Skalningsuppsättningen lägger till ett index till prefixet för varje virtuell dator, så att VM-namn har formatet `<computerNamePrefix>_<auto-generated-index>`.

I följande kodavsnitt att använda parametrar från tidigare för att ange administratörens användarnamn och lösenord för alla virtuella datorer i skalningsuppsättningen. Den här processen använder den `parameters` mallfunktionen. Den här funktionen använder en sträng som anger vilka parametern att referera till och anger värdet för parametern.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Ange konfiguration för VM-nätverk
Ange slutligen nätverkskonfiguration för de virtuella datorerna i skalningsuppsättningen. I det här fallet behöver du bara ange ID för det undernät som skapades tidigare. Detta talar om skalningsuppsättningen att placera nätverksgränssnitt i det här undernätet.

Du kan hämta ID för det virtuella nätverket som innehåller undernätet med hjälp av den `resourceId` mallfunktionen. Den här funktionen tar i typ och namnet på en resurs och returnerar den fullständigt kvalificerade identifieraren för den här resursen. Detta ID har formatet: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Identifierare för det virtuella nätverket är dock inte tillräckligt med. Ange specifika undernät som att de virtuella datorerna med skalningsuppsättningarna ska vara i. Gör detta genom att sammanfoga `/subnets/mySubnet` -ID: t för det virtuella nätverket. Resultatet är fullständigt kvalificerat ID för undernätet. Gör den här sammanfogning med den `concat` funktion som tar i en serie med strängar och returnerar sina sammanfogning.

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
