---
title: Iterativ utveckling och felsökning i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att utveckla och felsöka Data Factory pipelines upprepade gånger i Azure-portalen.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 04/16/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: b6a6fbd721e4f0af977fe7fa03adc4459de7611f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativ utveckling och felsökning med Azure Data Factory

Azure Data Factory kan du utveckla och felsöka Data Factory pipelines upprepade gånger.

## <a name="iterative-debugging-features"></a>Iterativ felsöka funktioner
Skapa pipelines och testa körs med hjälp av den **felsöka** kapaciteten i pipeline-arbetsytan utan att skriva en enda rad kod.

![Felsöka kapaciteten på pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image1.png)

Visa resultatet av testet körs i den **utdata** fönster för pipeline-arbetsytan.

![Utdatafönstret av pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image2.png)

När en testkörning lyckas, lägga till flera aktiviteter i din pipeline och fortsätta felsökningen iterativ sätt. Du kan också **Avbryt** testa medan det pågår.

![Avbryt testa](media/iterative-development-debugging/iterative-development-image3.png)

När du testa kör du behöver publicera ändringarna innan du väljer datafabriken **felsöka**. Det här är användbart i scenarier där du vill kontrollera att ändringarna som fungerar som förväntat innan du uppdaterar data factory-arbetsflöde.

## <a name="more-info-about-debugging"></a>Mer information om felsökning

1. Testet körs initieras med de **felsöka** kapaciteten är inte tillgängliga i listan på den **övervakaren** fliken. Du kan bara se körs utlösta med **utlösaren nu**, **schema**, eller **rullande fönster** utlöser i den **övervakaren** fliken. Du kan se det senaste testet kör initieras med de **felsöka** kapaciteten i den **utdata** fönster för pipeline-arbetsytan.

2. Att välja **felsöka** faktiskt kör pipeline. Så, till exempel om pipelinen innehåller kopieringsaktiviteten, kör test kopierar data från källan till mål. Därför rekommenderar vi att du använder test mappar i dina kopiera aktiviteter och andra aktiviteter vid felsökning. När du har felsöks pipeline, växla till de mappar som du vill använda under normal drift.

## <a name="setting-breakpoints-for-debugging"></a>Ange brytpunkter för felsökning

Data Factory kan du felsöka tills du når en viss aktivitet på arbetsytan pipeline. Fört in en brytpunkt på aktiviteten som du vill testa och välj **felsöka**. Data Factory säkerställer att testet körs endast till aktiviteten brytpunkt på arbetsytan pipeline. Detta *Debug tills* funktionen är praktisk när du inte vill testa hela pipeline, men bara en del av aktiviteter i pipelinen.

![Brytpunkter på pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-image4.png)

Markera ett element på arbetsytan pipeline för att ställa in en brytpunkt. En *Debug tills* alternativet visas som en tom röd cirkel i det övre högra hörnet av elementet.

![Innan du ställer in en brytpunkt på det valda elementet](media/iterative-development-debugging/iterative-development-image5.png)

När du har valt den *Debug tills* alternativet ändras till en fylld röd cirkel att ange den brytpunkten har aktiverats.

![När du har angett en brytpunkt på det valda elementet](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Nästa steg
[Kontinuerlig integrering och distribution i Azure Data Factory](continuous-integration-deployment.md)
