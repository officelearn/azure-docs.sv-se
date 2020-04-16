---
title: Iterativ utveckling och felsökning i Azure Data Factory
description: Lär dig hur du utvecklar och felsöker Data Factory-pipelines iterativt i Azure-portalen.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411516"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativ utveckling och felsökning med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med Azure Data Factory kan du iterativt utveckla och felsöka datafabrikspipelor.

För en åtta minuters introduktion och demonstration av den här funktionen, titta på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iterativa felsökningsfunktioner
Skapa pipelines och gör testkörningar med hjälp av **felsökningsfunktionen** på pipeline-arbetsytan utan att skriva en enda kodrad.

![Felsökningsfunktion på pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image1.png)

Visa resultaten av dina testkörningar i **utdatafönstret** på pipeline-arbetsytan.

![Utdatafönster för pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image2.png)

När en testkörning lyckas lägger du till fler aktiviteter i pipelinen och fortsätter att felsöka på ett iterativt sätt. Du kan också **avbryta** en provkörning medan den pågår.

![Avbryta en testkörning](media/iterative-development-debugging/iterative-development-image3.png)

När du gör testkörningar behöver du inte publicera ändringarna i datafabriken innan du väljer **Felsökning**. Den här funktionen är användbar i scenarier där du vill vara säker på att ändringarna fungerar som förväntat innan du uppdaterar arbetsflödet för datafabriken.

> [!IMPORTANT]
> Om du väljer **Felsök** körs pipelinen. Om pipelinen till exempel innehåller kopieringsaktivitet kopierar testet data från källa till mål. Därför rekommenderar vi att du använder testmappar i dina kopieringsaktiviteter och andra aktiviteter vid felsökning. När du har av misstagit pipelinen växlar du till de faktiska mappar som du vill använda i normala åtgärder.

## <a name="visualizing-debug-runs"></a>Visualisera felsökningskörningar

Du kan visualisera alla felsökningskörningar som pågår för datafabriken på ett ställe. Välj **Visa felsökningskörningar** i det övre högra hörnet på sidan. Den här funktionen är användbar i scenarier där du har huvudpipeldier som startar felsökningskörningar för underordnade pipelines och du vill att en enda vy ska se alla aktiva felsökningskörningar.

![Välj ikonen Visa aktiva felsökningskörningar](media/iterative-development-debugging/view-debug-runs-image1.png)

![Exempellista över aktiva felsökningskörningar](media/iterative-development-debugging/view-debug-runs-image2.png)

Om du har aktiva felsökningssessioner för dataflöde visas dessa sessioner på den nedre delen av det aktiva felsökningsfönstret. Du kan välja en aktiv dataflödessession och stoppa respektive kluster.

![Exempellista över aktiva felsökningskörningar för dataflöde](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Övervakning av felsökningskörningar

De testkörningar som initierats med **felsökningsfunktionen** är inte tillgängliga i listan på fliken **Bildskärm.** Du kan bara se körningar som utlöses med **utlösare nu,** **Schemaeller** **Tumlande** fönsterutlösare på fliken **Bildskärm.** Du kan se den senaste testkörningen som initierats med **felsökningsfunktionen** i **utdatafönstret** på pipeline-arbetsytan.

## <a name="setting-breakpoints-for-debugging"></a>Ange brytpunkter för felsökning

Med Data Factory kan du också felsöka tills du når en viss aktivitet på pipeline-arbetsytan. Sätt bara en brytpunkt på aktiviteten tills du vill testa och välj **Felsökning**. Data Factory säkerställer att testet bara körs tills brytpunktsaktiviteten på pipeline-arbetsytan. Den här *felsökningsfunktionen tills* funktionen är användbar när du inte vill testa hela pipelinen, men bara en delmängd aktiviteter inuti pipelinen.

![Brytpunkter på pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image4.png)

Om du vill ange en brytpunkt markerar du ett element på pipeline-arbetsytan. En *felsökning Tills* alternativet visas som en tom röd cirkel i elementets övre högra hörn.

![Innan du ställer in en brytpunkt på det markerade elementet](media/iterative-development-debugging/iterative-development-image5.png)

När du har valt alternativet *Felsökning tills* ändras den till en fylld röd cirkel för att indikera att brytpunkten är aktiverad.

![När du har ställt in en brytpunkt för det markerade elementet](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Nästa steg
[Kontinuerlig integrering och distribution i Azure Data Factory](continuous-integration-deployment.md)
