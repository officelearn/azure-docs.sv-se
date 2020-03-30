---
title: Resursparametrar för Azure Monitor-arbetsböcker
description: Förenkla komplex rapportering med färdiga och anpassade parameteriserade arbetsböcker
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658110"
---
# <a name="workbook-resource-parameters"></a>Parametrar för arbetsboksresurs

Resursparametrar tillåter plockning av resurser i arbetsböcker. Detta är användbart när du anger omfattningen som data från. Ett exempel är att tillåta användare att välja den uppsättning virtuella datorer, som diagrammen senare kommer att använda när de presenterar data.

Värden från resursväljare kan komma från arbetsbokskontexten, den statiska listan eller från Azure Resource Graph-frågor.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Skapa en resursparameter (arbetsboksresurser)
1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn:`Applications`
    2. Parametertyp:`Resource picker`
    3. Krävs:`checked`
    4. Tillåt flera val:`checked`
5. Hämta data från:`Workbook Resources`
6. Inkludera endast resurstyper:`Application Insights`
7. Välj Spara i verktygsfältet för att skapa parametern.

![Bild som visar skapandet av en resursparameter med hjälp av arbetsboksresurser](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Skapa en resursparameter (Azure Resource Graph)
1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn:`Applications`
    2. Parametertyp:`Resource picker`
    3. Krävs:`checked`
    4. Tillåt flera val:`checked`
5. Hämta data från:`Query`
    1. Frågetyp:`Azure Resource Graph`
    2. Prenumerationer:`Use default subscriptions`
    3. Lägg till det här kodavsnittet i frågekontrollen
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Välj Spara i verktygsfältet för att skapa parametern.

![Avbildning som visar skapandet av en resursparameter med Hjälp av Azure Resource Graph](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph är ännu inte tillgängligt i alla moln. Se till att det stöds i ditt målmoln om du väljer den här metoden.

[Azure Resource Graph-dokumentation](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Skapa en resursparameter (JSON-lista)
1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn:`Applications`
    2. Parametertyp:`Resource picker`
    3. Krävs:`checked`
    4. Tillåt flera val:`checked`
5. Hämta data från:`JSON`
    1. Lägg till det här json-kodavsnittet i innehållskontrollen
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Tryck på den blå _uppdateringsknappen._
6. Ange eventuellt `Include only resource types` till _Application Insights_
7. Välj Spara i verktygsfältet för att skapa parametern.

## <a name="referencing-a-resource-parameter"></a>Referera till en resursparameter
1. Lägg till en frågekontroll i arbetsboken och välj en Application Insights-resurs.
2. Använd listrutan _Programstatistik_ för att binda parametern till kontrollen. Om du gör det anges frågans omfattning till de resurser som returneras av parametern vid körning.
4. Lägg till det här kodavsnittet i KQL-kontrollen
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Kör fråga för att se resultaten. 

![Bild som visar en resursparameter som refereras i en frågekontroll](./media/workbooks-resources/resource-reference.png)

> Den här metoden kan användas för att binda resurser till andra kontroller som mått.

## <a name="resource-parameter-options"></a>Alternativ för resursparameter
| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `{Applications}` | Det valda resurs-ID:et | _/subscriptions/<under-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication_ |
| `{Applications:label}` | Etiketten på den valda resursen | `acmefrontend` |
| `{Applications:value}` | Värdet på den valda resursen | _'/subscriptions/<under-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication"_ |
| `{Applications:name}` | Namnet på den valda resursen | `acmefrontend` |
| `{Applications:resourceGroup}` | Resursgruppen för den valda resursen | `acmegroup` |
| `{Applications:resourceType}` | Typen av vald resurs | _microsoft.insights/components_ |
| `{Applications:subscription}` | Prenumerationen på den valda resursen |  |
| `{Applications:grid}` | Ett rutnät som visar resursegenskaperna. Användbart att återge i ett textblock medan felsökning  |  |

## <a name="next-steps"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
