---
title: "Ange distributionsordning för Azure-resurser | Microsoft Docs"
description: "Beskriver hur du ställer in en resurs som är beroende av en annan resurs under distributionen så resurser distribueras i rätt ordning."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
ms.openlocfilehash: 3d6a46116ae9d7d940bc10dfa832540f42c0af7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definiera ordningen för att distribuera resurser i Azure Resource Manager-mallar
För en viss resurs, kan det finnas andra resurser som måste finnas innan resursen har distribuerats. Till exempel måste en SQLServer finnas innan du försöker att distribuera en SQL-databas. Du definierar relationen genom att markera en resurs som är beroende av andra resursen. Du definierar ett samband med den **dependsOn** elementet eller genom att använda den **referens** funktion. 

Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i ordningsföljden beroende. När resurserna inte är beroende av varandra, distribuerar dem i Resource Manager parallellt. Du behöver bara definiera beroenden för resurser som distribueras i samma mall. 

## <a name="dependson"></a>dependsOn
Inom din mall kan dependsOn-element du definiera en resurs som beroende på en eller flera resurser. Värdet kan vara en kommaavgränsad lista över resurser. 

I följande exempel visas en skaluppsättning för virtuell dator som är beroende av en belastningsutjämnare, virtuella nätverk och en loop som skapar flera lagringskonton. Dessa andra resurser visas inte i följande exempel, men de måste finnas någon annanstans i mallen.

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

I föregående exempel ingår ett beroende på de resurser som har skapats via en kopia skapas med namnet **storageLoop**. Ett exempel finns [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).

När du definierar beroenden, kan du inkludera resursleverantörens namnrymd och resurstyp för att undvika tvetydighet. Till exempel för att förtydliga en belastningsutjämnare och virtuella nätverk som kan ha samma namn som andra resurser, använder du följande format:

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

Du kanske har använda dependsOn för att mappa relationer mellan dina resurser, är det viktigt att förstå varför du gör den. Till exempel för att dokumentera hur resurser är sammankopplade är dependsOn inte rätt metod. Du kan inte fråga vilka resurser som har definierats i elementet dependsOn efter distributionen. Genom att använda dependsOn kan påverka du eventuellt distributionen eftersom Resource Manager inte distribuerar i parallella två resurser som är beroende av. Dokumentera relationerna mellan resurser genom att i stället använda [resurslänkningen](/rest/api/resources/resourcelinks).

## <a name="child-resources"></a>Underordnade resurser
Egenskapen resurser kan du ange underordnade resurser som är relaterade till resursen som definieras. Underordnade resurser kan bara vara definierade fem nivåers djup. Det är viktigt att Observera att en implicit beroende inte skapas mellan en resurs för underordnade och överordnade resursen. Om du behöver den underordnade resursen som ska distribueras när den överordnade resursen, måste du uttryckligen ange sambandet med egenskapen dependsOn. 

Varje överordnad resurs accepterar endast vissa typer av resurser som underordnade resurser. Godkända resurstyper har angetts i den [mallsschemat](https://github.com/Azure/azure-resource-manager-schemas) på den överordnade resursen. Namnet på resurstypen för underordnade innehåller till exempel namnet på resurstypen överordnade **Microsoft.Web/sites/config** och **Microsoft.Web/sites/extensions** finns både underordnade resurser av den **Microsoft.Web/sites**.

I följande exempel visas en SQLServer och SQL-databas. Observera att du har angett ett explicit beroende mellan SQL-databas och SQLServer, trots att databasen är en underordnad till servern.

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

## <a name="reference-function"></a>referens-funktion
Den [referera funktionen](resource-group-template-functions-resource.md#reference) gör att ett uttryck att erhålla dess värde från andra JSON namn och värdepar eller runtime-resurser. Referensuttryck deklarerar implicit att en resurs beror på en annan. Det allmänna formatet är:

```json
reference('resourceName').propertyPath
```

I följande exempel visas en CDN-slutpunkt beror uttryckligen på CDN-profilen och beror implicit på ett webbprogram.

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

Du kan använda det här elementet eller dependsOn-elementet för att ange beroenden, men du behöver inte använda både för samma beroende resurs. När det är möjligt Använd en implicit referens för att undvika att lägga till ett onödiga samband.

Läs mer i [referera funktionen](resource-group-template-functions-resource.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Rekommendationer för att ställa in beroenden

Använd följande riktlinjer när du bestämmer vilka beroenden för att ange:

* Ange beroenden så lite som möjligt.
* Ange en underordnad resurs som är beroende av den överordnade resursen.
* Använd den **referens** funktion för att ange implicit beroenden mellan resurser som ska dela en egenskap. Lägg inte till ett explicit beroende (**dependsOn**) när du redan har definierat ett indirekt samband. Den här metoden minskar risken för att onödiga beroenden. 
* Ange ett beroende när en resurs inte får vara **skapade** utan funktioner från en annan resurs. Ange inte ett beroende om endast interagera resurser efter distributionen.
* Låt beroenden cascade utan att ange dem explicit. Till exempel den virtuella datorn är beroende av ett virtuellt nätverksgränssnitt och virtuella nätverksgränssnittet beror på ett virtuellt nätverk och offentliga IP-adresser. Därför kan den virtuella datorn är distribuerad när alla tre resurser, men uttryckligen anger inte den virtuella datorn som är beroende av alla tre resurser. Den här metoden klargör beroendeordningen och gör det enklare att ändra mallen senare.
* Om ett värde kan fastställas innan distribution, försök att distribuera resursen utan ett beroende. Om ett konfigurationsvärde måste namnet på en annan resurs, kanske du inte behöver ett beroende. Den här vägledningen fungerar inte alltid eftersom vissa resurser verifiera att den andra resursen. Om du får ett felmeddelande, lägger du till ett beroende. 

Resource Manager identifierar cirkulärt tjänstberoende vid verifiering av mallen. Utvärdera din mall för att se om några beroenden behövs inte och kan tas bort om du får ett felmeddelande om att det finns ett cirkulärt beroende. Om du tar bort beroenden inte fungerar kan undvika du Cirkelberoenden genom att flytta vissa distributionsåtgärder till underordnade resurser som distribueras efter resurser som har cirkulärt beroende. Anta exempelvis att du distribuerar två virtuella datorer, men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1
2. vm2
3. Tillägg på vm1 beror på vm1 och vm2. Tillägget anger värden för vm1 får från vm2.
4. Tillägg på vm2 beror på vm1 och vm2. Tillägget anger värden för vm2 får från vm1.

Information om utvärdera distributionsordningen och beroende kodproblem finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Nästa steg
* Läs om hur du felsöker beroenden under distributionen i [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Läs om hur du skapar mallar för Azure Resource Manager i [Webbsidemallar](resource-group-authoring-templates.md). 
* En lista över alla tillgängliga funktioner i en mall finns [Mallfunktioner](resource-group-template-functions.md).

