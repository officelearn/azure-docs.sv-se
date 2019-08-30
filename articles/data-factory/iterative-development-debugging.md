---
title: Iterativ utveckling och fel sökning i Azure Data Factory | Microsoft Docs
description: Lär dig hur du utvecklar och felsöker Data Factory pipelines iterativt i Azure Portal.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: afc51735fcbd8f4131b2a4066a0fc4adbb8f1a41
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142339"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativ utveckling och fel sökning med Azure Data Factory

Med Azure Data Factory kan du iterativt utveckla och felsöka Data Factory pipeliner.

I en åtta minuters introduktion och demonstration av den här funktionen kan du titta på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iterativa fel söknings funktioner
Skapa pipelines och kör test med hjälp av **fel söknings** funktionen i pipeline-arbetsytan utan att skriva en enda rad kod.

![Fel söknings funktion på pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image1.png)

Visa resultatet av test körningarna i fönstret **utdata** på arbets ytan för pipelinen.

![Fönstret utdata för pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image2.png)

När en testkörning lyckas lägger du till fler aktiviteter i din pipeline och fortsätter att felsöka på ett iterativt sätt. Du kan också **avbryta** en test körning medan den pågår.

![Avbryta en test körning](media/iterative-development-debugging/iterative-development-image3.png)

När du utför test körningar behöver du inte publicera dina ändringar i data fabriken innan du väljer **Felsök**. Den här funktionen är användbar i scenarier där du vill se till att ändringarna fungerar som förväntat innan du uppdaterar data Factory-arbetsflödet.

> [!IMPORTANT]
> Om du väljer **Felsök** körs pipelinen. Om pipelinen t. ex. innehåller kopierings aktivitet kopierar test körningen data från källa till mål. Därför rekommenderar vi att du använder test-mappar i dina kopierings aktiviteter och andra aktiviteter vid fel sökning. När du har felsökt pipelinen växlar du till de faktiska mapparna som du vill använda i normal drift.

## <a name="visualizing-debug-runs"></a>Visualiserar fel söknings körningar

Du kan visualisera alla fel söknings körningar som pågår för din data fabrik på ett och samma ställe. Välj **Visa fel söknings körningar** i det övre högra hörnet på sidan. Den här funktionen är användbar i scenarier där du har huvud pipeliner som startar fel söknings körningar för underordnade pipelines, och du vill ha en enda vy för att se alla aktiva fel söknings körningar.

![Välj ikonen Visa aktiva fel söknings körningar](media/iterative-development-debugging/view-debug-runs-image1.png)

![Exempel lista över aktiva fel söknings körningar](media/iterative-development-debugging/view-debug-runs-image2.png)

## <a name="monitoring-debug-runs"></a>Övervaka fel söknings körningar

Test körningar som initieras med **fel söknings** funktionen är inte tillgängliga i listan på fliken **övervaka** . Du kan bara se körningar som har utlösts med utlösare **nu**, **schema**eller **rullande fönster** utlösare på fliken **övervakning** . Du kan se att den senaste test körningen har initierats med **fel söknings** funktionen i fönstret **utdata** på pipeline-arbetsytan.

## <a name="setting-breakpoints-for-debugging"></a>Ange Bryt punkter för fel sökning

Data Factory kan också felsöka tills du når en viss aktivitet på pipeline-arbetsytan. Placera bara en Bryt punkt för aktiviteten tills du vill testa och välj **Felsök**. Data Factory säkerställer att testet bara körs tills Bryt punkts aktiviteten på pipeline-arbetsytan. Den här *fel sökningen tills* funktionen är användbar när du inte vill testa hela pipelinen, men endast en delmängd av aktiviteter i pipelinen.

![Bryt punkter på pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image4.png)

Om du vill ange en Bryt punkt väljer du ett element på pipeline-arbetsytan. Ett *fel söknings* alternativ visas som en tom röd cirkel i det övre högra hörnet av elementet.

![Innan du anger en Bryt punkt för det valda elementet](media/iterative-development-debugging/iterative-development-image5.png)

När du har valt alternativet *Felsök tills* ändras den till en fylld röd cirkel som visar att Bryt punkten är aktive rad.

![När du har angett en Bryt punkt för det valda elementet](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Nästa steg
[Kontinuerlig integrering och distribution i Azure Data Factory](continuous-integration-deployment.md)
