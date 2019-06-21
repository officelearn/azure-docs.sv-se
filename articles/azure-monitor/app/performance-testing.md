---
title: Prestanda och belastningstest med Azure Application Insights | Microsoft Docs
description: Konfigurera prestanda- och belastningstester med Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305019"
---
# <a name="performance-testing"></a>Prestandatestning

> [!NOTE]
> Det molnbaserade belastningstestet är inaktuell. Mer information om utfasningen, tjänstens tillgänglighet och alternativa tjänster finns [här](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights kan du skapa belastningstester för din webbplats. Som [tillgänglighetstester](monitor-web-app-availability.md), kan du skicka antingen enkla begäranden eller [begäranden med flera steg](availability-multistep.md) från Azure webbtestagenter runt om i världen. Prestandatester kan du simulera upp till 20 000 samtidiga användare i upp till 60 minuter.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

För att skapa ett prestandatest, måste du först skapa en Application Insights-resurs. Om du redan har skapat en resurs du vidare till nästa avsnitt.

Azure-portalen väljer du **skapa en resurs** > **utvecklarverktyg** > **Application Insights** och skapa en Application Insights resursen.

## <a name="configure-performance-testing"></a>Konfigurera prestandatestning

Om det här är första gången du skapar prestanda test väljer **ange organisation** och välj en Azure DevOps-organisation som källa för din prestandatester.

Under **konfigurera**går du till **prestandatestning** och klicka på **New** att skapa ett test.

![Fyll åtminstone i URL:en för din webbplats](./media/performance-testing/new-performance-test.png)

Om du vill skapa ett grundläggande prestandatest, Välj testtypen **manuell testa** och fyll sedan önskade inställningar för testet.

|Inställning| Maxvärde
|----------|------------|
| Användarbelastning | 20,000 |
| Varaktighet (minuter)  | 60 |  

När testet har skapats klickar du på **Kör test**.

När testet är klart visas resultat som liknar agentdistributionresultaten nedan:

![Testresultat](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfigurera Visual Studio-webbtest

Application Insights avancerad prestanda testfunktionerna är byggda på Visual Studio-prestanda och belastning testa projekt.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Nästa steg

* [Webbtester med flera steg](availability-multistep.md)
* [URL-Pingtest](monitor-web-app-availability.md)