---
title: Prestanda- och belastningstestning med Azure Application Insights | Microsoft-dokument
description: Konfigurera prestanda- och belastningstester med Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669870"
---
# <a name="performance-testing"></a>Prestandatestning

> [!NOTE]
> Den molnbaserade lasttestningstjänsten har inaktuell. Mer information om utfasningen, tjänstens tillgänglighet och alternativa tjänster finns [här](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights kan du generera belastningstester för dina webbplatser. Precis som [tillgänglighetstester](monitor-web-app-availability.md)kan du skicka antingen grundläggande begäranden eller [begäranden i flera steg](availability-multistep.md) från Azure-testagenter runt om i världen. Med prestandatester kan du simulera upp till 20 000 samtidiga användare i upp till 60 minuter.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

För att skapa ett prestandatest måste du först skapa en Application Insights-resurs. Om du redan har skapat en resurs fortsätt till nästa avsnitt.

På Azure-portalen väljer du **Skapa en resurs** > **developer tools** > **application insights** och skapar en Application Insights-resurs.

## <a name="configure-performance-testing"></a>Konfigurera prestandatestning

Om det är första gången du skapar prestandatest väljer du **Ange organisation** och väljer en Azure DevOps-organisation som källa för dina prestandatester.

Under **Konfigurera**går du till **Prestandatestning** och klickar på **Nytt** för att skapa ett test.

![Fyll åtminstone i URL:en för din webbplats](./media/performance-testing/new-performance-test.png)

Om du vill skapa ett grundläggande prestandatest väljer du en testtyp av **manuellt test** och fyller i önskade inställningar för testet.

|Inställning| Maxvärde
|----------|------------|
| Användarinläsning | 20 000 |
| Varaktighet (minuter)  | 60 |  

När testet har skapats klickar du på **Kör test**.

När testet är klart ser du resultat som liknar resultaten nedan:

![Testresultat](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfigurera webbtest för Visual Studio

Application Insights avancerade prestandatestfunktioner bygger på prestanda- och belastningstestprojekt i Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Nästa steg

* [Webbtester med flera steg](availability-multistep.md)
* [Url ping tester](monitor-web-app-availability.md)
