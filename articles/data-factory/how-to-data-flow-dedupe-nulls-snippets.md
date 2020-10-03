---
title: Avduplicera rader och hitta null-värden med data Flow-kodfragment
description: Lär dig hur du kan avduplicera rader och hitta null-värden med kodfragment i data flöden
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666525"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Avduplicera rader och hitta null-värden med data Flow-kodfragment

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Genom att använda kodfragment i mappnings data flöden kan du enkelt utföra vanliga uppgifter, t. ex. datadeduplicering och null-filtrering. I den här instruktions guiden lär du dig hur du lägger till dessa funktioner i dina pipelines enkelt med hjälp av skript kodfragment för data flödet.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj **+ ny pipeline** för att skapa en ny pipeline.

2. Lägg till en data flödes aktivitet.

3. Lägg till en käll omvandling och Anslut den till någon av dina data uppsättningar

    ![Käll kods tycke 2](media/data-flow/snippet-adf-2.png)

4. I kontroll avsnittet deduplicera och NULL används generiska mönster som använder schemat för data flödes schema så att de fungerar med scheman från din data uppsättning eller med data uppsättningar som inte har något fördefinierat schema.

5. [Gå till dokumentations sidan för data flödes skript och kopiera kodfragmentet för distinkta rader.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. I gränssnittet för data flödes designern klickar du på skript knappen längst upp till höger för att öppna skript redigeraren bakom data flödes diagrammet.

    ![Käll kods tycke 3](media/data-flow/snippet-adf-3.png)

7. Efter definitionen för ```source1``` i skriptet, tryck på RETUR och klistra in i kodfragmentet.

8. Du ansluter det inklistrade kodfragmentet till den tidigare käll omvandling som du skapade i diagrammet genom att skriva "source1" framför den inklistrade koden.

9. Du kan också ansluta den nya omvandlingen i designern genom att välja den inkommande data strömmen från noden ny omvandling i diagrammet.

    ![Käll kods tycke 4](media/data-flow/snippet-adf-4.png)

10. Nu kommer ditt data flöde att ta bort dubbla rader från källan med hjälp av den sammanställda omvandlingen som grupperas efter alla rader med hjälp av en allmän hash i alla kolumn värden.
    
11. Härnäst ska vi lägga till ett kodfragment för att dela data i en data ström som innehåller rader med NULL-värden och en data ström som inte har några NULL-värden.

12. [Gå tillbaka till kod biblioteket och den här gången kopierar du koden för NULL-kontrollerna.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. I data flödes designern klickar du på skript igen och klistrar in den här nya Transformations koden längst ned, och kopplar den till din tidigare omvandling genom att skriva namnet på omvandlingen framför det inklistrade kodfragmentet.

14. Ditt data flödes diagram bör nu se ut ungefär så här:

    ![Käll kods tycke 1](media/data-flow/snippet-adf-1.png)

  Nu har du ett fungerande data flöde med allmänna deduping och NULL-kontroller genom att ta befintliga kodfragment från data flödets skript bibliotek och lägga till dem i din befintliga design.

## <a name="next-steps"></a>Nästa steg

* Skapa resten av data flödes logiken med hjälp av [omvandlingar](concepts-data-flow-overview.md)av data flöden.
