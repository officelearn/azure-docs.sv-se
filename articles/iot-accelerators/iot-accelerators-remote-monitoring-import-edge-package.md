---
title: Remote Monitoring solution importera Edge-paketet – Azure | Microsoft Docs
description: Den här artikeln beskrivs hur du importerar en IoT Edge-paketet till lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51828712"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importera en IoT Edge-paketet till lösningsacceleratorn för fjärrövervakning

Ett manifest för distribution definierar vilka moduler som ska distribuera till en IoT Edge-enhet. Den här artikeln förutsätter att en utvecklare i din organisation redan har skapat distribution manifest. Mer information om hur utvecklare skapar ett manifest, ser du något av följande instruktionsartiklar för IoT Edge:

- [Distribuera Azure IoT Edge-moduler från Azure portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Distribuera Azure IoT Edge-moduler med Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Distribuera Azure IoT Edge-moduler från Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

En utvecklare som skapar och testar ett manifest för distribution i en utvecklingsmiljö. När du är klar kan du importera manifestet till lösningsacceleratorn för fjärrövervakning.

## <a name="export-a-manifest"></a>Exportera ett manifest

Använd Azure-portalen för att exportera distribution manifestet från din utvecklingsmiljö:

1. Navigera till IoT-hubben som du använder för att utveckla och testa dina IoT Edge-enheter i Azure-portalen. Klicka på **IoT Edge** och sedan **IoT Edge-distributioner**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Klicka på den distribution som har distributionskonfigurationen som du vill använda. Den **distributionsinformation** visas: ![IoT Edge distributionsinformation](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Klicka på **hämta IoT Edge-manifest**: ![Download distribution manifest](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Spara JSON-filen som en lokal fil med namnet **deploymentmanifest.json**.

Nu har du en fil som innehåller manifestet distribution. I nästa avsnitt importerar du den här manifest som ett paket till lösningen för fjärrövervakning.

## <a name="import-a-package"></a>Importera ett paket

Följ stegen nedan för att importera ett manifest för Edge-distribution som ett paket i din lösning:

1. Navigera till den **paket** sidan fjärrövervakning webbläsaren: ![paket sidan](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Klicka på **+ nytt paket**, Välj **Edge Manifest** som pakettyp och klicka på **Bläddra** att välja den **deploymentmanifest.json** fil du sparade i föregående avsnitt: ![väljer manifest](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Klicka på **överför** att lägga till paketet i lösningen för fjärrövervakning: ![överförd paket](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Du har nu överfört ett manifest för IoT Edge-distribution som ett paket. På den **distributioner** kan du distribuera det här paketet till dina anslutna IoT Edge-enheter.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar moduler till en IoT Edge-enhet från lösningen för fjärrövervakning, nästa steg är att lära dig mer om [IoT Edge](../iot-edge/about-iot-edge.md).
