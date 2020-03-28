---
title: 'Självstudiekurs: Använd Azure App-konfiguration för att hantera funktionsflaggor'
titleSuffix: Azure App Configuration
description: I den här självstudien får du lära dig hur du hanterar funktionsflaggor separat från ditt program med hjälp av Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: ccab8014000f9f684249bf2c1f800f74c92e7369
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76899361"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Självstudiekurs: Hantera funktionsflaggor i Azure App-konfiguration

Du kan lagra alla funktionsflaggor i Azure App-konfigurationen och administrera dem från en enda plats. Appkonfiguration har ett portalgränssnitt med namnet **Feature Manager** som är utformat speciellt för funktionsflaggor. Appkonfigurationen stöder också dataschemat för .NET Core-funktionens flagga.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Definiera och hantera funktionsflaggor i appkonfiguration.
> * Åtkomstfunktionsflaggor från ditt program.

## <a name="create-feature-flags"></a>Skapa funktionsflaggor

Funktionshanteraren i Azure-portalen för appkonfiguration tillhandahåller ett användargränssnitt för att skapa och hantera funktionsflaggor som du använder i dina program.

Så här lägger du till en ny funktionsflagga:

1. Välj **Funktionshanteraren** > **+Lägg** till om du vill lägga till en funktionsflagga.

    ![Lista över funktionsflagga](./media/azure-app-configuration-feature-flags.png)

1. Ange ett unikt nyckelnamn för funktionsflaggan. Du behöver det här namnet för att referera till flaggan i koden.

1. Om du vill kan du ge funktionsflagga en beskrivning.

1. Ange det ursprungliga tillståndet för funktionsflaggan. Detta tillstånd är vanligtvis *Av* eller *På*. *Tillståndet På* ändras till *Villkorat* om du lägger till ett filter i funktionsflaggan.

    ![Skapa funktionsflagga](./media/azure-app-configuration-feature-flag-create.png)

1. När tillståndet är *På*väljer du **+Lägg till filter** för att ange eventuella ytterligare villkor för att kvalificera tillståndet. Ange en inbyggd eller anpassad filternyckel och välj sedan **+Lägg till parameter** för att associera en eller flera parametrar med filtret. Inbyggda filter inkluderar:

    | Nyckel | JSON-parametrar |
    |---|---|
    | Microsoft.Procent | {"Värde": 0-100 procent} |
    | Microsoft.TimeWindow | {"Start": UTC-tid, "End": UTC-tid} |

    ![Flagga filter för funktion](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Uppdatera lägena för funktionsflagga

Så här ändrar du tillståndsvärdet för en funktionsflagga:

1. Välj **Funktionshanteraren**.

1. Till höger om en funktionsflagga som du vill ändra markerar du ellipsen (**...**) och väljer sedan **Redigera**.

1. Ange ett nytt tillstånd för funktionsflaggan.

## <a name="access-feature-flags"></a>Flaggor för accessfunktion

Funktionsflaggor som skapats av Funktionshanteraren lagras och hämtas som vanliga nyckelvärden. De förvaras under ett speciellt namnområdesprefix. `.appconfig.featureflag` Om du vill visa de underliggande nyckelvärdena använder du Konfigurationsutforskaren. Programmet kan hämta dessa värden med hjälp av konfigurationsleverantörer för appkonfiguration, SDK:er, kommandoradstillägg och REST-API:er.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du hanterar funktionsflaggor och deras tillstånd med hjälp av Appkonfiguration. Mer information om stöd för funktionshantering i Appkonfiguration och ASP.NET Core finns i följande artikel:

* [Använda funktionsflaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md)
