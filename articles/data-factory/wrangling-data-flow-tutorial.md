---
title: Komma igång med käbbel dataflöde i Azure Data Factory
description: En självstudiekurs om hur du förbereder data i Azure Data Factory med hjälp av käbbel dataflöde
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8ae109045381dba77610dedc5bb95e233b213eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682268"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Förbereda data med käbbel dataflöde

## <a name="create-a-wrangling-data-flow"></a>Skapa ett käbbeldataflöde

Det finns två sätt att skapa ett käbbeldataflöde i Azure Data Factory. Ett sätt är att klicka på plusikonen och välja **Dataflöde** i fabriksresursfönstret.

![Gräl](media/wrangling-data-flow/tutorial7.png)

Den andra metoden finns i aktivitetsfönstret på pipeline-arbetsytan. Öppna **dragspelet Flytta och omforma** och dra **dataflödesaktiviteten** till arbetsytan.

I båda metoderna väljer du **Skapa nytt dataflöde** i sidofönstret och väljer **Dataflöde för Käbbel**. Klicka på OK.

![Gräl](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Skapa ett käbbeldataflöde

Lägg till en **källdatauppsättning** för ditt käbbeldataflöde. Du kan antingen välja en befintlig datauppsättning eller skapa en ny. Du kan också välja en sink-datauppsättning. Du kan välja en eller flera källdatauppsättningar, men bara en diskho tillåts just nu. Det är valfritt att välja en sink-datauppsättning, men minst en källdatauppsättning krävs.

> [!NOTE]
> Endast ADLS Gen 2 Avgränsad text stöds för begränsad förhandsgranskning. 

![Gräl](media/wrangling-data-flow/tutorial4.png)

Klicka på **Skapa** för att öppna Power Query Online-mashupredigeraren.

![Gräl](media/wrangling-data-flow/tutorial5.png)

Skapa ditt käbbel dataflöde med hjälp av kodfria dataförberedelser. En lista över tillgängliga funktioner finns i [omformningsfunktioner](wrangling-data-flow-functions.md)/

![Gräl](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Köra och övervaka ett käbbeldataflöde

Om du vill köra en pipeline-felsökningskörning av ett käbbeldataflöde klickar du på **Felsök på** pipeline-arbetsytan. När du har publicerat dataflödet kör **Utlösaren nu** en körning på begäran av den senast publicerade pipelinen. Käbbel dataflöden kan schemaläggas med alla befintliga Azure Data Factory-utlösare.

![Gräl](media/wrangling-data-flow/tutorial3.png)

Gå till fliken **Bildskärm** om du vill visualisera utdata från en utlöst käbbeldataflödesaktivitetskörning.

![Gräl](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett mappningsdataflöde](tutorial-data-flow.md).