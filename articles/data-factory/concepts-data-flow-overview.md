---
title: Översikt över Azure Data Factory mappning av data flöde
description: En översikt över hur du kopplar data flöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123958"
---
# <a name="what-are-mapping-data-flows"></a>Vad är det för att mappa data flöden?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mappning av data flöden är visuellt utformad data omvandling i Azure Data Factory. Data flöden gör det möjligt för data tekniker att utveckla grafisk data omvandlings logik utan att skriva kod. De resulterande data flödena körs som aktiviteter i Azure Data Factory pipelines med utskalade Azure Databricks kluster.

Avsikten med Azure Data Factory data flödet är att ge en helt visuell upplevelse utan att behöva koda. Dina data flöden körs i ditt eget körnings kluster för att skala ut data bearbetningen. Azure Data Factory hanterar all kod översättning, Sök vägs optimering och körning av dina data flödes jobb.

Börja med att skapa data flöden i fel söknings läge så att du kan validera omvandlings logiken interaktivt. Lägg sedan till en data flödes aktivitet i din pipeline för att köra och testa ditt data flöde i fel sökning av pipeline, eller Använd "Utlös nu" i pipelinen för att testa data flödet från en pipeline-aktivitet.

Sedan kan du schemalägga och övervaka dina data flödes aktiviteter med hjälp av Azure Data Factory pipelines som kör data flödes aktiviteten.

Växlings läget för växlings läge på data flödets design yta möjliggör interaktiv skapande av data transformationer. Fel söknings läge tillhandahåller en data förberedelse miljö för förberedelse av data och data flödes utveckling.

## <a name="begin-building-your-data-flow-logical-graph"></a>Börja skapa ett logiskt diagram för data flöde

Börja skapa data flöden genom att använda plus tecknet under fabriks resurser för att skapa ett nytt data flöde.

![nytt data flöde](media/data-flow/newdataflow2.png "nytt data flöde")

Börja med att konfigurera din käll omvandling och Lägg sedan till data omvandlingen i varje efterföljande steg med hjälp av +-tecknet. När du skapar ett logiskt diagram kan du växla mellan diagram-och konfigurations lägen med hjälp av knappen "Visa diagram" och "Dölj Graf".

![Visa diagram](media/data-flow/showg.png "Visa diagram")

## <a name="configure-transformation-logic"></a>Konfigurera omvandlings logik

![Dölj diagram](media/data-flow/hideg.png "Dölj diagram")

Genom att dölja diagrammet kan du navigera bland dina Transformations noder senare.

![Navigera](media/data-flow/showhide.png "navigera")

## <a name="next-steps"></a>Nästa steg

* [Börja med en käll omvandling](data-flow-source.md)
