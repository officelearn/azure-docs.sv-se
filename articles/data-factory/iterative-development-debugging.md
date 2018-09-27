---
title: Iterativ utveckling och felsökning i Azure Data Factory | Microsoft Docs
description: Lär dig hur du utvecklar och felsöker Data Factory-pipelines upprepade gånger i Azure-portalen.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: 610f0c8691714bf30415347dd2775b6fa3625c11
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391109"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativ utveckling och felsökning med Azure Data Factory

Azure Data Factory kan du utveckla och felsöka Data Factory-pipelines upprepade gånger.

Titta på följande videoklipp för ett 8-minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iterativ felsökningsfunktioner
Skapa pipelines och testa med den **felsöka** funktionen i rityta för pipelinen utan att behöva skriva en enda rad kod.

![Felsöka-funktionen på ritytan för din pipeline](media/iterative-development-debugging/iterative-development-image1.png)

Visa resultatet av testet körs i den **utdata** tidsfönster rityta för pipelinen.

![Utdatafönstret för rityta för pipelinen](media/iterative-development-debugging/iterative-development-image2.png)

När en testkörning lyckas, lägga till fler aktiviteter i din pipeline och fortsätta felsökningen i en iterativ sätt. Du kan också **Avbryt** en testkörning medan det pågår.

![Avbryt en testkörning](media/iterative-development-debugging/iterative-development-image3.png)

När du testar körs kan du inte behöver publicera ändringarna till data factory innan du väljer **felsöka**. Den här funktionen är användbart i scenarier där du vill se till att ändringarna fungerar som förväntat innan du uppdaterar data factory-arbetsflöde.

> [!IMPORTANT]
> Att välja **felsöka** faktiskt kör pipelinen. Så till exempel kopierar om det innehåller kopieringsaktiviteten i pipelinen, test-körningen data från källa till mål. Därför rekommenderar vi att du använder test mappar i dina kopieringsaktiviteter och andra aktiviteter när du felsöker. När du har den felsöks pipelinen, byta till de mappar som du vill använda under normal drift.

## <a name="visualizing-debug-runs"></a>Visualiserar debug körs

Du kan visualisera alla debug-körningar som pågår för din datafabrik i ett och samma ställe. Välj **visa debug körs** i det övre högra hörnet på sidan. Den här funktionen är användbart i scenarier där du har master pipelines drog debug körningar för underordnade pipelines och du vill att en enda vy för att se alla aktiva debug körs.

![Välj ikonen Visa aktiva debug körningar](media/iterative-development-debugging/view-debug-runs-image1.png)

![Exempellistan över aktiva debug körningar](media/iterative-development-debugging/view-debug-runs-image2.png)

## <a name="monitoring-debug-runs"></a>Övervaka debug körs

Testet körs initierades med den **felsöka** funktionen är inte tillgängliga i listan på den **övervakaren** fliken. Du kan bara se körs utlösta med **Utlös nu**, **schema**, eller **rullande fönster** utlöser i den **övervakaren** fliken. Du kan se den senaste testkörning initierades med den **felsöka** kapaciteten i den **utdata** tidsfönster rityta för pipelinen.

## <a name="setting-breakpoints-for-debugging"></a>Ange brytpunkter för felsökning

Data Factory kan du felsöka tills du når en viss aktivitet på rityta för pipelinen. Lägg bara till en brytpunkt på den aktivitet som du vill testa och välj **felsöka**. Data Factory säkerställer att testet körs tills aktiviteten brytpunkt på rityta för pipelinen. Detta *felsöka tills* funktionen är användbar när du inte vill att testa hela pipelinen, men endast en delmängd av aktiviteter i pipelinen.

![Brytpunkter på rityta för pipelinen](media/iterative-development-debugging/iterative-development-image4.png)

Ange en brytpunkt genom att markera ett element på rityta för pipelinen. En *felsöka tills* alternativet visas som en tom röd cirkel i det övre högra hörnet av elementet.

![Innan du anger en brytpunkt på det valda elementet](media/iterative-development-debugging/iterative-development-image5.png)

När du har valt den *felsöka tills* alternativet ändras till en fylld röd cirkel att visa brytpunkten har aktiverats.

![När du har angett en brytpunkt på det valda elementet](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Nästa steg
[Kontinuerlig integrering och distribution i Azure Data Factory](continuous-integration-deployment.md)
