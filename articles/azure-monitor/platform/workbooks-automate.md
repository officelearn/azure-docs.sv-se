---
title: Azure Monitor-arbetsböcker och Azure Resource Manager-mallar
description: Förenkla komplex rapportering med fördefinierade och anpassade parameteriserade Azure Monitor-arbetsböcker som distribueras via Azure Resource Manager-mallar
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658412"
---
# <a name="programmatically-manage-workbooks"></a>Hantera arbetsböcker på ett programmässigt sätt

Resursägare har möjlighet att skapa och hantera sina arbetsböcker programmässigt via Resource Manager-mallar. 

Detta kan vara användbart i scenarier som:
* Distribuera organisations- eller domänspecifika analysrapporter tillsammans med resursdistributioner. Du kan till exempel distribuera organisationsspecifika prestanda- och felarbetsböcker för dina nya appar eller virtuella datorer.
* Distribuera standardrapporter eller instrumentpaneler med arbetsböcker för befintliga resurser.

Arbetsboken skapas i önskad under-/resursgrupp och med det innehåll som anges i Resource Manager-mallarna.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Azure Resource Manager-mall för distribution av arbetsböcker
1. Öppna en arbetsbok som du vill distribuera programmässigt.
2. Växla arbetsboken till redigeringsläge genom att klicka på alternativet _Redigera_ verktygsfält.
3. Öppna _Avancerade redigeraren_ med _</>_ knappen i verktygsfältet.
4. I redigeraren växlar du _malltyp_ till _Resurshanterarens mall_.
5. Mallen Resurshanteraren för att skapa visas i redigeraren. Kopiera innehållet och använd som det är eller slå ihop det med en större mall som också distribuerar målresursen.

    ![Bild som visar hur du hämtar Resource Manager-mallen från arbetsboksgränssnittet](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Exempel på Azure Resource Manager-mall
Den här mallen visar hur du distribuerar en enkel arbetsbok som visar en "Hello World!"
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Mallparametrar

| Parameter | Förklaring |
| :------------- |:-------------|
| `workbookDisplayName` | Det egna namnet på arbetsboken som används i galleriet eller den sparade listan. Måste vara unikt i resursgruppens och resurskällans omfattning |
| `workbookType` | Galleriet som arbetsboken ska visas under. Värden som stöds `tsg`inkluderar arbetsbok, , Azure Monitor, etc. |
| `workbookSourceId` | ID:et för den resursinstans som arbetsboken ska associeras till. Den nya arbetsboken visas relaterad till den här resursinstansen, till exempel i resursens innehållsförteckning under _Arbetsbok_. Om du vill att arbetsboken ska visas i arbetsboksgalleriet i Azure Monitor använder du strängen _Azure Monitor_ i stället för ett resurs-ID. |
| `workbookId` | Det unika guid för den här arbetsboksinstansen. Använd _[newGuid()]_ för att automatiskt skapa ett nytt guid. |
| `kind` | Används för att ange om den skapade arbetsboken är delad eller privat. Använd värdet _som delas_ för delade arbetsböcker och _användare_ för privata. |
| `location` | Den Azure-plats där arbetsboken ska skapas. Använd _[resourceGroup().location]_ för att skapa den på samma plats som resursgruppen |
| `serializedData` | Innehåller innehållet eller nyttolasten som ska användas i arbetsboken. Använda resurshanterarens mall från arbetsböckernas användargränssnitt för att hämta värdet |

### <a name="workbook-types"></a>Arbetsbokstyper
Arbetsbokstyper anger vilken arbetsboksgallerityp den nya arbetsboksinstansen ska visas under. Alternativen är:

| Typ | Galleriplats |
| :------------- |:-------------|
| `workbook` | Standardvärdet som används i de flesta rapporter, inklusive arbetsboksgalleriet i Application Insights, Azure Monitor osv.  |
| `tsg` | Galleriet Felsökningsguider i Application Insights |
| `usage` | Det _fler_ galleriet under _Användning_ i Application Insights |

### <a name="limitations"></a>Begränsningar
Av en teknisk anledning kan den här mekanismen inte användas för att skapa arbetsboksinstanser i _arbetsboksgalleriet_ i Application Insights. Vi arbetar med att ta itu med denna begränsning. Under tiden rekommenderar vi att du använder galleriet Felsökningsguide `tsg`(workbookType: ) för att distribuera Application Insights-relaterade arbetsböcker.

## <a name="next-steps"></a>Nästa steg

Utforska hur arbetsböcker används för att driva den nya [Azure Monitor for Storage-upplevelsen](../insights/storage-insights-overview.md).

