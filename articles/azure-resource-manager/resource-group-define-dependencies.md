---
title: Ange distributionsordning för Azure-resurser | Microsoft Docs
description: Beskriver hur du konfigurerar en resurs som är beroende av en annan resurs under distributionen för att säkerställa att resurserna distribueras i rätt ordning.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2018
ms.author: tomfitz
ms.openlocfilehash: 308ab9d35e07c8376fb183c794fcad77a74a1df9
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295571"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definiera ordningen för att distribuera resurser i Azure Resource Manager-mallar
För en viss resurs, kan det finnas andra resurser som måste finnas innan resursen är distribuerad. Till exempel måste en SQL-server finnas innan du distribuerar en SQL-databas. Du kan definiera den här relationen genom att markera en resurs som är beroende av andra resursen. Du definierar ett beroende med den **dependsOn** element, eller genom att använda den **referens** funktion. 

Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i beroendeordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Du behöver bara definiera beroenden för resurser som distribueras i samma mall. 

En självstudiekurs finns i [självstudie: skapa Azure Resource Manager-mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>dependsOn
I din mall kan dependsOn-element du definiera en resurs som beroende på en eller flera resurser. Värdet kan vara en kommaavgränsad lista över resursnamn. 

I följande exempel visas en skalningsuppsättning för virtuella datorer som är beroende av en belastningsutjämnare, virtuellt nätverk och en loop som skapar flera lagringskonton. Dessa andra resurser visas inte i följande exempel, men de måste finnas någon annanstans i mallen.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
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

I föregående exempel finns ett beroende på de resurser som skapas via en kopia skapas med namnet **storageLoop**. Ett exempel finns i [och skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).

När du definierar beroenden måste inkludera du de resursproviderns namnområde och en resurstyp för att undvika tvetydighet. Till exempel för att förtydliga en belastningsutjämnare och virtuella nätverk som kan ha samma namn som andra resurser, använder du följande format:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Du kan kodarbete du använder dependsOn för att mappa relationerna mellan dina resurser, är det viktigt att förstå varför du gör den. Till exempel för att dokumentera hur resurser är sammankopplade dependsOn är inte den rätta inställningen. Du kan inte fråga vilka resurser som har definierats i dependsOn-elementet efter distributionen. Genom att använda dependsOn kan påverka du eventuellt distributionstiden eftersom Resource Manager inte distribuerar i parallella två resurser som har ett beroende. 

## <a name="child-resources"></a>Underordnade resurser
Egenskapen resurser kan du ange underordnade resurser som är relaterade till resursen som definieras. Underordnade resurser kan bara vara definierade fem nivåers djup. Det är viktigt att notera att beroende av ett implicit inte skapats mellan en underordnad resurs och den överordnade resursen. Om du behöver den underordnade resursen som ska distribueras när den överordnade resursen, måste du uttryckligen ange sambandet med egenskapen dependsOn. 

Varje överordnad resurs accepterar endast vissa typer av resurser som underordnade resurser. Godkända resurstyper anges i den [mallsschemat](https://github.com/Azure/azure-resource-manager-schemas) av den överordnade resursen. Namnet på underordnade resurstyp innehåller namnet på resurstypen överordnade exempelvis **Microsoft.Web/sites/config** och **Microsoft.Web/sites/extensions** är både underordnade resurser till **Microsoft.Web/sites**.

I följande exempel visas en SQLServer och SQL-databas. Observera att beroende av ett explicit definieras mellan SQL database och SQLServer, trots att databasen är en underordnad till servern.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
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

## <a name="reference-and-list-functions"></a>referens och lista
Den [refererar till funktionen](resource-group-template-functions-resource.md#reference) aktiverar ett uttryck som hämtar sitt värde från andra JSON-namn och värdepar eller runtime-resurser. Den [lista * funktioner](resource-group-template-functions-resource.md#list) returvärden för en resurs från en lista-åtgärd.  Referens och lista uttryck deklarera implicit att en resurs beror på en annan, när den refererade resursen distribueras i samma mall och refereras till av sitt namn (inte resurs-ID). Om du skickar resurs-ID i funktionerna referens eller lista är är inte en implicit referens skapas.

Det allmänna formatet för funktionen referens är:

```json
reference('resourceName').propertyPath
```

Det allmänna formatet för funktionen Listnycklar är:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

I följande exempel visas en CDN-slutpunkt beror uttryckligen på CDN-profil och beror implicit på en webbapp.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Du kan använda det här elementet eller dependsOn-element för att ange beroenden, men du behöver inte använda båda för samma beroende resurs. När det är möjligt använder du en implicit referens för att undvika att lägga till en onödig beroende.

Mer information finns i [refererar till funktionen](resource-group-template-functions-resource.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Rekommendationer för att ställa in beroenden

Använd följande riktlinjer när du bestämmer vilka beroenden för att ställa in:

* Ange beroenden för så lite som möjligt.
* Ange en underordnad resurs som är beroende av dess överordnade.
* Använd den **referens** fungerar och ange resursnamnet att ställa in implicit beroenden mellan resurser som behöver dela en egenskap. Lägg inte till beroende av ett explicit (**dependsOn**) när du redan har definierat beroende av ett implicit. Den här metoden minskar risken för onödig beroenden. 
* Ange ett beroende när en resurs inte kan vara **skapade** utan funktioner från en annan resurs. När du inte ange ett beroende om resurserna som bara interagera efter distributionen.
* Låt beroenden cascade utan att ange dem explicit. Till exempel den virtuella datorn är beroende av ett virtuellt nätverksgränssnitt och virtuella nätverksgränssnittet är beroende av ett virtuellt nätverk och offentliga IP-adresser. Därför kan den virtuella datorn är distribuerad när alla tre resurser, men uttryckligen ange inte den virtuella datorn som beroende på alla tre resurser. Den här metoden visar beroendeordningen och gör det lättare att ändra mallen senare.
* Om ett värde kan fastställas före distributionen, försök att distribuera resursen utan koppling. Om ett konfigurationsvärde måste namnet på en annan resurs, kan du inte behöver ett beroende. Den här vägledningen fungerar inte alltid eftersom vissa resurser verifiera att den andra resursen. Om du får ett fel, lägger du till ett beroende. 

Resource Manager identifierar cirkulärt tjänstberoende under mallverifieringen. Om du får ett felmeddelande om att det finns ett cirkulärt beroende, utvärdera din mall för att se om några beroenden som inte behövs och kan tas bort. Om du tar bort beroenden inte fungerar kan undvika du cirkulärt tjänstberoende genom att flytta vissa distributionsåtgärder till underordnade resurser som distribueras efter de resurser som har det cirkulära beroendet. Anta exempelvis att du distribuerar två virtuella datorer men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1
2. vm2
3. Tillägget på vm1 är beroende av vm1 och vm2. Tillägget anger värden för vm1 får från vm2.
4. Tillägget på vm2 beror på vm1 och vm2. Tillägget anger värden för vm2 blir det från vm1.

Läs om hur utvärdera distributionsordning och kodproblem beroende [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill gå igenom en självstudiekurs, se [självstudie: skapa Azure Resource Manager-mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Läs om hur du felsöker beroenden under distributionen i [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Läs om hur du skapar Azure Resource Manager-mallar i [Webbsidemallar](resource-group-authoring-templates.md). 
* En lista över tillgängliga funktioner i en mall finns i [Mallfunktioner](resource-group-template-functions.md).

