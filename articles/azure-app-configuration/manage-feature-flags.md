---
title: Självstudie för att hantera flaggor för funktionen med hjälp av Azure-Appkonfiguration | Microsoft Docs
description: I den här självstudien får du lära dig hur du hanterar funktionen flaggor separat från ditt program med Azure-Appkonfiguration
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
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412345"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Självstudier: Hantera flaggor för funktionen i Azure-Appkonfiguration

Du kan lagra alla flaggor för funktionen i Azure-Appkonfiguration och administrerar dem från en enda plats. Den har en portalens användargränssnitt, som kallas **funktionen Manager**, som utformats speciellt för funktionen flaggor. Dessutom stöder konfiguration av .NET Core funktionen flaggan dataschema internt.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Definiera och hantera funktionen flaggor i Appkonfiguration.
> * Åtkomst till funktionen flaggor från ditt program.

## <a name="create-feature-flags"></a>Skapa funktionen flaggor

Den **funktionen Manager** i Azure-portalen för konfiguration av ger ett gränssnitt för att skapa och hantera funktionen flaggor som du använder i ditt program. Följ stegen nedan för att lägga till en ny funktionsflagga för.

1. Välj **funktionen Manager** > **+ skapa** att lägga till en funktionsflagga.

    ![Flaggan funktionslista](./media/azure-app-configuration-feature-flags.png)

2. Ange ett unikt namn som är viktiga för funktionsflagga. Du behöver det här namnet att referera till flaggan i din kod.

3. Du kan också ge funktionsflagga en mer mänskliga-vänlig beskrivning.

4. Ange det ursprungliga tillståndet för funktionsflagga. Det är vanligtvis bara *på* eller *av*.

    ![Skapa funktionsflagga](./media/azure-app-configuration-feature-flag-create.png)

5. När tillståndet är *på*kan du också ange ytterligare villkor att kvalificera den med **Lägg till filter**. Ange en nyckel för inbyggda eller anpassade filter och associera parametrar. Inbyggda filtren är:

    | Nyckel | JSON-parametrar |
    |---|---|
    | Microsoft.Percentage | {”Value”: 0 – 100 procent} |
    | Microsoft.TimeWindow | {”Start”: UTC-tid, ”slutar”: UTC-tid} |

    ![Funktionen flaggan filter](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Uppdatera flaggan funktionsstatus

Följ stegen nedan om du vill ändra värdet för en funktionsflagga state.

1. Välj **funktionen Manager**.

2. Klicka på **...**   >  **Redigera** till höger om en funktionsflagga som du vill ändra.

3. Ange ett nytt tillstånd för funktionsflagga.

## <a name="access-feature-flags"></a>Åtkomst till funktionen flaggor

Funktionen flaggor som skapats av den **funktionen Manager** lagras och hämtas som vanlig nyckel-värden. De sparas under en särskild namnrymdsprefixet *. appconfig.featureflag*. Du kan visa underliggande nyckel-värden med hjälp av den **Configuration Explorer**. Programmet kan hämta dem med hjälp av konfiguration av konfigurationstjänst, SDK: er, kommandorad tillägg och REST API: er.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att hantera flaggor för funktionen och deras tillstånd med hjälp av konfiguration. Se följande resurser för mer information på funktionsstöd för hantering i Appkonfiguration och ASP.NET Core.

* [Använd funktionen flaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md)
