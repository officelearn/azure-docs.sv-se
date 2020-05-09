---
title: Komma igång med datatransformering Data Flow i Azure Data Factory
description: En själv studie kurs om hur du förbereder data i Azure Data Factory med datatransformering Data Flow
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: f5a7f372f286a7b26a4a9916ed9df913b151e967
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628477"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Förbereda data med datatransformering data flöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Skapa ett datatransformering-dataflöde

Det finns två sätt att skapa ett datatransformering-data flöde i Azure Data Factory. Ett sätt är att klicka på plus ikonen och välja **data flöde** i fönstret fabriks resurser.

![Datatransformering](media/wrangling-data-flow/tutorial7.png)

Den andra metoden är i fönstret aktiviteter i pipeline-arbetsytan. Öppna draget **flytta och transformera** och dra **data flödes** aktiviteten till arbets ytan.

I båda metoderna, i sidofönstret som öppnas, väljer du **Skapa nytt data flöde** och väljer **datatransformering Data Flow**. Klicka på OK.

![Datatransformering](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Redigera ett datatransformering-dataflöde

Lägg till en **käll data uppsättning** för ditt datatransformering-dataflöde. Du kan antingen välja en befintlig data uppsättning eller skapa en ny. Du kan också välja en data uppsättning för mottagare. Du kan välja en eller flera käll data uppsättningar, men endast en mottagare tillåts för tillfället. Det är valfritt att välja en mottagar data uppsättning, men minst en käll data uppsättning krävs.

> [!NOTE]
> Endast ADLS gen 2-avgränsad text stöds för begränsad för hands version. 

![Datatransformering](media/wrangling-data-flow/tutorial4.png)

Klicka på **skapa** för att öppna Power Query online mashup-redigeraren.

![Datatransformering](media/wrangling-data-flow/tutorial5.png)

Redigera ditt datatransformering-dataflöde med hjälp av kod fri förberedelse av data. En lista över tillgängliga funktioner finns i [omvandlings funktioner](wrangling-data-flow-functions.md)/

![Datatransformering](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Köra och övervaka ett datatransformering-dataflöde

Om du vill köra en fel söknings körning av en pipeline för ett datatransformering-dataflöde klickar du på **Felsök** i pipeline-arbetsytan. När du har publicerat ditt data flöde kör **utlösaren nu** en körning på begäran av den senast publicerade pipelinen. Datatransformering data flöden kan schemaläggas med alla befintliga Azure Data Factory-utlösare.

![Datatransformering](media/wrangling-data-flow/tutorial3.png)

Gå till fliken **övervaka** för att visualisera utdata från en utlöst datatransformering data flödes aktivitet körning.

![Datatransformering](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett data flöde för mappning](tutorial-data-flow.md).