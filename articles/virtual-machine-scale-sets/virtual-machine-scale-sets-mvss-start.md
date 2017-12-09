---
title: "Lär dig mer om mallar för virtuella datorer scale set | Microsoft Docs"
description: "Lär dig hur du skapar en lägsta lönsam skala set mall för virtuella datorer"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 65f02c4675eb752dcc82e9a1d1c7f6c2c193fc32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Lär dig mer om scale set-mallar för virtuella datorer
[Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) är ett bra sätt att distribuera grupper av relaterade resurser. Den här självstudiekursen serien visar hur du skapar en mall för lägsta lönsam skala och hur du ändrar den här mallen så att den passar olika scenarier. Alla exempel kommer från den här [GitHub-lagringsplatsen](https://github.com/gatneil/mvss). 

Den här mallen är avsedd att vara enkla. Mer komplett exempel på skalan mallar finns i avsnittet den [Azure Quickstart mallar GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates) och Sök efter mappar som innehåller strängen `vmss`.

Om du redan är bekant med att skapa mallar som du kan hoppa till avsnittet ”nästa steg” om du vill se hur du ändrar den här mallen.

## <a name="review-the-template"></a>Granska mallen

Använda GitHub för att granska mall våra lägsta lönsam skala, [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

I den här självstudiekursen kommer vi undersöka diff (`git diff master minimum-viable-scale-set`) att skapa den lägsta lönsam skalan ange mall bit för bit.

## <a name="define-schema-and-contentversion"></a>Definiera $schema och contentVersion
Först måste vi definiera `$schema` och `contentVersion` i mallen. Den `$schema` elementet definierar versionen av mall-språket och används för syntaxmarkering för Visual Studio och liknande funktioner för verifiering. Den `contentVersion` elementet används inte av Azure. I stället hjälper dig att hålla reda på versionen av principmallen.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Definiera parametrar
Nu ska vi definierar två parametrar `adminUsername` och `adminPassword`. Parametrarna är värden som du anger vid tidpunkten för distribution. Den `adminUsername` parametern är helt enkelt en `string` typ, men eftersom `adminPassword` är en hemlighet vi ge den typen `securestring`. Dessa parametrar skickas senare i scale set-konfigurationen.

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
Resource Manager-mallar kan du definiera variabler som ska användas senare i mallen. Vårt exempel används inte några variabler, så vi har tomt JSON-objekt.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definiera resurser
Nästa är avsnittet resurser i mallen. Här kan definiera du vad du vill distribuera. Till skillnad från `parameters` och `variables` (som är JSON-objekt), `resources` är en JSON-lista över JSON-objekt.

```json
   "resources": [
```

Alla resurser som kräver `type`, `name`, `apiVersion`, och `location` egenskaper. Det här exemplet första resursen har typen `Microsft.Network/virtualNetwork`och namnet `myVnet`, och apiVersion `2016-03-30`. (Du hittar den senaste API-versionen för en resurstyp i [Azure REST API-dokumentation](https://docs.microsoft.com/rest/api/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Ange plats
Ange platsen för det virtuella nätverket som vi använder en [Resource Manager mallfunktionen](../azure-resource-manager/resource-group-template-functions.md). Den här funktionen måste stå inom citattecken och hakparenteser så här: `"[<template-function>]"`. I det här fallet används den `resourceGroup` funktion. Det tar i inga argument och returnerar ett JSON-objekt med metadata om den här distributionen distribueras till resursgruppen. Resursgruppen har angetts av användaren vid tidpunkten för distribution. Vi sedan index i den här JSON-objekt med `.location` att hämta platsen från JSON-objekt.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Ange egenskaper för virtuellt nätverk
Varje Resource Manager-resurs har sin egen `properties` avsnittet för konfigurationer som är specifika för resursen. I det här fallet vi anger att det virtuella nätverket ska ha ett undernät med privata IP-adressintervallet `10.0.0.0/16`. En skaluppsättning för är alltid finns i ett undernät. Det går inte att sträcka sig över undernät.

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
Förutom de nödvändiga `type`, `name`, `apiVersion`, och `location` egenskaper för varje resurs kan ha en valfri `dependsOn` lista med strängar. Den här listan anger vilken andra resurser från den här distributionen måste avslutas innan du distribuerar den här resursen.

I det här fallet finns bara ett element i listan över det virtuella nätverket från föregående exempel. Vi kan ange detta beroende eftersom skalan behöver ange nätverket måste finnas innan du skapar virtuella datorer. På så sätt kan skaluppsättning kan ge dessa privata IP-adresser för virtuella datorer från IP-adressintervall som tidigare har angetts i nätverksegenskaperna för. Formatet för varje sträng i listan dependsOn är `<type>/<name>`. Använda samma `type` och `name` använt tidigare i resursdefinitionen virtuellt nätverk.

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
## <a name="specify-scale-set-properties"></a>Ange egenskaper för skalan
Skaluppsättningar har många egenskaper för att anpassa de virtuella datorerna i skaluppsättning. En fullständig lista över dessa egenskaper finns i [skaluppsättning REST API-dokumentation](https://docs.microsoft.com/en-us/rest/api/virtualmachinescalesets/create-or-update-a-set). För den här självstudiekursen kommer vi ange några vanliga egenskaper.
### <a name="supply-vm-size-and-capacity"></a>Ange VM-storlek och kapacitet
Skaluppsättning måste du ange vilken storlek för den virtuella datorn för att skapa (”sku namn”) och hur många sådana virtuella datorer att skapa (”artikelnummerkapaciteten”). Vilka VM-storlekar är tillgängliga finns i [storlekar på VM-dokumentationen](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Välj typ av uppdateringar
Skalan också behöver veta hur du hanterar uppdateringar på skaluppsättning. Det finns två alternativ `Manual` och `Automatic`. Mer information om skillnaderna mellan två finns i dokumentationen på [hur du uppgraderar en skalningsuppsättning](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Välj operativsystem för VM
Skaluppsättning måste du ange vilket operativsystem du vill publicera på de virtuella datorerna. Här kan skapa vi de virtuella datorerna med en fullständigt korrigeringsfil Ubuntu 16.04 LTS avbildning.

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
Skaluppsättning distribuerar flera virtuella datorer. I stället för att ange namn på varje virtuell dator kan vi ange `computerNamePrefix`. Skaluppsättning lägger till ett index till prefix för varje virtuell dator så att VM-namn har formatet `<computerNamePrefix>_<auto-generated-index>`.

I följande fragment använder vi parametrar från innan ange administratörsanvändarnamn och lösenord för alla virtuella datorer i skaluppsättning. Vi göra detta med den `parameters` mallfunktionen. Den här funktionen använder en sträng som anger vilken parameter att referera till och anger värdet för parametern.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Ange konfiguration för VM-nätverk
Slutligen behöver vi ange nätverkskonfigurationen för de virtuella datorerna i skaluppsättning. I det här fallet behöver vi bara ange ID för det undernät som skapats tidigare. Detta visar skaluppsättningen att placera nätverksgränssnitten i det här undernätet.

Du kan hämta ID för det virtuella nätverket med undernätet med hjälp av den `resourceId` mallfunktionen. Den här funktionen tar i typ och namn på en resurs och returnerar fullständigt kvalificerade identifieraren för den här resursen. Detta ID har formatet:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Identifierare för det virtuella nätverket är dock inte tillräckligt med. Du måste ange specifika undernät som den skaluppsättning för virtuella datorer måste vara i. Om du vill göra detta, sammanfoga `/subnets/mySubnet` -ID: t för det virtuella nätverket. Resultatet är det fullständigt kvalificerade ID till undernätet. Gör den här sammanfogning med den `concat` funktion, vilket tar i en serie med strängar och returnerar sina sammanfogning.

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
