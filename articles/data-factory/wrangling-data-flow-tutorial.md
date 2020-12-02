---
title: Komma igång med datatransformering Data Flow i Azure Data Factory
description: En själv studie kurs om hur du förbereder data i Azure Data Factory med datatransformering Data Flow
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 5ee1069ca24dc16a52d30450c0f2163130f549cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494910"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Förbereda data med datatransformering data flöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> Datatransformering data flöde är för närvarande tillgängliga i offentlig för hands version

## <a name="create-a-wrangling-data-flow"></a>Skapa ett datatransformering-dataflöde

Det finns två sätt att skapa ett datatransformering-data flöde i Azure Data Factory. Ett sätt är att klicka på plus ikonen och välja **data flöde** i fönstret fabriks resurser.

![Skärm bild som visar data flöde i fönstret fabriks resurser.](media/wrangling-data-flow/tutorial7.png)

Den andra metoden är i fönstret aktiviteter i pipeline-arbetsytan. Öppna draget **flytta och transformera** och dra **data flödes** aktiviteten till arbets ytan.

I båda metoderna, i sidofönstret som öppnas, väljer du **Skapa nytt data flöde** och väljer **datatransformering Data Flow**. Klicka på OK.

![Skärm bild som visar alternativet Datatransformering Data Flow.](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Redigera ett datatransformering-dataflöde

Lägg till en **käll data uppsättning** för ditt datatransformering-dataflöde. Du kan antingen välja en befintlig data uppsättning eller skapa en ny. Du kan också välja en data uppsättning för mottagare. Du kan välja en eller flera käll data uppsättningar, men endast en mottagare tillåts för tillfället. Det är valfritt att välja en mottagar data uppsättning, men minst en käll data uppsättning krävs.

> [!NOTE]
> Endast ADLS gen 2-avgränsad text stöds för begränsad för hands version. 

![Datatransformering](media/wrangling-data-flow/tutorial4.png)

Klicka på **skapa** för att öppna Power Query online mashup-redigeraren.

![Skärm bild som visar knappen Skapa som öppnar Power Query online mashup-redigeraren.](media/wrangling-data-flow/tutorial5.png)

Redigera ditt datatransformering-dataflöde med hjälp av kod fri förberedelse av data. En lista över tillgängliga funktioner finns i [omvandlings funktioner](wrangling-data-flow-functions.md).

![Skärm bild som visar processen för att redigera ditt datatransformering-dataflöde.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Köra och övervaka ett datatransformering-dataflöde

Om du vill köra en fel söknings körning av en pipeline för ett datatransformering-dataflöde klickar du på **Felsök** i pipeline-arbetsytan. När du har publicerat ditt data flöde kör **utlösaren nu** en körning på begäran av den senast publicerade pipelinen. Datatransformering data flöden kan schemaläggas med alla befintliga Azure Data Factory-utlösare.

![Skärm bild som visar hur du lägger till ett datatransformering-dataflöde.](media/wrangling-data-flow/tutorial3.png)

Gå till fliken **övervaka** för att visualisera utdata från en utlöst datatransformering data flödes aktivitet körning.

![Skärm bild som visar utdata från körningen av en utlöst datatransformering data flödes aktivitet.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett data flöde för mappning](tutorial-data-flow.md).