---
ms.openlocfilehash: fa0c2f5bb00122b40fb4f4ea06b7cf55c0248904
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025149"
---

![Översikt](../../../media/quickstarts/gRPC-extension.svg)

Det här diagrammet visar hur signal flödet i den här snabb starten. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real Time Streaming Protocol). En [RTSP-källmapp](../../../media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video bild rutor till noden för [avkänning av rörelser](../../../media-graph-concept.md#motion-detection-processor) . Den här processorn identifierar rörelse och när identifieringen ska skickas, skickas video bild rutor till [gRPC-tillägget processor](../../../media-graph-concept.md#grpc-extension-processor) .

Noden gRPC-tillägg spelar rollen som en proxy. Den konverterar video bild rutorna till den angivna bild typen. Sedan vidarebefordrar den avbildningen över gRPC till en annan Edge-modul som kör en AI-modell bakom en gRPC-slutpunkt över ett [delat minne](https://en.wikipedia.org/wiki/Shared_memory). I det här exemplet skapas en Edge-modul med hjälp av [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) -modellen, som kan identifiera många typer av objekt. GRPC Extension processor-noden samlar in identifierings resultaten och publicerar händelser till noden [IoT Hub mottagare](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink) . Noden skickar sedan händelserna till [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

I den här snabb starten kommer du att:

1. Skapa och distribuera medie diagrammet.
1. Tolka resultatet.
1. Rensa resurser.
