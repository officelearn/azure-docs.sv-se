---
title: Översikt över vydefinition
description: Beskriver konceptet att skapa vydefinition för Azure Managed Applications.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 7a3d2234a140d1fb2eede50e3fe2eef5575da648
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391698"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Visa definitionsartefakt i Azure-hanterade program

Vydefinition är en valfri artefakt i Azure Managed Applications. Det gör det möjligt att anpassa översiktssidan och lägga till fler vyer som mått och anpassade resurser.

Den här artikeln innehåller en översikt över artefakten för vydefinition och dess funktioner.

## <a name="view-definition-artifact"></a>Visa definitionsartefakt

Artefakten för vydefinition måste namnges **viewDefinition.json** och placeras på samma nivå som **createUiDefinition.json** och **mainTemplate.json** i ZIP-paketet som skapar en hanterad programdefinition. Mer information om hur du skapar ZIP-paketet och publicerar en definierad hanterad programdefinition finns i [Publicera en definition av Azure Managed Application](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>Visa definitionsschema

Filen **viewDefinition.json** har bara `views` en egenskap på den översta nivån, vilket är en matris med vyer. Varje vy visas i användargränssnittet för hanterade program som ett separat menyalternativ i innehållsförteckningen. Varje vy `kind` har en egenskap som anger typen av vy. Den måste anges till ett av följande värden: [Översikt](#overview), [Mått](#metrics), [CustomResources](#custom-resources), [Associations](#associations). Mer information finns i aktuellt [JSON-schema för viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Exempel på JSON för vydefinition:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Översikt

`"kind": "Overview"`

När du anger den här vyn i **viewDefinition.json**åsidosätter den standardöversiktssidan i det hanterade programmet.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|sidhuvud|Inga|Rubriken på översiktssidan.|
|description|Inga|Beskrivningen av ditt hanterade program.|
|kommandon|Inga|Matrisen med ytterligare verktygsfältsknappar på [översiktssidan,](#commands)se kommandon .|

![Översikt](./media/view-definition/overview.png)

## <a name="metrics"></a>Mått

`"kind": "Metrics"`

Med måttvyn kan du samla in och aggregera data från dina hanterade programresurser i [Azure Monitor Metrics](../../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Inga|Vyns rubrik visas.|
|version|Inga|Den version av plattformen som används för att återge vyn.|
|Diagram|Ja|Matrisen med diagram över måttsidan.|

### <a name="chart"></a>Diagram

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Ja|Diagrammets rubrik visas.|
|diagramtyp|Inga|Den visualisering som ska användas för det här diagrammet. Som standard används ett linjediagram. Diagramtyper som `Bar, Line, Area, Scatter`stöds: .|
|metrics|Ja|Matrisen med mått som ska ritas i det här diagrammet. Mer information om mått som stöds i Azure Portal finns i [Mått som stöds med Azure Monitor](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Mått

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|namn|Ja|Namnet på måttet.|
|aggregeringstyp|Ja|Den aggregeringstyp som ska användas för det här måttet. Aggregeringstyper som stöds:`none, sum, min, max, avg, unique, percentile, count`|
|namnområde|Inga|Ytterligare information som ska användas när du fastställer rätt måttprovider.|
|resursTaggfilter|Inga|Matrisen för resurstaggar `or` (separeras med ord) för vilka mått skulle visas. Gäller ovanpå resurstypsfiltret.|
|resourceType|Ja|Den resurstyp för vilken mått skulle visas.|

![Mått](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Anpassade resurser

`"kind": "CustomResources"`

Du kan definiera flera vyer av den här typen. Varje vy representerar en **unik** anpassad resurstyp från den anpassade provider som du definierade i **mainTemplate.json**. En introduktion till anpassade leverantörer finns i [översikt över förhandsversionen av Azure Custom Providers](../custom-providers/overview.md).

I den här vyn kan du utföra get-, PUT-, DELETE- och POST-åtgärder för din anpassade resurstyp. POST-åtgärder kan vara globala anpassade åtgärder eller anpassade åtgärder i en kontext av din anpassade resurstyp.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Ja|Vyns rubrik visas. Titeln ska vara **unik** för varje CustomResources-vy i **din viewDefinition.json**.|
|version|Inga|Den version av plattformen som används för att återge vyn.|
|resourceType|Ja|Den anpassade resurstypen. Måste vara en **unik** anpassad resurstyp för din anpassade leverantör.|
|storleken på|Inga|Ikonen för vyn. Listan över exempelikoner definieras i [JSON Schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|skapaUIDefinition|Inga|Skapa gränssnittsdefinitionsschema för att skapa anpassat resurskommando. En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md)|
|kommandon|Inga|Matrisen med ytterligare verktygsfältsknappar i vyn CustomResources finns i [kommandon](#commands).|
|kolumner|Inga|Matrisen med kolumner för den anpassade resursen. Om den `name` inte har definierats visas kolumnen som standard. Kolumnen måste `"key"` ha `"displayName"`och . För nyckeln anger du nyckeln till egenskapen som ska visas i en vy. Om du är kapslad använder du `"key": "name"` punkt `"key": "properties.property1"`som avgränsare, till exempel, eller . För visningsnamn anger du visningsnamnet på den egenskap som ska visas i en vy. Du kan också `"optional"` erbjuda en fastighet. När den är inställd på true döljs kolumnen i en vy som standard.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Kommandon

Kommandon är en matris med ytterligare verktygsfältsknappar som visas på sidan. Varje kommando representerar en POST-åtgärd från din Azure Custom Provider som definierats i **mainTemplate.json**. En introduktion till anpassade leverantörer finns i [översikt över Azure Custom Providers](../custom-providers/overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Ja|Kommandoknappens namn visas.|
|path|Ja|Åtgärdsnamnet för den anpassade leverantören. Åtgärden måste definieras i **mainTemplate.json**.|
|storleken på|Inga|Ikonen för kommandoknappen. Listan över exempelikoner definieras i [JSON Schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|skapaUIDefinition|Inga|Skapa schema för UI Definition för kommando. En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Sammanslutningar

`"kind": "Associations"`

Du kan definiera flera vyer av den här typen. I den här vyn kan du länka befintliga resurser till det hanterade programmet via den anpassade provider som du definierade i **mainTemplate.json**. En introduktion till anpassade leverantörer finns i [översikt över förhandsversionen av Azure Custom Providers](../custom-providers/overview.md).

I den här vyn kan du `targetResourceType`utöka befintliga Azure-resurser baserat på . När en resurs har valts skapas en begäran om introduktion till den **offentliga** anpassade providern, som kan använda en bieffekt på resursen. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Ja|Vyns rubrik visas. Titeln ska vara **unik** för varje associationsvy i **din viewDefinition.json**.|
|version|Inga|Den version av plattformen som används för att återge vyn.|
|targetResourceType|Ja|Målresurstypen. Det här är den resurstyp som ska visas för resursbelöningen.|
|skapaUIDefinition|Inga|Skapa schema för UI Definition för kommandot skapa associationsresurser. En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Söker hjälp

Om du har frågor om Azure Managed Applications kan du prova att fråga på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). En liknande fråga kan redan ha ställts och besvarats, så kontrollera först innan du postar. Lägg till `azure-managedapps` taggen för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

- En introduktion till hanterade program finns i [Azure Managed Application overview](overview.md) (Översikt över Azure Managed Application).
- En introduktion till anpassade leverantörer finns i [översikt över Azure Custom Providers](../custom-providers/overview.md).
- Information om hur du skapar ett Azure Managed Application med Azure Custom Providers finns i [Självstudiekurs: Skapa hanterade program med anpassade provideråtgärder och resurstyper](tutorial-create-managed-app-with-custom-provider.md)
