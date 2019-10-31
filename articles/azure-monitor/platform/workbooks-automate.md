---
title: Program mässigt Hantera Azure Monitor arbets böcker med Azure Resource Manager mallar | Microsoft-dokument
description: Förenkla komplex rapportering med färdiga och anpassade parameterstyrda Azure Monitor arbets böcker som distribueras via Azure Resource Manager mallar
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d5e22093fa796a9fbd60dc2bc242f37a6cac7cf0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166114"
---
# <a name="programmatically-manage-workbooks"></a>Hantera arbets böcker program mässigt

Resurs ägare har möjlighet att skapa och hantera sina arbets böcker via programmering via Resource Manager-mallar. 

Detta kan vara användbart i scenarier som:
* Distribuera organisations-eller domänbaserade analys rapporter tillsammans med distributioner av resurser. Du kan till exempel distribuera org-/regionsspecifika prestanda-och arbets böcker för dina nya appar eller virtuella datorer.
* Distribuera standard rapporter eller instrument paneler med hjälp av arbets böcker för befintliga resurser.

Arbets boken kommer att skapas i önskad under-eller resurs grupp och med det innehåll som anges i Resource Manager-mallarna.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Azure Resource Manager mall för distribution av arbets böcker
1. Öppna en arbets bok som du vill distribuera program mässigt.
2. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
3. Öppna _avancerad redigerare_ med hjälp av knappen _</>_ i verktygsfältet.
4. I redigeraren byter du _Malltyp_ till _Resource Manager-mall_.
5. Resource Manager-mallen för att skapa visas i redigeraren. Kopiera innehållet och Använd det som är eller slå samman det med en större mall som också distribuerar mål resursen.

    ![Bild som visar hur du hämtar Resource Manager-mallen inifrån arbets bokens användar gränssnitt](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Exempel på Azure Resource Manager mall
Den här mallen visar hur du distribuerar en enkel arbets bok som visar Hello World!
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
| `workbookDisplayName` | Det egna namnet på arbets boken som används i galleriet eller sparade listor. Måste vara unikt i omfånget för resurs gruppen och källan |
| `workbookType` | Galleriet som arbets boken visas under. Värden som stöds är arbets bok, `tsg`, Azure Monitor osv. |
| `workbookSourceId` | ID för resurs instansen som arbets boken ska associeras med. Den nya arbets boken visas som är relaterad till den här resurs instansen – till exempel i resursens innehålls förteckning under _arbets bok_. Om du vill att arbets boken ska visas i arbets boks galleriet i Azure Monitor använder du strängen _Azure Monitor_ i stället för ett resurs-ID. |
| `workbookId` | Unikt GUID för arbets boks instansen. Använd _[newGuid ()]_ för att automatiskt skapa ett nytt GUID. |
| `kind` | Används för att ange om den skapade arbets boken är delad eller privat. Använd värdet _delat_ för delade arbets böcker och _användare_ för privata. |
| `location` | Den Azure-plats där arbets boken kommer att skapas. Använd _[resourceGroup (). location]_ för att skapa den på samma plats som resurs gruppen |
| `serializedData` | Innehåller det innehåll eller den nytto last som ska användas i arbets boken. Använd Resource Manager-mallen från arbets bokens användar gränssnitt för att hämta värdet |

### <a name="workbook-types"></a>Arbets boks typer
Arbets boks typer ange vilken arbets boks Galleri typ den nya arbets boks instansen ska visas under. Alternativen är:

| Typ | Galleri plats |
| :------------- |:-------------|
| `workbook` | Standardvärdet används i de flesta rapporter, inklusive arbets boks galleriet för Application Insights, Azure Monitor osv.  |
| `tsg` | Galleriet för fel söknings guider i Application Insights |
| `usage` | Galleriet _mer_ under _användning_ i Application Insights |

### <a name="limitations"></a>Begränsningar
Av teknisk anledning kan den här mekanismen inte användas för att skapa arbets boks instanser i galleriet för _arbets böcker_ i Application Insights. Vi arbetar på att lösa den här begränsningen. Under tiden rekommenderar vi att du använder galleriet för fel söknings guiden (workbookType: `tsg`) för att distribuera Application Insights relaterade arbets böcker.

## <a name="next-steps"></a>Nästa steg

Utforska hur arbets böcker används för att sätta nya [Azure Monitor för lagrings upplevelsen](../insights/storage-insights-overview.md).

