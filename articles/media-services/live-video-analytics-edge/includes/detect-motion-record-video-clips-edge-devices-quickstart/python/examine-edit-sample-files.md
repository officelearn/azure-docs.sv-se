---
ms.openlocfilehash: ce5611a92e9899d64ff2117385af008c37c22c5e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682326"
---
Som en del av kraven för den här snabb starten laddade du ned exempel koden till en mapp. Följ dessa steg om du vill undersöka och redigera exempel koden.

1. I Visual Studio Code går du till *src/Edge*. Du ser din *. kuvert* -fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten, där variabler används för vissa egenskaper. *. Miljö* filen innehåller värdena för variablerna.
1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:
    * ***operations.jsi*** listan över åtgärder som du vill att programmet ska köra.
    * ***main.py*** – exempel koden. Den här koden:

        * Läser in appinställningar.
        * Anropar direkta metoder som visar IoT Edge modulen för video analys i real tid. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](../../../direct-methods.md). 
        * Pausar så att du kan granska utdata från programmet i **terminalfönstret** och granska de händelser som genererats av modulen i fönstret **utdata** .
        * Anropar direkta metoder för att rensa resurser.

1. Redigera *operations.jspå* filen:
    * Ändra länken till graf-topologin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Under `GraphInstanceSet` redigerar du namnet på diagram sto pol Ogin så att det matchar värdet i föregående länk:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Redigera RTSP-URL: en för att peka på video filen:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Under `GraphTopologyDelete` redigerar du namnet:

        `"name": "EVRToFilesOnMotionDetection"`
