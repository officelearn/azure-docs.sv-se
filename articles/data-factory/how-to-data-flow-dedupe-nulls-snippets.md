---
title: Ta undupe-rader och hitta null-värden med hjälp av data Flow-kodfragment
description: Lär dig hur du kan avduplicera rader och hitta null-värden med hjälp av kodfragment i data flöden
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 1c630cdd66fa4f8e609524feb9c3f0bcad9711a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458174"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Ta undupe-rader och hitta null-värden med hjälp av data Flow-kodfragment

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Genom att använda kodfragment i mappnings data flöden kan du enkelt utföra vanliga uppgifter, till exempel datadeduplicering och null-filtrering. I den här artikeln förklaras hur du enkelt lägger till dessa funktioner i pipelinen med hjälp av skript kodfragment för data flöde.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj **Ny pipeline**.

1. Lägg till en data flödes aktivitet.

1. Välj fliken **käll inställningar** , Lägg till en käll omvandling och Anslut den sedan till någon av dina data uppsättningar.

    ![Skärm bild av fönstret "käll inställningar" för att lägga till en källtyp.](media/data-flow/snippet-adf-2.png)

    I check-kodfragmenten deduplicera och null används generiska mönster som drar nytta av data flödets schema avvikelse. Kodfragmenten fungerar med scheman från din data uppsättning eller med data uppsättningar som inte har något fördefinierat schema.

1. I avsnittet "distinkt rad med alla kolumner" i [data flödes skriptet (DFS)](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns), kopierar du kodfragmentet för DistinctRows.

1. I gränssnittet för data flödes designern väljer du knappen **skript** längst upp till höger för att öppna skript redigeraren bakom data flödes diagrammet.

    ![Skärm bild av ett käll kods tycke.](media/data-flow/snippet-adf-3.png)

1. Efter definitionen för i skriptet, `source1` Tryck på RETUR och klistra in kodfragmentet.

1. Gör något av följande:

   * Anslut det inklistrade kodfragmentet till käll omvandlingen som du skapade tidigare i diagrammet genom att skriva **source1** framför den inklistrade koden.

   * Du kan också ansluta den nya omvandlingen i designern genom att välja den inkommande data strömmen från noden ny omvandling i diagrammet.

     ![Skärm bild av fönstret "inställningar för villkorlig delning".](media/data-flow/snippet-adf-4.png)

   Nu kommer ditt data flöde att ta bort dubbla rader från källan med hjälp av den sammanställda omvandlingen, som grupperas efter alla rader genom att använda en allmän hash i alla kolumn värden.
    
1. Lägg till ett kodfragment för att dela data i en data ström som innehåller rader med null-värden och en annan ström utan null-värden. Så här gör du:

   a. Gå tillbaka till [kodfragments biblioteket](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)och kopiera sedan koden för kontrollerna null.

   b. I data flödes designern väljer du **skript** igen och klistrar sedan in den nya omvandlings koden längst ned. Den här åtgärden ansluter skriptet till din tidigare omvandling genom att placera namnet på den omvandlingen framför det inklistrade kodfragmentet.

   Ditt data flödes diagram bör nu se ut ungefär så här:

    ![Skärm bild av data flödes diagrammet.](media/data-flow/snippet-adf-1.png)

  Nu har du skapat ett fungerande data flöde med allmänna deduping och null-kontroller genom att ta befintliga kodfragment från data flödets skript bibliotek och lägga till dem i din befintliga design.

## <a name="next-steps"></a>Nästa steg

* Skapa resten av data flödes logiken med hjälp av [omvandlingar](concepts-data-flow-overview.md)av data flöden.
