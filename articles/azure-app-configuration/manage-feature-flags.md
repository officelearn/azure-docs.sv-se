---
title: Självstudie för att hantera flaggor för funktionen med hjälp av Azure-Appkonfiguration | Microsoft Docs
description: Lär dig hur du hanterar funktionen flaggor separat från ditt program med hjälp av Azure-Appkonfiguration i de här självstudierna.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b7fbf9add67a45c0db89fc11cee5c10bc537ab63
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393575"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Självstudier: Hantera flaggor för funktionen i Azure-Appkonfiguration

Du kan lagra alla flaggor för funktionen i Azure-Appkonfiguration och administrerar dem från en enda plats. Konfiguration av har en portal som Användargränssnittet med namnet **funktionen Manager** som har utformats speciellt för funktionen flaggor. Konfiguration av har också har inbyggt stöd för .NET Core-funktionsflagga dataschemat.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Definiera och hantera funktionen flaggor i Appkonfiguration.
> * Åtkomst till funktionen flaggor från ditt program.

## <a name="create-feature-flags"></a>Skapa funktionen flaggor

Hanteraren för funktionen i Azure-portalen för Appkonfiguration ger ett gränssnitt för att skapa och hantera flaggor för funktionen som du använder i dina program.

Lägga till en ny funktionsflagga för:

1. Välj **funktionen Manager** >  **+ Lägg till** att lägga till en funktionsflagga.

    ![Flaggan funktionslista](./media/azure-app-configuration-feature-flags.png)

1. Ange ett unikt namn som är viktiga för funktionsflagga. Du behöver det här namnet att referera till flaggan i din kod.

1. Om du vill ge en beskrivning i funktionsflagga.

1. Ange det ursprungliga tillståndet för funktionsflagga. Det här tillståndet är vanligtvis *av* eller *på*. Den *på* tillståndet ändras till *villkorlig* om du lägger till ett filter funktionsflagga.

    ![Skapa en funktion flagga](./media/azure-app-configuration-feature-flag-create.png)

1. När tillståndet är *på*väljer **+ Lägg till filter** ange eventuella ytterligare villkor måste anges för programmets status. Ange en nyckel för inbyggda eller anpassade filter och välj sedan **+ Lägg till parameter** att associera en eller flera parametrar med filtret. Inbyggda filtren är:

    | Nyckel | JSON-parametrar |
    |---|---|
    | Microsoft.Percentage | {”Value”: 0 – 100 procent} |
    | Microsoft.TimeWindow | {”Start”: UTC-tid, ”slutar”: UTC-tid} |

    ![Funktionen flaggan filter](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Uppdatera flaggan funktionsstatus

Ändra värdet för en funktionsflagga state:

1. Välj **funktionen Manager**.

1. Till höger om en funktionsflagga som du vill ändra, Välj ellipsen ( **...** ), och välj sedan **redigera**.

1. Ange ett nytt tillstånd för funktionsflagga.

## <a name="access-feature-flags"></a>Åtkomst till funktionen flaggor

Funktionen flaggor som skapats av funktionen Manager lagras och hämtas som vanlig nyckelvärden. De är kvar under en särskild namnrymdsprefixet `.appconfig.featureflag`. Använd Configuration Explorer om du vill visa de underliggande nyckelvärdena. Programmet kan hämta dessa värden med hjälp av konfiguration av konfigurationstjänst, SDK: er, kommandorad tillägg och REST API: er.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att hantera flaggor för funktionen och deras tillstånd med hjälp av konfiguration. Mer information om stöd för hantering av funktionen i Appkonfiguration och ASP.NET Core finns i följande artikel:

* [Använd funktionen flaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md)
