---
title: Ange distributionsorder för resurser
description: Beskriver hur du anger en resurs som beroende av en annan resurs under distributionen för att säkerställa att resurser distribueras i rätt ordning.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f11f79df875492a568a76f494dfffb4a163f64cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153292"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definiera ordern för att distribuera resurser i ARM-mallar

När du distribuerar en resurs kan du behöva se till att det finns andra resurser innan den distribueras. Du behöver till exempel en SQL-server innan du distribuerar en SQL-databas. Du definierar den här relationen genom att markera en resurs som beroende av den andra resursen. Du definierar ett beroende med **elementet dependsOn** eller med hjälp av **referensfunktionen.**

Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i beroendeordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Du behöver bara definiera beroenden för resurser som distribueras i samma mall.

## <a name="dependson"></a>dependsOn

I mallen kan du definiera en resurs som en resurs som beroende av en eller flera resurser i mallen. Dess värde är en kommaavgränsad lista med resursnamn. Listan kan innehålla resurser som [distribueras villkorligt](conditional-resource-deployment.md). När en villkorlig resurs inte distribueras tar Azure Resource Manager automatiskt bort den från de nödvändiga beroenden.

I följande exempel visas en skalningsuppsättning för virtuella datorer som är beroende av en belastningsutjämnare, ett virtuellt nätverk och en loop som skapar flera lagringskonton. Dessa andra resurser visas inte i följande exempel, men de måste finnas någon annanstans i mallen.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

I föregående exempel inkluderas ett beroende på de resurser som skapas via en kopiaslinga med namnet **storageLoop**. Ett exempel finns i [Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).

När du definierar beroenden kan du inkludera resursproviderns namnområde och resurstyp för att undvika tvetydighet. Om du till exempel vill förtydliga en belastningsutjämnare och ett virtuellt nätverk som kan ha samma namn som andra resurser använder du följande format:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Även om du kan vara benägen att använda dependsOn för att mappa relationer mellan dina resurser, är det viktigt att förstå varför du gör det. Om du till exempel vill dokumentera hur resurser är sammankopplade är det inte rätt metod att göra det. Du kan inte fråga vilka resurser som har definierats i elementet dependsOn efter distributionen. Genom att använda dependsOn kan du påverka distributionstiden eftersom Resource Manager inte distribuerar parallellt två resurser som har ett beroende.

## <a name="child-resources"></a>Underordnade resurser

Med egenskapen resurser kan du ange underordnade resurser som är relaterade till den resurs som definieras. Underordnade resurser kan bara definieras som fem nivåer djupt. Det är viktigt att notera att ett implicit distributionsberoende inte skapas mellan en underordnad resurs och den överordnade resursen. Om du behöver den underordnade resursen som ska distribueras efter den överordnade resursen måste du uttryckligen ange att beroende med egenskapen dependsOn anges.

Varje överordnad resurs accepterar endast vissa resurstyper som underordnade resurser. Godkända resurstyper anges i [mallschemat](https://github.com/Azure/azure-resource-manager-schemas) för den överordnade resursen. Namnet på den underordnade resurstypen innehåller namnet på den överordnade resurstypen, till exempel **Microsoft.Web/sites/config** och **Microsoft.Web/sites/extensions** är båda underordnade resurser på **Microsoft.Web/sites**.

I följande exempel visas en SQL-server och SQL-databas. Observera att ett explicit beroende definieras mellan SQL-databasen och SQL-servern, även om databasen är underordnad servern.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>referens- och listfunktioner

[Referensfunktionen](template-functions-resource.md#reference) gör det möjligt för ett uttryck att härleda dess värde från andra JSON-namn- och värdepar eller körningsresurser. [List*-funktionerna](template-functions-resource.md#list) returnerar värden för en resurs från en liståtgärd.  Referens- och listuttryck deklarerar implicit att en resurs är beroende av en annan, när den refererade resursen distribueras i samma mall och refereras till med dess namn (inte resurs-ID). Om du skickar resurs-ID:t till referens- eller listfunktionerna skapas ingen implicit referens.

Referensfunktionens allmänna format är:

```json
reference('resourceName').propertyPath
```

Det allmänna formatet för listKeys-funktionen är:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

I följande exempel är en CDN-slutpunkt uttryckligen beroende av CDN-profilen och är implicit beroende av en webbapp.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Du kan använda elementet eller elementet DependsOn för att ange beroenden, men du behöver inte använda båda för samma beroende resurs. Använd en implicit referens när det är möjligt för att undvika att lägga till ett onödigt beroende.

Mer information finns i [referensfunktionen](template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Cirkulära beroenden

Resource Manager identifierar cirkulära beroenden under mallvalideringen. Om du får ett felmeddelande om att det finns ett cirkulärt beroende utvärderar du mallen för att se om några beroenden inte behövs och kan tas bort. Om det inte fungerar att ta bort beroenden kan du undvika cirkulära beroenden genom att flytta vissa distributionsåtgärder till underordnade resurser som distribueras efter de resurser som har det cirkulära beroendet. Anta till exempel att du distribuerar två virtuella datorer, men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1 (vm1)
2. vm2 (vm2)
3. Tillägg på vm1 beror på vm1 och vm2. Tillägget anger värden på vm1 som det får från vm2.
4. Tillägg på VM2 beror på vm1 och vm2. Tillägget anger värden på vm2 som det får från vm1.

Information om hur du bedömer distributionsordern och löser beroendefel finns i [Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom en självstudiekurs finns i [Självstudiekurs: skapa Azure Resource Manager-mallar med beroende resurser](template-tutorial-create-templates-with-dependent-resources.md).
* Rekommendationer när du anger beroenden finns i [metodtips för Azure Resource Manager-mall .](template-best-practices.md)
* Mer information om felsökning av beroenden under distributionen finns [i Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).
* Mer information om hur du skapar Azure Resource Manager-mallar finns i [Skapa mallar](template-syntax.md).
* En lista över tillgängliga funktioner i en mall finns i [Mallfunktioner](template-functions.md).

