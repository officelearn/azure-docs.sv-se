---
title: Översikt över vydefinitionen i Azure Managed Applications | Microsoft Docs
description: Beskriver konceptet med att skapa vydefinitionen för Azure Managed Applications.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478750"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Visa definition artefakt i Azure Managed Applications

Visa definition är en valfri artefakt i Azure Managed Applications. Det kan du anpassa översiktssidan och lägga till fler visningar, till exempel mått och anpassade resurser.

Den här artikeln innehåller en översikt över Visa definition artefakt och dess funktioner.

## <a name="view-definition-artifact"></a>Visa definitionsartefakt

Visa definition artefakten måste ha namnet **viewDefinition.json** och placeras på samma nivå som **createUiDefinition.json** och **mainTemplate.json** i .zip paket som skapar en definition för hanterade program. Information om hur du skapar ZIP-paketet och publicerar en definition för hanterade program finns [publicera ett hanterat program i Azure-definition](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Visa definitionsschema

Den **viewDefinition.json** filen har endast en toppnivå `views` egenskapen, som är en matris med vyer. Varje vy visas i användargränssnittet för det hanterade programmet som ett separat menyalternativ i tabellen i innehållsförteckningen. Varje vy har en `kind` egenskap som anger typen av vyn. Det måste anges till något av följande värden: [Översikt över](#overview), [mått](#metrics), [CustomResources](#custom-resources). Mer information finns i aktuell [JSON-schema för viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Exempel-JSON för vydefinitionen:

```json
{
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
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
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
    ]
}

```

## <a name="overview"></a>Översikt

`"kind": "Overview"`

När du anger den här vyn i **viewDefinition.json**, åsidosätter standard översiktssidan i det hanterade programmet.

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
|sidhuvud|Nej|Rubriken på översiktssidan.|
|description|Nej|Beskrivning av det hanterade programmet.|
|Kommandon|Nej|Matris med ytterligare verktygsfältsknappar på översiktssidan finns i [kommandon](#commands).|

## <a name="metrics"></a>Mått

`"kind": "Metrics"`

Måttvyn kan du samla in och samla ihop data från dina resurser för hanterat program i [Azure Monitor Metrics](../azure-monitor/platform/data-platform-metrics.md).

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
|displayName|Nej|Rubriken visas i vyn.|
|version|Nej|Versionen av den plattform som används för återgivning av vyn.|
|Diagram|Ja|Matris med diagram över sidan mått.|

### <a name="chart"></a>Diagram

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Ja|Rubriken visas i diagrammet.|
|chartType|Nej|Visualiseringen som ska användas för det här diagrammet. Som standard används ett linjediagram. Diagramtyper som stöds: `Bar, Line, Area, Scatter`.|
|metrics|Ja|Matris med mått att rita i det här diagrammet. Läs mer om mått som stöds i Azure-portalen i [stöds mått med Azure Monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Mått

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|name|Ja|Namnet på måttet.|
|aggregationType|Ja|Sammansättningstyp ska användas för det här måttet. Aggregeringstyper som stöds: `none, sum, min, max, avg, unique, percentile, count`|
|namnområde|Nej|Ytterligare information för att använda när du fastställer rätt mått-providern.|
|resourceTagFilter|Nej|Resursen taggar matris (skiljs åt med `or` word) för vilka mått visas. Gäller ovanpå filter för en resurstyp.|
|resourceType|Ja|Den resurstyp som mått visas.|

## <a name="custom-resources"></a>Anpassade resurser

`"kind": "CustomResources"`

Du kan definiera flera vyer i den här typen. Varje vy representerar en **unika** anpassade resurstyp från den anpassade providern du har definierat i **mainTemplate.json**. En introduktion till anpassade providrar finns i [anpassade Providers förhandsversion av Azure-översikt](custom-providers-overview.md).

I den här vyn kan du utföra GET, PUT, ta bort och publicera åtgärder för din egen resurstyp. POST-åtgärder kan vara globalt anpassade åtgärder eller anpassade åtgärder i en kontext för ditt anpassade resurstypen.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
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
|displayName|Ja|Rubriken visas i vyn. Rubriken ska vara **unika** för CustomResources vyerna i din **viewDefinition.json**.|
|version|Nej|Versionen av den plattform som används för återgivning av vyn.|
|resourceType|Ja|Den anpassa resurstypen. Måste vara en **unika** anpassade resurstypen för den anpassade providern.|
|createUIDefinition|Nej|Skapa Användargränssnittet Definition schemat för Skapa anpassad resurs-kommando. En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md)|
|Kommandon|Nej|Matris med ytterligare verktygsfältsknappar CustomResources-vyn finns i [kommandon](#commands).|
|Kolumner|Nej|Matris med kolumner i den anpassade resursen. Om inte har definierats i `name` kolumnen kommer att visas som standard. Kolumnen måste ha `"key"` och `"displayName"`. För nyckeln, anger du nyckeln för egenskapen ska visas i vyn. Om kapslade, Använd punkt som avgränsare, till exempel `"key": "name"` eller `"key": "properties.property1"`. Ange visningsnamnet på egenskapen som ska visas i vyn för visningsnamn. Du kan också tillhandahålla en `"optional"` egenskapen. När värdet är true, kolumnen är dold i en vy som standard.|

## <a name="commands"></a>Kommandon

Kommandon är en matris med ytterligare knappar som visas på sidan. Varje kommando representerar en åtgärd efter från din Azure anpassad Provider som definierats i **mainTemplate.json**. En introduktion till anpassade providrar finns i [översikt över Azure anpassade Providers](custom-providers-overview.md).

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
|displayName|Ja|Visningsnamnet för kommandoknappen.|
|sökväg|Ja|Anpassad provider åtgärdsnamn. Åtgärden måste definieras i **mainTemplate.json**.|
|storleken på ikoner|Nej|Ikonen för kommandoknappen. Lista över stöds ikoner har definierats i [JSON-Schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nej|Skapa UI-definitionsschema för kommandot. En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="next-steps"></a>Nästa steg

- En introduktion till hanterade program finns i [översikten över Azure Managed Application](overview.md).
- En introduktion till anpassade providrar finns i [översikt över Azure anpassade Providers](custom-providers-overview.md).
